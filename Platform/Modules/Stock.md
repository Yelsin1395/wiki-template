El modulo de Stock se encarga de contener el stock de una variante de producto y de manejar su insercion optima.

Contiene tres entidades
- `StockEntry`: Cada entrada o salida o manipulacion de stock de una variante.
- `StockProductWarehouse`: Entidad de resumen de la sumatoria de stock por variante dentro de un almacen.
- `StockProduct`: Entidad de resumen de la sumatoria de stock por variante dentro de todos los almacenes.

Para saber cada movimiento de stock que se hizo para una variante en cualquier almacen, se lee `StockEntry`.

Para saber el stock detallado por almacen, leo `StockProductWarehouse`.

Para saber cuanto stock esta disponible de una variante X de un producto Y, leo la tabla `StockProduct`.

## StockEntry.totalAvailableQuantity
Este campo tiene un constraint en BD para que sea mayor o igual que cero. Esto para evitar cualquier desincronizacion posible que si ocurria en el legacy.

Se supone que ahora con las colas no deberia ocurrir tanto, PERO siempre esta la posibilidad, y es mejor mantener la integridad de la data.

## StockEntry.entryType
Este campo merece una explicacion mas detallada.

El `entryType` determinara el tipo de movimiento que se hizo en el almacen. 

Si es `T`, significa que se esta estableciendo el stock total del producto. Es decir: El stock es igual a 23 unidades.

Si es `D`, significa que se esta agregando o quitando una cantidad del total. Es decir: El stock aumento en 2 unidades, si tenia 23, ahora tengo 25. Si el stock disminuyo 5 unidades, si tenia 23, ahora tengo 18. El signo determina la accion.

Esto se hizo para que al ser actualizado, no se necesite leer innecesariamente el stock, ademas que de esa manera, la trazabilidad es mejor vs lo que el usuario quiso hacer. 

En la version legacy de juntoz, si el usuario seteaba el stock a una cantidad (T), el sistema obtenia la cantidad actual para luego convertirlo en delta. Aunque ahora se hace implicitamente tambien, se trata que la operacion sea lo mas corta posible.

## Transaccion
Las tres tablas siempre se actualizan en una transaccion para mantener su integridad. Esto se hace en el stored procedure `usp_stockentry_insert`.

Asi que si hay cambios, esto tiene que cuidarse mucho: el performance de la insercion.

## APIs y colas
Como todos los modulos tendran un api publica y un api core.

El api publica es solo para la lectura de stock de una variante.

El api core le corresponde la insercion del stock y la lectura del stock global de una variante o por warehouse, o si el warehouse tiene alguna variante asignada o etc.

Sin embargo, el api core NO ejecuta realmente la insercion. Esto se hace a traves de una cola.

Es decir: cuando alguna aplicacion necesita actualizar el stock de una variante, el API recibe la peticion y lo inserta en una cola llamada `stock_execute_update`.

Luego el azure function `juntoz-af-stock.stock_update` recibe el pedido y llama al sp `usp_stockentry_insert`.

De esta forma, garantizamos que el stock es actualizado en orden.

Y es adentro de este azure function que se genera un mensaje al topic `stock_update` para notificar que fue modificado.

## Futuro: reserva de stock
En el futuro si se necesita implementar las reservas de stock, se tendria que agregar dos campos mas:
- totalReservedQuantity
- totalOnHandQuantity

Y es por eso que el campo actual se llama `totalAvailableQuantity`, xq ese es el concepto que ya esta representando.

Cuando se implemente, obviamente hay muchos mas cambios que solo agregar los campos, dado que deberia permitir hacer la insercion de la reserva y la disminucion automatica del onhand, y etc.

| Nombre | Descripcion |
|--|--|
| available | lo que tengo disponible para vender o usar |
| reserved | lo que tengo reservado para despachar o por alguna otra actividad necesaria y que separa ese stock |
| onhand | lo que veo fisicamente en el almacen, osea available + reserved |
