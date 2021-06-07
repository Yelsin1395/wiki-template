Cada producto se le puede asignar una o varias categorias.

Las categorias de productos sirven para clasificar los productos por tipos (taxonomia) y asi dar un catalogo ordenado al usuario en base a lo que esta buscando.

En el legacy, las categorias fueron disenadas siguiendo el patron clasico de SQL para objetos jerarquicos (parentid por cada categoria). Sin embargo, este modelo funciona solo para bases de datos pequenas.

Cuando tenemos bases de datos muy grandes con millones de registros, esta estructura basica no ayuda y impone todo el peso de procesamiento en la consulta. La mejor practica aqui es que el procesamiento se haga en la escritura.

Por lo tanto las categorias siempre tendran registrados todo el arbol jerarquico hacia arriba para poder obtener rapidamente todo el arbol.

```
{
    categoryId
    categoryCode
    categoryName
    categorySeoUrl
    categoryFullPath
    level
    categoryParents: [ // en orden ascendente por level
        {
            categoryId
            categoryCode
            categoryName
            categorySeoUrl
            categoryFullPath
            level
        }
    ]
}
```
Esto permite que cuando un producto se le asigne una categoria, tambien se pueda obtener todo el full path y todos los seourls asociados y asi generar los breadcrumbs rapidamente.

## Algunos terminos
- Nodo = Cada pieza del arbol.
- Raiz = aquellos nodos del arbol que estan en el primer nivel y no tienen un padre asociado.
- Rama = Raiz/Padre/Hijo/Nieto.
- Hoja = el ultimo nivel de cada rama. En el ejemplo, seria Nieto.

# Reglas
Para garantizar que el arbol este "sano" siempre y no hayan problemas ni de lectura ni escritura, las siguientes reglas se estan aplicando:

1. Las categorias siempre se agregan al final de una rama. No se puede agregar una categoria en medio de una rama.
2. Solo se puede eliminar las hojas. Si necesitan eliminar una rama completa, se debe eliminar de abajo hacia arriba (del nivel mas profundo al menos profundo).
3. Para eliminar una categoria, se debe primero **deslinkear** todos los productos asociados.

# La gran desventaja
Con este modelo la gran desventaja es cuando una categoria se le debe renombrar. Cuando esto se necesite, se tendra que ejecutar una mega-actualizacion de categorias hijas y todos sus productos asociados.

Esto hasta de repente no se deberia poder. Asi que por ahora esta bloqueado el cambio de nombre.
