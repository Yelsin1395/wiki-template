On Sun, Dec 2, 2018 at 12:35 AM Kat Lim Ruiz <katlim@ieholding.com> wrote:
Hola

Un poco para ir afinando el tema de permisos sobre el SQL Server estos son los cambios que he agregado.

USE AddressDb;
USE Catalog_IntegrationDb;
USE CatalogDb;
USE CMSDb;
USE CollectionDb;
USE CouponDb;
USE CustomerDb;
USE IdentityDb;
USE IdSrv4Db;
USE LookupDb;
USE MetaDataDb;
USE PaymentDb;
USE PaymentIntegrationDb;
USE ReviewDb;
USE SalesDb;
USE SalesDB_BI;
USE ShippingDb;
USE ShippingIntegrationDb;
USE StorageDb;
USE WarehouseDb;

CREATE USER [Juntoz-Azure-Staging] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_owner', 'Juntoz-Azure-Staging';

CREATE USER [Juntoz-Azure-Prod-Db-Read] FROM EXTERNAL PROVIDER;
exec sp_addrolemember 'db_datareader', 'Juntoz-Azure-Prod-Db-Read';
CREATE USER [Juntoz-Azure-Prod-Contributors] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_owner', 'Juntoz-Azure-Prod-Contributors';

USE SalesDB_BI
CREATE USER [Juntoz-Azure-BI] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_dataowner', 'Juntoz-Azure-BI';

CREATE USER [Juntoz-Azure-BI-PowerBI] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_datareader', 'Juntoz-Azure-BI-PowerBI';

-- select * from sys.database_principals
-- select * from sys.database_role_members

Donde,
Juntoz-Azure-Staging somos equipo de desarrollo
Juntoz-Azure-Prod-Db-Read somos equipo de desarrollo
Juntoz-Azure-Production son solo algunos (hoy estamos equipo de desarrollo por facilidad, pero lo voy a ir cortando poco a poco).

Entonces esto quiere decir que a partir de hoy, TODOS pueden ingresar a los servidores de bd con su usuario propio de azure (ej: katlim@ieholding.com y su password de azure).

A partir de hoy, asimismo, queda prohibido usar el usuario de la aplicacion (llamado juntoz). Conforme vaya controlando mejor los ambientes y los deploys, ese usuario solo sera de read y write y los passwords voy a empezar a rotarlos cada 3 meses.

NOTA: al añadir el usuario a master
master > 
STAGING: CREATE USER [Juntoz-Azure-Staging] FROM EXTERNAL PROVIDER;
PROD: CREATE USER [Juntoz-Azure-Prod-Db-Read] FROM EXTERNAL PROVIDER;
Ahora todos los usuarios pueden tener acceso a todas las bases de datos al mismo tiempo (manteniendolo en Read-Only). Antes tenian que abrir una conexion por cada base de datos lo que hace que sea dificil hacer soporte.

--- Sobre BI
Les he dado acceso READ a la SalesDbBI, entiendo que es lo unico que necesitan.

--- Sobre freelancers
Quedan prohibidos de acceder a la bd de staging o produccion salvo permiso explicito pedido a Andres o mio.

--- Sobre Cristian Rodriguez (Chile)
Te voy a tener que quitar permiso explicito de acceso a la BD aunque sea de solo lectura. Solo el equipo de desarrollo esta permitido de acceso (y como te daras cuenta, aun no todos necesariamente podrian tener acceso). Recuerdo que me dijiste que veias cierta data que no aparecia en MC. Por favor pasame el query para ver la forma de incorporarlo en MC para que este acceso se cierre.

Como siempre espero esto se comprenda que es por un tema de seguridad y control necesarios en todo ambiente de produccion.
Algun pedido o acceso especial que se haya truncado por esto, me avisan y lo conversamos.

Gracias

--
Kat Lim Ruiz
CTO
Juntoz.com-