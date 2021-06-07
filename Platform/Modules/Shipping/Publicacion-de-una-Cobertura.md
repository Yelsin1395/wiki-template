Las coberturas, como ya se describio, tienen muchos datos a registrarse entre zonas, poligonos, tarifarios, rutas, condiciones y tarifas.

Llegar a terminar de configurar una cobertura podria tomar horas o dias (dado que tiene que ver tambien con terminos contractuales con carriers, coordinacion entre varias areas, etc). Por lo que el diseno de la cobertura incluye el estado de publicacion como discriminador si es que checkout usa esa cobertura o no. 

El estado de publicacion puede tener tres valores: pending, in progress y published. Cualquier cambio de una cobertura devuelve el estado a pending y solamente checkout usara esa cobertura si esta en estado published.

De esta forma nos aseguramos que el usuario no incluya cambios parciales mientras va modificando la cobertura.

Yendo mas a fondo, para poder lograr esto, se necesita entonces tener dos estructuras de datos: la core llamada `ShippingCoverage` y la public llamada `ShippingCoverageRoute` (aunque creo que el nombre deberia cambiar).

ShippingCoverage te da el one source of truth que sirve para que el site owner pueda manipular su cobertura, y ShippingCoverageRoute es la data pre-procesada que permite obtener el precio de envio para una suborden de manera rapida y simple.

El proceso tiene los siguientes pasos:
1. El usuario crea una cobertura para un metodo de envio especifico.
2. Dentro de la cobertura crea las zonas, poligonos, rutas y tarifas.
3. Durante todo este momento, la cobertura esta en estado Pending y no es posible usarla en el checkout. 
4. Cuando el usuario esta listo, selecciona la opcion "Publicar Cobertura".
5. El sistema entonces marcara la cobertura como "In Progress". A partir de este momento, la cobertura queda congelada y no podra ser modificada.
6. Esto empieza el proceso de publicacion (mas detalle mas adelante) y empieza a generar los multiples registros de ShippingCoverageRoute (uno por cada condicion).
7. Cuando el proceso termina, el registro ShippingCoverage es marcado como "Published", se guarda las auditorias correspondientes y a partir de ahi, el checkout ya puede usar esta cobertura publicada.

## Proceso tecnico
TODO: a llenar la parte tecnica