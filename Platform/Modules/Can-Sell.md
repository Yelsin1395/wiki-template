Para poder comprar una variante de producto hay que validar las siguientes valores:

1. La tienda CanSell = yes
2. El producto CanSell = yes
3. La variante CanSell = yes
4. El stock de la variante > 0

```
TotalCanSell = 
  store.canSell == yes && 
  product.canSell == yes &&
  product.variation.canSell == yes &&
  stock.variation.stock > 0
```

Esto se tiene que validar en varios puntos:

1. En product page, si TotalCanSell == false, el boton Add To Cart no debe aparecer (ni renderizar). MUST HAVE
2. En Cart API, endpoint AddToCart NEED TO HAVE
3. Al mostrar el Cart (opcional) NICE TO HAVE
4. Cuando va de Cart a Checkout MUST HAVE
5. Dentro de checkout, al entrar a checkout (opcional) NEED TO HAVE
6. Dentro de checkout, al finalizar checkout MUST HAVE
7. En Sales API, endpoint CreateOrder (opcional) NEED TO HAVE

