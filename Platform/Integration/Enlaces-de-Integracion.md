Actualmente Juntoz necesita cubrir algunos puntos de la plataforma con integracion a distintos partners. De esta forma, se automatiza los procesos y todo fluye mejor.

# Enlaces necesarios
Hay tres integraciones que necesitamos:
1. Juntoz a Store o Store a Juntoz
Esta integracion cubre las necesidades basicas de una tienda acerca de su catalogo de productos y sus ordenes.

2. Juntoz a Site Owner
Esta integracion basicamente se hace para que se notifique al Site Owner acerca de las ordenes creadas en Juntoz para que el Site Owner tenga acceso directo a su data y de la orden se puede obtener informacion de usuarios, compradores, productos, cantidades, montos, etc.

3. Carrier a Store y Store a Carrier
Esta integracion se hace para que el Carrier notifique a la tienda y a Juntoz que la orden fue cambiada de estado (Entregada, En transito, etc).
En esta ultima hay que considerar el caso en donde el SITE ES CLIENTE DEL CARRIER o CADA STORE ES CLIENTE DEL CARRIER.
Esto es importante dado que la tarifa puede ser distinta entonce si hay un incentivo por uno u otro lado.

Adicionalmente, hay integraciones con pasarelas de pago, pero estas son consideradas fuera de este modulo dado que son interactivas y acotadas a los estados previos a la confirmacion.

# Tipos
Consideraremos cuatro tipos de integracion:
- Inbound Pull: cuando Juntoz debe obtener la informacion (jalar), mapearla a nuestro formato y luego introducirla a nuestra plataforma.
- Inbound Receive: cuando Juntoz obtiene la informacion proactivamente (la recibe), la mapea a nuestro formato y la introduce a la plataforma.
- Outbound Push: cuando el evento nace en la plataforma de Juntoz y envia los datos (mapeados o no) al partner segun sea necesario.
- Outbound Get: cuando el partner llama a Juntoz para obtener data (mapeada al formato del partner o no).

# Detalle de enlaces
Este es la lista de operaciones que estan consideradas en la integracion con Store, Carriers y Site Owners.

El outbound get asi como el inbound receive son llamadas pasivas a nuestra data entonces en ese caso son la mejor opcion siempre.

|For|Entity|Inbound Pull|Inbound Receive|Outbound Push|
|-|-|-|-|-|-|-|-|-|-|-|-|-|-|
|Store|Product.Price|y-current|y-best|optional|
|Store|Product.Stock|y-current|y-best|optional|
|Store|Product.Info.Update (dificil determinar los campos)|y-current|y-best|optional|
|Store|Product.Create (requiere UI)||needs mapping|optional|
|Store|Product.SetCategories (requiere UI)||needs mapping|optional|
|Store|Order.PendingToPay|||y|
|Store|Order.Confirm|||y|
|Store|Order.ReadyToShip||y-current-best|optional|
|Store|Order.InTransit|||optional|
|Store|Order.Delivered|||optional|
|Store|Order.FailToDeliver|||optional|
|Store|Order.Canceled|||optional|
|Site|Order.PendingToPay|||y|
|Site|Order.Confirm|||y|
|Site|Order.ReadyToShip|||y|
|Site|Order.InTransit|||y|
|Site|Order.Delivered|||y|
|Site|Order.FailToDeliver|||y|
|Site|Order.Canceled|||y|
|Carrier-Store/Site|Order.Confirm|||optional|
|Carrier-Store/Site|Order.ReadyToShip > Carrier.CreateShippingNumber|y-current-best|||
|Carrier-Store/Site|Order.InTransit|y-current|y-best||
|Carrier-Store/Site|Order.Delivered|y-current|y-best||
|Carrier-Store/Site|Order.FailToDeliver|y-current|y-best||
|Carrier-Store/Site|Order.Canceled|||y|

*** y-current es que se necesita xq hoy existe, sin embargo y-best es que esa es la mejor opcion para Juntoz.