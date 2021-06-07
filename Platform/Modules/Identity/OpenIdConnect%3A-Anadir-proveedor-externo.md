# Como se autentica con un proveedor externo?
Como ya se menciono antes en otras partes del wiki, la nueva plataforma de Juntoz tiene un nuevo proyecto para autenticacion que lo llamamos JuntozIdentityV3 (o IdentityV3). Este se basa en OpenIdConnect para generar los tokens para toda la plataforma y utiliza la libreria `IdentityServer4` (open source).

Actualmente en la plataforma de Juntoz.com, se permite hacer login con usuario y contrasena tradicional, pero tambien permite usar Google y Facebook como proveedores externos de autenticacion.

En estos casos, el usuario cuando dice "Login con Google", el browser redirige a Google Login, el usuario ingresa su usuario y contrasena, y cuando es correcta, el browser redirige a un callback dentro de IdentityV3 donde el sistema recibe por parte del proveedor externo, las credenciales e informacion publica del usuario (tipicamente: id, email, nombre y apellidos).

El callback debe usar esta informacion para registrar al usuario dentro de la platafoma Juntoz (si es que aun no esta registrado). Este usuario se le asigna un ID unico y estara marcado como "Autenticacion Externa" (authScheme=Google) y no tiene contrasena asignada. 

Si es que el usuario ya habia hecho Login con Google antes, simplemente el sistema permite hacer el login automaticamente.

A partir de ahi, el usuario siempre tendra que usar "Login con Google".

Esto esta dentro ya configurado en IdentityV3 y esta tambien demostrado que funciona.

En la siguiente seccion describiriremos como anadir un nuevo proveedor externo de autenticacion.

# Como registrar un nuevo proveedor externo de autenticacion por Site?

Son varios cambios en varios APIs, asi que leer con cuidado este wiki.

## Juntoz.Api.User

Lo primero es modificar el `Api.User/core` para que acepte un nuevo valor en el campo authScheme.

```js
    authScheme: JuntozJoi.string()
        .description('which auth scheme was used to register the user')
        .required()
        .valid('Cookies', 'Google', 'Facebook', 'MiNuevoScheme'),
```

Con esto permitira registrar esos nuevos usuarios.

## Juntoz.Api.Client/id-infra
En el repo `Juntoz.Api.Client` en la carpeta `id-infra` esta el API interna hecha exclusivamente para Juntoz.Id.

En este proyecto esta el endpoint `/id/site/config/:siteid` que obtiene la informacion basica de un site y algunos entries especificos para que Juntoz.Id pueda operar. Este endpoint hay que modificarlo levemente para que extraiga mas parametros de configuracion para el nuevo proveedor de autenticacion.

```cs
const siteSettings = await this.entityService.getSiteSomeSettings(
    ctx.params.siteId,
    [
        'googleSigninClientId',
        'googleSigninClientSecret',
        'facebookSigninClientId',
        'facebookSigninClientSecret',
        'consentMarketingListNeeded',
        'MiNuevoSchemeSetting1',
        'MiNuevoSchemeSetting2'
    ]);

result.settings = siteSettings;
```

Estos settings deben registrarse en la tabla `SiteCfgItem` que la gobierna el `Api.Site/core` y la lee esta API tambien.

## Juntoz.Id

### AuthSchemeType

En el archivo `src/Eagle.OpenIdServer.Core/Infrastructure/ClientEagle.cs`, agregar el nuevo Scheme aqui tambien:

```cs
    public enum AuthSchemeType
    {
        Cookies,
        Google,
        Facebook,
        MiNuevoScheme
    }
```

El valor ingresado debe ser igual al valor que se ingreso en `Api.User` (case sensitive).

### SiteConfig
En el archivo `src/Eagle.OpenIdServer.Core/Infrastructure/JuntozIdentityInfrastructureProxy.cs` esta la clase `SiteConfig` que debe exponer los nuevos parametros para este proveedor de autenticacion (esto es mas por convencion y encapsulamiento que otra cosa, dado que el array de Settings ya esta y que proviene del endpoint `/id/site/config` descrito aqui antes).

```cs
    public class SiteConfig
    {
        public string Id { get; set; }
        public string SiteName { get; set; }
        // ...
        public string FacebookSigninClientId => GetSettingValue("facebookSigninClientId");
        public string FacebookSigninClientSecret => GetSettingValue("facebookSigninClientSecret");
        public bool AllowFacebookSignin => !string.IsNullOrEmpty(FacebookSigninClientId);

        public string NuevoSchemeSigninClientId => GetSettingValue("nuevoSchemeSigninClientId");
        public string NuevoSchemeSigninClientSecret => GetSettingValue("nuevoSchemeSigninClientSecret");
        public bool AllowNuevoSchemeSignin => !string.IsNullOrEmpty(NuevoSchemeSigninClientId);
```

### Startup_AuthSchemes

En el archivo `Startup_AuthSchemes.cs` se debe hacer varios cambios. Este archivo contiene la rutina de configuracion que ejecuta al hacer startup de la aplicacion. 

Lo primero que se tiene que hacer es registrar a todos los `Client` interactivos a traves de todos los Sites registrados (Si, en algun momento pueden haber muchos Sites y muchos clients interactivos por lo que en ese momento hay que mejorar esta parte). Tipicamente habra uno o dos client interactivos por site.

1. Entonces por cada client interactivo se le debe registrar los AuthSchemes que el Site soporta. Por defecto todos soportan Cookies, pero los externos hay que configurarlos a demanda (segun si el Site tiene los parametros configurados o no).

