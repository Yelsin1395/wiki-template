**DELIVERY**

(A)- Compra Regular con **T.Crédito**
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger T.Crédito
4. Checkout summary, debe mostrar numero de cuotas escogidas y T.Crédito enmascarada.
5. Finalizar Compra y en la pagina de éxito: Debe mostrar T.Crédito escogida enmascarada, debe mostrar monto pagado.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: Tarjeta de Crédito, información de Usuario: Dirección de entrega/Referencia seleccionada

Test Fail 
- Compra Regular con T.Crédito falsa
1. crear carrito
2. Checkout shipping escoger direccion
3. Checkout billing escoger T.Crédito invalida
4. Checkout summary, debe mostrar mensaje de error 'La pasarela de pago no pudo procesar la orden'.
5. Se espera que No se pueda completar la compra.


(A)- Compra Regular con **P.Efectivo** 
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger P.Efectivo 
4. Checkout summary, debe mostrar la dirección de envío y el método de pago PEfectivo.
5. Finalizar Compra y en la pagina de éxito: debe mostrar NOrden generada, cod. de pasarela de pago, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: P.Efectivo, información de Usuario: Dirección de entrega/Referencia seleccionada


(A)- Compra Regular con **Safety Pay**
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger SafetyPay  
4. Checkout summary, debe mostrar la dirección de envío y el método de pago SafetyPay.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: SafetyPay, información de Usuario: Dirección de entrega/Referencia seleccionada


(A)- Compra Regular con **Cash On Delivery** 
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger Cash On Delivery
4. Checkout summary, debe mostrar la dirección de envío y el método de pago Cash On Delivery.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada y monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: Cash On Delivery, información de Usuario: Dirección de entrega/Referencia seleccionada


(A)- Compra Regular con **Deposito en Cuenta** 
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger Deposito en Cuenta 
4. Checkout summary, debe mostrar la dirección de envío y el método de pago Deposito en Cuenta.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, Nro. de cta. corriente, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: Deposito en Cuenta, información de Usuario: Dirección de entrega/Referencia seleccionada


- Se **crea cupón** para aplicarlo en compra regular con la siguiente configuración:
1. Nombre del cupón: cuarentena2020
2. Fecha de vigencia: 12.08 al 12.12
3. Porcentaje o monto fijo de descuento: 20%
4. Monto máximo de descuento (en soles): Sin máximo
5. Aplica a productos con descuento: No
6. Monto mínimo de compra: Sin mínimo
7. Número máximo de cupones (total): 200
8. Método de Pago: safety Pay
9. Usos por cliente: Uno (1)
10. A qué productos aplica: Toda la tienda

(B)- Compra Regular con SafetyPay y aplicando cupón: cuarentena2020
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger SafetyPay 
4. Checkout summary, debe mostrar la dirección de envío y el método de pago SafetyPay e ingresar cupón:cuarentena2020.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela pago, debe mostrar descuento, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: SafetyPay, descuento obtenido, cupón = cuarentena2020, información de Usuario: Dirección de entrega/Referencia seleccionada


(B)- Compra Regular con T.Crédito y aplicando cupón
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing escoger T.Crédito
4. Checkout summary, debe mostrar la dirección de envío, numero de cuotas escogidas y T.Crédito enmascarada e ingresar cupón vigente.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela pago, debe mostrar descuento, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: Tarjeta de Crédito, descuento obtenido, cupón utilizado, información de Usuario: Dirección de entrega/Referencia seleccionada


(A)- Compra Regular y **Registro de una T.Crédito**
Pasos:
1. Crear carrito con producto
2. Checkout shipping escoger direccion
3. Checkout billing agregar T.Credito y registrar nueva tarjeta: Seleccione la marca de tarjeta: Visa, Mastercard, Diners, Amercian Express, Tipo: Crédito o Débito, Agregar Nro. de Tarjeta (16 dígitos), Nombres y Apellidos, mes, año y CVV.
4. Checkout summary, debe mostrar la dirección de envío, numero de cuotas escogidas y T.Crédito enmascarada.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, Nro de transacción, debe mostrar monto pagado.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, Método de Pago: Tarjeta de Crédito, información de Usuario: Dirección de entrega/Referencia seleccionada


