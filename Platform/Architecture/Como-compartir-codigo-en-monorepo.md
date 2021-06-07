# Introduccion
Bajo la arquitectura definida, cada repositorio corresponde a una entidad o un dominio. Y esta entidad por lo general es leida o modificada bajo dos ambitos: el ambito `core` y el `public` cada uno con su propio API microservicio. Ambos microservicios son tipicamente distintos uno del otro dado que basicamente el api core es un CRUD y el api public es de solo lectura.

Sin embargo, hay casos en donde ambas APIs comparten una logica de negocio especial que no es posible duplicar (o seria ineficiente y costoso) y que por lo tanto requieren que se cree un proyecto `common` para su reutilizacion.

Por ejemplo, para el caso donde tenemos la entidad User que se puede modificar por el ambito core (desde SiteCentral) o desde una aplicacion web de ecommerce (My Account), es necesario crear un proyecto `common` para que ambos APIs puedan reutilizar la misma logica de negocio.

# Modelo Tradicional
En el modelo tradicional, hubieramos creado tres proyectos de nodejs y el proyecto `common` tendria que versionarse y publicarse para que luego los proyectos de core y public, instalen esa nueva version para que esten coordinados siempre.

```
common
{
  name: @juntoz/juntoz-api-user-common,
  version: 1.0.23
}

core
{
  dependencies: {
    @juntoz/juntoz-api-user-common: 1.0.23
  }
}
```

Para lograr que esto funcione, se tendria que utilizar el npm publico o un npm privado.

Esto aunque funciona bien, tiene una gran desventaja: es muy dificil de probar `common`. Cada cambio tendria que generar una nueva version de la libreria e instalarlo en `core` y en `public`, y si es que un bug es detectado, se tendria que republicar nuevamente y asi sucesivamente. Esto es un proceso bastante engorroso y poco eficiente.

Por lo tanto, para atacar este problema que hacia muy engorroso el desarrollo, decidimos usar `npm workspaces`.

# Npm Workspaces
Un NPMWS nos permite compartir codigo a traves de varios proyectos dentro de un mismo repositorio. Esto esta muy ligado (y practicamente la razon por la que aparecio esta funcionalidad) a los monorepos, que son repositorios git de codigo que contienen muchisimos proyectos y comparten entre si definiciones, proyectos, librerias, etc. En contraste con los polirepos que divide cada proyecto en un repo distinto y separado para forzar justamente la separacion de funcionalidades (separation of concerns) y que es la forma tradicional de trabajo.

Npm workspaces viene out of the box en npm 7 y ya esta contenido dentro de Nodejs 15.

En el mercado hay otras dos posibles soluciones a esto, Yarn Workspaces y Lerna (y que tienen varios meses o hasta anhos). Yarn workspaces requeriria pasarnos a yarn cuando npm es bastante bueno ya. Y Lerna se usa mas para una libreria mas grande compuesta por varios proyectos como lodash.

Entonces, al usar NPMWS nos permitira compartir codigo y tambien muy importante, compartir dependencias para que todos los proyectos tengan la misma version y no haya problemas de incompatibilidad.

## Como lo usamos?
Lo primero que hay que hacer es crear un `package.json` en la raiz del repo. Y dentro de este archivo, debe contener un array llamado `workspaces` con la lista de rutas que contendran los paquetes que estaran dentro del workspace.

```
{
    "name": "@juntoz/juntoz-api-user",
    "private": true,
    "version": "2.0.0",
    "workspaces": [
        "packages/*"
    ],
```

Y dentro de esta ruta, es que pondremos nuestros proyectos. En el ejemplo que estamos trabajando aqui, habrian tres proyectos dentro de la carpeta `packages`.

```
/
--/packages
----/common
----/core
----/public
--package.json
```

El `core` y `publi`c serian APIs microservicios donde configuramos las rutas, seguridad, etc. Y `common` es una libreria comun donde definimos los servicios de logica de negocio y acceso de datos.

Si anteriormente, teniamos que instalar `common` dentro de `core` y `public` como una dependencia, ahora YA NO es necesario y lo unico que necesitamos hacer es usar el paquete como si ya estuviera instalado.

