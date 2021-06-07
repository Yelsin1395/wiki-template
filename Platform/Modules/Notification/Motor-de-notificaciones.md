# Flujo de trabajo
![Notification.io.Api.jpg](/.attachments/Notification.io.Api-03091b22-6657-431e-8634-5bc045fdec5a.jpg)

## Explicación
El motor de notificaciones opera sobre un función de azure (azure function). A continuación, se procede a explicar cada capa del proyecto.

### Notification Binder
Esta capa es la principal del proyecto y lo que hace es recibir las colas para armar el contenido del mensaje. Por ejemplo, si se registra un nuevo usuario recibirá un objeto que contiene la información del usuario y el binder tendrá que armar las variables necesarias que serán usados por la plantilla de la notificación

Cabe resaltar que por cada evento se crea un binder.

Ejemplo

- Actualización de stock > notification-databinder-product-stock
- Manipulación de órdenes > notification-databinder-order

### Notification Audience
Esta capa resuelve la lista de usuarios a las que debe llegar la notificación. Asimismo, los grupos de usuario son agrupados por destinatario (socket, email, etc).

### Notificacion Save
Esta capa tiene la responsabilidad de resolver la plantilla para la notificación solicitada.

1. Resuelve la plantilla de la notificación usando la data enviada por **Notification Binder** para armar la plantilla de la notificación.
2. Registra el log de la creación de la plantilla. Esto sirve para trackear el momento en que se crea y si ocurrió algún error al intentar armar la plantilla.
3. Encola el mensaje para enviar la notificación a los proveedores de envío.

### Notification Out
Esta capa se encarga de recibir la notificación y la lista de usuarios para finalmente enviar la notificación por el destino elegido (socket, email).

Para los e-mails se hace a través de un plugin que hemos creado y dicho plugin se configura desde el SiteCentral.