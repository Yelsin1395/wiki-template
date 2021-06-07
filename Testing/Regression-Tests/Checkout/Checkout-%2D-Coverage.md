
(B)- Envío Express donde usuario compra producto dentro de zona de cobertura.
Se utiliza el courier 134 LaCajitaCorriendoExpress que tiene como cobertura:
ZONA134 BARRANCO
ZONA134 CHORRILLOS 
ZONA134 LURIN
Se utilizará el almacén de chorrillos
Pasos:
1. Crear carrito con producto
2. Checkout Shipping se selecciona dirección en chorrillos.
3. Checkout summary, debe mostrar la dirección de envío: chorrillos.
4. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, debe mostrar monto total.
5. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Express, Información de User/Dirección de entrega: CHORRILLOS, Informacion de Courier/ Nombre: LaCajitaCorriendoExpress, Tipo: Express

(B)Test Fail 
- Envío Express donde usuario compra producto **fuera de zona de cobertura**.
Se utiliza el courier 134 LaCajitaCorriendoExpress que tiene como cobertura:
ZONA134 BARRANCO
ZONA134 CHORRILLOS
ZONA134 LURIN
Pasos:
1. Crear carrito con producto
2. Checkout Shipping se selecciona dirección: La Molina
3. Checkout Billing Muestra mensaje de Error: No existen zonas de cobertura para el usuario.
4. La validación en checkout no permite seguir con el flujo de compra, se bloquea botón 'Siguiente'

Con la ayuda de estas 2 herramientas: https://geojson.io y https://www.google.com/maps
se pudo realizar la prueba.
(B)- Envío Express donde **usuario compra producto dentro de zona de cobertura con 2 almacenes** disponibles.
Se utiliza los courier con coberturas:
- [ ] - _134 LaCajitaCorriendoExpress_ :
ZONA134 BARRANCO
ZONA134 CHORRILLOS
ZONA134 LURIN
- [ ] _135 LosPollerosExpress_ :
ZONA135 BARRANCO
ZONA135 LAVICTORIA
Con almacén: Barranco1(más cerca a la dirección del usuario) y Barranco2(mas alejado del usuario)
Pasos:
1. Crear carrito con producto
2. Checkout Shipping se selecciona dirección en Barranco.
3. Checkout summary, debe mostrar la dirección de envío: Barranco.
4. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, debe mostrar monto total.
5. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Express, Información de User/Dirección de entrega: BARRANCO, Informacion de Courier/ Nombre: LosPollerosExpress, Tipo: Express,Almacén: El pollo de Pios Chicken - Barranco1

(B)Test Fail
- Envío Express donde usuario **compra producto dentro de **zona de cobertura con 2 almacenes disponibles y no hay stock en 1 almacén** ** Barranco1 y el otro si tiene stock
Se utiliza los courier con coberturas:
- [ ] - _134 LaCajitaCorriendoExpress_ :
ZONA134 BARRANCO
ZONA134 CHORRILLOS
ZONA134 LURIN
- [ ] _135 LosPollerosExpress_ :
ZONA135 BARRANCO
ZONA135 LAVICTORIA
Con almacén: Barranco1(más cerca a la dirección del usuario y NO hay stock) y Barranco2(mas alejado del usuario)
Pasos:
1. Crear carrito con producto
2. Checkout Shipping se selecciona dirección en Barranco.
3. Checkout summary, debe mostrar la dirección de envío: Barranco.
4. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, debe mostrar monto total.
5. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Express, Información de User/Dirección de entrega: BARRANCO, Informacion de Courier/ Nombre: LosPollerosExpress, Tipo: Express, Almacén: El pollo de Pios Chicken - Barranco2
6. Como resultado esperado es que si se pudo realizar la compra sabiendo que el almacén Barranco2 se encontraba más lejos y con un precio de delivery más alto.

(B)Test Fail 
- Envío Express donde usuario compra producto dentro de **zona de cobertura donde no hay stock en almacén** escogido: Chorrillos
Se utiliza el courier 134 LaCajitaCorriendoExpress que tiene como cobertura:
ZONA134 BARRANCO
ZONA134 CHORRILLOS
ZONA134 LURIN
Pasos:
1. Crear carrito con producto
2. Checkout Shipping se selecciona dirección: Chorrillos
3. Checkout Billing Muestra mensaje de Error: Algunos productos no cuentan con stock disponible para la cantidad solicitada.
4. La validación en checkout no permite seguir con el flujo de compra, se bloquea botón 'Siguiente'

(B)Test Fail
- Compra **Store Pickup con 2 almacenes habilitados**; Chorrillos y Lince, solo el almacén de Chorrillos tiene stock y el de Lince no 
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y se selecciona almacén de lince(no stock).
Muestra mensaje : Actualmente no contamos con almacenes en el sitio buscado.
La validación en checkout no permite continuar con el flujo de compra hasta cambiar de almacén.