```
(inside core)
const { UserService } = require('@juntoz/juntoz-api-user-common');
```

Para lograr esto hay algunos compromisos que debemos asumir y acciones que debemos tomar para que funcione:

1. El `package.json` raiz es el unico lugar donde definiremos las dependencias. Si necesitamos instalar una nueva dependencia, se hace a nivel de la raiz (`npm install XXX` o `npm install -D XXX`).
2. Por lo tanto, el unico `package-lock.json` permitido esta tambien en la raiz y la carpeta `node_modules` aparecera a nivel de la raiz y ya no a nivel de los paquetes internos.
3. **Para cuando necesitemos instalar o iniciar nuestro proyecto** (`npm install` o `npm ci`) se debe hacerse A NIVEL DE LA RAIZ.
4. Cada paquete si podra tener su propio `package.json` PERO no contendra la declaracion de `dependencies` ni `devDependencies`.
5. Dado que el `package.json` raiz es el que debe utilizarse para definir las dependencias, el archivo `.npmrc` para configurar el npm privado estaria tambien a nivel de la raiz.

### Como se ven los archivos?
**Directorio final**
```
/
--/node_modules
--/packages
----/common
------package.json
------(no package-lock.json)
------(no node_modules)
----/core
------package.json
------(no package-lock.json)
------(no node_modules)
----/public
------package.json
------(no package-lock.json)
------(no node_modules)
--.npmrc
--package.json
--package-lock.json
```

**/package.json** (raiz)
```
{
    "name": "@juntoz/juntoz-api-user",
    "private": true,
    "version": "2.0.0",
    "workspaces": [
        "packages/*"
    ],
    "dependencies": {
        "@juntoz/api-infra": "^1.0.7",
        ...
    },
    "devDependencies": {
        ...
    }
}
```

**/common/package.json**
```
{
  "name": "@juntoz/juntoz-api-user-common",
  "version": "1.0.50",
  "private": true,
  "description": "A common layer used by public and core APIs of User.",
}
```

**/core/package.json**
```
{
    "name": "@juntoz/juntoz-api-user-core",
    "version": "1.0.1",
    "description": "Juntoz Api Core for User",
    "main": "src/index.js",
    "private": true,
}
```

**/public/package.json**
```
{
    "name": "@juntoz/juntoz-api-user-public",
    "version": "1.0.1",
    "description": "Juntoz Api Public for User",
    "main": "src/index.js",
    "private": true,
}
```

## FAQ
1. Que debo hacer si quiero agregar una dependencia?
Debes ir a la raiz del repo, y hacer `npm install` alli. No adentro de los proyectos.

2. Pueden los proyectos internos tener dependencias propias?

No, todas deben ir en el `package.json` raiz. Si es que por error, se inserta una adentro, entonces simplemente es:
- Ejecutar `npm remove`
- O quitar el paquete del package.json interno, y pegarlo dentro de la raiz. No olvidar de correr un `npm install` a nivel de raiz, para que el `package-lock.json` sea actualizado.

A nivel de paquete interno, NO debe existir ni node_modules, ni package.json ni package-lock.json.

Si esto ocurriera, a la hora de ejecutar core o public, encontrarias errores de ejecucion de que no encuentra ciertos paquetes.

3. Que hacer si necesito actualizar `@juntoz/cli`?
Esto requerira un poquito de trabajo adicional dado que el comando `npx` ya no funciona.

- Ir a Command Line y ubicate en la raiz del repo.
- Actualizar `@juntoz/cli` a la ultima version `npm i @juntoz/cli@latest`.
- Ubicarte ahora en p.e. el folder `public`.
- Copiar **temporalmente** el archivo `.npmrc` de la raiz hacia el folder `public`, de esta manera, el CLI podra acceder al npm privado.
- Una vez dentro del folder public, ejecutar el comando `node "..\..\node_modules\@juntoz\cli\index.js"`. Esto ejecutara el CLI dentro de public y copiara los archivos, insertara dependencias, y etc.
- Como ya dijimos antes, en los paquetes internos NO se debe tener dependencias, por lo que entonces una vez que haya terminado el CLI, movemos las dependencias hacia la raiz. Y borramos `package-lock.json` y `node_modules`.
