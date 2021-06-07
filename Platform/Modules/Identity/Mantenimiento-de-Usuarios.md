El mantenimiento de usuarios en la plataforma Juntoz no es tan sencillo como en otras, dado que hay varios niveles y varios actores actualizando esta información.
Y si además le agregamos que la información de usuarios es siempre delicada, entonces hay que tomar medidas extras para que esto funcione de manera correcta.

Este documento contiene todo lo relacionado a los usuarios y como fueron diseñados.

# Usuarios segregados por *site*
Los usuarios están segregados por *site* para garantizar su seguridad y que no haya cruces entre *sites*. Entonces puede haber un usuario con el *username* katlim@ieholding.com en el *site* 1 y en el *site* 2 al mismo tiempo. 

En *legacy* esto era un problema dado que a un usuario que se registra en Juntoz, automáticamente se le daba acceso a kidsmadehere. Y si se realizaba un cambio de contraseña en kidsmadehere (dado que es el mismo registro físico), esa contraseña tambien se cambiaba en Juntoz.com. Actualmente, en la nueva plataforma, si yo como usuario katlim@ieholding.com me registro en Lifemiles.com y en MiTienda.com, generaré dos registros únicos y separados, asimismo, las contraseñas podrían ser distintas en ambos *sites*.

Esta funcionalidad se logra a través del campo `User.siteId`.

# Niveles o Categorías de Usuarios
El caso de la plataforma Juntoz es un caso especial dado que se quiere que soporte el modelo *marketplace* desde el *core*, y por lo tanto, no es el caso sencillo de una tienda única en donde se tiene un conjunto de usuarios como staff de la empresa y nada mas.

La plataforma entonces tiene cuatro niveles de usuarios que distintos grupos de roles pueden leer o modificar.

1. **End Users o Customers** que son quienes compran en la app *ecommerce*. Estos se registran por si mismos con su *username*, nombres, apellidos, contraseña, etc.

2. **Site Users** que son los usuarios que pertenecen a la organización del *Site* y que se encargan de la administración de la app *ecommerce* sea un *marketplace* o un e-store (tienda sola *standalone*).

3. **Merchant Users** que, para el caso de *marketplace*, son los usuarios que pertenecen a la organización de un *merchant* y que se encargan de la tienda (catalogo, ventas y operaciones) dentro del *marketplace*.

4. **Logistic Users** que, para el caso del *marketplace*, son los usuarios que pertenecen a un *carrier* que no cuenta con integración con la plataforma y necesita actualizar los estados de entrega de la orden.

5. **Sys Users**, son aquellos que pertenecen a JuntozTech y que pueden leer y modificar cualquier *site*.

Todos se registran en la misma tabla `User` pero se diferencian por los campos `ownerId` y `ownerType`. El `ownerId` es el id de la entidad "dueña" del usuario. El `ownerType` es el tipo de esa entidad.

OwnerType = [site, merchant, logistic]

Notar que para el caso del `ownerType == site`, aunque el campo `siteId` y el campo `ownerId` puedan tener el mismo valor, el primero representa DONDE se le ubica a este usuario o a donde fue registrado y el segundo representa la organización dueña de ese usuario.

La idea aquí es que el *merchant* pueda administrar sus propios usuarios asi como el *site* y el *logistic*.

Por ejemplo, para administrar los usuarios del *merchant*, se ha habilitado el rol `merchantadmin`. Sin embargo, el rol `sitemerchantrep` tambien lo puede hacer (dado que representa al *customer representative* encargado de ayudar a los *merchants* a operar dentro del *marketplace*).

A continuación se muestra una tabla donde se puede ver cada tipo de usuario, sus características y roles que pueden actualizarlo.

|Tipo|OwnerId|OwnerType|Roles|
|-|-|-|-|
|Sys User|null**|null**|sysadmin|
|Site User|<siteId>|site|siteadmin, syssiterep|
|Merchant User|<merchantId>|merchant|merchantadmin, sitemerchantrep, syssiterep|
|Logistic User|<logisticId>|logistic|logisticadmin, sitemerchantrep, syssiterep|
|End User or Customer|NULL|NULL|siteenduserrep, syssiterep|

** Aun no se define bien este nivel de usuarios y si necesitamos segregarlos de alguna manera (probablemente si y tendremos el OwnerType = sys y OwnerId = juntoz).

Algunos ejemplos:
- José como *sysadmin* puede administrar los usuarios que pertenecen a la plataforma.
- María pertenece a la empresa SurfCo y es *merchant* del site ShopStar. Por lo tanto, María puede registrar a Carlos y a Omar como empleados de la empresa SurfCo y que ayudan a empaquetar y monitorear los pedidos.
- Juan es asistente del *carrier* Motito.com y esta registrado como *logisticuser* dentro del *logistic* Motito. El administrador de Motito es Laura y ella esta registrada como *logisticadmin*.
- Cynthia es un *customer representative* dentro del *site* ShopStar y puede modificar la lista de usuarios de Motito y ayudar a Laura a borrar a Juan que ya no trabaja allí.

