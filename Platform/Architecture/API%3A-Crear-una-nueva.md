
Primero crear un proyecto nodejs.

# Crear proyecto NodeJs

1. Primero crear el repo y hacer clone en una carpeta.
2. En esta carpeta, ir a command line y poner el comando `npm init -y`.
3. Esto creara el archivo `package.json`.
4. Ir al archivo y modificarlo asi:
```
{
  "name": "@juntoz/juntoz-api-core-store",
  "version": "1.0.0",
  "description": "Juntoz Api Core Store",
  "main": "src/index.js",
  "scripts": {
    "test": "jest"
  },
  "keywords": [],
  "author": "Juntoz.com",
  "license": "ISC"
}
```
Con esto tenemos el proyecto base armado.

# Instalar CLI localmente
Segundo es ejecutar el paquete `@juntoz/cli`. El paquete esta ubicado en Azure Artifacts (feed privado). 
En teoria, se supone deberia instalarse asi `npm i -g @juntoz/cli`. Sin embargo, como esta en un feed privado entonces no es facil acceder y vamos a instalarlo local en el proyecto.

Lo primero es poder acceder al feed privado. Para hacer eso, hay que crear un archivo `.npmrc` en la raiz del proyecto (junto al `package.json`) con el siguiente contenido:

```
registry=https://eagleperu.pkgs.visualstudio.com/Juntoz/_packaging/Juntoz.NodeJs/npm/registry/
always-auth=true
```
Luego seguir este wiki: https://dev.azure.com/eagleperu/Juntoz/_wiki/wikis/JuntozTech/223/Como-instalar-del-feed-privado, para poder entrar al feed privado realmente.

Una vez todo este ok, ejecutar el comando `npm i -D @juntoz/cli`. Esto lo que hara es conectarse al feed privado para instalar este paquete como dependencia de desarrollo.

# Correr CLI por primera vez
Una vez instalado el cli, hay que correr en la raiz del proyecto el commando `jz` y dar Enter.

Si es que no puede encontrar el comando `jz`, entonces escribir esto en el command prompt y ejecutar (es lo mismo que hace jz).

`> node "./node_modules/@juntoz/cli/index.js"`

o tambien podemos usar

`> npx jz`

(npx es un tool que permite correr symlinks ejecutando localmente un proyecto, `npm i -g npx`).

Esto ejecuta el cli y empiezan las preguntas para crear un nuevo proyecto.

La primera pregunta es que tipo de proyecto, por ahora solo esta habilitado `koa-api`.

Luego se pregunta, cual es el `api audience` o que tipo de API es. En este caso hay tres tipos:
`api-core`, `api-public` y `api-integration`.

Cual es el app namespace en kubernetes y te da uno por defecto que es `juntoz-(audience)`. Dar Enter dado que esto normalmente no se debe cambiar.

Cual es el nombre de la entidad que sera cubierta en este API? Poner el nombre de la entidad todo en minusculas e.g. `store` o `product`.

Cual es el nombre de la imagen docker y te da uno por defecto que es `(namespace)-(entity)`. Entonces si es que puse `juntoz-api-core` y puse `merchant`, el valor por defecto seria `juntoz-api-core-merchant` que es correcto.

Si es que tu entidad tiene dos palabras como `productcategory` sugerimos poner todo junto.

Que puerto debe escuchar? 3000 por defecto, por estandar queremos que todas las apis escuchen en el puerto 3000.

FINAL OK? Si.

De aqui el CLI copiara muchos archivos y instalara todas las dependencias. Esto tomara 5 minutos aprox.

### Que ocurre si no quiero usar Cosmos? 

La conectividad a Cosmos se instala a traves de los implementadores de la clase Repository. Simplemente al crear el registro de Dependency Injection para tu repositorio, no uses Cosmos y ya.

De igual manera para Azure Service Bus. Todo se usa a demanda. Sin embargo los proyectos tendran los npm package instalados aunque no se usen. Si deseas desinstalar el paquete tambien se puede hacer.

# Ultimos toques

Para ejecutar la aplicacion hay que poner los connection strings de las base de datos y el topic de azure service bus.

Esto hay que hacerlo en el archivo `/config/dev.yml`.

