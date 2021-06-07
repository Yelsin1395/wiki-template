El producto es la entidad principal de todo la plataforma Juntoz. Todo se da gracias al producto, por lo que es importante que su definicion sea clara y precisa.

Un producto en Juntoz es un objeto que puede es almacenado por una tienda para ser vendido a un usuario final. En el futuro, este objeto podria ser digital o un servicio (ambos aun no modelados). Por ahora se asume que todos los productos son fisicos o tienen un stock asociado.

# Producto por Tienda
La ubicacion del producto dentro de la jerarquia de entidades seria asi:

```
Site
  Merchant
    Store
      Product
    Warehouse
```

Un producto siempre tiene que estar dentro de una tienda.

Si un producto es vendido por dos tiendas del mismo merchant, se tendra que duplicar en cada tienda.

Si un producto es vendido por dos tiendas de distintos merchants, cada merchant tendra que registrarlo. 

Esto es dado que se busca que el modelo de datos refleje un marketplace "mas suelto" (Amazon asocia los sellers al producto, Juntoz es al reves: asocia el producto al seller).

# Product Type
En la nueva plataforma hemos anadido un nuevo concepto llamado Product Type, que todo producto debe tener asignado. El detalle de como funciona tiene su wiki propio.

Es importante decir que el Product Type determina las variaciones del producto y algunos atributos.
Tener el product type adecuado permite que el usuario pueda ser clasificado correctamente y por ende encaje correctamente al hacer el slice-n-dice en el catalogo.

# Producto en varios almacenes
Un producto dentro de una tienda puede estar ubicada en varios almacenes asociados al merchant.

# Variaciones
El producto esta modelado como una abstraccion, es decir, "no existe". La "existencia" es en realidad una variacion del producto. En la nueva plataforma, hemos enforzado que SIEMPRE haya una variacion al menos.

La variacion tiene el SKU y el precio de venta especial. Asimismo el stock es asignado a la variacion, no al producto.

Por ejemplo, el producto seria "Polo Billabong Oahu". Este producto puede tener varias variaciones: una por cada color y talla. No es obligatorio que todas las combinaciones esten disponibles. La tienda decidira esto.

|SKU|Color|Talla|
|-|-|-|
|P4931001|Negro|XS|
|P4931002|Blanco|XS|
|P4931003|Rojo|XS|
|P4931004|Negro|S|
|P4931006|Rojo|S|
|P4931007|Negro|M|
|P4931008|Blanco|M|
|P4931009|Rojo|M|

## variationName
Los productos tienen su atributo llamado `productName` que representa el nombre del producto. Sin embargo, este se debe usar solo en el catalogo y pagina de producto. El verdadero atributo que debe usarse es llamado `variationName` y esta en cada variacion.

Entonces al agregar la variacion al cart (porque anades la variation, no el producto), el cart y todos los procesos posteriores deben usar el variationName.

Que especial tiene este campo?
Es debido a que este campo es "calculado": es la union entre el `productName` y los valores de los atributos de la variacion.

Por ejemplo, para el caso anterior:
```
productName = Polo Billabong Oahu
variation1.variationName = Polo Billabong Oahu Negro/XS
```

La diferencia es muy importante porque de esta manera el usuario y todos los procesos despues tienen inequivocadamente el nombre exacto de la variacion escogida por el usuario (en su color y talla) y asi habran MENOS errores de envio y de pago.

En la plataforma legacy, las variaciones tenian el mismo nombre que el producto por lo tanto esto causaba confusion y nunca se sabia cual variacion exacta fue escogida (solo por el SKU pero que no es humanamente relacionable) y asimismo ocultaba si el sistema tenia un error en la seleccion de la variacion.

# Categorias
Un producto puede tener asociado varias categorias. Cuando una categoria como Moda/Moda Hombre/Polos es asignada al producto, el sistema automaticamente asignara tres categorias al producto: Moda, Moda Hombre y Polos. Esto es asi para que cuando el usuario este en la web ecommerce y busque Moda, automaticamente el sistema pueda encontrar aquellos productos de las categorias hijas rapidamente y con poco esfuerzo.

Sin embargo, en esta nueva plataforma estamos reformando el guardado de la relacion de categorias para mejorar su usabilidad y performance.

En la plataforma legacy, el producto tenia aquellas tres categorias pero solamente los IDs (como tipicamente uno modela una base de datos SQL). Esto obligaba a siempre hacer un query a la tabla Category y ademas para poder obtener el arbol de categorias o el orden en el que se deben mostrar, habia que hacer queries recursivos para cada una. Esto era un proceso carisimo.

En la nueva plataforma, al registrar la categoria Moda/Moda Hombre/Polos, el sistema tambien registrara los padres hacia arriba de la jerarquia pero los registrara de una manera especial.

El modelo antiguo era asi. NO habia visibilidad de la jerarquia ni de los nombres, etc.
```
{
    productName: "Polo",
    categories: [
        {
            categoryId: 100 // Moda
        },
        {
            categoryId: 101 // Moda Hombres
        },
        {
            categoryId: 102 // Polos
        }
    ]
}
```

El nuevo modelo es asi.
```
{
    productName: "Polo",
    categories: [
        {
            categoryId: 102,
            categoryName: "Polos",
            categoryFullPath: "Moda/Moda Hombre/Polos",
            categorySeoUrl: "polos",
            level: 2
            parents: [
                {
                    categoryId: 100,
                    categoryName: "Moda",
                    categoryFullPath: "Moda",
                    categorySeoUrl: "moda",
                    level: 0
                },            
                {
                    categoryId: 101,
                    categoryName: "Moda Hombre",
                    categoryFullPath: "Moda/Moda Hombre",
                    categorySeoUrl: "moda-hombre",
                    level: 1
                }
            ]
        }
    ]
}
```

La diferencia es muy grande dado que en la ultima podemos rapidamente obtener el breadcrumb del producto con todas sus categorias y ademas podemos determinar rapidamente cuales categorias fueron realmente asignadas por el usuario vs cuales el sistema calculo y trajo para completar y mejorar las busquedas.

# Descripcion y Contenido
El producto tiene dos campos `productDescription` y `productContent`.

El primero sirve para una descripcion corta y en texto plano de max 1000 caracteres. Esta descripcion le sirve al frontend para mostrar un resumen de las capacidades del producto en varios lugares que no sean el product page (aunque ahi tambien aparecera).

El segundo es un texto largo enriquecido que puede contener html o markdown. Max 10,000 caracteres. Este texto SOLO aparecera dentro de la pagina de producto.

# Imagenes de Producto y Variaciones
Se ha disenado el esquema del producto para que el usuaro pueda definir imagenes en dos niveles: a nivel producto y a nivel variaciones.

Esto debido a que para los productos que sus variaciones no son por xej color (como una computadora), no tiene mucho sentido introducir imagenes para las variaciones (que son por CPU o Memoria y no cambia la apariencia del producto).

Para aquellos productos que si hay diferencias visibles entre sus variaciones, es que se puede registrar en cada variacion una lista de imagenes.

El producto puede tener max 5 imagenes y cada variante 3 imagenes, por ahora.

# Precios de Variacion y Precios especiales
Ver articulo separado

# CanView, CanSearch y CanSell
Ver wiki separado