(A)- Compra Regular y **Registro de una nueva Dirección**
Pasos:
1. Crear carrito con producto
2. Checkout shipping agregar dirección, registrar nueva dirección, seleccionar tipo de dirección: Domicilio, Oficina, ingresar dirección según el formato indicado, ingrese distrito, ingrese referencia, datos de la persona que recibirá el pedido: Nombre, Apellidos, DNI, Teléfono o Celular. 
Corrobar ubicación según google maps y confirmar.
3. Checkout summary, debe mostrar la dirección de envío.
4. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada y monto total.
5. Verificar el envío de correo de compra
6. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, información de Usuario: Dirección de entrega/Referencia seleccionada

(A) Fail **Compra Regular con ubigeo fuera de SRT** 
Pasos:
1. Crear carrito con producto.
2. Checkout shipping escoger dirección fuera del rango de la SRT.
3. Checkout billing, debe mostrar mensaje de error 'La dirección de entrega no está disponible por ahora. No es posible realizar la compra'.
4. Se espera que para la tienda Store-Regresion2019 no tenga cobertura y se vea un mensaje especifico.

(A) Fail **Compra Regular múltiple con alguna tienda fuera de la SRT**
Pasos:
1. Crear carrito con productos de al menso 2 tiendas deferentes.
2. Checkout shipping escoger dirección que este fuera del rango de la SRT de alguna de las tiendas.
3. Checkout billing, debe mostrar mensaje de error de manera explícita para la tienda que no tiene cobertura:  'StoreXXX : La dirección de entrega no está disponible por ahora. No es posible realizar la compra'.


(A)- Compra y verificación de comprobante (RUC)
Pasos:
1. Crear carrito con producto
2. Checkout shipping seleccionar dirección
3. Checkout summary, debe mostrar la dirección de envío, método de pago, entrega estimada.
En 'Resumen de Compra' se verifica comprobante: Factura/Importaciones SAC/12345678922,
subtotal, costo de envío y Total.
4. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada y monto total.
5. Verificar el envío de correo de compra
6. Ir a MC-Admin/Listado de Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, información de Usuario: Dirección de entrega/Referencia seleccionada, información de Courier/Información Adicional: Tipo Documento / N° : Factura / 12345678922 / Importaciones SAC
Se presiona el botón Descarga por excel y se verifica los campos de RUC, Razón social
Se verifica en la opción de Acciones 'Imprimir' se visualiza en el label Tipo de comprobante: Factura Razón Social: Importaciones SAC - RUC: 12345678922

 7. Ir a MC/Listado de Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: Regular, información de Usuario: Dirección de entrega/Referencia seleccionada, información de Courier/Información Adicional: Tipo Documento / N° : Factura / 12345678922 / Importaciones SAC
Se presiona el botón Descarga por excel y se verifica los campos de RUC, Razón social

**STORE PICKUP**

(A)- Compra **StorePickup con T.Crédito**
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger Retira tu pedido, buscar lugar de recojo.  
3. Checkout billing escoger T.Crédito
4. Checkout summary, debe mostrar dirección de recojo, numero de cuotas escogidas y T.Crédito enmascarada.
5. Finalizar Compra y en la pagina de éxito: Debe mostrar T.Crédito escogida enmascarada, debe mostrar monto pagado.
6. Verificar el envío de correo de compra.
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: Tarjeta de Crédito, Dirección de entrega/Referencia almacén seleccionado

Test Fail 
- Compra StorePickup con T.Crédito falsa
1. Crear carrito
2. Checkout billing escoger T.Crédito invalida
3. Checkout summary, debe mostrar mensaje de error 'La pasarela de pago no pudo procesar la orden'.
4. Se espera que No se pueda completar la compra.

(A)- Compra **StorePickup con P.Efectivo** 
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger Retira tu pedido, buscar lugar de recojo. 
3. Checkout billing escoger P.Efectivo 
4. Checkout summary, debe mostrar dirección de recojo.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: P.Efectivo, Dirección de entrega/Referencia almacén seleccionado

(A)- Compra **StorePickup con SafetyPay**
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger SafetyPay
4. Checkout summary, debe mostrar dirección de recojo.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: SafetyPay, Dirección de entrega/Referencia almacén seleccionado

(A)Test Fail
- Compra **StorePickup con Cash On Delivery**
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger Cash On Delivery
4. Checkout summary, no permite finalizar compra.
5. Se espera un mensaje claro de error.

(A)- Compra **StorePickup con Deposito en Cuenta Bancaria**
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger Deposito en Cuenta Bancaria.
4. Checkout summary, debe mostrar dirección de recojo.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada y monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: Deposito en Cuenta Bancaria, Dirección de entrega/Referencia almacén seleccionado

