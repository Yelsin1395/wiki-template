# Carrito o Cart
Todas las aplicaciones o websites de ecommerce tienen el concepto de Cart o Carrito de Compras, en donde un usuario registrado o no (guest) puede acumular lo que quiere comprar para luego llevarlo a checkout.

## Conceptos
Un carrito es un grupo de items que el usuario desea comprar.

Un item de carrito representa una variacion de producto que el usuario desea comprar. Recordar que el producto tiene 1 o mas variaciones (1 zapatilla, variacion azul, variacion roja).

El carrito tiene una edad considerando los varios componentes que un carrito tiene. La web o aplicacion decide si esa edad es muy antigua o no. Y en base a esta comparacion, la web o aplicacion decide si el cart debe actualizarse o no. Mas adelante se explica esto mas a fondo.

## Estructura

Un carrito tiene basicamente la siguiente estructura:
- id: el id del usuario que es dueno del carrito. El id puede ser de un usuario real o de un guest (que no esta registrado en la BD, solo se genera un cookie a nivel de la web).
- siteId: el cart del usuario es separado por cada site.
- currencyCode, currencySymbol: moneda base del carrito.
- currencyCodeUI, currencySymbolUI: moneda UI del carrito (se explica mas adelante).
- quantityTotal: suma de `quantity` de cada item. Si tengo un carrito con 3 pantalones y 3 faldas = 6.
- subTotal: suma de `amount` de cada item.
- subTotalUI: suma de `amountUI` de cada item.
- isGuest: cuando el usuario es guest.
- siteAdditionalInfo: bolsa especial para guardar informacion de cada site.
- savingTotal: suma de `saving` de cada item (mas adelante se explica que es un saving).
- savingTotalUI: suma de `savingUI` de cada item.
- items: lista de items del cart. Un item del cart es una variante de producto.
- exchangeRateUIToBase: tipo de cambio usado para convertir de currency UI a base.
- exchangeRateBaseToUI: tipo de cambio usado para convertir de currency base a UI.
- exchangeRateDate: fecha en la que el exchange rate fue traido.

Y un item del carrito tiene la siguiente estructura:
- productId: producto agregado al carrito. Esto no representa la existencia a comprar.
- productSeoUrl: seourl del producto. Usado para poder crear el link hacia el dentro de la web. Tambien es usado como identificador publico del producto.
- storeId: tienda a la que el producto pertenece.
- storeName: nombre de la tienda.
- storeSeoUrl: seourl de la tienda.
- variationId: variacion del producto a comprar. Esto si representa la existencia del producto.
- variationSku: el sku de la variacion.
- variationName: el nombre de la variacion (que esta confromado por el productName + variacion). Por ejemplo, si el productName es zapatilla y la variacion es Azul, el variationName dice completo = Zapatilla Azul. De esta forma es muy facil identificar cual variacion se esta comprando y no hay forma que se confunda con el nombre de producto.
- variationImageMedium: url completo para ver la imagen en tamano medio.
- variationBasePrice: precio base de la variacion y en moneda base. El precio base es el precio original de la variacion. Tipicamente se asocia con un precio especial para connotar una oferta (~~20~~19.9).
- variationBasePriceUI: precio base en moneda UI.
- variationSpecialPrice: precio especial de la variante.
- variationSpecialPriceUI: precio especial en moneda UI.
- variationPriceUpdatedDate: Fecha en la que el precio fue actualizado.
- quantity: cantidad de unidades de esta variacion a vender.
- amount: variationBasePrice|variationSpecialPrice * quantity
- amountUI: variationBasePriceUI|variationSpecialPriceUI * quantity
- _stock: stock obtenido al ingresar la variacion al carrito. Este dato es completamente descartable y se usa solo para hacer validaciones rapidas o temporales (Por eso el _ al inicio, para connotar temporal).
- customExchangeRateUIToBase: tipo de cambio para este item en particular.
- customExchangeRateBaseToUI: tipo de cambio para este item en particular.
- customExchangeRateDate: fecha en la que el tipo de cambio fue usado.
- siteAdditionalInfo: informacion adicional para el sitio. Cada site lo utiliza a su necesidad.
- savingAmount: cuanto ahorraste por este item en moneda base (variationBasePrice - variationSpecialPrice) * quantity.
- savingAmountUI: ahorro en moneda UI (variationBasePriceUI - variationSpecialPriceUI) * quantity.

