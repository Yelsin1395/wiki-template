CosmosDb es una base de datos NOSQL en formato PAAS. El formato de guardado de datos es json (como MongoDb), pero tambien soporta otros formatos (Graph, Cassandra, etc).

Como una bd NOSQL hay que seguir distintos principios que con un SQL:

- Esta bien redundar en data cuando esta cambia poco o nunca (por ejemplo redundar el storeName en el producto o el brandName). La redundancia ayuda a la alta escalabilidad.
- No debemos hacer queries o busquedas complejos. Si un objeto necesita una busqueda completa, entonces debemos hacer el esfuerzo de crear un indice separado para su busqueda (como Algolia o Elastic).
- TODOS los objetos en CosmosDb deben tener el partitionKey /siteId. Excepto la data de los Site (que tienen un partition fijo llamado `juntoz`).
- NO hacer stored procedures ni funciones ni triggers. Esto "oculta" la logica de negocio y es muy malo para el mantenimiento. Esta logica debe hacerse en el api.

# Como crear un CosmosDb database
- Al crear la bd en CosmosDb, hay que estandarizar el tema del scaling y RUs. Hasta ahora no hay un estandar, pero probablemente deberiamos irnos por el caso en donde:

![image.png](/.attachments/image-c8540edb-ae79-4580-b194-25782fd4f7f2.png)

- Provision database throughput CHECKED: esto hara que los RUs sean compartidos por todos los containers. Como si le pusieramos un CPU y MEM asignado a una BD de SQL (lo "tipico"). Cuando no esta checked, lo RUs se asignan al container (caso antiguo), esto puede resultar en que sea mas caro el costo pero puede tener mejor performance.
- Usar Throughput 400 y Manual al inicio. Throughput Autoscale es bueno xq no nos preocupamos de esto. PERO, es mas caro. Hay que usarlo con cuidado. Por lo general la mayoria de containers no tendran picos de uso, salvo aquellos que se usen directamente en la web o se tengan muchos objetos registrados.
- PartitionKey: debemos asignar **/siteId** como estandar.

**Luego de crear el container, NO OLVIDAR DE ASIGNAR EL INDEXING. Esto es MUY IMPORTANTE.**

## Indexing
El indexing es clave para un buen performance. Cuando se deja por defecto, el indice es por todos los campos y aunque es tentativo hacerlo para que se "pueda buscar por cualquier campo" esto no es bueno al final.

Esto nace asi:
```
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        }
    ]
}
```

Hay que cambiarlo asi:
```
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/\"productName\"/?"
        }
    ],
    "excludedPaths": [
        {
            "path": "/\"_etag\"/?"
        },
        {
            "path": "/*"
        }
    ]
}
```

Donde cada `includedPath` es un indice separado. Si deseas indices compuestos es otra sintaxis (ver documentacion). Y el path `/*` (que es el "indice por todos los campos") hay que pasarlo a los `excludedPaths`.
