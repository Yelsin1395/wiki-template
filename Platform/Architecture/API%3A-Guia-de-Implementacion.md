# Un API = Un microservicio = Una entidad

NOTA: para crear el api, usar el otro wiki para la creacion de apis (en esta misma carpeta).

- Las apis seran microservicios y encapsularan una entidad solamente. Si hay entidades hijas, estaran en la misma api, dentro de lo posible y buen mantenimiento.

Es decir, Product y ProductVariant deben estar en la misma api, dado que la segunda es hija de la primera.

Category y Product, aunque esten relacionados entre si, deben ir en apis separadas.

# Repositorio

Se creara **UN SOLO REPO** para la entidad. Tipicamente una entidad tendra dos apis, una publica y una core.

La estructura debe ser entonces asi:

```
Git Repo: Juntoz.Api.Brand
  - /core
      index.js
      package.json
  - /public
      index.js
      package.json
```

Esto se debe considerar en el `azure-pipelines.yml` de cada api para que el `srcPath` sea especifico a cada ruta, ademas que el trigger de build tambien.

```
trigger:
  branches:
    include:
    - master
  paths:
    include:
    - core

variables:
- name: srcPath
  value: $(build.sourcesDirectory)/core
```

NOTA: si, hay hoy algunos que son solo public y el repo se llama Juntoz.Api.Public.Currency, pero eso lo debemos ir afinando.

En general queremos que las APIs sean simples y que encapsulen la funcionalidad. Y aunque puedan haber varias APIs que usen Algolia por ejemplo, no quiere decir que debemos juntarlas en un solo API. Las APIs deben disenarse por entidad y flujo de negocio. No por tecnologia.

Las api core son aquellas que tipicamente seran para buscar (con campos simples), obtener por id, crear, modificar y eliminar.

Las api public son aquellas que las web ecommerce o apps usaran para leer la informacion. Si la data del core, debe salir tal cual hacia Public:
1. Validar si es necesario tener una core, y solo una public.
2. Es mejor tener un Api que lee solo la data (y duplique el codigo un poco), a dar acceso a endpoints que el usuario no debe.
3. No debe haber llamadas de Public a Core. Esto es porque el token del usuario del ecommerce SOLO tendra acceso al audience api-public. (Por evaluarse).

Hay un tercer tipo que son las APIs de integracion que lo usaran empresas externas a Juntoz para integrarse con la plataforma. En estos casos, hay que implementar un "extended token" para que de las apis de integracion se pueda acceder al core.

# Koa
Las APIs estan siendo hechas en Koa con un framework creado in-house. Para crear un API se usa una herramienta llamada @juntoz/cli que nos permite crear un API desde cero con toda la estructura base y buenas practicas que estamos acumulando.

Asimismo, seguimos el principio de "usa la mejor herramienta para el trabajo especifico". Eso quiere decir que si en algun momento se necesita otra API o otro framework, se evalua y se usa.

# Seguridad
Todas las APIs estan detras del API gateway por lo tanto compartiran el mismo Kubernetes cluster y no deben tener colisiones de path.

Todas las apis usaran JWT tokens. Estos son emitidos por el proyecto Juntoz.Id (Identity V3). Todas las apis (core, public y int) estaran aseguradas por el token. No hay apis "libres".

Todas las apis necesitan tambien el header X-JZ-SITE para reducir el scope de los queries y operaciones a este Site (la unica que no tiene esto es la API para manejar la data de los sites).

Dentro de la api, cada endpoint tambien definira los roles que pueden llamarlo. Para entender la estructura de roles (que es jerarquica), por favor leer el wiki de seguridad de roles.

# Un API es un producto separado
Esto es muy importante. Uno de los errores de Juntoz fue usar las APIs para el backoffice Merchant Central. Esto fue un error dado que hizo que las APIs tengan muchos endpoints, algunos adaptados especificos para las pantallas de MC y eso hizo que NO se pueda reutilizar.