Algunas conclusiones de los ejemplos y de la tabla
- Los roles -rep son aquellos asignados a los *customer representative* y son aquellos que pueden ayudar a los niveles mas bajos en sus tareas.
- El *syssiterep* ayuda a los *sites*, y los *sitemerchantrep* ayudan a los *merchants*.
- El rol *siteenduserrep* ayuda a los *customers* de los *sites*.

# Ventanas de Mantenimiento de Usuarios
Entonces en base a lo expuesto anteriormente, habrán pantallas especiales de mantenimiento de usuarios a cada nivel de la plataforma.

Deberían haber por lo menos:
1. Ventana de Usuarios Registrados dentro para el Site Owner (los End Users que no incluyen los de merchant) para ser usada por el siteenduserrep (y syssiterep podra entrar aquí).
2. Ventana de Manejo de Staff para el Site Owner para ser usada por el siteadmin (y syssiterep podra entrar aqui).
3. Ventana de Manejo de Staff para el Merchant para ser usada por el merchantadmin (y syssiterep sitemerchantrep podran entrar aqui).
4. Ventana de Manejo de Staff para el Logistic para ser usada por el logisticadmin (y syssiterep sitemerchantrep podran entrar aqui).

# Backend para Mantenimiento de Usuarios
Para lograr esta separación y poder manejar la seguridad y los accesos mas fácilmente es que el API de Usuarios se ha modificado para que pueda manejar todos estos niveles y asegurar cada nivel de manera especifica.

**NOTA: para llamar a todos los *endpoints*, empiezan con el prefijo `/c/user` y luego el path mostrado aquí debajo.**

Primero se definió este *endpoint* para verificar la autenticidad del usuario
```
.post('matchPwd', '/pwd/:logintype/match', invoker('matchPassword'))
```

donde *logintype* puede ser `customer` que será enviado por el *login* para las apps *ecommerce*, y `staff` que será usado para el *login* de SiteCentral.

A continuación se han definido los siguientes *endpoints* a nivel de SITE y que se le autoriza a los roles ['siteadmin', 'syssiterep'].

``` js
.get('search', '/site/<siteId>/search/', checkRoute, invoker('search'))
.get('getByUsername', '/site/<siteId>/by/username/:username', checkRoute, invoker('getByUsername'))
.get('getByEmail', '/site/<siteId>/by/email/:email', checkRoute, invoker('getByEmail'))
.get('getById', '/site/<siteId>/:id', checkRoute, invoker('getById'))
.post('create', '/site/<siteId>/create', checkRoute, invoker('create'))
.post('createForClient', '/site/<siteId>/create/for/client', checkRoute, invoker('createForClient'))
.post('updateBasic', '/site/<siteId>/update/basic', checkRoute, invoker('updateBasic'))
.post('addRoles', '/site/<siteId>/update/roles/add', checkRoute, invoker('addRoles'))
.post('deleteRoles', '/site/<siteId>/update/roles/delete', checkRoute, invoker('deleteRoles'))
.delete('delete', '/site/<siteId>/:id', checkRoute, invoker('delete'))
.post('setPwd', '/pwd/site/<siteId>/update', checkRoute, invoker('setPwd'))
```

Los mismos *endpoints* fueron definidos a nivel MERCHANT y que se le autoriza a los roles ['merchantadmin', 'sitemerchantrep'].

``` js
.get('search', '/merchant/<merchantid>/search/', checkRoute, invoker('search'))
.get('getByUsername', '/merchant/<merchantid>/by/username/:username', checkRoute, invoker('getByUsername'))
.get('getByEmail', '/merchant/<merchantid>/by/email/:email', checkRoute, invoker('getByEmail'))
.get('getById', '/merchant/<merchantid>/:id', checkRoute, invoker('getById'))
.post('create', '/merchant/<merchantid>/create', checkRoute, invoker('create'))
.post('createForClient', '/merchant/<merchantid>/create/for/client', checkRoute, invoker('createForClient'))
.post('updateBasic', '/merchant/<merchantid>/update/basic', checkRoute, invoker('updateBasic'))
.post('addRoles', '/merchant/<merchantid>/update/roles/add', checkRoute, invoker('addRoles'))
.post('deleteRoles', '/merchant/<merchantid>/update/roles/delete', checkRoute, invoker('deleteRoles'))
.delete('delete', '/merchant/<merchantid>/:id', checkRoute, invoker('delete'))
.post('setPwd', '/pwd/merchant/<merchantid>/update', checkRoute, invoker('setPwd'))
```

Los mismos *endpoints* fueron definidos a nivel SYS y que se le autoriza a los roles ['sysadmin']. En este caso, el `ownerId` siempre será `juntoz`.