Y usar esto:
```
# override settings for local development environment
cosmos:
  db:
    cnt:
      host: https://juntoz-ddb-srv-staging.documents.azure.com:443
      authKey: pGN30TLDeOnJSG55Lr7FcRWvoCDuYPHHRRWDrs83gnnMXE04DJMnUMjAQGXGy3vVVGBRaTRoGSKrtk5bqlD9qg==
serviceBus:
  connstring: Endpoint=sb://juntoz-esb-platformv3-staging.servicebus.windows.net/;SharedAccessKeyName=ReadWrite;SharedAccessKey=EGPUJhj7STZysN5tUVu1sYmndgItAumqDbjvm1LdoTc=
```

Esto solo aplicara a tu ambiente local.

Para Staging y Produccion se usan otras variables que estan en el `azure-pipelines.yml`, `k8s-apply.yml` y en Azure Pipelines Libraries.

Asimismo en el archivo default.yml, hay que cambiar la database y container de cosmos.

En el `package.json`, hay una linea asi:

```
"scripts: {
  "test": "..."
}
```

hay que agregar los siguientes scripts para facilidad de testing y desarrollo:
```
"scripts: {
  "start:debug": "nodemon --inspect src/index.js",
  "start:dev": "nodemon src/index.js",
  "test": "jest"
}
```

# Upgrade del CLI
El CLI va evolucionando por lo tanto de vez en cuando sera necesario hacer upgrade del CLI. Para hacerlo hay que correr el mismo comando para instalarlo pero con el @latest al final. Osea:

```
npm i -D @juntoz/cli@latest
```

Una vez terminado este comando, lo unico que ha hecho es instalar la ultima version del CLI. 

**Antes de ejecutarlo, asegurate de hacer backup de tus cambios o hacer commit local** para que luego puedas detectar los cambios que el CLI ha hecho.

Para ejecutar el CLI, hay que usar los comandos ya descritos antes (`npx jz` o `node "./node_modules/@juntoz/cli/index.js"`). CUIDADO: esto sobreescribira los archivos que pertenecen al CLI.

Las respuestas que ya fueron dadas en ejecuciones anteriores, se mantienen guardadas por lo tanto las nuevas habra que responder.

Esta actualizacion tambien reinstalara todos los paquetes que el CLI necesita y los subira a la ultima version de aquellos.

## Upgrade del CLI a version 2.3.0
En la version 2.3.0 se hizo un cambio grande en el CLI: se separo la logica comun del CLI y se creo un paquete llamado `@juntoz/api-infra`. Esto para que los archivos no esten duplicados en todos los APIs. Ademas esto ha permitido poder hacer unit testing del api-infra facilmente.

Entonces esta version es clave. Por lo que hay algunos pasos que hay que seguir.

1. Ejecutar el upgrade del CLI normalmente como se describe aqui.
2. Y ejecutar el CLI con `npx jz`. Llenar las respuestas a las preguntas ya descritas. No hay cambios en las preguntas en esta version.
3. Como es usual, hay que hacer backup del repo antes de ejecutar el CLI.
4. Una vez que empiece a correr el CLI, sobreescribira varios archivos como los archivos .repository o .service.
5. Dejar terminar la ejecucion del CLI por completo para que no hayan cruces.

Luego de esto hay algunos cambios que hay que hacer manualmente:
1. Eliminar los directorios: `./authorize`, `./common`
2. Eliminar los archivos: `./initialize.js`.
3. Todos las clases que antes estaban en `authorize` o `common` ahora estan dentro de `@juntoz/api-infra`. Y estan disponibles a nivel de raiz.

Entonces si antes era:

`const { authorize } = require('../authorize/middlewares')`

ahora es

`const { authorize } = require('@juntoz/api-infra')`

Asimismo, habian algunas referencias que eran directas o habian otras referencias que se hacia el destructuring. Ahora todas deben hacerse destructuring. Esto creo que ahora es mas ordenado.

Asi antes era

`const CosmosImpl = require('../common/cosmos/cosmos.impl')`

ahora es 

`const { CosmosImpl } = require('@juntoz/api-infra')`

4. En el archivo `setup-container.js` el cambio basico es que el metodo principal ahora recibe tambien la aplicacion de koa como parametro.

Antes

`module.exports = function configureContainer(container) {`