Entonces un API es un producto SEPARADO y se disena API First, es decir, se disena definiendo las capacidades exactas que la entidad necesita para ser modificada, no a traves de una pantalla, sino a traves del API.

Esto quiere decir que la pantalla SE ADAPTA AL API. No al reves. Esto lograra que tengamos APIs reutilizables. 

Si es que la pantalla necesita un poco de mas informacion o de otra manera, el Product Owner del API o el dueno del API revisa esta peticion, y si esta en el roadmap del API o si es que beneficia A TODOS o la mayoria, entonces se acepta y se hace el cambio.

Recordar que las APIs core seran utilizadas por empresas externas y Juntoz cobrara por el uso. SON NUESTRO PRODUCTO. Por lo tanto es importante que mantengamos las APIs con buena salud y bien disenadas.

# Division de Endpoints
Uno de los grandes problemas del api de Juntoz actual es que PERMITE hacer muchas modificaciones de la data sin ningun control. Asi por ejemplo, el modelo para actualizar Product tiene 50 campos. Y teniendo entre si varios campos con distintas velocidades de cambio o lifecycle. Esto NO es bueno y solo lleva a una falta de control que no nos podemos dar ese lujo. Por lo tanto los siguientes principios aplican:

1. La creacion de la entidad debe ser muy simple y con los campos necesarios nada mas.

2. La actualizacion de la entidad debe hacerse SEPARANDO el lifecycle de cada campo y ademas de la importancia de distinguir el cambio en ese campo dado que representa una logica pesada o representa un cambio importante.

Por ejemplo, para crear un producto, solo necesito el nombre y el tipo. 

Para modificar el producto, debo tener varios endpoints de update.

- `/product/update/basic` para actualizar data basica como el nombre o la descripcion.
- `/product/update/price` para actualizar el precio solamente. Esto es importante dado que un cambio de precio puede afectar otros modulos y flujos. Peor aun, el cambio de precio es delicado por lo que updates concurrentes pueden ocasionar problemas de integridad. En este caso hasta podria ser recomendado enrutar el update no en el api, sino a una cola separada solo para ese update. De esa forma garantizamos el orden.
- `/product/update/boost` para actualizar el boost solamente.

De igual manera NO DEBEMOS permitir el update global (con toda la estructura completa) del producto debido a que tiene entidades hijas (como las variantes) y esto puede llevar a tambien problemas de integridad.

Entonces como regla primordial, **TODOS LAS OPERACIONES SOLO DEBEN AFECTAR UN NIVEL** de la entidad. Si la entidad es multinivel, deben crearse endpoints para esas sub-entidades.

Entonces habra:
- `/product/update/variation/create`
- `/product/update/variation/update`

y aqui igual seguir con las mismas indicaciones de reducir el impacto de los endpoints sobre la integridad de la data y la correcta identificacion de eventos.

Y esto es muy importante para el siguiente punto.

## Eventos
Todas las APIs deben tener un Topic de Azure Service Bus y que debe recibir todos los eventos de cambios (creacion, update y borrado) de esa entidad.

Asi por ejemplo tenemos `product_upd`, `brand_upd`, etc.

Cuando creamos el objeto debemos enviar un evento asi:

```
            await this.busSender.sendToTopic(
                this.TOPIC_NAME,
                {
                    body: {
                        old: null,
                        new: dbEntity
                    },
                    userProperties: {
                        event: 'create'
                    }
                }
            );
```

Al modificarlo la seccion basica (update-basic), si fuera el boost (update-boost) ...
```
            await this.busSender.sendToTopic(
                this.TOPIC_NAME,
                {
                    body: {
                        old: oldEntity,
                        new: dbEntity
                    },
                    userProperties: {
                        event: 'update-basic'
                    }
                }
            );
```

Al eliminarlo
```
            await this.busSender.sendToTopic(
                this.TOPIC_NAME,
                {
                    body: {
                        old: dbEntity,
                        new: null
                    },
                    userProperties: {
                        event: 'delete'
                    }
                }
            );
```

