Hay dos tipos de grupos en Azure: "de oficina" y de "azure". Esto es solo una convencion para poder ordenar la seguridad.

Los de oficina son aquellos que contienen a los usuarios sueltos:
- Juntoz-Dev-Team
- Juntoz-BI-Team

Los de Azure son:
- Juntoz-Azure-BI; grupo para poder contribuir sobre los recursos de BI y la BD de SalesDbBI como lectura.
- Juntoz-Azure-Owners: grupo que contiene las personas que son Owners de todo Azure (Kat Lim y James)
- Juntoz-Azure-Prod-Contributors: grupo que contiene las personas que si pueden modificar los recursos de Produccion (lideres de equipo y arquitectos)
- Juntoz-Azure-Prod-Db-Read: grupo que contiene a Juntoz-Dev-Team para poder leer la BD de Produccion
- Juntoz-Azure-Prod-DevInsights: grupo que contiene a Juntoz-Dev-Team para poder leer los Application Insights de Produccion
- Juntoz-Azure-Staging: grupo que contiene a Juntoz-Dev-Team que puede contribuir a todo staging

Actualmente los permisos estan en los siguientes niveles:
- Juntoz-Azure-BI esta a nivel de resource group juntoz-rg-vm
- Juntoz-Azure-Owners esta a nivel de suscripcion para poder acceder a todos los recursos a la vez
- Juntoz-Azure-Prod-Contributors esta a nivel de resource group juntoz-rg-production
- Juntoz-Azure-Prod-Db-Read esta a nivel de juntoz-sql-srv-production y luego en cada database como db-data-reader
- Juntoz-Azure-Prod-DevInsights esta a nivel de resource group juntoz-rg-production-appinsights
- Juntoz-Azure-Staging esta a nivel de resource group juntoz-rg-staging

# FAQ
## Nuevo developer?
Agregar a Juntoz-Dev-Team

## Nuevo Lider de equipo o arquitecto?
Agregar a Juntoz-Prod-Contributors

## Nuevo App Insight para Produccion?
Mover a resource group juntoz-rg-production-appinsights

## Nueva Db?

### Para Staging
```
USE XXX;
CREATE USER [Juntoz-Azure-Staging] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_owner', 'Juntoz-Azure-Staging';
```

### Para Prod
```
CREATE USER [Juntoz-Azure-Prod-Db-Read] FROM EXTERNAL PROVIDER;
exec sp_addrolemember 'db_datareader', 'Juntoz-Azure-Prod-Db-Read';
GRANT VIEW DEFINITION TO [Juntoz-Azure-Prod-Db-Read];
CREATE USER [Juntoz-Azure-Prod-Contributors] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_owner', 'Juntoz-Azure-Prod-Contributors';
```

### Para BI
```
USE SalesDB_BI
CREATE USER [Juntoz-AzureBI] FROM EXTERNAL PROVIDER
exec sp_addrolemember 'db_datareader', 'Juntoz-AzureBI';
```