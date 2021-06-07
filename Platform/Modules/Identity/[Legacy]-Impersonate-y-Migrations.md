El proyecto de Web.Admin, para poder hacer la funcion de Impersonate de la cual SAC depende, hace una jugada especial para lograr esto.

Utiliza directamente el asp net Identity, conectandose a la base de datos IdentityDb directamente para poder validar el usuario y aplicarle el cookie login a juntoz.com

Esto significa que cuando se hace una migracion en Api.Identity, esta tambien debe verse reflejada en Web.Admin porque sino te da un error de "Pending Migration" o algo por el estilo.

**Como Fixear:**
1) ir al repo de Api.Identity, descargar la ultima version y compilarlo en local
2) Encontrar el DLL `\Api.Identity\src\Eagle.EntityLayer.Identity\bin\Debug\Eagle.EntityLayer.Identity.dll`
3) Abrir https://portal.azure.com con usuario full
4) Ubicar el admin web app service en produccion
5) Ingresar via el App Service Editor
6) abrir la carpeta bin, y reemplazar el `Eagle.EntityLayer.Identity.dll` existente por el nuevo compilado en el paso Nro. 1