Y estos son la mayoria de eventos que solamente deberiamos tener. Seran los subscriptores a esos eventos quienes haran lo que necesiten.

IMPORTANTE: el evento debe enviarse en `userProperties`, esto es importante dado que cuando se suscribe a un topic, puede crear un filtro usando los userProperties, de esta forma el suscriptor no recibira los eventos que no quiere.

## Formato de nombres de eventos
Aunque los eventos pueden tener cualquier nombre, tener una convencion ayudara a mantener el codigo ordenado.

La convencion es la siguiente:
Para la entidad principal se debe usar
```
create
update-[field or group of fields]
delete
```

Para las subentidades se debe usar
```
[subentity]-create
[subentity]-update-[field or group of fields]
[subentity]-delete
```

Si los hay campos dentro de la subentidad que se soportan varios valores, se trata de seguir la misma convencion
```
[subentity]-update-[field]-create|update|delete
```

Ejemplos
|Evento|Nomenclatura|
|-|-|
|Al crear un producto|create|
|Al update basico de un producto|update-basic|
|Al update del can search del producto|update-cansearch|
|Al eliminar un producto|delete|
|Al crear una variante de producto|variation-create|
|Al update basico de una variante|variation-update-basic|
|Al update del can sell de una variante|variation-update-cansell|
|Al asignar un nuevo rol del usuario|update-role-create|
|Al eliminar un rol del usuario|update-role-delete|

*** Por definirse si es - o _, pero la estructura si debe ser asi.

# Emision de Errores
Las APIs pueden generar errores controlados o no controlados.

Los **no controlados** son aquellos que se emiten como un **HTTP 500**. La idea es minimizar estos y siempre mantenerlos en el log para su revision. Esto es automatico con el proyecto `koa-pino-logger`.

Los **controlados** son aquellos que se emiten por excepciones que lo puede generar el api en si, o modulos internos. 

Todas las excepciones controladas deben emitise con las clases
- `AppError` (declarado en @juntoz/api-infra)
- `RepositoryError` o `ValidationError` (estos dos ultimos dentro de @juntoz/joi).

Para la clase `AppError`, todos los errores emitidos deben crearsele un codigo unico y que siempre empiece con el prefijo `ERR_` y debe tratar de seguirse el patron: `ERR_{objeto}_{causa}`.

Ejemplos correctos:
- ERR_PRODUCT_VARIATION_NOT_FOUND

Ejemplos incorrectos:
- ERR_NOT_FOUND_PRODUCT_VARIATION

Todos estos errores terminan en un **HTTP 400.**

Dado que al serializarse, todo queda como un `Error` simple (o peor aun el mensaje solamente), la clase `AppError` automaticamente llena el campo `Error.message` con el siguiente formato `{code}:{message}`.

Por lo tanto, si es que se crea asi: `throw new AppError('ERR_OBJECT_CAUSE', 'The object was invalid')`, el mensaje del error termina siendo:
```
ERR_OBJECT_CAUSE:The object was invalid
```

De esta manera, teniendo esta convencion definida, todas las UI pueden parsear el mensaje y mostrar el mensaje adecuado.

## INGLES!

**IMPORTANTE: EL CAMPO ERROR.MESSAGE DEBE SER GENERADO SIEMPRE EN INGLES.**

Esto es una convencion necesaria para asegurar su universalidad. NO PUEDE GENERAR MENSAJES EN ESPANOL.

Es la responsabilidad de la UI "traducirlo", no de la API.

_NOTA: Hay una propuesta para que el API genere los mensajes en espanol a traves de un archivo de traduccion. Esto es muy plausible pero debe ser hecho de manera generica y que sea estandarizado a todas las APIs._

## Codigos estandar
Hay un codigo de error especial que es `ERR_GET_ID` (que para nuestra mala suerte, efectivamente no cumple con el formato :( jaja).

Cuando este AppError es generado, el framework lo convierte en un **HTTP 404.**


