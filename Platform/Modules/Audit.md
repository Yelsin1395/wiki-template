En la nueva plataforma, tenemos la ventaja de un nuevo diseno y mejores piezas fundamentales.

Entonces gracias a que cada entidad ahora tienen un topic por cada uno donde se pueden notificar los cambios que se estan haciendo a las entidades y que todas generan la misma data, es que podemos generar data de auditoria muy facilmente.

Todos las entidades (o la mayoria) tiene un topic que siempre se envia un mensaje con cada cambio en el api-core.

xej para `Product` se tiene un topic llamado `product_upd`. Cada cambio en la entidad (creacion, modificacion, eliminacion, anadir una variante, anadir una categoria, etc) genera un evento a ese topic con este formato:

```
{
  body: {
    old: { <version original de la entidad antes de los cambios> },
    new: { <version nueva de la entidad despues de los cambios> }
  },
  userProperties: {
    event: 'create|update|delete|...',
    siteId: '<site que contiene a la entidad>',
    bearerToken: '<token usado para llamar al api, sirve para que dentro del suscriptor pueda hacer llamadas a otras apis>'
  }
}
```

Gracias a este evento generico que contiene toda la informacion necesaria, podemos crear el modulo de auditoria.

# Base de Datos

Lo primero que hay que saber es que para poder almacenar millones de registros y tener un buen performance de escritura, se escogio usar Azure CosmosDb Cassandra. 

*** *Esto es un enmascaramiento de ACDB usando Cassandra API. Osea parece un Cassandra pero "no lo es". Sin embargo, esta por confirmar. Sí necesitamos las ventajas de un Cassandra en este caso.*

Cassandra te da buen performance de escritura, y te restringe la lectura. Para este caso de uso es perfecto puesto que la auditoria son millones de registros pero que no necesitas leer a cada rato.

La lectura solo se puede dar por el PK y solo para registros contiguos. Entonces encaja muy bien dado que las busquedas de auditoria por lo general se dan por un objecto especifico o por un usuario especifico.

Los servidores son `juntoz-cass-srv-staging` y `juntoz-cass-srv-production`. El keyspace es `auditdb`.

# Tablas

Para esto se han creado dos tablas:
```
    create table auditdb.audit_object
    (
    site_id text,
    object_id text,
    created_on timeuuid,
    object_name text,
    old_data text,
    new_data text,
    diff text,
    user_id text,
    event text,
    primary key ((site_id, object_id), created_on)
    ) with clustering order by (created_on desc);

    create table auditdb.audit_user
    (
    site_id text,
    object_id text,
    created_on timeuuid,
    object_name text,
    old_data text,
    new_data text,
    diff text,
    user_id text,
    event text,
    primary key ((site_id, user_id), created_on)
    ) with clustering order by (created_on desc);
```

Cada una para responder las dos preguntas basicas: **quien hizo esto?** y **que hizo esta persona?**.
La parte importante de los objetos es el primary key.

En ambos casos tienes tres columnas como PK: site id, user / object y created_on. Teniendo created_on configurada como descendente.

Entonces Cassandra solo permite hacer queries por estas tres columnas (es suficiente para nosotros) y de manera contigua. Osea dado que el pk es site+user, entonces todos los registros se "agrupan" fisicamente, y el rango de fechas permite acotar el resultado a un rango especifico. Hasta ahora el modelo cumple con las restricciones de la BD.

|Campo|Descripcion|
|-|-|
|site_id|El site que contiene la data|
|object_id|El id del objeto auditado|
|user_id|El usuario que hizo la modificacion al objeto|
|created_on|La fecha y hora que se hizo el cambio|
|object_name|El nombre del objeto (product, brand, user, etc)|
|old_data|JSON.stringify de la data original del objeto. Cuando se creo el objeto es {}. Cuando se modifico es la data original. Cuando se borro es la version del objeto antes de borrarse.|
|new_data|JSON.stringify de la data nueva del objeto. Cuando se creo el objeto es la data completa. Cuando se modifico, es la data con los cambios. Cuando se borro es {}|
|diff|El diferencial de los old_data vs new_data. Mas adelante se explica el algoritmo.|
|event|El nombre del evento que desencadeno la modificacion (create, delete, update, variation_update, etc)|

# Codigo Fuente

Este modulo de auditoria esta en el repo `Juntoz.Api.Audit` y tiene dos partes:

1. core: el api core que permite LEER la auditoria
2. func: el contenedor de azure functions que se suscribiran al topic de cada entidad y generara la auditoria.

# Core
El api core de Audit tiene dos endpoints con el mismo formato de firma:
```
search/by/object/:id?startDate=&endDate=&more
search/by/user/:id?startDate=&endDate=&more
```

*** Como siempre todo esta limitado por el header X-JZ-SITE para enfocar todo lo hecho en el api para ese site.

id: el object id o el user id
startDate y endDate: fecha y hora en formato `YYYYMMDDTHHMMSSZ` (anho, mes, dia, hora, mes, segundos y timezone).
more: indica a la busqueda si debe traer los campos `old_data` y `new_data`.

Ademas, la busqueda se limita maximo a 50 registros, si se quiere paginar, simplemente vas avanzando en el startDate y endDate.

Las fechas y horas deben contener el timezone, sino, el servidor asumira hora local y dara resultados no esperados en las fechas.

# Diff
Para obtener el diff, se uso la libreria `diff` en npm: `npm i diff` y con el metodo `.diffJson`.

El resultado es un arreglo de objetos con el siguiente formato:
```
count: numero de lineas
add/remove/empty: si es que se agrego esa linea o se borro o esta intacto
value: valor del texto comparado
```

Esto es facilmente parseable y se puede mostrar facilmente en formato `diff`.

# Func
La insercion de la auditoria se hara a traves de Azure Functions escuchando a los topics de cada objeto auditable.

Hay que seguir los siguientes pasos:
1. Crear una subscription al topic de la entidad. Por ejemplo, para el topic `brand_upd`, hay que crear una subscripcion llamada `insert_audit`.
2. Luego en el proyecto `Juntoz.Api.Audit`, en `/func` hay que copiar y pegar una de las carpetas ya hechas (como `insert_audit_product`).
3. Esto resulta en una carpeta que podemos llamar `insert_audit_brand` y tendra dos archivos `function.json` y `index.js`.

![image.png](/.attachments/image-19268511-69e2-4042-9c3f-c1915fdcedb6.png)

4. El archivo `function.json` hay que modificarlo para que se escuche el topic y subscription correcto.

```
{
  "bindings": [
    {
      "name": "inMsg",
      "type": "serviceBusTrigger", <<< trigger de azure service bus
      "direction": "in",
      "topicName": "brand_upd", <<< topic
      "subscriptionName": "insert_audit", <<< subscription
      "connection": "inServiceBusEndpoint" <<< conexion al azure service bus
    }
  ]
}
```

5. El archivo `index.js` hay que modificarlo para que se identifique bien la funcion que esta corriendo.

```
const generateRunner = require('../common/runner');
module.exports = generateRunner('P1', 'P2');
```
* donde P1 debe ser el nombre de la entidad, en este caso seria `brand`.
* donde P2 debe ser el nombre de la funcion para identificarla en los logs, en este caso podria ser `insert-audit-brand`.

El metodo `generateRunner` es una funcion generica que ya encapsula todo lo necesario para grabar la auditoria. La idea es que sea muy facilmente generar la auditoria y que todos los objetos se graben igual. Este metodo ya sabe como leer el modelo de data estandar que genera los topics (`{ old, new }`).

La funcion insertara la data en ambas tablas `audit_object` y `audit_user`.

