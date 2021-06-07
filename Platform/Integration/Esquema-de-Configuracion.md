Para poder insertar la configuracion necesario en la plataforma, se necesitan crear algunas entidades:

## IntegrationSubscription

Este modelo es el basico que permitira a un Site o Store introducir un esquema integrado para sus operaciones en la plataforma.

Una estructura potencial en CosmosDb

```
IntegrationSubscription
-----------------------
siteId: string
id: string
subscriptionName: string,
ownerId (storeId or siteId)
ownerType (ST or SI)
pluginName (Store.Product.Price-Prestashop-InboundPull, etc)
pluginConfig (the plugin will export needed fields, and structure)
```

Dentro del plugin es donde todo se vuelve adhoc: autenticacion, mapping, etc. Al plugin hay que enviarle a la hora de ejecucion, un objeto context que le permita interactuar con lo que sea necesario.

Asimismo habran dos tablas en Cassandra para el tracking de la integracion:

```
IntegrationTrackingObject
-------------------------
[PK] siteId: string
[PK] objectId: string
[PK] createdOn: timeuuid
subscriptionId: string
action: string
objectName: string
createdBy: string
configUsed: string
request: string
response: string
humanResponseMessage: string
responseStatus: string
trackingId: string

IntegrationTrackingSubscription
-------------------------
[PK] siteId: string
[PK] createdOn: timeuuid
[PK] subscriptionId: string
action: string
objectId: string
objectName: string
createdBy: string
configUsed: string
request: string
response: string
humanResponseMessage: string
responseStatus: string
trackingId: string
```

Ambas tablas tienen la misma estructura de campos, pero cambian en su PK. De esta forma podemos responder la pregunta: que paso en este producto? y que paso este dia de la integracion?

Como se explico en la seccion de Plugin, este conformara a un esquema de input y un esquema de output, de esta manera podemos cargar estas tablas de manera uniforme.

# Repositorio de Plugins
Para que esto funcione tambien necesitamos tener un repositorio de plugins separado para que puedan ser registrados aqui y el motor de integracion solo necesite invocarlo, traer su programa y ejecutarlo