``` js
.get('search', '/sys/juntoz/search/', checkRoute, invoker('search'))
.get('getByUsername', '/sys/juntoz/by/username/:username', checkRoute, invoker('getByUsername'))
.get('getByEmail', '/sys/juntoz/by/email/:email', checkRoute, invoker('getByEmail'))
.get('getById', '/sys/juntoz/:id', checkRoute, invoker('getById'))
.post('create', '/sys/juntoz/create', checkRoute, invoker('create'))
.post('updateBasic', '/sys/juntoz/update/basic', checkRoute, invoker('updateBasic'))
.post('addRoles', '/sys/juntoz/update/roles/add', checkRoute, invoker('addRoles'))
.post('deleteRoles', '/sys/juntoz/update/roles/delete', checkRoute, invoker('deleteRoles'))
.delete('delete', '/sys/juntoz/:id', checkRoute, invoker('delete'))
.post('setPwd', '/pwd/sys/juntoz/update', checkRoute, invoker('setPwd'))
```

Para el caso de CUSTOMER, solo se habilitaron algunos *endpoints* y se le autoriza a los roles ['siteenduserrep'].

``` js
.get('search', '/customer/search/', checkRoute, invoker('search'))
.get('getByUsername', '/customer/by/username/:username', checkRoute, invoker('getByUsername'))
.get('getByEmail', '/customer/by/email/:email', checkRoute, invoker('getByEmail'))
.get('getById', '/customer/:id', checkRoute, invoker('getById'))
.post('create', '/customer/create', checkRoute, invoker('create'))
.post('updateBasic', '/customer/update/basic', checkRoute, invoker('updateBasic'))
.delete('delete', '/customer/:id', checkRoute, invoker('delete'))
.post('setPwd', '/pwd/customer/update', checkRoute, invoker('setPwd'))
```

Además de estos cuatro niveles, se agrego el nivel SELF, que permite ejecutar algunas operaciones por si mismo. En este caso no se valida los roles, pero si se valida que el *claim* `sub` del token haga match con el usuario que se esta obteniendo.

``` js
.get('getById', '/self/:id', checkRoute, invoker('getById'))
.post('create', '/self/create', checkRoute, invoker('create'))
.post('updateBasic', '/self/update/basic', checkRoute, invoker('updateBasic'))
.post('setPwd', '/pwd/self/update', checkRoute, invoker('setPwd'))
```

# ¿User y Owner al mismo tiempo?
¿Puede un usuario del *merchant* tambien comprar en el *marketplace*? Sí.

Lo que ocurrirá en ese escenario es que si el usuario Omar fue registrado como merchantlogistic y quiere comprar, el merchantadmin puede agregarle el rol `user` y con eso ya estaría apto para entrar a la web ecommerce.

Cuando es el caso contrario (el usuario ya es cliente y de ahi se convierte tambien en merchant), creo que por ahora tendrá que ser hecho por el dpto. de soporte del *site* o sys (osea siteenduserrep o syssiterep) para que lo marque como tal con un rol y un owner del merchant.

EN TEORÍA, para el caso del *merchant*, ese usuario tendría que usar su email de trabajo y para las compras, debería usar su email personal. Sin embargo, mucha gente usa su email de trabajo tambien para sus actividades personales por lo que es necesario soportar el caso.

# ¿Puede un usuario tener mas de un owner?
Por ahora la respuesta es NO. Según el modelo de datos, un user solo puede pertenecer a un `ownerId`.
Hay casos extremos donde esto podría pasar, pero estamos "descartando", por ahora, su soporte. Cuando se de, veremos el caso más a fondo.

- Si José tiene el email jose@merco.com y luego pasa a otro *merchant* como jose@polo.com obviamente aquí no hay problema dado que es otro usuario por completo.
- Si José tiene el email jose@hotmail.com y con eso fue registrado para el *merchant* Caterio. Y luego José se cambia de trabajo y pasa al *merchant* Looper, y quiere usar su mismo email, entonces ahi si tendrían que acudir a soporte. Sin embargo, en este caso se podría borrar a jose@hotmail.com y crearlo de nuevo. Aunque pierde historia, esa seía la forma de poder asignar ese email al nuevo owner. Esto se tendría que hacer en coordinación con Caterio o a través de soporte. Este caso es poco probable dado que por lo general las empresas tienen su propio dominio de email.
- Actualmente, por ejemplo, se esta empezando a convertir en tendencia que haya un operador logístico que opere (catalogo, ventas e inventarios) en varias tiendas a la vez, como Dinet que opera Kimberly Clark junto con otros "merchants", o el caso de Tawa que operaba las dos tiendas de Samsung en Juntoz (bajo dos merchants distintos). Sin embargo, aquí la probabilidad que UNA persona se encargue de todas esas tiendas tambien es poco probable. Probablemente seria maria@dinet.com para KC, tania@dinet.com para OM y etc.


