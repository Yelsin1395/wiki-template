Para recargar los banners de KidsMadeHere (TheBox y BigHead).

Ir a azure, al servicio juntoz-rds-srv-production.

Ir a la seccion Overview/Console (en barra superior).

Meter el comando `get CMSCACHESERVICE.IMAGECOLLECTION.1249.WL-HOME-BANNERS` (donde 1249 es KidsMadeHere, y notar que Redis es case sensitive). Este comando deberia darte la data.

Meter el comando `del CMSCACHESERVICE.IMAGECOLLECTION.1249.WL-HOME-BANNERS` para borrarlo.

Con esto la aplicacion ya ira a BD y sacara la nueva lista.

Este mismo procedimiento es para cualquier recarga de una entrada de REDIS.

Los whitelabels hasta ahora son
```
1034 The Box
1050 Big Head
1249 Kidsmadehere
2277 Billabong
2369 Columbia
2285 Hush Puppies
2355 Samsung EPP
```