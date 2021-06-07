Para algunas entidades se tienen los siguientes campos:

- CanSell: puede vender
- CanView: puede verlo
- CanSearch: puede buscarlo

En todos funcionan igual cuando esten presentes.

# CanView
Esto aplica para Store y Product.

Ambos tienen pantallas ancla y por lo tanto, este flag controla si se puede ver o no.
Valores: True/False

# CanSearch
Esto aplica para Store, Product, Category y Brand.

Permite controlar si se puede buscar aquella entidad. Esto es muy importante debido a que debemos permitir que el usuario pueda crear esas entidades usarlas bajo ciertas restricciones o "bajo el radar" y luego cuando este listo, permitir el uso general.

Tambien puede servir para tener un producto que solo se pueda comprar por acceso directo a la pagina y no a traves de la busqueda. O cuando este en falso y el producto este como CanView true, el producto pueda verse un preview del mismo mientras el merchant lo va creando y ajustando a como lo desea.

Es importante mencionar que las variaciones no son "buscables", solo los productos.
Valores: True/False

# CanSell
Esto aplica para Store, Product y Variacion.

Esto es un campo muy importante que determina si ese producto se puede comprar o no.

Cuando es YES, se puede comprar.
Cuando es NO, no se puede comprar.
Cuando es FUTURE, no se puede comprar POR AHORA.

Los dos ultimos son un NO, pero separandolos permite poder decir "Proximamente" si la UI lo decide.

Entonces para poder vender una variacion,
```
Store.CanSell == yes && Product.CanSell == yes && Product.Variation.CanSell == yes
```
Las tres condiciones se tienen que dar en verdadero.
Adicionalmente para vender hay varias otras validaciones (como el Stock), asi que en este caso solo hablamos de los CanSell, pero no es la unica variable.

Valores: Yes/No/Future