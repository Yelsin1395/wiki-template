El Product Type es un nuevo concepto dentro de la nueva plataforma de Juntoz debido a que en el legacy no habia un ancla de definicion para los productos y los merchants definian los productos de manera irregular (mas por falta de control de MC mas que por ignorancia).

Entonces en la nueva plataforma se incorporo este concepto para poder tener un control real sobre la definicion del producto. Esto lograra menor cantidad de bugs y mejor mantenimiento del front y backend.

Los Product Types son registrados por el departamento de Contenido del Site quienes se encargan de manejar la taxonomia del mismo (Product Types, Categorias, Marcas). Por lo que es muy importante su seniority y ownership.

Un ejemplo de PT seria uno llamado VESTIMENTA. Vestimenta es un tipo de producto que engloba todos los tipos de vestimentas muy bien: polos, pantalones, zapatos, etc.

# Atributos
Las piezas basicas del PT son los llamados Atributos. Un PT puede tener uno o varios atributos, siendo tipicamente uno o dos atributos maximo.

Y esto tiene una razon importante: a mas atributos, mas combinaciones de variaciones son posibles pero a la vez representa una mayor dificultad de mantenimiento.

Entonces, para el caso de Vestimenta se registran DOS atributos: Color y Talla. Y todo lo que engloba vestimenta realmente se puede modelar usando estos dos atributos como llave de la variacion del producto.

```
{
  productTypeName: "Vestimenta",
  productAttributes: [
    {
        attributeName: "Color"
    },
    {
        attributeName: "Talla"
    }
  ]
}
```
Cuantos atributos y que atributos son entera decision de Contenido. Sin embargo, el crear o modificar Product Types conlleva una gran responsabilidad. El registrar mal un PT puede llevar a una masa de merchants que tengan que corregir su data nuevamente, o si es que hay un exceso de PT, se fragmentaria el catalogo y incrementaria la complejidad y la cantidad de typos y errores.

## + Atributos + Variaciones
Cuando un product type es asignado a un producto, entonces las variaciones que se crearan dentro del producto tendran su "PK" asignada como la lista de atributos del PT.

Entonces para el caso de Vestimenta, el producto tendra que registrar sus variaciones SIEMPRE adjuntando un valor valido para cada uno de los atributos. Quedaria algo asi:

```
{
    productName: "Polo Billabong Oahu",
    productType: {
        productTypeId: "xxx",
        productTypeName: "Vestimenta",
        productTypeVersion: 1
    },
    variations: [
        {
            variationAttributes: {
                Color: "Blanco",
                Talla: "XS"
            },
            price: {...}
        },
        {
            variationAttributes: {
                Color: "Negro",
                Talla: "S"
            },
            price: {...}
        }
    ]
}
```

## Atributos Generan o no Variaciones
Para que un atributo sea el "PK" de una variante debera tener asignado el campo `generatesVariation=true`.
Cuando el atributo `generatesVariation=false`, lo que ocurrira es que ese atributo NO determinara la llave de una variante y por lo tanto se registrara en `product.productAttributes`. Esto, para que el usuario pueda llenar esa informacion estandar que el product type necesita tener.

Por ejemplo, para Vestimenta, se tendria un nuevo atributo llamado "Lavado" (que determinara que tipo de lavado esa prenda debe tener). El "lavado" no determinara la creacion de nuevas variantes, pero es un atributo importante que le dara mas informacion al usuario final para hacer la compra.

## Atributos Valores Validos
El equipo de Contenido tambien podran asignar "valores validos" (el campo `validValues`) a cada atributo para que el merchant no pueda salir de esa lista (si necesitan mas valores, se tienen que contactar con Contenido).

Por ejemplo, Color puede tener un listado hecho por el equipo de Contenido para que los merchants solo puedan escoger aquellos.

Esto es importante asimismo para mejorar la data del marketplace y ayudara a que no hayan errores ni fragmentacion de los productos.

## Atributos Tipo de Presentacion
Cada atributo tambien tendra un campo llamado `displayType` que es una sugerencia de como la UI debe mostrar ese atributo. Esto es importante dado que cuando hablamos de Color se quiere mostrar o una foto de la variacion o una paleta de colores. Y cuando hablamos de Talla, un dropdown con los valores es suficiente.

Por lo tanto, tenemos identificados dos displayType posibles por ahora: `firstImage` y `list`.

Para el caso de `firstImage`, el frontend debe encontrar la primera foto de la variacion y mostrar eso como icono para escoger esa variacion. Y mostrar todas las primeras imagenes de cada variacion.

Para el caso de `list`, el frontend puede mostrar solo la lista de combinaciones del atributo Talla de todas las variaciones.

# Borrador, Publicacion y Versionamiento
Dado la importancia de los PT y la necesidad de tener una buena definicion desde el inicio, es que se decidio agregar algunos campos que ayuden a manejar esto de una manera consistente y auditable.

Hay tres conceptos que fueron creados: `productTypeVersion`, `isPublished` y `isLatest`.

## Como funciona esto?
La primera version del PT (xej Computadora) tiene version 1 y nace como `isPublished=false`.

Mientras que no este publicado, el PT puede ser modificado libremente. Los cambios que se hagan en esta version estaran contenidos alli y nadie los vera hasta que se publiquen. Los productos solo pueden crearse con un PT publicado. Y mientras haya una version draft, **no se puede crear otra**.

Cuando el usuario publica el PT, este PT podra ser usado para crear nuevos productos. Al crear uno, el PT y su version se graban dentro del producto como referencia de cual modelo se esta usando.

Si el usuario necesita hacer cambios sobre lo publicado, la unica opcion que tiene es hacer un upgrade. Esto creara una "copia" del PT actual (v1) con el mismo nombre que el anterior, con version 2 y en modo draft.

Una vez terminado los cambios en v2, el usuario publicara esa version. Al hacerlo, la V2 se marca como `isLatest=true` y entonces los nuevos productos solo podran ser creados a partir de Computadora V2. Computadora V1 quedara registrado como historial.
