El modulo de shipping trata acerca del proceso de obtencion del precio de envio de un paquete y luego la asignacion del carrier adecuado para un pedido.

# Introduccion
## En juntoz.com
En juntoz.com el modulo de shipping tambien se encargaba de lo mismo pero lo hacia de otra manera. Todo el proceso de shipping se diseno desde el punto de vista del carrier y por encima, se monto un modulo de campanas de subsidio para reducir el costo en base a un subsidio que el site (juntoz.com) o el store (e.g. xiaomi) quisiera realizar para que el costo de envio siempre salga bajo.

El proceso es el siguiente:
1. Se creaba un Shipping Carrier (p.e. Olva) asociado a un Shipping Method (Express, Regular, etc). Si el mismo shipping carrier podia soportar varios metodos de envio, se creaba mas de uno (p.e. Olva Regular, Olva Express).
2. Se creaban zonas de envio. Una zona de envio consistia de una lista de ubigeos (en Peru serian los distritos, en EEUU serian los zip codes probablemente) o de una lista de poligonos (una lista de pares [longitud, latitud] que tienen el mismo inicio y fin).
3. Se creaba un SRT (Shipping Routing Table) por shipping carrier. El SRT contiene un registro con: Zona Desde, Zona Hasta, Peso Minimo, Peso Maximo, Costo de Envio (que cobra ese Shipping Carrier) y Tiempo de Entrega (horas calendario).
4. El dpto de Ops de Juntoz asigna a cada merchant un shipping carrier segun el metodo de envio (con esto, el sistema puede saber cuales metodos de envio el merchant puede soportar).

Entonces, estando en el checkout, el usuario selecciona su direccion de envio y el sistema puede calcular el almacen que tiene stock del producto y que sea "adecuado" (mas cercano y con cobertura). Con el carrier asignado al merchant, se busca el SRT que corresponda y se obtiene el costo de envio del paquete.

Luego de obtener el costo de envio, se calcula un posible subsidio que el site o el store quiere aplicar (tambien es una configuracion de zonas y porcentajes de descuento o topes de envio).

Entonces el precio de envio que el usuario debe pagar es
```
Shipping Price = Shipping Cost - Shipping Subsidy
```

Por ejemplo, para enviar un horno microondas desde Ate a San Borja es necesario tener configurado:

0. Datos previos que siempre se deben tener: dimensiones de producto, stock de producto, direccion de warehouse, direccion de usuario.
1. Merchant asociado a un carrier
2. El carrier debe tener su SRT con un registro que diga:
```
Zona Desde = Ate
Zona Hasta = San Borja
Peso Minimo = 0kg
Peso Maximo = 10kg
Costo = 25 soles
Tiempo = 48h
```
3. Si es que el costo quiere bajarse, se registra una campana de subsidio:
```
Subsidio
Zona Desde = Ate
Zona Hasta = San Borja
Peso Maximo = 20kg
Tipo de Subsidio = Porcentaje
Porcentaje de Subsidio = 50%
```
4. Lo que el usuario obtiene como precio de envio seria: `12.5 soles`.

Como se ve, para poder obtener el precio de envio, Juntoz Ops debe configurar todo esto y aun cuando el envio lo haga una flota propia del merchant (en donde "no hay" costo de envio).

## Precio o Costo
En temas contables, comerciales, etc, siempre se distingue dos conceptos: precio y costo. Precio es lo que el comprador paga, y el costo es cuanto dinero se necesito para construir/dar el servicio.

Por lo tanto, esta distincion es importante para todo este wiki dado que hay bastante diferencia entre ambos conceptos y es el precio lo que queremos prevalecer en la nueva plataforma.

# Cambios en la nueva plataforma
En la nueva plataforma estamos tratando de mejorar este modelo con el principal objetivo: simplificar el proceso para que el merchant y el site puedan generar estos modelos de envio sin ayuda.

## Punto de vista
Entonces el primer cambio que se ha hecho es que el punto de vista ha cambiado. Ya no es por carrier, sino ahora es por quien vende. Entonces el punto de vista ahora es el del Site o el del Store.

Antes, para obtener cuanto iba a pagar el usuario habia que hacer un calculo doble (primero el costo y luego ver si habia subsidios). Ahora, el punto de vista cambio y por lo tanto, saber cuanto el usuario pagara es un calculo directo:

```
1. El Store escoge si usa la Flota Site o su propia Flota Store
2. Con la flota escogida, obtener Desde, Hasta, Peso, Monto de Compra => Precio de Envio
```

Con este cambio de punto de vista, entonces, como ya se dijo, el precio configurado en la cobertura (o SRT) es el **precio de envio**.

Esto significa varias cosas:
1. Ya no habra subsidio alguno para configurar cuanto es lo que el usuario pagara. Lo que el site o store configura, es lo que el usuario pagara. Esto ayuda a la transparencia, pruebas y comprension de lo que el sistema hace.

