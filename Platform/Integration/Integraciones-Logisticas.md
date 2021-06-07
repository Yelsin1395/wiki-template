Una integracion con un logistico o carrier se ejecuta en los siguientes pasos:

1. Cuando un merchant le cambia el estado de una orden de Confirmado a Listo para Enviar, el sistema hace un hit a la integracion con el carrier para pedir el numero de remito.

El numero de remito es basicamente la confirmacion que el carrier ha creado un nuevo envio y entrega un numero unico.

A veces usan el mismo numero de la orden de Juntoz.

Si es que el remito es generado con exito, entonces la orden recien pasa a LPE.

2. Cuando el carrier llega al almacen del merchant y recoge el producto, el carrier debe generar un hit a Juntoz diciendo: Orden 123, En Transito.

3. Cuando el carrier entrega el producto al comprador, el carrier debe generar un hit a Juntoz diciendo: Orden 123, Entregado.

Asimismo hay otros estados que deberia Juntoz recibir como:
- Intento fallido de Entrega (el cliente no esta, o no encuentra la direccion, o el cliente desiste la entrega).

NOTA: en los pasos 2 y 3, hoy Juntoz hace un PULL cada hora para saber el estado de las ordenes. Esto es ineficiente y se esta viendo que las nuevas integraciones no sean asi.

NOTA 2: los carriers a veces no actualizan sus estados de las ordenes en el momento preciso, sino al final del dia o etc. Esto impacta en la experiencia del usuario de Juntoz dado que recibe el email muy tarde.

## En el futuro 
- Las integraciones con los logisticos deberian ser solo a demanda (osea paso 1 2 y 3 como se describen).
- Juntoz podria entregar un app para la integracion directa en el momento con los logisticos. Asi la confirmacion de entrega es inmediata. Y ademas asi con el app se puede tomar fotos de la entrega como evidencia.
- La integracion deberia ser para los costos de envio tambien asi todo el proceso es automatizado y hasta en linea.
- Actualmente los logisticos de Juntoz trabajan por coberturas. Hay algunos que soportan tambien por kilometraje. Pero el sistema no esta listo para eso aun.
- La mayoria de logisticos actuales son tradicionales: recogen el producto, lo acopian, y al dia siguiente salen a dejarlos en una ruta.
- Los logisticos de punto a punto son los menos.





