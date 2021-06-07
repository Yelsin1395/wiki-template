En la plataforma Juntoz hemos considerado tres audiencias de apis para resguardar la seguridad de la plataforma (public, core e int).

Todos los usuarios de la web ecommerce (guest y user) usan el audience `api-public`, y todas las apis que las webs usan estan dentro de este audience.

Por lo tanto, tambien el token que se genera para estos usuarios SOLO tiene acceso al audience api-public.

Sin embargo, hay algunos casos en donde este usuario debe cruzar de una audiencia a otra y es necesario crear este puente especial. Pero solo en casos especiales.

Para esto se usara un nuevo extension grant del servidor JuntozIdentity llamado juntoz-public-to-core y se usa de la misma manera que los otros extension grants (de integracion, integracion a core y guest).

Haciendo un POST al endpoint `/connect/token` con el tipo de body `x-www-form-urlencoded`.

```
grant_type: jz-public-to-core
client_id: cccccccccccccccccccccccccccccccc
client_secret: cad92980a90d11eaacca433d2a70e109
acr_values: site:b58ac3ec05f44414b674d9244e4538a0
token: eyJhbGciOiJSUzI1NiI...
```
donde
- grant_type debe ser el grant exacto que se necesita `jz-public-to-core`.
- client_id: siempre el valor ccccc..., puesto que este es el unico client que esta habilitado para este flujo.
- client_secret: siempre el valor mostrado.
- acr_values: es un map de key:value y aqui se debe enviar el site al que se quiere acceder.
- token: aqui debe ir el token que tiene la audiencia `api-public`.

Si el input es correcto y las validaciones son satisfactorias, este endpoint retornara un response parecido a esto:

```
{
    "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6Ijk5REM0Q0IzMjc4M...",
    "expires_in": 3600,
    "token_type": "Bearer",
    "scope": "email juntoz jz-core jz-public openid phone profile testapiscope"
}
````

Donde el campo `access_token` contiene el nuevo token que podra acceder al audience `api-core`.

# En que casos se usa esto?
Hasta ahora estos:
- Cuando estoy en checkout y creo la orden se usa un api-public. Pero adentro de la creacion de la orden para llamar al Api Payment Gateway, es cuando se debe usar un token api-core (el PG es core y debe quedar como core para resguardar su uso).

