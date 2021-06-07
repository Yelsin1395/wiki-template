# Tokens 
Las APIs V3 seran aseguradas usando token JWT.

Estos tokens seran generados por el proyecto Eagle.OpenIdServer en el repo Juntoz.ID.

![image.png](/.attachments/image-a2f33fba-b021-4a08-abd4-4b69c9b91539.png)

El formato JWT se esta convirtiendo en un estandar en la industria debido a su facilidad de implementacion y que especialmente porque son autocontenidos (es decir, contienen mucha informacion del usuario por lo tanto no necesitas leer una BD para obtener la informacion basica).

El JWT token (por ahora) no estara encriptado, pero si estara encoded base64 y ademas estara firmado. Firmado significa que el contenido se garantiza que no se ha modificado en el camino (pero claro, si algun usuario "pierde" el token, alguien mas podria usarlo. Sin embargo, los tokens no duran mucho tiempo).

Un ejemplo es:
```
eyJhbGciOiJSUzI1NiIsImtpZCI6Ijk5REM0Q0IzMjc4M0U3Q0RBRUExNkI4RjlDQjFGNzVBMEI5N0JFMDkiLCJ0eXAiOiJhdCtqd3QiLCJ4NXQiOiJtZHhNc3llRDU4MnVvV3VQbkxIM1dndVh2Z2sifQ.eyJuYmYiOjE1OTIzMzkwNDIsImV4cCI6MTYyMzQ0MzA0MiwiaXNzIjoiaHR0cHM6Ly9pZC5qdW50b3otc3RhZ2luZy5jb20iLCJhdWQiOlsiYXBpLWNvcmUiLCJhcGktcHVibGljIl0sImNsaWVudF9pZCI6ImZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmZmIiwic3ViIjoiMjZiZWM3M2YtNWE4OC00YTA0LWE3ZTItMmU4NTU5ODM5OWFkIiwiYXV0aF90aW1lIjoxNTkyMzM5MDQyLCJpZHAiOiJsb2NhbCIsIm5hbWUiOiJrYXRsaW1AaWVob2xkaW5nLmNvbSIsImdpdmVuX25hbWUiOiJLYXQgTGltIiwiZmFtaWx5X25hbWUiOiJSdWl6IiwiZW1haWwiOiJrYXRsaW1AaWVob2xkaW5nLmNvbSIsInBob25lX251bWJlciI6Ijk5NzQ5ODQ1OSIsInJvbGUiOiJ1c2VyIiwidXJuOmp1bnRvejpzaXRlIjoiOTNjNGE4NDFmNDI3NDUzYTgzYWE0M2RiZDg5Yzg4YzgiLCJ1cm46anVudG96OnRrdjFvdGFjIjoiMjU2NDA5MjcwIiwic2NvcGUiOlsiZW1haWwiLCJqdW50b3oiLCJvcGVuaWQiLCJwaG9uZSIsInByb2ZpbGUiLCJqei1jb3JlIiwianotcHVibGljIl0sImFtciI6WyJwYXNzd29yZCJdfQ.IqdwBOmJcgLiHS8TbNLdpIn2K5CJAmSnsmGi1m9TEy25gnqesM4SEvAVLMVNn7LceqT4oCRprgCm6UKZGG5B0_5eCL77h720LK5twabWJ-vctmFqi_jwRqD7p15QXPNptt2HX2kNmz-qqKDhLP0uppWve_-dC1Ava7udK5EgB0jZOtjjVXlQSr7mxXxoMGm1nivD9J8GmfrmVnvTj3r_sUhFk76LPgGldxcBQVm73WJrk60F85qoQmdTXqDfo1TFla8bBt8c2LAOPuggchUF_4jy15nDGBvoOQ6HoXJvKy0Zbk_B10U_5D0Sc_eIqEsNArPB7SFAhUvWweFtrSFbpero9w
```

Si van a https://jwt.io, y pegan este texto, el contenido esta disponible para leerse.