## Carrito Guest
Cuando el usuario no se ha registrado o no se ha autenticado en la web, todas las web tienen la obligacion de asignarle un codigo de guest unico. Al agregar un item al carrito, se debe enviar el IsGuest=true para que ese cart se marque como guest (esto nos sirve a la plataforma para poder hacer una limpieza facilmente).

El guest puede hacer todas las mismas operaciones que un cart autenticado.

Cuando el usuario hace login, la web es responsable de traspasar los items del cart guest, hacia el cart del usuario (el API de Cart provee un endpoint especial para eso para que el proceso sea uniforme y se mantengan las validaciones y operaciones, etc).

## Ahorro
Cada item calcula cuanto se ahorro:
```
(variationBasePrice - variationSpecialPrice) * quantity.
```
Esto sirve mucho para supermercados.

## Modelo Multimoneda
El cart es el lugar de inicio para el manejo de sites multimoneda y checkouts multimoneda.
Y lo permite a traves de los campos base y los campos UI.

La moneda base esta declarada en la tabla Site y determina la moneda con la que toda la contabilidad es llevada. Esta deberia ser la moneda del pais en donde el site opera. Si el site opera en multiples paises, igual es necesario tener una moneda base que seguiria siendo la moneda en donde el HQ de la empresa esta probablemente.

La moneda UI se usa en la web o app y se usa para mostrar los precios o montos en una moneda arbitraria (escogida por el usuario o por X formas). La plataforma tambien otorga el objeto ExchangeRate que permite insertar ratios de cambio de moneda por dia y la web lo puede usar para operar.

Es la responsabilidad de la web que el exchange rate y los campos base y UI queden coherentes entre si.

Al hacer add to cart es posible mandar el precio base, y mandar el exchange rate para que el api de cart calcule el valor UI o viceversa.

En sites con una sola moneda, el cart es posible usarlo mandando solo los valores base (y por dentro el modulo de cart inserta exchange rate = 1).

### Puntos de fidelidad o Millas
En el caso de puntos de fidelidad o millas, el modelo multimoneda se ajusta muy bien, por lo que ya estaria cubierto. Sin embargo, en estos casos, la particularidad es que en el caso de millas por ejemplo, se podria tener un factor distinto por cada producto (segun su categoria) y por lo tanto, el modelo de Cart permite anadir un exchange rate a nivel de item tambien (distinto al de la cabecera). Es un modelo un poco mas complicado, pero ya esta implementado. 

De nuevo, el control de la coherencia de la data esta mas a nivel de la web que a nivel del API. Por lo tanto, se debe hacer el desarrollo y el testing con cuidado.

## Edad del carrito
Cuando un item es anadido al cart, el precio se copia y es guardado dentro del carrito "eternamente". Que pasaria si es que el precio cambio en el interin? Los sites tienen que estar listos para mantener esto actualizado o poder responder mejor ante esto.

Para manejar esto, se ha creado un concepto de "Edad del carrito" y basicamente describe que tan antiguo es el carrito.

La edad del carrito se determina a traves de tres factores:
- Fecha de precio
- Fecha de creacion de item
- Fecha de exchange rate

Cada site determina cuales de los tres factores se usan y si usa uno, dos o los tres factores.

La edad del carrito estaria determinado por el mas antiguo de los factores.

Por ejemplo:
- Precio: 11/05/2020
- Tipo Cambio: 12/05/2020
- Creacion Item: 12/05/2020

Si hoy es 16/05/2020, la edad seria 16-11 = 6 dias. Esto si es que el site escoge usar las tres. Si escoge usar solo el precio, seria igual 16-11. Pero si escoge que sea la creacion del item solamente seria 16-12=5.

### Como usar la edad?
El API de Cart solo te dice la edad. La web decide si esa edad es suficiente o muy antigua o que factores se usan.

Si es que la web decide que el carrito esta muy viejo, entonces la web es responsable de obtener los precios de nuevo o los exchange rates de nuevo y actualizarlo dentro del cart.

El API Cart tiene endpoints para hacer esas actualizaciones y el API de Cart se encarga de recalcular los montos acorde.
