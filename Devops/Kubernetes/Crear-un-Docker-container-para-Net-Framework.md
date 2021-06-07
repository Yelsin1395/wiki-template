**Usando Eagle.Api.Address como base**

1. Crear un archivo dockerfile en la raiz del proyecto

2. Dentro del archivo docker, la primera linea es importante dado que dara la imagen base que usaremos.

FROM microsoft/dotnet-framework:4.7.2-sdk

En este caso, usaremos la imagen oficial de microsoft net fw 4.7.2 (la ultima).

3. Este es el codigo de ejemplo


```
# STAGE: build
FROM microsoft/dotnet-framework:4.7.2-sdk AS app-build

# LABELS
LABEL company="Juntoz.com"  /
      createdby="katlim@ieholding.com"  /
      category="juntoz-api-internal"

# copy the needed files to build
COPY Eagle.Address.sln /appsrc/
COPY src /appsrc/src
COPY FolderProfile.pubxml /appsrc/

# build the package, and it will generate output to \appsrc\out folder (location in pubxml file)
RUN ["msbuild", "C:\\appsrc\\src\\Eagle.Rest.Address.WebHost\\Eagle.Rest.Address.WebHost.csproj", "/p:Configuration=Release;DeployOnBuild=true;PublishProfile=C:\\appsrc\\FolderProfile.pubxml"]

# STAGE: configure OS to have IIS and ASPNET (try not to modify this)
FROM microsoft/aspnet:4.7.2 as aspnet-run

COPY --from=app-build C:\\appsrc\\out C:\\inetpub\\wwwroot\\apprun

RUN New-WebAppPool -Name 'eagle-api-address-pool';`
    New-WebApplication -Site 'Default Web Site' -Name 'api.address' -PhysicalPath 'C:\inetpub\wwwroot\apprun' -ApplicationPool 'eagle-api-address-pool';
```

Explicacion:
- FROM microsoft: imagen base. Primero usar la sdk para compilar el codigo
- Labels para describir la imagen
- Copiar todo el codigo fuente desde el directorio local hacia adentro de la imagen
- RUN msbuild para compilar el codigo (en este caso revisar bien los parametros de DeployOnBuild,PublishProfile)
- FROM microsoft: imagen para runtime (que incluye aspnet y iis)
- Copiar el resultado del build hacia dentro de IIS
- Crear web app pool y web application para que corra la aplicacion
- **El archivo dockerfile de nuestro proyecto no necesita ENTRYPOINT, dado que el de MS ya lo da**


5. Build el nuevo contenedor

```
cd C:\dev\eagleperu\juntoz\Api.Address\src\Eagle.Rest.Address.WebHost
docker build -t eagle-api-address-net .
```

Aqui se va a demorar muchos minutos dado que tiene que bajar la imagen de aspnet (1.6gb + 580mb). Estas imagenes base se guardaran en el cache local de la maquina asi que a partir de ahi sera rapido.

6. Run el contenedor

```
docker run --detach --publish 80:80 eagle-api-address-net
```

El parametro publish es importante porque sino el puerto no se puede entrar desde afuera.

7. Para poder llamar al web dentro del contenedor, primero debemos saber cual es el IP del contenedor.

Puedes usar lo siguiente:
```
docker exec <containerid> ipconfig
```

** Cuando el contenedor es linux (corriendo en Docker for Windows), al parecer la IP interna del contenedor no es accesible desde afuera. Entonces tienes que usar otro IP. Dos posibilidades:

```
- CMD > IPCONFIG
- Ubicar "Ethernet adapter vEthernet (DockerNAT)"
- Si ese no funciona, usar uno a uno los otros ip address.
```

99. Otros
Para saber que containers estan corriendo usar
```
docker ps
```