_NOTA: Hay un caso de uso especial que si tendra subsidio: aquel en que la tienda usa la flota otorgada por el site, pero quiere "apoyar" para que el costo de envio no sea tan alto y se pueda incentivar la venta de sus productos. Este seria el unico caso donde si habria un subsidio. Para el caso de flota propia, no necesitas subsidio, dado que el merchant configurara directamente el precio de envio._

2. De esta manera, en la que separamos el precio de envio del costo de envio tenemos una figura nueva interesante. En checkout, en el momento de hacer la venta NO se necesita saber quien hara el envio (carrier). Antes si era necesario dado que el precio de envio estaba directamente relacionado al costo de envio.
3. De igual manera, al no tener el costo de envio, esto reduce el numero de configuraciones necesarias para que una venta se cree con un envio pagado por el usuario. Solo se necesita configurar la cobertura de envio (desde el punto de vista de la venta). Osea, se configura a donde puedo hacer envios. COMO hago el envio es un tema interno y que podria ser completamente opcional.

_Esto se valido con el equipo de Ops. En el modelo anterior, el costo y el subsidio se registraba dentro de la orden y de esa forma se podia verificar y cuadrar el reporte otorgado por el carrier para su correspondiente pago con nuestra propia data. Sin embargo, en la vida real, esto no se hace. Hay tantas ordenes y es tan variable el contexto (se cae un carrier, se toma otro, se llama, se devuelve, etc) que esto no se da y no vale la pena mantenerlo._

4. Por otro lado, considerar que la mayoria (sino es todos) de las plataformas de e-commerce del mundo lo hacen como este "nuevo modelo". Se disena desde el punto de vista de venta, y las tarifas internas, el carrier usado y etc, NO entran en el diseno para no sobre-complicarlo (dado que es un sistema de ecommerce, no es un sistema de logistica).

## Tallas de paquete
Un concepto nuevo que se esta introduciendo al modelo es el de Talla de Paquete. Antes en el modelo de juntoz.com, con las medidas del producto y peso unitario, se obtenia lo que se llamaba Peso Volumetrico y este se ajustaba con un Factor de Peso Volumetrico dictado por cada carrier. Esto lo estamos cambiando para mantener el mismo objetivo: reducir la complejidad.

Dado que ahora se hace desde el punto de vista del vendedor, entonces, 1) no necesitamos el factor de peso ni las formulas dictadas por el carrier 2) los pesos y costos estaban influenciados por estas configuraciones por carrier y era dificil de uniformizar y tener un precio de envio simple y transparente.

Lo que se hara ahora es clasificar los paquetes en tallas. Y cada site decidira las medidas correspondientes de cada talla (segun su propio criterio).

Es decir, el site A podra decir que la talla XS corresponde a un cubo de medidas 20x20x20cm, y otro podria decir que XS es 5x5x5cm. Mas detalle en el articulo dedicado a las tallas de envio.

## Asignar carrier lo mas tarde aceptable
En la plataforma anterior, dado que el precio de envio se asociaba al costo y el costo venia del carrier, entonces siempre se tenia predeterminado el carrier asignado a la orden.

Esto funciona en la mayoria de los casos, pero siempre habia excepciones y el sistema no permitia hacer un cambio. Ademas si por X motivo, la tarifa cambiaba en el camino desde Confirmed hasta Ready, el costo de envio interno quedaba congelado desde checkout.

Entonces con el nuevo modelo, en donde NO se necesita saber que carrier hara el envio, esto puede ser una decision mucho mas tardia y mas adecuada al contexto (si seguimos el principio del cono de la incertidumbre, siempre es mejor tomar la decision lo mas tarde responsablemente que se pueda).

De esta forma tambien ganamos un gran beneficio: Desacoplar la asignacion del carrier del checkout. 

Esto nos da mayor velocidad en esa etapa pero tambien nos permite crear varios modelos mas avanzados de asignacion (ademas que otros equipos puede desarrollar esto sin necesidad de conocer checkout).

Mas detalle de esto en el articulo correspondiente.

## Zonas y Poligonos
En la antigua plataforma, se predomina el uso de distritos y ubigeos para la definicion de los costos de envio (SRT). Sin embargo, esto es un modelo muy latinoamericano (Peru y Colombia tenemos evidencia que lo hacen asi) y "tradicional".

Para tratar ir hacia una solucion mas avanzada y que dure mas en el tiempo, se decidio que en la nueva plataforma se predominara el uso de poligonos para la definicion de las zonas de envio.

El mismo Site Owner o el mismo Merchant podran definir sus propios poligonos y asi definir cuanto seria el precio de un envio de Zona 1 a Zona 2.

Asimismo, todas las direcciones de almacen tienen su longitud y latitud y todas las direcciones de usuario que pasaron por checkout, tambien tienen su longitud y latitud. De esta manera, se asegura que todas las subordenes tienen su coordenada de inicio (almacen) y de fin (usuario).

Si una direccion es creada a traves de una pagina de My Account, en checkout esto se valida y se obliga al usuario de configurar su longitud y latitud en ese momento.

----
En los siguientes secciones se detallara como es que el modelo funcionara y con esto se podra entender mejor toda la figura completa.
