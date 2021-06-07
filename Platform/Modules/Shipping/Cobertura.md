El objeto central del modulo de shipping es `ShippingCoverage`. Este objeto permite configurar la cobertura de envio del site o del store.

Esta cobertura contiene las zonas y poligonos que la cobertura cubre, las rutas posibles de una zona a otra junto con su tarifa y duracion en el tiempo.

Si hablamos de juntoz.com, el `ShippingCoverage` seria la union de las tres entidades: SRT, Zonas y Campanas de subsidio.

Como dijimos antes, el nuevo modelo ahora se hace desde el punto de vista de la venta, por lo tanto, lo que se registra son precios de envio. Por lo tanto, si antes para lanzar la campana "Todos los envios a 1 sol", tenia que tener configurada la SRT (para determinar la cobertura real) y ademas la Campana de Subsidio (para determinar que el precio de envio queda fijo como 1 sol), ahora con el nuevo modelo, solo se debe configurar en un solo lugar y solo con el precio final es posible hacerlo.

## Flotas
Lo primero que hay que explicar es que, asi como en juntoz.com, hay dos tipos de flotas que se pueden configurar en la nueva plataforma: Flota Site y Flota Store.

La **Flota Site** es aquella que el site marketplace owner otorga a sus merchants como un servicio propio para garantizar costos de envios competitivos (dado que se logra tener una masa critica de envios) y buenos carriers, etc.

La **Flota Store** es aquella que el propio merchant participante del marketplace utiliza para hacer los envios de sus subordenes. En esta categoria estan por lo general las flotas de tiendas que ya no son mypes, sino mas bien pymes como Bosch, Samsung, Efe, La Curacao, etc.

Para cubrir estos dos escenarios se utilizara el mismo objeto `ShippingCoverage` pero la diferencia es el campo `ownerType`. Para una cobertura definida por el site, `ownerType=site`. Para una cobertura de flota propia de la tienda `ownerType=store`. Y siendo el campo acompanante `ownerId` la llave para saber quien es el dueno de la cobertura.

Hay un tercer tipo `ownerType=carrier` que es para la definicion de la cobertura del carrier y esto se utilizaria principalmente para hacer la asignacion automatica de carriers a las ordenes.

**Donde se escoge que cobertura usar?**
Es a nivel de tienda que se define que flota se usara PARA LA VENTA. Es decir, quien sera responsable del envio del almacen al usuario final: el site o la tienda misma.

## Cobertura por Metodo de Envio
Cada cobertura se le asigna un metodo de envio. No puede haber dos coberturas para el mismo metodo de envio y owner.

Es necesario tener distintas coberturas por metodo de envio dado que para p.e. Express, la cobertura siempre sera mas reducida y cercana al almacen, vs Regular que podria ser a otra ciudad sin problemas.

Adicionalmente, si un site tiene tres coberturas: Express, SameDay, Regular, la idea es que a nivel de checkout, si la tienda escogio Flota Site, entonces el usuario podra escoger de estos tres metodos de envio (con distinto precio y duracion, obviamente).

Si es que a nivel de Flota Store tambien se registran distintas coberturas, el efecto sera el mismo. Eso si, es responsabilidad del store que cada metodo de envio se mantenga en optimas condiciones.

## Zonas y Poligonos
Dentro de una cobertura, se puede definir un conjunto de zonas y dentro de cada zona, un conjunto de poligonos que determinaran la cobertura fisica que se esta cubriendo. Si algun punto de longitud y latitud cae dentro de un poligono de alguna zona, es que esa cobertura es valida para ese punto y debe evaluarse (en realidad, se necesita tambien que la ruta del punto de inicio al punto final exista dentro de esa cobertura, pero al 99% que si lo tendra).

Para definir las zonas y poligonos, usaremos el estandar `GeoJson` que define los objetos que tipicamente se utilizaria en un sistema GIS.

Cada zona es lo que se llama un `FeatureCollection` y cada poligono se le define como un `Feature`.

