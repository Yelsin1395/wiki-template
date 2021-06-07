
1) Correr:
Install-Package Microsoft.NETCore.Runtime.CoreCLR -Version 2.0.2-servicing-25727-02 -Source https://dotnet.myget.org/F/dotnet-core/api/v3/index.json
1) Abrir el archivo .csproj
1) En la sección  <PropertyGroup> agregar las dos siguientes líneas debajo del tag TargetFramework (Verificar que no están y sis están borrarlas previamente)
```
<RuntimeFrameworkVersion>2.0.2-servicing-25727-02</RuntimeFrameworkVersion>
<RuntimeIdentifier>win7-x64</RuntimeIdentifier>
```
4) Pasar a la vista Archivos y agregar a nivel del root un archivo nuget.config con el siguietne contenido:

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <!-- To inherit the global NuGet package sources remove the <clear/> line below -->
    <clear />
    <add key="dotnet-core" value="https://dotnet.myget.org/F/dotnet-core/api/v3/index.json" />
    <add key="api.nuget.org" value="https://api.nuget.org/v3/index.json" />
    <!-- la línea siguiente va solo si es tiene que llamar directamente a algun componente de Juntoz -->
    <!-- add key="Eagle.Release" value="https://eagleperu.pkgs.visualstudio.com/_packaging/Eagle.Release/nuget/v3/index.json" / -->
  </packageSources>
</configuration>
```
5) En el build de VSTS cambiar el Task Group **Commit Web Core - STD** por  **Commit Web Core 2.0.2 - STD**
6) Rezar!!! y dar build.


>NOTA: El nupkg se encuentra en:
>https://dotnet.myget.org/Package/OpenPackageWithNuGetPackageExplorer/dotnet-core?packageType=nuget&packageId=Microsoft.NETCore.Runtime.CoreCLR&packageVersion=2.0.2-servicing-25712-01
>https://dotnet.myget.org/feed/dotnet-core/package/nuget/Microsoft.NETCore.Runtime.CoreCLR/2.0.2-servicing-25712-01

