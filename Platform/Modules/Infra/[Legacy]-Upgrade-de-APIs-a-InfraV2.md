# Introduccion
El proyecto Eagle.Infrastructure nacio por la necesidad de reutilizar mucho codigo base para todas las API que Juntoz necesitaba crear.

Sin embargo, el problema ha sido que en Eagle.Core, Eagle.Infrastructure y Eagle.Infrastructure.Rest se introdujo muchisimas clases y dependencias que no necesitaban estar juntas dentro de un mismo proyecto.

La consecuencia de todo esto es la importacion de dependencias en un solo csproj de ITextSharp (creacion de PDFs), Azure Service Bus, Azure CosmosDb, etc cuando ese csproj en cuestion, solo necesitaba de repente una referencia a una clase de OAuth.

La solucion ha sido DIVIDIR estos tres proyectos (E.Core, E.Infr, E.Infr.Rest) en multiples proyectos pequeños y asi poder importar esas referencias que solamente son necesarias.

No fue tan dificil. Era cuestion de hacerlo y empezar a importarlo. Este wiki es para describir los pasos a seguir (que no son 100% a raja tabla, puesto que cada proyecto puede tener sus propias caracteristicas).

# Pasos
NOTA: Lo recomendable es que con cada paso hecho, hacer un commit local. Asi se puede hacer rollback mas facilmente, y ademas, se puede ver con mejor detalle la evolucion de los cambios.

## 0. Preparar Solucion
Estos pasos aunque no son necesarios, si son buenas practicas que hay que hacer, no demandan mucho esfuerzo.

1. Eliminar:
- /.nuget: no es necesario, Azure Devops ya tiene el nuget.exe correcto.
- /scripts/SwapSlots.ps1: Azure Devops ya lo da como Task
- (opcional) /test/FunctionalTest y test/UnitTest, si ven estos tests tienen un coverage casi nulo, o son tests vacios
- /tools/migrate.exe: normalmente todos los proyectos de entidad tienen el archivo tool/migrate.exe dentro, asi que no es necesario tenerlo afuera.

2. Mover:
- /Eagle.CMS.sln hacia dentro de la carpeta src. Es buena practica que el solution este en la raiz de los csproj.
- /nuget.config hacia dentro de la carpeta src. Es buena practica que este junto al solution.

|Antes|Despues|
|--|--|
|![Items.png](/.attachments/Items-85bc38ff-97aa-44c4-a9a6-ecd5acc46936.png)|![Items2.png](/.attachments/Items2-2b0f938e-a3d1-4e34-bf79-0b4b1fbd35ca.png)![Items3.png](/.attachments/Items3-0cc90c1d-27a7-4d7b-8105-6c99b181e9c1.png)|

- Una vez movido el sln, hay que actualizarlo con las nuevas rutas (tip: abrir sln como texto y modificar las rutas que dicen "src/")

3. Migrar a formato PackageReference
Para hacerlo, primero click derecho en References. Si no aparece el menu `migrate to PackageReferences`, abrir `Manage Nuget Packages`. Esto lo que hace es que fuerza a VS a cargar los nugets en memoria. Una vez hecho esto, el menu debe aparecer al hacer click derecho.

![Items4.png](/.attachments/Items4-c398894f-587d-4898-b1b6-7506bdae3430.png)

Esto demora un poco. Aparece un popup de aceptacion, dar click en Accept. Esto recreara todas las referencias como PackageReference y dejara aquellas que VS detecta que son top-level (osea las que el usuario escogio explicitamente como referencia).

NOTA: Si es que el csproj representa un Web Application, el menu no aparecera. En este caso, lo que haremos sera eliminar todas las referencias. Ejecutar este comando de powershell (en `Package Manager Console` para el proyecto que estamos viendo): `Get-Package -ProjectName "Eagle.Rest.Common.WebHost" | Uninstall-Package -ProjectName "Eagle.Rest.Common.WebHost" -RemoveDependencies -Force`. Seguir con los pasos siguientes.

Ir a Manage Nuget Packages y desinstalar todos los paquetes. TODOS. Esto hara que no compile, esta ok.

4. Migrar todos los csproj a Net472
Hay que seleccionar el Target Framework a Net472.
IMPORTANTE: Muchas veces parece que ya estan los nuget packages instalados correctamente y aun asi no lo compila, esto se debe a que el proyecto esta en net461.

