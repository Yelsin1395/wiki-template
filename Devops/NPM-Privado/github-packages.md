# GitHub Packages

## Como publicar?
Primero hay que agregar los siguientes campos al archivo `package.json`:

```
    "name": "@jztechpe/mypackage",
    "repository": "https://github.com/jztechpe/infra.git",
    "publishConfig": {
        "registry": "https://npm.pkg.github.com"
    },

```

- El campo `name` debe estar encerrado (o scoped) con el scope `@jztechpe`.
- El campo `repository` debe contener el url al proyecto git.
- El campo `publishConfig` debe contener el registry de GHP.

El prerequisito para poder publicar el paquete es haberse uno autenticado hacia el registry.

**Como autenticarte?**
Ejecutar este comando:
```
npm login --scope=@jztechpe --registry=https://npm.pkg.github.com
```

Y a continuacion pedira:
- Username (usuario de github)
- Password (personal access token de tu propio usuario github)
- Email (email que quieres asociar al token)

Esto guardara un token dentro de tu maquina local.

Para obtener tu personal access token, debes ir a tu cuenta de Github, Settings, Developer Settings, Personal Access Token y generar uno con permisos para `packaging:write`.

**Publicacion**
Luego, para hacer la publicacion se ejecuta:
```
npm publish
```

## Como instalar?
Para poder instalar el paquete, se debe crear un archivo `.npmrc` en la raiz del proyecto (que debe estar junto con `package.json` y `node_modules`).

Este archivo debe contener el siguiente texto:
```
@jztechpe:registry=https://npm.pkg.github.com
always-auth=true
```

Con este archivo, al hacer `npm install`, npm obtendra los paquetes de ese registry y no de npmjs.com. Sin embargo, dado que los paquetes son privados, seguramente tendras que ya estar autenticado localmente (los pasos estan en la seccion anterior).
