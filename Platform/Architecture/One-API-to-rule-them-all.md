Como dijimos, las APIs seran re-escritas en NodeJs (lenguaje dinamico, buen performance, liviano en ejecucion, small footprint, etc).

# API CORE

El primer paso es crear el API CORE. Esta es el API maestra, corazon del sistema. Todo el backend depende de este API para funcionar, y asi esta bien.

El API CORE sera nuestro One Source of Truth.

Todas las entidades del sistema se modelaran a traves del API CORE.

![image.png](/.attachments/image-c3441907-a78b-430e-a138-a300d634884b.png)

Atachado al API Core, tenemos dos mas: API Public y API Integration.

# API PUBLIC

El API Public sirve de unico punto de entrada para el core desde un sistema externo como un E-commerce.

Esta API esta disenada especialmente para soportar las operaciones de un e-commerce (sea web o app), que estas operaciones son muy distintas vs las operaciones para manipular la data.

Es decir, por ejemplo, para yo crear una Marca, necesito operaciones basicas (CRUD). Sin embargo, para leer la marca en una web e-commerce hay varias formas:
- Leer solo el nombre, no necesita ningun otro campo.
- Para buscar una marca, no debo ir a la BD transaccional. Probablemente debo ir a una BD de alto rendimiento de solo lectura o un indice. Esto no debe cruzarse con el API CORE (Esto si es un CQRS de verdad).

![image.png](/.attachments/image-5dfa74db-f672-412b-9a8f-966e0f2d456a.png)

Esta imagen muestra un ejemplo de como el Api Public y el Api Core interactuan. El Api Core permite que desde SiteCentral se pueda modificar la data de una entidad. Esto genera un evento generico llamado product_upd. Suscrito a este evento hay un Azure Function que tomara la data que fue modificada y actualizara el indice de algolia que queremos usar para las busquedas en linea usadas por la web e-commerce.

NOTA: El Api public SI podria ir a la BD misma del Api Core. No hay ninguna regla que lo prohibe. Pero el hecho es que esa API Public retornara un fragmento o una transformacion de ese objeto y no el objeto directo (porque asi se necesita, no tanto por una regla en especifico).

# API INTEGRATION

Como tercer pieza del rompecabezas, tenemos el API Integration que permitira interactuar con sistemas externos de una manera segura y estandar.

Con el API Integration se busca que pueda soportar el modelo Inbound (recibimos nosotros una llamada) y Outbound (nosotros enviamos una llamada) para varias entidades que son tipicamente necesarias para interactuar entre dos sistemas o empresas.

El API Integration permitira integrar las operaciones:
![image.png](/.attachments/image-15a4f757-78dc-4df6-a17f-9f4ce18514f9.png)

- Nuevo Producto
- Modificar Product
- Modificar Precio y Precio Especial
- Publicar o Despublicar producto
- Borrar producto
- Actualizar, Aumentar o Reducir Stock
- Orden Pendiente de Pago
- Orden Confirmada
- Orden Lista para Enviar
- Crear Envio
- Envio en Transito
- Envio Entregado
- Orden Cancelada

** La parte de pagos no es considerada en esta API de Integracion.

La seccion del catalogo hay dos posibles direcciones: Inbound y Outbound. La idea de que sea inbound es cuando Juntoz NO es el One Source of Truth (OST). Sera outbound, cuando Juntoz si es el OST (esa es la esperanza que ocurra pronto :)).
Las demas operaciones deben ocurrir en ese sentido siempre.

La idea es pasar todas las integraciones actuales a este modelo.

Sin embargo, en el caso de los carriers, actualmente muchos esperan que el comercio llame a su API cada cierto tiempo (lo que es muy ineficiente). Entonces para poder mantener este esquema (al menos por ahora), la capa de integracion tambien puede crear cron jobs para llamar al carrier, pero que el retorno lo reenviara al API de integraciones para que se use el camino oficial.