Ejemplo de una cobertura con zonas y poligonos:
```
{
    zones: [
        {
            type: FeatureCollection,
            metadata: {
                id: 'z1',
                zoneName: 'zone 1',
                createdBy: 'xx',
                createdOn: 'utc'
            },
            features: [
                {
                    type: Feature,
                    properties: {
                        id: 'p1'
                    },
                    geometry: {
                        type: Polygon,
                        coordinates: [
                            [1,1],
                            [2,1],
                            [2,2],
                            [1,2],
                            [1,1]
                        ]
                    }
                }
            ]
        }
    ]
}
```

Como se puede ver en el ejemplo, ya la misma zona esta definida como GeoJson y los atributos tipicos del objeto que estarian en el nivel raiz, se tienen que insertar en un subobjeto.
A nivel de `FeatureCollection`, no existe el subobjeto `properties`, por lo tanto, creamos un subobjeto llamada `metadata`, de esta manera mantenemos el mismo "patron" de uso (claro se pudo poner `properties` tambien pero para evitar pensar que existe en el estandar, es que se decidio optar por el nombre actual). Desde el nivel `Feature` hacia abajo, GeoJson si define el subobjeto `properties`.

Usando GeoJson por ejemplo, nos permitio utilizar herramientas online como `geoman` y poder interactuar facilmente con el Google Maps JavaScript API.

**Por que no solo usar el modelo una zona == un poligono?** Esto es para permitir que el usuario no tenga que definir muchas zonas cuando se quiere, por ejemplo, cubrir un distrito por partes. La zona se puede llamar La Victoria y tener poligonos separados de zonas que si se puede cubrir y otras que no.

**Se podria crear un solo poligono y que cuidadosamente cubra las areas permitidas y disenarlo de manera continua y sin cortes?** Si, seria lo mejor de hecho. Pero queriamos dar esa flexibilidad al usuario.

**Por que no se uso `type=MultiPolygon`?** Debido a que segun la documentacion de CosmosDb (que es donde se guardan los poligonos y nos da comandos para su uso facil), menciona que un MultiPolygon NO PUEDE TENER OVERLAPPINGS. Esto aunque es posible de lograr, segun nuestra experiencia, ES MUY DIFICIL y muy propenso a errores. Por lo tanto por eso se decidio permitir varios objetos Polygon y que si hay overlappings, no importa dado que igual pertenecen a la misma zona.

Algunas otras anotaciones:
- Una zona debe tener siempre al menos un poligono
- El nombre de la zona no se puede repetir
- En el mapa en SiteCentral, todos los poligonos de una misma zona tienen el mismo color.

## Tarifas
Una vez que las zonas estan registradas, es que se puede dar paso a crear las tarifas de la cobertura. Las tarifas estaran siempre enmarcados en un tarifario y en una ruta.

### Tarifarios 1 y 2
En la plataforma anterior, las campanas de subsidio permitian cambiar temporalmente las tarifas de envio (por ende el nombre campaña), sin embargo, el mecanismo de temporizador es bastante pesado para la plataforma, dado que "a cada segundo" podia cambiar una tarifa y el sistema tenia que estar preparado para esto (caches coordinados y cortos, verificaciones de existencia de tarifas, etc).

Para esta nueva plataforma por lo tanto hemos decidido recortar este modelo y permitir que el administrador del site pueda registrar dos tarifarios en una misma cobertura y que pueda seleccionar cual esta activo en ese momento.

De esta manera, si hay una campana de subsidio temporal, se puede tener el tarifario 1 como principal y el tarifario 2 para la campana, y en el momento que la campana debe activarse, entonces el usuario lo selecciona y el sistema empezara a usarlo. Cuando es necesario apagar y regresar al tarifario 1, se cambia la seleccion y con eso ya se cerraria el ciclo.

### Precios de Envio
Entonces dentro de un tarifario (sea el 1 o 2, ambos tienen la misma estructura), lo primero que se define son rutas.

Una ruta tiene los siguientes campos:
- Zona Desde
- Zona Hasta
- Duracion (Horas calendario)