(B)- Compra StorePickup con PEfectivo y aplicando cupón
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger PEfectivo
4. Checkout summary, debe mostrar dirección de recojo e ingresar cupón vigente.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar descuento obtenido, debe mostrar monto total a pagar.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: PEfectivo, descuento obtenido y cupón aplicado, Dirección de entrega/Referencia almacén seleccionado

(B)- Compra StorePickup con T.Crédito y aplicando cupón
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger T.Crédito
4. Checkout summary, debe mostrar dirección de recojo, numero de cuotas escogidas y TC enmascarada, Finalmente Ingresar cupón vigente.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, Nro de transacción, debe mostrar descuento obtenido, debe mostrar monto total pagado.
6. Verificar el envío de correo de compra.
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: T.Crédito, descuento obtenido y cupón aplicado, Dirección de entrega/Referencia almacén seleccionado.

(B)Test Fail- **Compra Delivery aplicando cupón con exclusión de tienda**
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger Pago efectivo
4. Checkout summary, aplicar cupón para la tienda excluida.
5. Se espera un mensaje claro de advertencia.

(B)Test Fail- **Compra StorePickup aplicando cupón con exclusión de tienda**
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing escoger Tarjeta de crédito
4. Checkout summary, aplicar cupón para la tienda excluida.
5. Se espera un mensaje claro de advertencia.


(A)- Compra Store Pickup y Registro de una T.Crédito
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar. 
3. Checkout billing agregar T.Crédito y registrar nueva tarjeta: Seleccione la marca de tarjeta: Visa, Mastercard, Diners, Amercian Express, Tipo: Crédito o Débito, Agregar Nro. de Tarjeta (16 dígitos), Nombres y Apellidos, mes, año y CVV.
4. Checkout summary, debe mostrar la dirección de recojo, numero de cuotas escogidas y T.Crédito enmascarada.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, Nro de transacción, debe mostrar monto pagado.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, Método de Pago: Tarjeta de Crédito, Dirección de entrega/Referencia almacén seleccionado

(C)- Compra Store Pickup y Edición de selección de almacén
Pasos:
1. Crear carrito con producto
2. Checkout SelectDelivery escoger opción 'Retira tu pedido', buscar lugar de recojo y seleccionar almacén A, se presiona el botón Editar para poder cambiar a otro almacén.
4. Checkout summary, debe mostrar la dirección de recojo actualizado.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, dirección exacta del lugar de recojo de productos, Dirección de entrega/Referencia almacén seleccionado

(A)- Compra Store Pickup sin operador logístico
Pasos:
1. Apagar operador logísticos de la tienda.
2. Crear carrito con productos de la tienda.
3. Checkout SelectDelivery, solo se mostrará la opción de 'Retira tu pedido', buscar lugar de recojo y seleccionar(se continua con flujo de compra)
4. Checkout summary, debe mostrar dirección de recojo, método de pago, Costo total.
5. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, debe mostrar monto total.
6. Verificar el envío de correo de compra
7. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar el tipo de envío: SPickup, información de courier/ Nombre de courier: no aplica, DeliveryTime: 0 horas, Tipo: StorePickup, Dirección de entrega/Referencia almacén seleccionado

**MIXTA**

(A)- Compra Mixta (Regular y StorePickup) con T.Crédito Pasos:
1. Crear carrito con 1 producto como minimo para tienda A con delivery y 1 producto como minimo para tienda B con storePickup.
2. Checkout SelectDelivery escoger Despacho a domicilio para tienda A y Retira tu pedido para tienda B, buscar lugar de recojo.
3. Checkout shipping escoger direccion para envio en tienda A.
4. Checkout billing escoger T.Crédito.
5. Checkout summary debe mostrar la direccion de envio, numero de cuotas escogidas y T.Crédito enmascarada.
6. Checkout summary debe mostrar para los items de la tienda A, el tipo de envío y la entrega estimada. 
7. Checkout summary debe mostrar para los items de la tienda B, el nombre del almacen y la direccion seleccionada.
8. Finalizar Compra y en la pagina de éxito: Debe mostrar T.Crédito escogida enmascarada, debe mostrar monto pagado.
9. Verificar el envío de correo de compra.
10. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar 2 ordenes: Regular y StorePickup, Método de Pago: Tarjeta de Crédito, información de Usuario: Dirección de entrega/Referencia seleccionada.

