**NOTA: Esto solo es para WINDOWS, no para Linux.**

**NOTA 2: NO USAR Powershell, usar Bash o CMD.**

Lo primero es que al costado del archivo `package.json` se inserte un archivo `.npmrc` con este contenido:
```
registry=https://eagleperu.pkgs.visualstudio.com/Juntoz/_packaging/Juntoz.NodeJs/npm/registry/
always-auth=true
```

Esto lo que le dira a cualquier comando `npm`, es que vaya contra este registry y no el npmjs por defecto.

Una vez que este el archivo, y trates de instalar un componente del npm privado, seguramente aparecera este error:

```
D:\Dev\eagleperu\juntoz\Juntoz.Api.User>npm i @juntoz/authorize
npm ERR! code E401
npm ERR! Unable to authenticate, need: Bearer authorization_uri=https://login.windows.net/1d2696b0-d159-47cf-9176-51a7dd7d161d, Basic realm="https://pkgsprodeus21.pkgs.visualstudio.com/", TFS-Federated
```

Si este ese el error que te aparece, lo que tienes que hacer es primero instalar este tool
`npm install -g vsts-npm-auth`.

Y luego, ir a la ruta del proyecto donde esta el `package.json` y `.npmrc`, y ejecutar el comando:
```
vsts-npm-auth -config .npmrc
```

Al correr este comando, aparecera la ventana de login a Azure Devops (dado que nuestro npm privado esta ahi). Esto generara un archivo `.npmrc` en `%USERPROFILE%\.npmrc`.

Con estas credenciales podras conectarte al feed interno y poder instalar paquetes del feed privado y del publico. Esto por debajo genera un `Personal Access Token` en tu cuenta de Azure Devops (y normalmente tienen 365 dias de duracion).

NOTA: si te da problemas la autenticacion, con caracteres invalidos o errores:
```
The input is not a valid Base-64 string as it contains a non-base 64 character, more than two padding characters, or an illegal character among the padding characters.
```
Borra el archivo `%USERPROFILE%\.npmrc` y repite los pasos aqui.

O puedes ejecutar este comando `vsts-npm-auth -config .npmrc -force` para forzarlo
