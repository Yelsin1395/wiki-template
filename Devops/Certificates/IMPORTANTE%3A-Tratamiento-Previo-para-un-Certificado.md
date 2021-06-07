Segun hemos visto con marketplacelifemiles.com, todos los certificados deben ser apropiadamente generados para que no solo el browser, sino tambien las validaciones de facebook o google pasen sin problemas.

Entonces lo que habia ocurrido era lo siguiente.

## DV
Si compro un certificado DV por namecheap.com, recibimos:
- crt
- key
- ca-bundle

En la practica, si uso el crt y el key directamente, la web funciona bien. Sin embargo, los validadores de facebook parece que no.

Lo que hay que hacer entonces primero es CONCATENAR crt y ca-bundle.

```
type crt ca-bundle>final.crt

## Esto es en Windows, Linux creo que funcionaria con un `cat`.
```

Y este `final.crt` es lo que se debe usar como certificado final junto con el key para ser instalado en un servidor e.g. nginx.

## OV
Si obtengo un certificado OV (como Lifemiles), recibimos:
- crt
- key
- intermediate
- root

Lo que hay que hacer aqui es CONCATENAR crt y intermediate (el root lo ignoramos), usando el mismo comando `type`.

NOTA: en el caso del certificado que nos dio LifeMiles con marketplacelifemiles.com, si incluiamos el root, no funcionaba. No se si esto es una regla en los OV o en este caso solamente, pero toma nota de esto y prueba ambas formas: CRT+INT o CRT+INT+ROOT.