Ahora

`module.exports = function configureContainer(app, container) {`

5. Desinstalar los siguientes paquetes (correr el comando `npm uninstall <package-name>`) que se supone estan ya pre-incluidos en el `@juntoz/api-infra`.
- @azure/cosmos
- @juntoz/authorize
- @juntoz/koa-health-probe
- @juntoz/koa-last-request
- @juntoz/koa-package-info
- @juntoz/koa-require-header
- @koa/cors
- axios
- fast-glob
- fs-extra
- ip
- koa
- koa-bodyparser
- koa-passport
- koa-pino-logger
- koa-x-request-id
- lodash.get
- lodash.merge
- passport-jwt
- qs

```
npm uninstall @azure/cosmos @juntoz/authorize @juntoz/koa-health-probe @juntoz/koa-last-request  @juntoz/koa-package-info @juntoz/koa-require-header @koa/cors axios fast-glob fs-extra ip koa koa-bodyparser koa-passport koa-pino-logger koa-x-request-id lodash.get lodash.merge passport-jwt qs
```

*** Si el paquete lo esta usando el API, en este caso es claro que NO se debe desinstalar.

# Paquetes config y js-yaml
Hay un paquete que el CLI lo inserta en la api llamado `config`. Este paquete permite tener una jerarquia de archivos de configuracion y combinar con variables de entorno.

En nuestro framework los archivos de configuracion son `.yml`, por lo tanto para que `config` pueda acceder a ellos, se necesita un paquete llamado `js-yaml` que tambien debe estar instalado.

Es muy importante que ambos esten instalados en el CLI. Si es que el `js-yaml` no esta instalado, entonces los archivos de configuracion no podran ser leidos y la aplicacion correra como si no tuviera nada configurado.

Cuando `js-yaml` no esta, un error similar a este se ve:
```
# node "./src/index.js" --port 3000  --env prod
No YAML parser loaded.  Suggest adding js-yaml dependency to your package.json file.
WARNING: NODE_ENV value of 'prod' did not match any deployment config file names.
WARNING: See https://github.com/lorenwest/node-config/wiki/Strict-Mode
WARNING: No configurations found in configuration directory:/app/config
WARNING: To disable this warning set SUPPRESS_NO_CONFIG_WARNING in the environment.
/app/node_modules/config/lib/config.js:182
    throw new Error('Configuration property "' + property + '" is not defined');
    ^

Error: Configuration property "name" is not defined
    at Config.get (/app/node_modules/config/lib/config.js:182:11)
    at Object.<anonymous> (/app/src/index.js:28:24)
    at Module._compile (internal/modules/cjs/loader.js:723:30)
```

Entonces pareciera como si no hubieran archivos de configuracion y cuando uno intenta leer una entrada de la configuracion, un error "not defined" es lanzado.

Sin embargo, la clave esta en la primera linea del log `No YAML parser loaded.  Suggest adding js-yaml dependency to your package.json file.`.

Lo malo es que es un poco imperceptible y uno se deja llevar por los WARNING. Sin embargo, hay que tener cuidado con esto.

# Siguientes pasos

Una vez termine el CLI de ejecutar, esto generara archivos pre-definidos y configuraciones pre-definidas y ademas agregara los paquetes necesarios al archivo `package.json`.

**1. Que endpoints hacer? Recuerda que hay que ser muy granular para que cada update de un grupo de campos importantes sea facil de trackear.**

**2. Que roles pueden ejecutar CADA endpoint? La seguridad es importante y es lo primero que se debe analizar junto con el PO. Una regla simple: get libre; create, update con rol regular (sitecms); delete con rol admin (sitecmsadmin).**

**3. El esquema. Joi permite una gran variedad de validaciones sobre el esquema (hasta se puede referenciar otros campos) asi que mientras mas validaciones esten en Joi, mejor.**

Esto es lo crucial de cada API. Todo lo demas ya es boilerplate y ya el cli nos lo va a dar.

# Cual fue el resultado?
Esto lo que hace en resumen es crear una aplicacion modelo para un microservicio que permite leer, crear, modificar y eliminar una entidad.

Tipicamente habra un ms para cada entidad y no debemos reusar ms dado qeu esto no escala y se traduce en peor codigo y mantenimiento.