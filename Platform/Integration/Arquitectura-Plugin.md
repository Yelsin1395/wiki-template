# Introduccion
Para que estas integraciones puedan ser bien mantenidas, es necesario tener una arquitectura de plugins que encapsule la logica necesario para la integracion y de esa manera el mantenimiento del codigo sea mucho mas sencillo y escalable.

Tipicamente, los sistemas mas avanzados permiten generar plugins para distintas fases o procesos de negocio para que de esa manera, el codigo core sea mantenible y no se necesite mas costo de mantenimiento del necesario.

Un plugin, como lo dice su nombre, es plug-and-play, es decir, un usuario administrador, en el plano de la configuracion, inserta el plugin que esta ya empaquetado anteriormente y en el plano de la ejecucion solo se le invoca con los parametros necesarios.

Se espera que cada integracion sea con un carrier o un sistema ecommerce o un ERP sea creado en formato plugin. Y solo seria necesario configurarlo.

Asimismo, tambien es esperable que un plugin necesite de otro para poder funcionar optimamente y porque no, mejorar aun mas la experiencia del usuario.

Por ejemplo, si quisieramos hacer una integracion Juntoz-Prestashop, se tendrian que instalar dos plugins: uno dentro de la plataforma de Juntoz y otro dentro del sistema Prestashop para que la comunicacion fluya mucho mejor y poder crear flujos de negocio mejorados.

De todo esto tambien es posible obtener un revenue stream por lo que encaja perfectamente en el modelo de negocio tecnologico que queremos lograr en Juntoz.

# @juntoz/integration-plugin-base
Lo primero que creamos fue un package base para todos los plugins. 

Este paquete se llama `@juntoz/integration-plugin-base` y esta contenido en el repo `Juntoz.Integration.Plugin` dentro de Azure Devops. Sin embargo, este es un candidato claro para ponerlo en github como open source y ademas publicarlo en npm (actualmente esta en el npm privado de juntoz).

Este package fue creado en Typescript considerando que esa es la recomendacion para crear los plugins.

El contenido del paquete esta conformado por basicamente interfaces base para poder crear los cuatro tipos de plugins: Inbound Receive, Inbound Push, Outbound Get y Outbound Push; y la definicion del runtime context que todos los plugins recibiran para su ejecucion.

El `plugin-base` es el paquete comun a todas las capas: para crear plugins y para ejecutar los plugins.

## Algunas interfaces y clases a mencionar
Estas son las interfaces mas importantes que definen el plugin. 

En la primera seccion, una por cada tipo de integracion y todas heredan de la interface `Plugin` que contiene los campos basicos de metadata del plugin.

En la segunda seccion, las interfaces de respuesta usadas dependiendo del objetivo de cada tipo de integracion.
```
Plugin
|--PluginInboundReceive
|--PluginInboundPull
|--PluginOutboundPush
|--PluginOutboundGet

PluginResponse
|--PluginResponseToJuntoz
|--PluginResponseToTarget
```

Estos son los guards de Typescript que se han definido para verificar si los objetos que se estan enviando como plugins, sea uno como tal y de un tipo especifico.
```
IsPlugin
IsPluginInboundPull
IsPluginInboundReceive
IsPluginOutboundGet
IsPluginOutboundPush
```
Otras clases e interfaces que vale la pena mencionar:
- RuntimeContext: la clase que el runtime creara y el plugin luego recibira para su ejecucion.
- Type EntityType: `stock|price|product|order`
- Type PluginDirection: `inbound|outbound`
- Type PluginAction: `receive|push|pull|get`

En otro wiki describiremos los pasos para crear un nuevo plugin y publicarlo.

# @juntoz/integration-plugin-directory
Este segundo paquete fue creado para poder encapsular la ubicacion de los plugins y poder utilizarlos en el runtime.

Al 2020-09, el metodo para la busqueda de plugins dentro del directorio solo incluye la clase `ModuleFinder` que ubicara los paquetes instalados dentro de la aplicacion que se esta ejecutando.

Por ejemplo, si estamos en el componente `InboundReceiverProcessor` (quien ejecuta la conversion de un objeto externo hacia un objeto juntoz y la llamada al `api-core`), es aqui donde primero tendriamos que instalar todos los plugins a los que podria tener acceso. El `directory` lo que hara sera ubicar el plugin dentro de estos y lo devolvera para su uso.

Todos los plugins seran registrados con un full-name y una version.

