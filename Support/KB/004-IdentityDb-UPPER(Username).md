En Marzo 2020, ocurrio un problema con la base de datos IdentityDb y es que se encontro que habian algunos queries que demoraban demasiado y esto ocasionaba que muchos otros procesos se cayeran.

El query que se encontro en el portal de Azure, en la DB performance overview era:

``` sql
(@p__linq__0 nvarchar(4000))
SELECT TOP (1) 
    [Extent1].[Id] AS [Id], 
    [Extent1].[IsDeleted] AS [IsDeleted], 
    ...
FROM [dbo].[AspNetUsers] AS [Extent1]
WHERE ((UPPER([Extent1].[UserName])) = (UPPER(@p__linq__0))) OR ((UPPER([Extent1].[UserName]) IS NULL) AND (UPPER(@p__linq__0) IS NULL))
```

De este lo grave es `UPPER(Extent1.UserName)` dado que al hacer esto, el motor de SQL Server no puede mapear Username vs ningun indice descrito, por lo tanto hace un Index Scan => LENTO.

Al revisar el codigo de Api.IdentityV1, encontramos varios metodos que hacian (algo parecido):

``` cs
User GetByUsername(string username)
{
  return Table.Where(x => x.Username.Equals(username, OrdinalComparison)).FirstOrDefault();
}
```

Lo que forzaba que la comparacion sea CASE INSENSITIVE (y por ello EF fuerza el UPPER). Lo ironico es que SQL Server es CI por defecto (y toda la BD y columnas tenian el collation CI). Por lo tanto este codigo no era necesario y recien ha dado problemas por la cantidad de registros que ahora tiene la tabla.

La solucion es:
``` cs
User GetByUsername(string username)
{
  return Table.Where(x => x.Username == username).FirstOrDefault();
}
```

Linq interpreta el == y lo mapea directo hacia SQL: `Username = @username` y como ya es CI, entonces funciona correctamente.

UPDATE:
Luego de algunas semanas se reviso, y el mismo query estaba dando lentitud. Y si es que nuestro codigo ya no tenia el query, entonces eso significa que ese query se estaba ejecutando desde adentro de ASPNET IDENTITY y EF, y ahi si no podemos modificar nada (salvo subir de version ASPNETID, pero por ahora no es posible).

Investigando un poco encontre esta solucion: https://stackoverflow.com/a/28378073/915865
y efectivamente funciono.

Este fue la instruccion que se inserto en PROD:

``` sql
alter table aspnetusers add upper_username as upper(username)
-- alter table aspnetusers drop column upper_username

create nonclustered index idx_aspnetusers_userupper on aspnetusers(upper_username)
-- drop index idx_aspnetusers_userupper on aspnetusers
```

Tal cual como se predijo, inicialmente el query hacia INDEX SCAN (dado que no habia un indice apropiado para que use `Upper(Username)`).

Luego de crear la tabla computada y el indice, el mismo query ya hace un INDEX SEEK (que siempre es lo recomendado y te da el mejor performance) dado que ahora si hay un match con el UPPER(Username).

Esperemos que ya no de problemas.

