Para poder estandarizar los envios y tarifas y ademas asegurar que sea mas facil de configurar, se creo el concepto de Shipping Size o Talla de Envio.

NOTA: Esto no tiene ninguna relacion con las tallas de un pantalon o una camisa. Es para tener el tamano de un paquete que dentro puede contener 4 camisas y 4 pantalones. Lo que hay adentro finalmente no interesa.

Esto es una forma de "esconder" los detalles de un paquete detras de una talla y asi poder configurar los precios de envio en base a esta talla.

Actualmente el sistema tiene registrado los siguientes tallas:
- XXS
- XS
- S
- M
- L
- XL
- XXL

_Es bastante poco probable que se vayan a registrar mas de estas tallas. Si fuera necesario, solo se debe crear hacia los extremos osea XXXS y XXXL._

Cada site permite entonces registrar el peso, largo, ancho y alto maximo de cada talla y asi poder encajar los productos en estas tallas.

Se trata de decir: si este objeto encaja en este cubo y no pesa mas que este limite, entonces se le considera p.e. XS. Si sobrepasa este limite, se pasa a la siguiente talla y asi sucesivamente.

Para que el site pueda operar, es necesario que el site cree este indice de tamanos.

Cuando se crea, se registrara automaticamente con estos valores por defecto:
```
dbEntity.sizes = [
            {
                shippingSizeCode: 'XXS',
                maxLengthCms: 10,
                maxWidthCms: 10,
                maxHeightCms: 10,
                maxWeightKg: 1
            },
            {
                shippingSizeCode: 'XS',
                maxLengthCms: 20,
                maxWidthCms: 20,
                maxHeightCms: 20,
                maxWeightKg: 5
            },
            {
                shippingSizeCode: 'S',
                maxLengthCms: 30,
                maxWidthCms: 30,
                maxHeightCms: 30,
                maxWeightKg: 10
            },
            {
                shippingSizeCode: 'M',
                maxLengthCms: 60,
                maxWidthCms: 60,
                maxHeightCms: 60,
                maxWeightKg: 20
            },
            {
                shippingSizeCode: 'L',
                maxLengthCms: 100,
                maxWidthCms: 100,
                maxHeightCms: 100,
                maxWeightKg: 30
            },
            {
                shippingSizeCode: 'XL',
                maxLengthCms: 200,
                maxWidthCms: 200,
                maxHeightCms: 200,
                maxWeightKg: 50
            },
            {
                shippingSizeCode: 'XXL'
                // maximum bound for 'ELSE' scenario
            }
        ];
```

Esta en potestad del site de modificar los valores o no (dependera de los productos en venta por ejemplo).

## ELSE o POR DEFECTO
Para que no haya un bloqueo para obtener el precio de envio, se asume que la ultima talla siempre es el valor ELSE o valor por defecto.

Es decir, si el tamano del paquete es demasiado grande para encajar en la talla XL (penultima talla), siempre el algoritmo devolvera XXL.

## Activacion o desactivacion de tallas
El sistema tambien permite al site desactivar tallas si asi lo desea (para que? de repente su catalogo de productos no estan variado en tamanos y pesos por lo tanto tener tantas clasificaciones hace mas complicado la configuracion).

Asi como se desactivan, luego tambien se pueden reactivar.

Para realizar cualquiera de las dos acciones, el sistema solo permite hacerlo para los extremos de la lista.

Entonces para esta lista de activos: `[XXS, XS, S, M, L, XL, XXL]`, solo podria desactivar `XXS` y `XXL`.

Para poder desactivar `XS`, primero tendria que desactivar `XXS` y asi ir en orden.

Esto es para garantizar que no hayan huecos de tamanos y que la regla de verificacion incremental (si no encaja en esta, encajara en el siguiente?) se mantenga vigente siempre. Se favorece el centro (que son las tallas mas usuales) que los extremos.

IMPORTANTE: El site debe tener al menos una talla activa siempre. Si es que todas estan desactivadas, entonces ningun tamano se obtendra y probablemente el envio se bloqueara en el checkout.

# Como se calcula el tamano?

Imaginemos este caso, donde tengo un cart con 4 items (3 productos): una camisa, dos pantalones y una billetera.
- La camisa empaquetada viene doblada por lo tanto sus medidas pueden ser 40x25x5 y un peso de 0.3kg.
- El pantalon tambien doblado tendria medidas 30x30x4 y un peso de 0.4kg.
- La billetera viene en una caja pequena casi del mismo tamano que el producto con medidas 15x10x3 con un peso de 0.1kg.

Entonces asumiendo que todos los items vienen de la misma tienda, entonces se espera que se haga un solo envio hacia el usuario.

Para hacer el calculo entonces el input es cada producto con sus dimensiones unitarias y la cantidad de productos que se va a enviar:
```
{
  items: [
     {
        packageLengthCmsSingle: 40,
        packageWidthCmsSingle: 25,
        packageHeightCmsSingle: 5,
        packageWeightKgSingle: 0.3,
        quantity: 1
     },
     {
        packageLengthCmsSingle: 30,
        packageWidthCmsSingle: 30,
        packageHeightCmsSingle: 4,
        packageWeightKgSingle: 0.4,
        quantity: 2
     },
     {
        packageLengthCmsSingle: 15,
        packageWidthCmsSingle: 10,
        packageHeightCmsSingle: 3,
        packageWeightKgSingle: 0.1,
        quantity: 1
     }
  ]
}
```

Con estos datos el sistema calculara el tamano del paquete completo.

Debido a que el tema de optimizacion de objetos dentro de una caja es un problema NP completo (osea muy complejo), vamos entonces a hacer el siguiente algoritmo para reducir el numero de errores.

1. Lo primero es obtener el volumen y peso de cada item.
```
ItemVolume[i] = Length[i] * Height[i] * Width[i] * Quantity
ItemWeight[i] = Weight[i] * Quantity
```
2. Por cada talla revisar si es que el ItemVolume y el ItemWeight encaja en la talla.
3. Luego como medida adicional para evitar errores, se validara que todos los lados de cada producto encaje en esa talla.
Osea, esto para regular el caso en donde por ejemplo, uno de los productos es una varilla de 1m (aunque su volumen sea pequeno y su peso tambien).
4. Si todo el paquete no encaja, se pasa a la siguiente talla habilitada.
5. Si ninguna talla encaja, se usa siempre la ultima talla habilitada.

El resultado del endpoint es
```
{
  data: {
    shippingSizeCode: 'XS'
  }
}
```
