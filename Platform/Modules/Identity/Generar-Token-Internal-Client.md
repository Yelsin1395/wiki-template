Hay algunos flujos que en su punto inicial no tienen un usuario asociado, o necesitan un token especial que pueda cruzar los distintos ambitos de seguridad (core a public o viceversa).

Por ejemplo:
- Integration Inbound Pull Timer (el evento comienza por un timer por lo que no hay una accion de usuario).
- Notificaciones (hay notificaciones que se inician desde el ambito public y que necesitan del api core para ejecutarse).
- Integration Outbound Push (aqui igual, puede haber acciones que inicaron desde el ambito public y necesitan api core)
- Order Update Payment Status (el evento comienza por un timer para actualizar el estado de pago de una orden).

Por lo tanto, para estos casos necesitamos crear un token especial.

Para obtener el token, ejecutar una peticion HTTP POST

```
Server: https://id.juntoz.com
Content-Type: application/x-www-form-urlencoded

POST /connect/token
grant_type: jz-internal-client
client_id: dddddddddddddddddddddddddddddddd
client_secret: 396514c03aa611eb86bdc3184050d854
acr_values: site:<siteId> sub:<sub>
scope: juntoz jz-public openid
```

- El `client_id` y `client_secret` son fijos globalmente.
- El `siteId` debe ser el id del site sobre el que se esta trabajando.
- El `sub` debe ser un string identificador del proceso por ejemplo: `inbound-pull-timer-function` o `order-update-payment-status-function`.

NOTA: Se asume que en estos casos es imposible obtener un token de usuario o un usuario al que asociar el token. Si es que ya tienes un token de usuario pero que debe cruzar otro ambito, mejor es usar otro grant-type como el `jz-public-to-core`.

El token contendra algunas caracteristicas importantes:
1. El claim `aud` (audience) tendra acceso a `jz-core`, `jz-public` y `jz-int`.
2. El claim `role` tendra el rol especial `sysclient`. El rol en si no da ningun acceso especial o privilegiado, pero la ventaja es que se puede agregar ese rol a cada endpoint necesario y estar seguro que no se cruzara con algun rol de usuario.
3. El token dura 1h.
