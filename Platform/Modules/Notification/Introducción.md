El motor de notificaciones nos permite generar notificaciones a nuestros usuarios a través de las diferentes acciones que podamos realizar dentro de la plataforma.

Veamos algunos ejemplos:

- Terminó la carga masiva de productos
- El stock ha sido actualizado
- Se ha confirmado una orden

## Las notificaciones
A continuación vamos a detallar todos los elementos que conforman una notificación.

### MessageTypes
Las notificaciones pertenecen a un messageType específico, el cual es su identificador único.

- order_cancelled_merchant
- order_confirmed_merchant

### Audiencia
Una audiciencia define el segmento de una notificación, es decir que con que grupo de usuarios va a trabajar.

Actualmente tenemos 3 tipos:

- **User**: la notificación esta disponible para un solo usuario.
- **Merchant**: la notificación esta disponible para los usuarios que se han suscrito a la lista de distribución del merchant.
- **Site**: la notificación esta disponible para los usuarios que se han suscrito a la lista de distribución del site.

**Nota**: para la audiencia del tipo merchant, solo se podrán registrar usuarios de tipo merchant. Lo mismo aplica para el Site.

Esta suscripción se realiza a través del SiteCentral y la encuentras en el módulo de **notificaciones > destinatarios**.

### Destinatarios
Los destinatarios son los medios de comunicación por el cual el mensaje es entregado.

Actualmente trabajamos con 2 tipos:

- **socket**: notificaciones real-time a través de socket.io y websocket.
- **email**: notificaciones a través de correo electrónico.

### Listas de distribuciones
Las listas de distribuciones son grupos de usuarios segmentados que se suscriben a una notificación.

Para crear una lista de distribución hay que tener en cuenta lo siguiente:

- **MessageType**: tipo de notificación.
- **Destination**: el medio de comunicación de la notificación.
- **Audience**: a que tipo de usuario le dará acceso a suscribirse a la lista.
- **DistributionOwnerId**: el ID del nivel de la audiencia (site, merchant).