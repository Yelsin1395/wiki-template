Para las webs o apps ecommerce es tipico que el usuario final no necesite hacer login hasta que inicie el checkout.

Sin embargo, para poder interactuar con la plataforma esa aplicacion debe enviar un token y por lo tanto entonces se generara un token guest.

Este token guest tendra una estructura similar a la de un token regular de usuario autenticado, por lo que se puede utilizar de igual manera (con sus restricciones correspondientes).

# Informacion acerca del token
1. El guest NO es un usuario registrado en ninguna base de datos. Esto para ahorrar espacio y registros dentro de la BD. En la plataforma legacy estos se registraban, sin embargo, por ahora no es necesario.
2. Para identificar un token guest, se debe revisar el claim `role` si es que contiene el rol `guest` (recordar que el claim `role` puede ser un string unitario o un array de strings).
3. El token guest tiene una duracion de **6 meses**. La aplicacion cliente es responsable de regenerarlo.

# Como generarlo?
Para generar el token y reusar toda la infrastructura del servidor openid, hemos creado un extension grant llamado `jz-guest`.

Esta extension permitira generar el token guest y necesita ejecutar el siguiente request:
```
Server: https://id.juntoz.com
Content-Type: application/x-www-form-urlencoded

POST /connect/token
grant_type: jz-guest
client_id: <client-id>
client_secret: <client-secret-plain-text>
acr_values: site:<site> device:<deviceid>
scope: juntoz jz-public openid
```

Creo que todos son bastante auto-explicativos excepto los dos ultimos.

- El parametro `device` debe contener el id del device que esta generando el token. Esto lo explicare despues. Este parametro terminara siendo el claim `sub` del token.
- El parametro `scope` debe contener estos valores explicitamente `juntoz jz-public openid`.
- El scope `juntoz` permitira que los claims propios de la plataforma puedan ser obtenidos.
- El scope `jz-public` permitira tener acceso solo al audience `api-public`. Y esta bien, el token guest SOLO debe poder acceder a la parte publica de la plataforma.
- El scope `openid` es mandatorio siempre para que el proceso de openid funcione.

El response debe verse parecido a esto:
```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6Ijk5REM0Q0IzM...",
    "expires_in": 15552000,
    "token_type": "Bearer",
    "scope": "juntoz jz-public openid"
}
```

Y este token si lo inspeccionamos en https://jwt.io: aparece esto:
```
{
  "nbf": 1599431442,
  "exp": 1614983442,
  "iss": "http://localhost:5000",
  "aud": "api-public",
  "client_id": "1551bced57344d07a4597ce57b75f3f7",
  "sub": "<deviceid>",
  "auth_time": 1599431442,
  "idp": "local",
  "urn:juntoz:site": "<site>",
  "name": "guestuser",
  "role": "guest",
  "scope": [
    "juntoz",
    "openid",
    "jz-public"
  ],
  "amr": [
    "jz-guest"
  ]
}
```

# Formato del DeviceId
El device DEBE tener el prefijo "jzd", es decir asi: `jzd9089540943` o `jzddhu483298ju43`.

De esta forma limitamos que no se pueda generar un token guest con cualquier formato y pueda haber un hueco de seguridad que se impersone a un userid valido.

# Por que device?

Todas las webs ecommerce avanzadas hacen tracking del usuario para poder otorgar recomendaciones personalizadas, ofertas en base al historial de las visitas y etc. Entonces para lograrlo, se necesita anclar todo en base a un deviceid (uno trackea al device, no al usuario como tal).

En una web se utiliza un cookie con un `uuid` dentro. En un app podria ser el deviceid habilitado para advertising (o de alguna otra manera, con tal que el numero sea unico globalmente).

Entonces para generar un token guest, se espera que se envie este deviceid como parte del request. Y como dice antes, este deviceid llegara a ser el claim `sub` (subject) del token, osea el "usuario" del token. Esto sigue el mismo patron que un token regular que tiene el claim `sub` con el userid.

Si es que el browser no contiene el cookie, la aplicacion web debe generarlo nuevamente y con este, generar un nuevo token.