## Prerequisitos para el ModuleFinder
Para que el plugin pueda ser cargado por la clase `ModuleFinder`, es necesario que el `package.json`/`name` del plugin sea `@juntoz/plugin-{plugin-full-name}`, donde `plugin-full-name` es el nombre con el que el plugin se ha creado (y que debe devolver en la funcion `pluginFullName()`) y con el que sera registrado en la subscripcion (muy importante).

El pluginFullName() tipicamente devolvera el siguiente formato: `${entityName}-${pluginDirection}${pluginAction}-${pluginTarget}-${pluginAuthor}`.

Por ejemplo, para un plugin de inbound receive para obtener el stock de SIAL, el plugin debera estar creado asi:

```
package.json/name: @juntoz/plugin-stock-inbound-receive-sial-juntoz
plugin full name: stock-inbound-receive-sial-juntoz
```

## Otros Finders
En el futuro se espera incluir un `MarketplaceFinder` o un `RemoteFinder` para que los plugins sean registrados en una ubicacion externa y asi los componentes del motor de integracion no necesitaran instalarlos y sera realmente dinamico.

# @juntoz/integration-plugin-runtime
El paquete que finalmente sera usado para ejecutar la integracion se llama @juntoz/integration-plugin-runtime y utiliza los otros dos paquetes.

El runtime sera usado para ejecutar cualquiera de los cuatro tipos de plugin.

El flujo de trabajo que sigue es asi:
1. Los datos de entrada del runtime son: la informacion de la subscripcion, el objeto de entrada (que puede ser externo o de juntoz) y opcionalmente un tracking id que deberia servir para poder hacer seguimiento a traves de todos los componentes del motor de integracion. Si es que no es enviado, el runtime generara uno. 

2. Dentro de la subscripcion hay dos campos pluginFullName y pluginVersion. Con estos dos valores, el runtime utiliza el objeto `Directory` y trata de buscar ese plugin. Como dije, por defecto sera el `ModuleFinder`, pero podria ser otro finder en el futuro. La busqueda se hace de manera exacta con la version.

3. Si el plugin es encontrado, y es del tipo que se busca (InboundReceive, InboundPull, etc), la instancia del plugin es retornado al runtime y este lo ejecuta. Si el plugin no es encontrado o no es del tipo, un error se generar.

4. Para ejecutar el plugin, se le envian dos parametros: un RuntimeContext y el objeto de entrada (del paso 1). El RuntimeContext lo describimos mas adelante.

5. El plugin debe retornar un objeto con los siguientes campos: `status`, `result` y `errors`. El status debe ser `success` o `error`. El result es el objeto resultante del plugin (osea por ejemplo convertir del formato Juntoz a un formato externo). Deberia ser null si es que hay errores. Y por ultimo, se tiene un array de `Error` que el plugin puede haber generado por dentro.

6. El runtime nunca devolvera una excepcion lanzada, tiene un `try catch` global que captura todos los errores. Siempre devolvera el objeto de respuesta descrito.

## RuntimeContext
Para que los plugins puedan ser ejecutados de manera independiente y de manera que el motor de integracion no necesite saber el resultado de los mismos, es necesario que el contexto tenga informacion suficiente o tenga las herramientas necesarias para que el plugin no necesite hacerlo por si mismo. Este es el objetivo de la clase RuntimeContext.

El RuntimeContext tiene la siguiente estructura:
```
{
  subscription: {
    subscriptionId: string;
    subscriptionName: string;
    siteId: string;
    ownerId: string;
    ownerName: string;
    ownerType: string;

    clientId?: string;
    clientSecret?: string;

    pluginFullName: string;
    pluginVersion: string;
    pluginConfig: [name: string]: string;
  },
  trackingId: string,
  logger: Logger {
    debug(msg: string): void;
    info(msg: string): void;
    error(msg: string, error?: Error): void;
  }
}
```

- subscription: contiene todos los campos necesarios de la subscripcion. De aqui los mas importantes son el `pluginFullName`, `pluginVersion` y `pluginConfig`. Los campos `pluginFullName` y `pluginVersion` se utilizaran para cargar el plugin del directorio. Y el campo `pluginConfig` son los valores de configuracion que el plugin requiere y que seran registrados dentro de la subscripcion. Es un diccionario key:value de strings.
- trackingId: el tracking para hacer seguimiento.
- logger: Es una interface de logger para que el plugin pueda emitir mensajes facilmente. Por cada mensaje que el plugin emita, el runtime automaticamente insertara el prefijo: ownerName-subscriptionId-trackingId. Asi el plugin no necesita emitirlo y todos lo hacen de manera estandar.









