El esquema de seguridad del modulo de integraciones es un poco especial, y hay que explicarlo un poco mas a fondo.

Lo primero es que toda la plataforma de Juntoz se basa en JWT tokens. Estos tokens son generados por nuestro servidor `id.juntoz.com` que es un servidor OpenIdConnect que usa IdentityServer como framework base.

Hay tres ambitos o realms que hemos creado en la plataforma: `api-core, api-public y api-int`.

Las apis de integracion estan incluidos dentro del ultimo: `api-int`. 

Cómo es que las Apis se autentican y usan estos ambitos esta explicado en otro wiki page (TODO: incluir link).

Para este caso, solo necesitamos saber que todo software externo que se quiere comunicar con la plataforma de Juntoz debera contar con un Client y este estar habilitado para la integracion.

# Que es un Client y como obtenerlo?
Un client es un termino propio de OpenId/Oauth y refiere a una entidad externa (o cliente) que necesita interactuar con el servidor de autenticacion.

Todos los clients tiene un clientId unico y uno o muchos clientSecrets (como passwords) activos que pueden ser utilizados para autenticarse contra el servidor.

Para obtener un cliente de integracion, se debe crear a traves de (alguna) pantalla de administracion o conectandose al api core de client con el endpoint `/client/create/integration`.

Un cliente de integracion tiene algunas caracteristicas especiales y que deben tomarse en cuenta.

* IsInteractive: false, permite identificar que el cliente no tiene UI y que sirve para comunicacion en background.
* AllowedGrantTypes: `[jz-integration]`, estos dos seran los grants permitidos para estos clients. Mas adelante se explica mas a fondo. Sin embargo, aun cuando son clients que se usaran en background, NO USAREMOS client_credentials debido a que queremos tener mas control sobre el proceso de generacion del token.
* AllowedScopes: `[openid, email, juntoz, jz-int, jz-core]`, estos seran los scopes que el token puede tener acceso. Los tres primeros son mejor explicados en otro wiki. Los dos ultimos son scopes especiales asociados a APIs (en IdentityServer se les llama IdentityResources) y permiten generar desde el scope, el audience correcto correspondiente. Esto esta mejor explicado en otro wiki, pero la idea es que son solo estos cinco scopes que deben ser permitidos. 
* Esta asociado a un usuario especial. Este usuario estara marcado como `forClient` y debe ser creado antes de registrar al cliente. Se necesita un usuario para poder guardar en la auditoria de la entidad quien hizo el cambio y la mejor forma es mantener el estandar que se guarda un user id.

_**Por que jz-core tambien?**
Aunque parece un error, no lo es. El client debe tener acceso a jz-int (para el api de integracion) pero dado que usaremos una extension para que el mismo client entre al api de core, entonces hay que darle ese scope. Justamente para que esto no represente un problema de seguridad es que se crearon dos extension grants y son explicados mas adelante._

Estas configuraciones son automaticamente hechas por el endpoint descrito antes.

## Para crear un usuario
Para crear un usuario de integracion, se usa un endpoint dedicado `/c/user/create/for/client`.

De igual manera este usuario sera creado y tendra algunas caracteristicas especiales.
* `forClient`: estara marcado como true y significara que esta asociado a un cliente. Tipicamente un cliente tiene un usuario asociado solo para integraciones.
* role: `merchantint` o `siteint`. Al crear el usuario de integracion dependiendo de quien lo este creando o para quien, es que se marcara con un rol especial. Si lo esta haciendo un usuario de la organizacion del siteowner, es que el nuevo usuario de integracion sera para integraciones a nivel de site, y por lo tanto debe tener el rol `siteint`. Si el usuario esta siendo creado por un usuario de nivel merchant, entonces tendra el rol `merchantint`.

**** Aun no esta bien definido donde se usaran esos roles, pero seguramente estaran asociados a los endpoints que seran afectados por las integraciones (actualizacion de stock, de precio, etc) y de esta forma se esta dando autorizacion explicita a estos usuarios.

## Generar el token de integracion
Como dijimos, para que una aplicacion externa pueda conectarse a la plataforma Juntoz debera obtener un token.

Para hacerlo, hemos habilitado un grant type customizado (o extended grant type) llamado "jz-integration" y se debe hacer la siguiente llamada:

```
POST https://id.juntoz-staging.com/connect/token
x-www-form-urlencoded
grant_type=jz-integration
client_id=<clientid>
client_secret=<clientsecret>
acr_values=site:<siteid>
scope=jz-int email juntoz openid
```
El acr_values es un campo especial que contiene parametros customizados. En este caso se enviara el siteId al que el client pertenece. Esto es muy importante.

El scope corresponde a los scopes que el token necesita y en este caso es necesario enviar esa lista tal cual esta definida. El endpoint validara exactamente esa lista.

Una vez que todo este ok, el endpoint generara un token dedicado solamente para las apis de integracion (el scope jz-int sirve para esto).

La respuesta se ve algo parecido a esto:
```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6Ijk5...",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "email juntoz jz-int openid"
}
```
El token dura una hora (3600 segundos) y solo da acceso al scope que se envio en el request.

```
{
  "nbf": 1598758398,
  "exp": 1598761998,
  "iss": "https://id.juntoz-staging.com",
  "aud": "api-int",
  "client_id": "c927ab50e8bb11eabe13cb873b43dce8",
  "sub": "41c3b440e95811ea9dec191a256b2d31",
  "auth_time": 1598758398,
  "idp": "local",
  "name": "forustechint@forussstest123.com",
  "given_name": "Forus",
  "family_name": "Integration",
  "email": "forustechint@forussstest123.com",
  "role": "merchantint",
  "urn:juntoz:site": "b58ac3ec05f44414b674d9244e4538a0",
  "urn:juntoz:userlevel": "merchant",
  "scope": [
    "email",
    "juntoz",
    "openid",
    "jz-int"
  ],
  "amr": [
    "jz-integration"
  ]
}
```
Si vemos el token por dentro (usando jwt.io), vemos algunas caracteristicas:
* Esta asociado al usuario creado dedicado para el client.
* Tiene el scope requerido para el token
* El authentication method es jz-integration que es el grant type especial creado para este fin.
* El token es mas como un token de usuario que uno de client_credentials. Esto es importante dado que al tener un token mas completo, podemos usarlo este token para los flujos regulares y no causar ningun problema o interrupcion.

Este token entonces sera usado por el cliente para llamar e.g. a las apis de integracion de manipulacion de stock de producto.
```
POST /i/stock/set
application/json
{}
Authorization: Bearer eyJhbGciOiJSUzI1NiIsImtpZCI6Ijk5...
```

Sin embargo, espero se haya notado que este token esta asociado solo al scope jz-int que da acceso a las APIs de integracion. Sin embargo, la data real esta en las api-core (scope jz-core) y que este token no serviria para esto.

Y aunque podria ser "sencillo" generar un token aparte, esto no es lo correcto dado que no habria sino el "puente" de trazabilidad necesario.

Entonces para esto entonces se usara otro segundo extension grant llamado `jz-int-to-core` que permite validar el token de integracion y con este token generar uno nuevo para acceder al api-core.

La llamada seria la siguiente (usando un clientid y secret fijo)
```
POST https://id.juntoz-staging.com/connect/token
x-www-form-urlencoded
grant_type=jz-int-to-core
client_id=bbbbbbbbbbbbbbbbbbbbbbbbbbbbbbbb
client_secret=56bc303059d211eba342d1eaea8dbd26
acr_values=site:<siteid> passkey:nzyb2qcsrsu6uzm3vf72
token=eyJhbGciOiJSUzI1NiIsImtpZCI6Ijk5...
```
Donde en este caso el `acr_values` incluye un campo adicional llamado `passkey` que se supone solo lo conocemos dentro de la plataforma y "asegura" que no se pueda llamar desde afuera.

Adicionalmente, se debe enviar el campo llamado `token` que debe incluir el token de integracion.

Una vez ejecutado el request, la respuesta es igual a la anterior
```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6...",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "email juntoz jz-core jz-int openid phone"
}
```

Pero en este caso, el scope si incluira acceso al api-core (scope `jz-core`). 

En este caso, el token tambien contiene al usuario asociado al client como claim `sub` de esta manera, cualquier API que fuera tocada por este token para modificar data, estara guardado en la auditoria regular de todos los objetos.

Todo este mecanismo aunque parezca convulsionado, es muy necesario dado que sino el token de integracion ya tendria acceso al api-core y eso significaria que podria haber una posible falla de seguridad si es que el developer tiene acceso a la lista de api-core disponibles.

De esta manera al menos podemos estar seguros que tenemos varias capas de seguridad para evitar un ataque o un robo de informacion.


