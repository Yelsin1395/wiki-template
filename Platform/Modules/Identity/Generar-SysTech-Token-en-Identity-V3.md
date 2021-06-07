Todo el departamento de Tech necesita tokens duraderos para poder probar APIs usando Postman u otras herramientas.

Para generarlos, usaremos los siguientes datos y Postman.

1. Ya debes tener un usuario y password creado en ese ambiente.

1. Crear un request en Postman con estos datos:

```
host: https://id.juntoz.com (production)
POST /connect/token
Body x-www-form-urlencoded
grant_type: password
client_id: ffffffffffffffffffffffffffffffff
client_secret: 16c47af031e111eba25227de9c775111
username: <youruser>
password: <yourpassword>
acr_values: site:<yoursiteid> passkey:fn3hw49fkdiu1di06zqf
```
El `client_id: ffff` es el unico habilitado para crear este token de systech. No debe usarse en otras instancias.
El `passkey` es secreto para dentro de juntoz. No difundir.

En caso quieras el token para el ambiente de staging, cambiar el host por `https://id.juntoz-staging.com` (obviamente tu usuario y password tambien deben corresponder).

**Importante: usar https**, http no funciona.

El resultado debe ser:
```
{
    "access_token": "eyJhbGc...Jww",
    "expires_in": 31104000,
    "token_type": "Bearer",
    "scope": "email juntoz jz-core jz-public openid phone profile testapiscope"
}
```

**El token dura 360 dias.**

El token sirve para desarrollar en el ambiente local de SiteCentral y ademas para llamar las APIs usando una herramienta como Postman.

En Postman, en el request, en el tab Auth, usar "Bearer Token" y poner el token "`eyJh...Jww`".

Para revisar el contenido del token, puedes ir a https://jwt.io.

NOTA: Se asume que el client_id `ffff...` tiene acceso a todos los scopes necesarios. Si le faltan, hay que agregarlos al Client (en CosmosDb).

# Mensajes de error

Si el cliente no es `ffffffff`, el resultado sera: InvalidClient.

Si el passkey es incorrecto, el resultado sera: NullException o OutOfRangeException.

Si el site es vacio, el resultado sera NullException.

Si el usuario es incorrecto, el resultado sera InvalidRequest.

Si el password es incorrecto, el resultado sera InvalidRequest.

Si el usuario no se autentico con password anteriormente (osea fue por e.g. google), el resultado sera UnsupportedGrantType.
