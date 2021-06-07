(A)- Compra Regular con **T.Crédito**
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger T.Crédito
4. Checkout summary, debe mostrar numero de cuotas escogidas y T.Crédito enmascarada.
5. Finalizar Compra y en la pagina de éxito: Debe mostrar T.Crédito escogida enmascarada, debe mostrar monto pagado.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar tipo de tienda (whitelabel), Método de Pago: Tarjeta de Crédito, información de Usuario: Dirección de entrega/Referencia seleccionada

Test Fail 
- Compra con T.Crédito falsa
1. crear carrito
2. Checkout shipping escoger direccion
3. Checkout billing escoger T.Crédito invalida
4. Checkout summary, debe mostrar mensaje de error 'La pasarela de pago no pudo procesar la orden'.
5. Se espera que No se pueda completar la compra.

(A)- Compra con **P.Efectivo** 
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger P.Efectivo 
4. Checkout summary, debe mostrar la dirección de envío y el método de pago PEfectivo.
5. Finalizar Compra y en la pagina de éxito: debe mostrar NOrden generada, cod. de pasarela de pago, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar tipo de tienda (whitelabel), Método de Pago: P.Efectivo, información de Usuario: Dirección de entrega/Referencia seleccionada

(A)- Compra con **Safety Pay**
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger SafetyPay  
4. Checkout summary, debe mostrar la dirección de envío y el método de pago SafetyPay.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar tipo de tienda (whitelabel), Método de Pago: SafetyPay, información de Usuario: Dirección de entrega/Referencia seleccionada

(A)- Compra y **Registro de una T.Crédito**
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger dirección
3. Checkout billing agregar T.Credito y registrar nueva tarjeta: Seleccione la marca de tarjeta: Visa, Mastercard, Diners, Amercian Express, Tipo: Crédito o Débito, Agregar Nro. de Tarjeta (16 dígitos), Nombres y Apellidos, mes, año y CVV.
4. Checkout summary, debe mostrar la dirección de envío, numero de cuotas escogidas y T.Crédito enmascarada.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, Nro de transacción, debe mostrar monto pagado.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar tipo de tienda (whitelabel), tipo de envío: Regular, Método de Pago: Tarjeta de Crédito, información de Usuario: Dirección de entrega/Referencia seleccionada

(A)- Compra Regular y **Registro de una nueva Dirección**
Pasos:
1. Crear carrito con producto
2. Checkout shipping agregar dirección, registrar nueva dirección, seleccionar tipo de dirección: Domicilio, Oficina, ingresar dirección según el formato indicado, ingrese distrito, ingrese referencia, datos de la persona que recibirá el pedido: Nombre, Apellidos, DNI, Teléfono o Celular. 
Corrobar ubicación según google maps y confirmar.
3. Checkout summary, debe mostrar la dirección de envío.
4. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada y monto total.
5. Verificar el envío de correo de compra
6. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar tipo de tienda (whitelabel), información de Usuario: Dirección de entrega/Referencia seleccionada


- Se **crea cupón** para aplicarlo en compra con la siguiente configuración:
1. Nombre del cupón: cuponTestWL
2. Fecha de vigencia: 12.08 al 12.12
3. Porcentaje o monto fijo de descuento: 20%
4. Monto máximo de descuento (en soles): Sin máximo
5. Aplica a productos con descuento: No
6. Monto mínimo de compra: Sin mínimo
7. Número máximo de cupones (total): 200
8. Usos por cliente: Uno (1)
9. A qué productos aplica: Toda la tienda
10. Excluir Whitelabel (Kidsmadehere)

(B)- Compra Regular con SafetyPay y aplicando cupón: cuponTestWL
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger SafetyPay 
4. Checkout summary, debe mostrar la dirección de envío y el método de pago SafetyPay e ingresar cupón:cuarentena2020.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela pago, debe mostrar descuento, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden debe de mostrar tipo de tienda (whitelabel), Método de Pago: SafetyPay, descuento obtenido, cupón = cuponTestWL, información de Usuario: Dirección de entrega/Referencia seleccionada


(B)- Compra Regular con T.Crédito y aplicando cupón
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger T.Crédito
4. Checkout summary, debe mostrar la dirección de envío, numero de cuotas escogidas y T.Crédito enmascarada e ingresar cupón vigente.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela pago, debe mostrar descuento, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar tipo de tienda (whitelabel), Método de Pago: Tarjeta de Crédito, descuento obtenido, cupón utilizado, información de Usuario: Dirección de entrega/Referencia seleccionada