5. Eliminar las referencias directas que no se usan.
Los csproj al ser creados agregan automaticamente algunas librerias del Net Framework. Estas a veces se cruzan con los nuget packages. Es mejor borrarlas.

La lista basica deberia quedar:

```
Microsoft.CSharp
System
System.Core
System.Data
```
![Items5.png](/.attachments/Items5-352e7831-fafc-484d-9899-90fa7c5bdc83.png)

6. Borrar todos los App.config en los proyectos intermedios.
Los unicos proyectos que deben tener App.config o Web.config son los proyectos raiz (Web o Exe). Toda la configuracion debe ser agregada ahi.
Aun cuando VS cree de nuevo los app.config, NO AGREGARLOS a los csproj.

## 1. Agregar las nuevas referencias
Una vez hecho los pasos anteriores, hay que hacer los otro


5. Agregar de nuevo las referencias

Una vez hecho esto, agregar una a una segun sea necesario. Si es que VS no las agrega como Package Reference, hay que ir a configurarlo en las Preferences de VS. Cuando VS detecta que no tiene ninguna referencia, las empieza a agregar como PR. Si es que hay referencias en formato antiguo, VS seguira usando el formato antiguo, asi que es importante borrar todas. Si es que de alguna manera no pasa al nuevo formato, borrar la carpeta bin y obj del csproj y compilar de nuevo.

6. Proxy
Ir a Visual Studio > Package Manager Console
Asegurarte de estar en el Default Project: Eagle.Rest.XXX.Proxy

Install-Package Eagle.InfraV2.HttpProxy
Install-Package Eagle.Utils
Install-Package Eagle.Exceptions
Install-Package System.Net.Http
(a veces) Install-Package Eagle.InfraV2.Rest.Common >> si es que se usan las clases RepresentationBase (y relacionadas)

7. Model
No deberia tener ninguna
(a veces) Install-Package Eagle.InfraV2.Rest.Common >> si es que se usan las clases RepresentationBase (y relacionadas)

8. QueryLayer
Install-Package Eagle.InfraV2.Rest.Common
(probablemente) Install-Package Microsoft.AspNet.WebApi.OData

Si hay una clase para hacer los mappings de AutoMapper, convertirlo en esta clase:
    
```
public class AutoMapperProfile: Profile
{
    public AutoMapperProfile()
    {
        CreateMap<Merchant, MerchantDto>();
        ...
    }
}
```
Y hay que llamarla desde WebHost (ver en la seccion de WebHost - clase AutoMapperInitializer).

9. Entity
Install-Package EntityFramework
Install-Package EntityFramework

9. WebHost

Solo deben quedar dos clases: UnityContainer y Startup.

Linkear al nuget Eagle.InfraV2.Rest.Host.

Y debe quedar asi:


```
using Eagle.InfraV2.Rest.Host;
using Eagle.Rest.Address.Controllers;
using Microsoft.Owin;
using Owin;
using System.Reflection;

[assembly: OwinStartup(typeof(Eagle.Rest.Address.WebHost.Startup))]
namespace Eagle.Rest.Address.WebHost
{
    public class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            AutoMapperInitializer.Init("Eagle.QueryLayer.Address", "Eagle.Rest.Address"));
            var config = new RestHostConfig(new Assembly[] { typeof(AddressController).Assembly }, HostUnityContainer.Configure());
            config.Configure(app);
        }
    }
}
```
Todos los API siempre tienen la misma WebApiConfig con algunas cosas customizables como el assembly Rest y el UnityContainer. Esto se contempla ya en este codigo.

Si es que se necesita customizar, lo que debe hacerse es heredar de RestHostConfig y hacer override de los eventos publicados.

El caso donde se encuentren AutoMapper mappings, seguir las instrucciones anteriores: crear una clase AutoMapper.Profile y en esta clase Startup inicializarlo. Aqui la ventaja es que se inicializa con el nombre del assembly y no con el link directo al assembly. Esto ayuda en el mantenimiento y mantener el proyecto lightweight.

A veces sale un error como "cannot create IModelBinder". No estoy seguro a que se debe ese error, pero la solucion que he encontrado es instalar el paquete Eagle.Infra.Query.Odata y luego desinstalarlo. Creo tiene que ver que no encuentra esa dll inicialmente o tiene que ver con el cache de Nuget. Aun cuando instalo, desinstalo, borro bin y obj, y compilo, ya empieza a funcionar.
