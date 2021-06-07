Todo Data Mart sigue normalmente tres fases: ETL = Extract, Transform, Load.

Sin embargo, esto hace que haya una demora (que tipicamente es 1 hora, 1 dia, etc) entre que el evento ocurre (una venta se hizo, se registro un usuario, etc) y la data esta disponible para hacer reportes y graficos.

Un DM moderno ahora sigue por lo general solo el modelo EL = Extract and Load. Esto gracias a que ahora las herramientas han evolucionado y el poder de procesamiento tambien.

Una de los grandes cambios actuales es el uso de bases de datos especializadas llamadas Time Series Databases. Las TSD son bases de datos optimizadas para la ingesta de eventos puntuales (marcados todos con un timestamp) y su uso en reportes, tendencias, etc. Tienen la particulariedad que permiten millones de inserciones rapidamente, y que su explotacion para reportes es inmediata.

Se usa mucho en ambientes IOT en donde conocer el estado del aparato a cada segundo es vital para poder saber el estado del mismo y que el usuario pueda tener informacion puntual para tomar decisiones (o que el mismo aparato pueda reaccionar y adecuarse a la situacion ej: auto-reiniciarse, o apagarse para evitar danos).

En nuestro caso, usaremos TimeScaleDb que es una base de datos TS en modo PAAS. La ventaja de usar TimeScaleDb es que es una capa de servicios montada sobre una base de datos PostgreSql, lo que permite su familiaridad y ademas potencialmente usarla en modo mixto (OLTP y OLAP) para distintas preguntas y respuestas que necesitemos para el DM.

Dado que usaremos una TSD, tenemos la ventaja que el proceso para llegar a tener los reportes es mucho menos complejo que si hubiera sido un DM clasico. Ademas la arquitectura de la plataforma nueva que se ha creado, sigue un modelo desacoplado y por eventos, por lo tanto es facil conectar distintos modulos a traves de topics/subscriptions sin necesidad de conocer mucho sobre el modelo fuente y garantizando que ambas partes correran independientes y resistentes.

## Ingesta de Entidades
El caso tipico por ahora sera que la ingesta se hara a traves de los topics que todos los modulos exponen. De esta manera, el data mart NO se acoplara con ningun modulo, y ademas que tendremos el historial de eventos que ocurren sobre un objeto y asi poder determinar si usar toda la historia o solo algunos eventos que deben procesarse en el datamart.

Se creo un azure function app llamado `jz-stg-af-datamart-ingest`. En este function app es donde se insertaran todas las funciones de ingesta de data cada una con su propia subscripcion.

### Orders
La tipica pregunta que se le hace a un data mart es respecto a las ventas de una tienda. Por lo tanto es el primer caso que ha sido implementado.

Para lograrlo se hicieron los siguientes pasos:
1. Se creo una funcion (dentro del function app mencionado) que escuchara al topic `order_upd` con una subscripcion propia.
2. Al recibir un mensaje del topic, se analizara cual evento ha sido realizado. En este caso solo se analizara dos casos: `update-status-confirmed` y `update-status-cancelled`.
3. Al recibir el evento correcto, la data es transformada a la estructura que necesitamos guardar y se ejecuta el insert a la tabla `fact_order_variation_sold`.

Esto representa nuestro proceso `E` y `L`.

Luego para poder mostrar los dashboards y graficos, se creara una API de ordenes llamada `juntoz-api-core-datamart-report` que permitira a SiteCentral leer la data de la BD y mostrarla en graficos dentro del dashboard principal de SiteCentral.

El mismo modelo se aplicaria para otras entidades de la plataforma.

## Ingesta de Trafico (FUTURO)
Parte del futuro del dashboard incluye tambien poder mostrar estadisticas de trafico de la web o del app y asi poder cruzarla con la informacion de ventas por ejemplo.

Para hacerlo, se mejorara el componente actual de Juntoz Tag Manager para que empiece a emitir eventos a un tracker propio de Juntoz.

Seguramente este tracker propio usara un API propio y que por dentro esto insertara un evento a Azure Event Hub para que luego un azure function procese este mensaje y lo inserte tambien en la TSDB para que pueda luego ser accedida a traves del data mart.

Seguramente en este caso ultimo, habra que analizar bien la estructura de la data para tratar de mantener el modelo EL lo mas posible para que se pueda ver la data lo mas realtime posible.