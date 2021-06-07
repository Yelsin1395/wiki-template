Hoy juntoz.com permite Cash On Delivery o el llamado pago contra entrega. Es decir, el envio se hace, y cuando el paquete es recibido por el usuario, entonces ahi hace el pago sea en efectivo o con un POS de tarjeta de credito.

Este es un modo especial de pago (Payment Option), pero que sin embargo necesita de cambios a nivel de varios modulos a la vez y no solo eso, sino que el carrier debe poder proveer el servicio dado que hay un cargo adicional al servicio (para poder acopiar el dinero y manejarlo).

Por lo tanto, uno de los modulos afectados por COD es el modulo de Shipping por lo tanto, la nueva plataforma debe considerar este caso tambien.

Lo primero que se debe considerar es que si hay un costo que el carrier le carga al site, entonces esto se traslada hacia el precio de envio para que el usuario final lo asuma (quieres COD, tienes que pagar un poquito mas). Por lo general este cargo es entre 1-3 soles en Peru.

El COD, no solo es un servicio que el carrier te da, sino que tambien el carrier te dice en que zonas puede hacer COD. En zonas peligrosas, por ejemplo, el COD no es aceptado por el carrier. Por lo tanto, para definir si el usuario puede escoger COD, se debe definir a nivel de zona de la cobertura.

Esto se hace en el campo allowCashOnDelivery en el objeto Zone.
```
{
    zones: [
        {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {
                        "id": "a6dc71706cb411eb93c983e74b9d8b92",
                        "parentZoneId": "a6a052d06cb411eb93c983e74b9d8b92"
                    },
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [...]
                    }
                }
            ],
            "metadata": {
                "id": "a6a052d06cb411eb93c983e74b9d8b92",
                "zoneName": "ZonaA",
                "allowCashOnDelivery": false, <<<<
            }
        }
    ]
}
```

Luego a nivel de cobertura hay un campo adicional llamado cashOnDeliverySurcharge, que dice cuanto es el sobrecargo que el carrier cobra por ejecutar COD.

Considerar esto tambien, la cobertura definida por el site o el store es para el precio de venta. Por lo tanto, el cargo COD pagado por el usuario es porque el dueno del site o dueno de la flota tiene COD disponible para esa zona, sin importar, lo que el carrier cobra o no. Si hay varios carriers que cobran, entonces el site owner probablemente deberia usar el maximo o el promedio. 

Si es cero, es que no hay sobrecargo.

```
{
    "id": "cb4d489068ae11ebbcadc73e364d3a23",
    "siteId": "c432ea71b768fe06e568206fa8034862",
    "ownerType": "site",
    "ownerId": "c432ea71b768fe06e568206fa8034862",
    "shippingMethodId": "10",
    "shippingMethodName": "Express",
    "currencyCode": "PEN",
    "cashOnDeliverySurcharge": 0 <<<<
}
```

Hasta ahora se ha descrito como se define en `ShippingCoverage`, pero esto es necesario traducirlo hacia el esquema de `ShippingCoverageRoute` que es lo que usa checkout para el calculo de precio de envio.
En esa tabla entonces habra un campo adicional al `tariffValue`: `tariffValueCOD`.
```
tariffValueCOD = tariffValue + cashOnDeliverySurcharge
```

Este campo permite saber la tarifa de esa ruta incluyendo el sobrecargo de COD y al juntarlo con el flag `allowCashOnDelivery`, entonces podemos detemrinar si el COD esta habilitado y adicionalmente cuanto le costara al usuario final.

