La nueva plataforma de Juntoz se basa en uno de los nuevos paradigmas o tecnologias de autenticacion llamado OpenIdConnect o OpenId.

Este es un protocolo que se monta sobre uno ya existente llamado OAuth2.0. Sin embargo, OpenId es mejor dado que permite una autenticacion interactiva unificada.

La idea detras de OpenId es que puedas autenticar a una persona sin que la aplicacion que requiere hacerlo, no sepa el password ni otros llaves de autenticacion. Es basicamente un sistema de confianza.

Para que todo esto funcione, se necesita entonces un servidor de OpenIdConnect. En Juntoz se implemento uno usando `IdentityServer4` que es un framework open source de .NETCore para implementaciones de OpenId. Esta es una de las pocas librerias que conforman con el estandar en un alto grado.

Implementar OpenId no es sencillo, por lo tanto usar un framework ya probado y maduro es clave.

En nuestro caso y ademas ya es un estandar de facto, el resultado de esta autenticacion sera un token JWT que contendra informacion basica del usuario y su sesion. Lo bueno de los tokens JWT es que son autocontenidos y no necesitan verificacion contra un servidor para poder ser usados. Lo malo es tambien justamente esto y la informacion dentro del token debe ser publica y no sensitiva (aunque esto es bastante ya usual). 

Para mas detalle de como se esta usando y que contiene, ver el wiki [aqui](/Platform/Architecture/Seguridad-de-APIs-y-Multi%2DTenancy).

Nuestro servidor esta en el repo `Juntoz.Id` y esta hecho en AspNetCore y el backend utiliza APIs hechas en NodeJs y que son parte de las Api Core de la plataforma.

# Estructura de OIDC
Para entender el esquema y modelo de autenticacion, es necesario tambien entender la estructura de OpenIdConnect y de como se dividen en varios componentes que estan interrelacionados. Cada uno tendra una entrada del wiki mas detallada, debajo solo un resumen rapido.

## Site
Aunque este no es un concepto propio de OIDC, si lo es dentro de la plataforma de Juntoz. Toda autenticacion se hara en el marco de un Site. Un Site representa a una entidad que gira alrededor del ecommerce y es dueno de las operaciones de comercio electronico hechas a traves de una web o en un app, o en ambos.

Por ejemplo, Juntoz.com es un Site. Si Juntoz.com tiene una web y tiene un app mobile, ambos seran el mismo Site.

## Client
Un client es una aplicacion que se conectara al servidor OpenId para obtener autenticacion de los usuarios. Los Client son piezas importantes dentro del esquema OpenId y dentro del esquema de autenticacion de Juntoz.

En el ejemplo anterior, Juntoz.com tendria dos clients: un client W123 para la web ecommerce, y el app mobile seria otro client A123.

El client es el vehiculo para que todo el esquema inicie y se puedan ejecutar los flujos de autenticacion. Es al cliente a quien se le permite uno o muchos flujos de autenticacion y se le permite acceder a los recursos disponibles.

Hay dos tipos de clients: interactivos y de integracion. 

En los ejemplos, una web ecommerce o un app mobile son clients interactivos dado que se conectan al OpenId server y piden las credenciales en linea. 

Los de integracion son los clients registrados que los merchants o sites deben usar para conectarse con la plataforma Juntoz.

Hay un tercer tipo que esta esperado exista, pero hasta ahora no lo vemos en la nueva plataforma.
Seria aquel client no interactivo que pertenece a la plataforma. Ejemplo, proceso batch que corre cada noche. Por ahora no se puede crear uno asi.

## Recurso o API
Toda plataforma con un proceso de autenticacion, lo que esta haciendo es basicamente protegiendo un recurso o varios recursos de que una persona desconocida o no autorizada (osea sabemos quien es, pero no esta autorizada).

Entonces en nuestro caso, tendremos tres APIs: api core, api public y api integration. Y son estas las que estaremos protegiendo.

## Grant Types
Todo client que se registra se le debe asignar los grant types autorizados que puede ejecutar. Un grant type es un flujo de autenticacion. Los grant types que soportamos actualmente son hybrid (interactivo), client_credentials (que esta en evaluacion) y password (solo para un caso especifico).

Hay otros custom o extension grant types creados para soportar (por ahora) el proceso de integracion con empresas exteriores: `jz-integration` y `jz-int-to-core`.

## Scopes
(aqui viene la parte confusa) Un scope es basicamente un claim o un grupo de claims (Que es un claim? Es una pieza de informacion dentro de un usuario: email, nombre, apellido, rol, etc) que es parte del token. Y la confusion viene porque los scopes se usan para muchas cosas.

### Identity Scopes
Cuando el usuario es autenticado con exito, el token termina teniendo varios Identity Scopes: `email`, `phone`, `profile`, `juntoz`. Cada identity scope presentara uno o varios claims. Por ejemplo: `profile` significa que contendra `given_name`, `family_name`; el scope `juntoz` tendra los claims: `urn:juntoz:site` y `urn:juntoz:userlevel`.

Los clients tienen tambien asignado `AllowedScopes` que determina que Identity Scopes el client podra leer. Por ejemplo, cuando hacemos un external login usando GMAIL, despues de ingresar la contrasena, GMAIL presenta una pantalla de consentimiento en la que se le da permiso a la aplicacion recipiente a recibir el nombre, email, telefono y etc.

### API Scopes > Audience
Los scopes tambien sirven para unir y verificar si el client puede llamar a un API o no. Esto lo hace a traves del claim audience (aud) que debe estar en el token y que al hacer el request al API, se revisa si hay match con el audience al que esa API pertenece para que el request pueda suceder.

Sin embargo, para que el audience aparezca en el token, no es tan directo como parece.

Cada Resource o API tiene dos campos principales: nombre y scope. Por ejemplo, el API de integracion tiene nombre: api-int y scope: jz-int. El audience es asignado **a traves del scope** que es asignado al client en el campo `AllowedScopes` (si, junto con los identity scopes).

Si tenemos el client con `AllowedScopes=[email,openid,jz-int]`, IdentityServer detectara el scope `jz-int`, verificara que API tiene ese scope, y asignara el claim `aud=api-int`.

# Flows
En resumen para poder ejecutar la autenticacion y generar un token se debe:

1. Crear un site
2. Crear uno o mas clients para el site
3. Por cada client, se debe habilitar
- Grant Types
- Identity Scopes
- API Scopes
4. Si el client es una web o app, se debe usar un client interactivo.
Este usuario interactivo debe tener callback urls registrados que son validados al iniciar la autenticacion.
5. Cuando un usuario necesita hacer login, el client debe enviar un pedido de autenticacion (challenge en netcore) hacia el servidor de openid. En este pedido se necesita enviar el clientid y el clientsecret.
6. Cuando el usuario ingresa su usuario y password correctos, el servidor llamara al callback registrado y con eso la web o app se enteran que el usuario fue autenticado correctamente y recibira el token.
7. Si el client es para integracion, se debe registrar de esa manera (client de integracion).
8. Si el client es un batch process interno de la plataforma, se usara un client no interactivo (pero que hasta ahora no tenemos uno asi).
