La estructura de seguridad en general para todos los objetos manejados por el equipo de desarrollo debe seguir el mismo patron: crear un grupo de seguridad y asociar el objeto al mismo. 

NO ASOCIAR usuarios individuales.

Actualmente en Azure tenemos tres grupos que pueden acceder a BD, 

Juntoz-Azure-Staging: acceso libre a staging
Juntoz-Azure-Prod-Db-Read: acceso solo lectura a Prod
Juntoz-Azure-Prod-Contributors: acceso de modificacion a Prod

# Pasos

1. Crear el nuevo usuario dentro de esa BD. Este usuario viene del EXTERNAL PROVIDER (que es Azure AD). El password y los datos del usuario estaran dentro de Azure, no dentro de Sql Server.
```
USE MyNewDb;
CREATE USER [Juntoz-Azure-Staging] FROM EXTERNAL PROVIDER
```
2. Si es staging, dar role db_owner
```
exec sp_addrolemember 'db_owner', 'Juntoz-Azure-Staging';
```
3. Si es production, dar role db_datareader o db_datawriter, etc. 

**NUNCA dar role db_owner. La premisa es ir de lo mas restrictivo a lo menos e ir ajustando si es necesario.**
```
exec sp_addrolemember 'db_datareader', 'Juntoz-Azure-Prod-Db-Read';
```

3.1 Solo Prod-Contributors debe tener db_owner
```
exec sp_addrolemember 'db_owner', 'Juntoz-Azure-Prod-Contributors';
```

4. Para que el usuario pueda leer la definicion de los objetos, hay que dar este permiso

```
GRANT VIEW DEFINITION TO [Juntoz-Azure-Prod-Db-Read]
```

## Si hay un nuevo usuario
Agregar a los grupos que ya estan creados!