```cs
var authSchemes = new List<AuthSchemeType>();
authSchemes.Add(AuthSchemeType.Cookies);

if (site.AllowGoogleSignin)
{
    authSchemes.Add(AuthSchemeType.Google);
}

// ...

if (site.AllowNuevoSchemeSignin)
{
    authSchemes.Add(AuthSchemeType.NuevoScheme);
}
```

2. Luego por cada `AuthScheme` registrado en este arreglo, hay que agregar el handler correspondiente para cada proveedor. Para este nuevo proveedor hay que agregar entonces una extension `AddNuevoScheme` al AuthenticationBuilder (mas adelante lo describiremos como hacerlo).

```
if (sc == AuthSchemeType.Facebook)
{
    authBuilder.AddFacebook(siteSchemeName, (options) =>
    {
        ConfigureExternalAuthentication(sc, options, site);
    });
}
else if (sc == AuthSchemeType.NuevoScheme)
{
    authBuilder.AddNuevoScheme(siteSchemeName, (options) =>
    {
        ConfigureExternalAuthentication(sc, options, site);
    });
}
```

En el metodo `ConfigureExternalAuthentication` podemos customizar el objeto `Options` y registrarse a los eventos del proveedor de autenticacion (estos eventos son generados por AspnetCore).

Hay que agregar de todas maneras las siguientes lineas:
```
else if (authSchemeType == AuthSchemeType.NuevoScheme)
{
    clientId = site.NuevoSchemeSigninClientId;
    clientSecret = site.NuevoSchemeSigninClientSecret;

    // This is a ref Juntoz value to track better when correlation failed
    crflvalue = "crfllm";
}
```

## Crear un nuevo AuthenticationBuilder
En la seccion anterior, el codigo necesita un nuevo metodo llamado `AddNuevoScheme` agregado al objeto authBuilder a traves de una extension.

Para crear esta extension, hay que seguir los siguientes pasos:
1. En el proyecto `Eagle.OpenIdServer.AuthenticationBuilders.csproj`, crear una nueva carpeta donde estaran todos los archivos creados asociados a este nuevo proveedor de autenticacion.

Son cuatro archivos:
```
LifeMilesAuthenticationOptionsExtensions.cs
LifeMilesDefaults.cs
LifeMilesHandler.cs
LifeMilesOptions.cs
```

El archivo `extensions.cs` debe tener el siguiente codigo que es estandar de todos los authentication builders (tomamos como ejemplo el de Facebook o de Google en github https://github.com/dotnet/aspnetcore/blob/master/src/Security/Authentication/Facebook/src/FacebookExtensions.cs).

```
using System;
using Microsoft.AspNetCore.Authentication;
using Microsoft.Extensions.DependencyInjection;

namespace Eagle.OpenIdServer.AuthenticationBuilders
{
    public static class NuevoSchemeAuthenticationOptionsExtensions
    {
        public static AuthenticationBuilder AddNuevoScheme(this AuthenticationBuilder builder)
            => builder.AddNuevoScheme(NuevoSchemeDefaults.AuthenticationScheme, _ => { });

        public static AuthenticationBuilder AddNuevoScheme(this AuthenticationBuilder builder, Action<NuevoSchemeOptions> configureOptions)
            => builder.AddNuevoScheme(NuevoSchemeDefaults.AuthenticationScheme, configureOptions);

        public static AuthenticationBuilder AddNuevoScheme(this AuthenticationBuilder builder, string authenticationScheme, Action<NuevoSchemeOptions> configureOptions)
            => builder.AddNuevoScheme(authenticationScheme, NuevoSchemeDefaults.DisplayName, configureOptions);

        public static AuthenticationBuilder AddNuevoScheme(this AuthenticationBuilder builder, string authenticationScheme, string displayName, Action<NuevoSchemeOptions> configureOptions)
            => builder.AddOAuth<NuevoSchemeOptions, NuevoSchemeHandler>(authenticationScheme, displayName, configureOptions);
    }
}
```

El archivo `defaults.cs` debe contener una estructura similar y se debe registrar todos los valores que la implementacion va a usar por defecto. De tal manera que la configuracion sea lo mas plug-and-play posible.
```
namespace Eagle.OpenIdServer.AuthenticationBuilders.NuevoScheme
{
    public class NuevoSchemeDefaults
    {
        public const string AuthenticationScheme = "NuevoScheme";
        public const string DisplayName = "NuevoScheme";
        public static readonly string AuthorizationEndpoint = "https://NuevoScheme/oauth";
        public static readonly string TokenEndpoint = "https://NuevoScheme/oauth/access_token";
        public static readonly string UserInformationEndpoint = "https://NuevoScheme/oauth/me";
    }
}
```

El archivo `options.cs` debe contener las opciones pre-llenadas para este proveedor de autenticacion.
```
    public class LifeMilesOptions: OAuthOptions
    {
        public LifeMilesOptions()
        {
            CallbackPath = "/signin-lifemiles";
            AuthorizationEndpoint = LifeMilesDefaults.AuthorizationEndpoint;
            TokenEndpoint = LifeMilesDefaults.TokenEndpoint;
            UserInformationEndpoint = LifeMilesDefaults.UserInformationEndpoint;

            Scope.Add("openid");
            Scope.Add("profile");
            Scope.Add("email");
        }
    }
```
NOTA: Esto es solo un ejemplo de las cosas que se podrian configurar, pero dependera de cada implementacion.

Y finalmente tenemos el archivo `handler.cs` que contiene la implementacion en si de la autenticacion (creacion de ticket, recibo de respuesta, etc).
TODO: completar con la implementacion de LifeMiles