```
{
  "nbf": 1592339042,
  "exp": 1623443042,
  "iss": "https://id.juntoz-staging.com",
  "aud": [
    "api-core",
    "api-public"
  ],
  "client_id": "ffffffffffffffffffffffffffffffff",
  "sub": "26bec73f-5a88-4a04-a7e2-2e85598399ad",
  "auth_time": 1592339042,
  "idp": "local",
  "name": "katlim@ieholding.com",
  "given_name": "Kat Lim",
  "family_name": "Ruiz",
  "email": "katlim@ieholding.com",
  "phone_number": "997498459",
  "role": "user",
  "urn:juntoz:site": "93c4a841f427453a83aa43dbd89c88c8",
  "urn:juntoz:tkv1otac": "256409270",
  "scope": [
    "email",
    "juntoz",
    "openid",
    "phone",
    "profile",
    "jz-core",
    "jz-public"
  ],
  "amr": [
    "password"
  ]
}
```
El token tiene varios datos importantes:
- sub: Claim estandar de id de usuario
- name: nombre de usuario (por lo general el mismo, pero para LifeMiles por ejemplo sera distinto)
- email: email de usuario
- role: array de roles (si es uno es un solo string).
- urn:juntoz:site: el site al cual el usuario pertenece o se ha hecho login
- urn:juntoz:tkv1otac: este OTAC sirve para obtener un token v1 relacionado a este usuario tambien para que se pueda llamar a las APIs legacy. El OTAC sirve por 5 minutos, asi que lo recomendable es que apenas se termine el login, se llame a este para obtener el token y guardarlo en la sesion. El token v1 dura como 360 dias (cosa que en realidad es una muy mala practica).
- scope: la lista de scopes que este usuario puede acceder.

Este token de ejemplo es particular. Uno se da cuenta por algunos puntos:
- amr: password. Si fuera un token normal deberia ser Cookies o Google o Facebook.
- clientId: fffff.

Estos dos datos denotan un token especial dado que es un token generado solo para Juntoz Tech que permite hacer muchas cosas. Hay otro wiki al respecto (Busca: `Generar token v3 para systech`).

# Como usar el token?
Todas las APIs entonces deberan recibir un token JWT en el header Authorization asi:

```
Headers: {
  Authorization: 'Bearer <jwt-token>'
}
```

Y cada API debera tener la llave publica usada para generar el token. Con la llave publica podemos verificar que realmente NO ha sido cambiada en el camino. Esta llave publica y privada esta dentro de Juntoz.Id y ese es el lugar oficial donde debe instalarse.

Asimismo las APIs pueden ser configuradas con un audience (`aud` en el token) que mas o menos se lee asi: "Este token solo permite al portador llamar a la audience X" y el API se le configura "La audiencia de este API es X". Si hacen match, el token funciona. Sino, devuelve un http 401.

# Autorizacion en endpoints
A continuacion algunas reglas a seguir para mantener un orden y una PREDICTIBILIDAD necesaria ante cientos de microservicios y endpoints.

Las operaciones de lectura SIEMPRE deberan pedir el token JWT valido (audience y firma). Son siempre GET.

Las operaciones de escritura SIEMPRE deberan pedir que el token JWT tenga un claim `sub` (que representa el user id). Son siempre POST, PUT, PATCH, DELETE. Esto para evitar el uso de client_credentials solamente y ademas para que pueda guardarse en la auditoria.

Cada operacion de lectura y/o escritura deberan tener definidos CUALES ROLES pueden usar ese endpoint. La estructura de roles tiene otra pagina de wiki. Los roles salen del claim `role` dentro del token.

# Todo esta relacionado a Site
Uno de los shortcomings de la plataforma actual es que no permite la division de Site. Juntoz.com siempre fue vendido como un SAAS donde el tenancy es a nivel de merchant (1 marketplace, N tenants = merchants). La nueva plataforma incrementa la granularidad hacia arriba (N marketplaces o sites).

Y para evitar que las APIs siempre tengan el SiteId en el url (lo que lo hace un poco dificil de leer y usar), se ha creado un header que ademas del token, hay que tambien enviar.

El token se llama `X-JZ-SITE` y se debe enviar con el site id como valor.

```
Headers: {
  Authorization: 'Bearer <jwt-token>',
  X-JZ-SITE: '<my site id>'
}
```

NOTA: por un tema de seguridad de repente en el algun momento no sea el site id, sino un site token.

Entonces el API esta preparado de leer el Site del header y esto debe usarse para todos los queries.

Ademas, todas las bases de datos en CosmosDb tienen como partition el campo `siteId`.