Y dentro de una ruta es que se ingresan las condicionales para llegar al precio de envio.

Una condicion de precio de envio tiene los siguientes campos:
- Tallas
- Monto Minimo de SubOrder
- Monto Maximo de SubOrder
- Precio de Envio

Las condiciones permiten generar precios de envio diferenciados.

Por ejemplo, para hacer una campana de Free Shipping para compras de mas de 99 soles se configuraria asi:
```
conditions = [
    {
      inPackageSize: [] // any package
      subTotalFrom: 0,
      subTotalTo: 98.99,
      tariffValue: 5 // this is the regular shipping price
    },
    {
      inPackageSize: [XS, S, M] // medium packages
      subTotalFrom: 99,
      subTotalTo: 999999,
      tariffValue: 0 // this is the free shipping campaign
    }
]
```

Los parametros de una condicion se unen usando el operador AND. Es decir, en el ejemplo arriba seria

1. Si el (subtotal empieza en cero) y (subtotal es hasta 98.99), entonces la tarifa es 5 soles.
2. Si (el tamano del paquete es XS o S o M) y (subtotal empieza en 99) y (subtotal es hasta 999999), entonces la tarifa es 0 soles.

## Ejemplo de Cobertura
```
{
    "id": "cb4d489068ae11ebbcadc73e364d3a23",
    "siteId": "c432ea71b768fe06e568206fa8034862",
    "ownerType": "site",
    "ownerId": "c432ea71b768fe06e568206fa8034862",
    "shippingMethodId": "10",
    "shippingMethodName": "Express",
    "currencyCode": "PEN",
    "currentTariff": 2,
    "createdBy": "7daa0050-f0ad-45c7-8e67-73ad297ce530",
    "createdOn": "2021-02-06T19:09:11.000Z",
    "shippingCoverageStatus": true,
    "lastPublishVersion": "454444",
    "zones": [
        {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {
                        "id": "a6dc71706cb411eb93c983e74b9d8b92",
                        "parentZoneId": "a6a052d06cb411eb93c983e74b9d8b92",
                        "fillColorRgb": "#e91e63",
                        "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                        "createdOn": "2021-02-11T22:01:11.000Z",
                        "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                        "updatedOn": "2021-02-11T22:19:59.000Z"
                    },
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [
                            [
                                [
                                    -76.67912027697756,
                                    -11.87460972567545
                                ],
                                ...
                                [
                                    -76.67912027697756,
                                    -11.87460972567545
                                ]
                            ]
                        ]
                    }
                }
            ],
            "metadata": {
                "id": "a6a052d06cb411eb93c983e74b9d8b92",
                "zoneName": "ZonaA",
                "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                "createdOn": "2021-02-11T22:01:11.000Z",
                "allowCashOnDelivery": false,
                "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                "updatedOn": "2021-02-11T22:19:59.000Z"
            }
        },
        {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {
                        "id": "303852906cb511eb93c983e74b9d8b92",
                        "parentZoneId": "2fe80fb06cb511eb93c983e74b9d8b92",
                        "fillColorRgb": "#4caf50",
                        "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                        "createdOn": "2021-02-11T22:05:01.000Z",
                        "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                        "updatedOn": "2021-02-11T22:13:03.000Z"
                    },
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [
                            [
                                [
                                    -76.42497560839846,
                                    -12.167384325130312
                                ],
                                ...
                                [
                                    -76.42497560839846,
                                    -12.167384325130312
                                ]
                            ]
                        ]
                    }
                }
            ],
            "metadata": {
                "id": "2fe80fb06cb511eb93c983e74b9d8b92",
                "zoneName": "Zona2",
                "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                "createdOn": "2021-02-11T22:05:01.000Z",
                "allowCashOnDelivery": false,
                "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                "updatedOn": "2021-02-11T22:13:03.000Z"
            }
        },
        {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {
                        "id": "552f1a106cb611eb93c983e74b9d8b92",
                        "parentZoneId": "54ea97006cb611eb93c983e74b9d8b92",
                        "fillColorRgb": "#8bc34a",
                        "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                        "createdOn": "2021-02-11T22:13:13.000Z",
                        "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                        "updatedOn": "2021-02-11T22:17:39.000Z"
                    },
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [
                            [
                                [
                                    -77.00466481194815,
                                    -12.03257999671604
                                ],
                                ...
                                [
                                    -77.00466481194815,
                                    -12.03257999671604
                                ]
                            ]
                        ]
                    }
                }
            ],
            "metadata": {
                "id": "54ea97006cb611eb93c983e74b9d8b92",
                "zoneName": "Zona3",
                "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                "createdOn": "2021-02-11T22:13:12.000Z",
                "allowCashOnDelivery": false,
                "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                "updatedOn": "2021-02-11T22:17:39.000Z"
            }
        },
        {
            "type": "FeatureCollection",
            "features": [
                {
                    "type": "Feature",
                    "properties": {
                        "id": "4bb85a406cb711eb93c983e74b9d8b92",
                        "parentZoneId": "4b7a18c06cb711eb93c983e74b9d8b92",
                        "fillColorRgb": "#03a9f4",
                        "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                        "createdOn": "2021-02-11T22:20:07.000Z",
                        "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                        "updatedOn": "2021-02-11T22:20:36.000Z"
                    },
                    "geometry": {
                        "type": "Polygon",
                        "coordinates": [
                            [
                                [
                                    -76.86847841601562,
                                    -12.156522372638808
                                ],
                                ...
                                [
                                    -76.86847841601562,
                                    -12.156522372638808
                                ]
                            ]
                        ]
                    }
                }
            ],
            "metadata": {
                "id": "4b7a18c06cb711eb93c983e74b9d8b92",
                "zoneName": "Zona4",
                "createdBy": "cf37b0d0251011eba08a91766b9d7284",
                "createdOn": "2021-02-11T22:20:06.000Z",
                "allowCashOnDelivery": false,
                "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
                "updatedOn": "2021-02-11T22:20:36.000Z"
            }
        }
    ],
    "tariff1": {
        "tariffName": "tariff",
        "routes": [
            {
                "id": "222",
                "zoneIdFrom": "a6a052d06cb411eb93c983e74b9d8b92",
                "zoneIdTo": "a6a052d06cb411eb93c983e74b9d8b92",
                "hoursToDeliver": 5,
                "conditions": [
                    {
                        "id": "11",
                        "inPackageSize": [
                            "L"
                        ],
                        "subTotalFrom": "0",
                        "subTotalTo": "100",
                        "tariffValue": "45"
                    },
                    {
                        "id": "11",
                        "inPackageSize": [
                            "XS"
                        ],
                        "subTotalFrom": "0",
                        "subTotalTo": "100",
                        "tariffValue": "30"
                    }
                ]
            }
        ]
    },
    "tariff2": {
        "routes": [
            {
                "id": "333",
                "zoneIdFrom": "a6a052d06cb411eb93c983e74b9d8b92",
                "zoneIdTo": "4b7a18c06cb711eb93c983e74b9d8b92",
                "hoursToDeliver": 10,
                "conditions": [
                    {
                        "id": "11",
                        "inPackageSize": [
                            "XS",
                            "L"
                        ],
                        "subTotalFrom": "0",
                        "subTotalTo": "100",
                        "tariffValue": "45"
                    }
                ]
            }
        ],
        "tariffName": "tariff"
    },
    "cashOnDeliverySurcharge": 0,
    "publishStatus": "published",
    "isDeleted": false,
    "modifiedBy": "cf37b0d0251011eba08a91766b9d7284",
    "updatedOn": "2021-02-11T22:20:36.000Z",
    "lastPublishedOn": "2021-02-17T22:46:14.000Z",
    "lastPublishedBy": "cf37b0d0251011eba08a91766b9d7284",
    "lastPublishedCurrentTariff": 2,
    "_ts": 1613601974
}
```