(A) - Compra Mixta con T.Crédito falsa
1. Crear carrito con 1 producto como minimo para tienda A con delivery y 1 producto como mínimo para tienda B con storePickup.
2. Checkout SelectDelivery escoger Despacho a domicilio para tienda A y Retira tu pedido para tienda B, buscar lugar de recojo.
3. Checkout shipping escoger direccion para envio en tienda A.
4. Checkout billing escoger T.Crédito invalida.
5. Checkout summary, debe mostrar mensaje de error 'La pasarela de pago no pudo procesar la orden'.
6. Se espera que No se pueda completar la compra.


(A)- Compra Mixta con P.Efectivo Pasos:
1. Crear carrito con 1 producto como minimo para tienda A con delivery y 1 producto como minimo para tienda B con storePickup.
2. Checkout SelectDelivery escoger Despacho a domicilio para tienda A y Retira tu pedido para tienda B, buscar lugar de recojo.
3. Checkout shipping escoger direccion para envio en tienda A.
4. Checkout billing escoger P.Efectivo.
5. Checkout summary debe mostrar la direccion de envio y el método de pago PEfectivo.
6. Checkout summary debe mostrar para los items de la tienda A, el tipo de envío y la entrega estimada. 
7. Checkout summary debe mostrar para los items de la tienda B, el nombre del almacen y la direccion seleccionada.
8. Finalizar Compra y en la pagina de éxito: debe mostrar NOrden generada, cod. de pasarela de pago, debe mostrar monto total.
9. Verificar el envío de correo de compra
10. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar 2 ordenes: Regular y StorePickup, Método de Pago: P.Efectivo, información de Usuario: Dirección de entrega/Referencia seleccionada.


(A)- Compra Mixta con Safety Pay Pasos:
1. Crear carrito con 1 producto como minimo para tienda A con delivery y 1 producto como minimo para tienda B con storePickup.
2. Checkout SelectDelivery escoger Despacho a domicilio para tienda A y Retira tu pedido para tienda B, buscar lugar de recojo.
3. Checkout shipping escoger direccion para envio en tienda A.
4. Checkout billing escoger SafetyPay.
5. Checkout summary debe mostrar la direccion de envio y el método de pago SafetyPay.
6. Checkout summary debe mostrar para los items de la tienda A, el tipo de envío y la entrega estimada. 
7. Checkout summary debe mostrar para los items de la tienda B, el nombre del almacen y la direccion seleccionada.
8. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar monto total.
9. Verificar el envío de correo de compra.
10. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar 2 ordenes: Regular y StorePickup, Método de Pago: SafetyPay, información de Usuario: Dirección de entrega/Referencia seleccionada.

(A)- Compra Mixta con Cash On Delivery (Fail) Pasos:
1. Crear carrito con 1 producto como minimo para tienda A con delivery y 1 producto como minimo para tienda B con storePickup.
2. Checkout SelectDelivery escoger Despacho a domicilio para tienda A y Retira tu pedido para tienda B, buscar lugar de recojo.
3. Checkout shipping escoger direccion para envio en tienda A.
4. Checkout billing escoger Cash On Delivery.
5. Se espera un mensaje claro de error.

(A)- Compra Mixta con Deposito en Cuenta  Pasos:
1. Crear carrito con 1 producto como minimo para tienda A con delivery y 1 producto como minimo para tienda B con storePickup.
2. Checkout SelectDelivery escoger Despacho a domicilio para tienda A y Retira tu pedido para tienda B, buscar lugar de recojo.
3. Checkout shipping escoger direccion para envio en tienda A.
4. Checkout billing escoger Deposito en Cuenta.
5. Checkout summary, debe mostrar la dirección de envío y el método de pago Deposito en Cuenta.
6. Checkout summary debe mostrar para los items de la tienda A, el tipo de envío y la entrega estimada. 
7. Checkout summary debe mostrar para los items de la tienda B, el nombre del almacen y la direccion seleccionada.
8. Finalizar Compra y en la pagina de éxito: debe mostrar N.Orden generada, cod. de pasarela de pago, debe mostrar monto total.
9. Verificar el envío de correo de compra.
10. Ir a MC Pedidos, buscar pedido por N.Orden, debe de mostrar 2 ordenes: Regular y StorePickup, Método de Pago: SafetyPay, información de Usuario: Dirección de entrega/Referencia seleccionada.





