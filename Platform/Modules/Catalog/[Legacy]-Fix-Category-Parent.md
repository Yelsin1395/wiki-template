Stored Procedure para arreglar categorización de productos.
sp_FillParentCategoryCode

La ejecución es de la siguiente manera:
@pTop es un número, si le pones @pTop mayor a cero, pues aplicará el top que especificas, si @pTop es cero no aplicará top y tampoco ordenará por boost
@pBoost es un bit, usar sólo 0 o 1, si @pBoost es 1 ordernará por boost siempre y cuando @pTop sea mayor a cero, si @pBoost es cero no ordenará por boost
@pFilterStoreId es un número, este indica el storeId, si es mayor a 0 filtratá por tienda, si es 0 no aplicará filtro por tienda
@pFilterPublihState es un bit, usar sólo 0 o 1, si es 1 filtrará sólo las publicadas, si es 0 no aplicará filtros de publicación tomando publicados y no publicados.

Se ejecutará:
exec sp_FillParentCategoryCode @pTop = 100, @pBoost = 1, @pFilterStoreId = 20, @pFilterPublihState = 1

Nota: si no se le especifica ningún parámetro, por seguridad ejecutará sólo los 100 primeros productos publicados ordenados por boost descendientemente del catálogo de juntoz, para que no ejecute los miles de miles de productos y cause bloqueos en la escritura/lectura en los distintos procesos internos.

*Si no se aplica @pTop, no tomará en cuenta el boost porque un ordenamiento dentro de una subconsulta sin top se caería.