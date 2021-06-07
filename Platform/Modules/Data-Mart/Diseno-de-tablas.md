# Como disenar una tabla
En general, las tablas que creemos **NO deben seguir** el modelo **OLTP** = Normalizacion = optimizadas para escritura (esto viene de mucho tiempo atras donde la data antes era muy cara de organizar y guardar, por lo que era necesario ahorrar lo mas posible).

Las tablas del datamart **deben seguir** mas un modelo **OLAP**: optimizadas para lectura.

La diferencia de una tabla OLAP es que uno lo que busca es justamente lo contrario a una tabla OLTP: QUEREMOS DESNORMALIZAR.

Mientras mas desnormalizada, es mejor dado que el RDBMS no tendra que buscar en otras tablas, indices y etc. Y ademas cada tabla debe ser disenada para responder **una pregunta** especifica. En un OLTP podriamos hacer queries (+- complejos) para obtener distintas respuestas a preguntas, dado que queremos AHORRAR espacio y siempre ver la data exacta. En un OLAP, se busca que una tabla responda a una pregunta solamente, para que asi tambien sea mucho mas rapido de ejecutar.

Por ejemplo, si es que tuvieramos un OLTP con ordenes de venta que contienen montos y fechas de ejecucion (cuando se despacho, cuando se ejecuto), podriamos mas o menos crear varios queries sobre las mismas tablas.

En un OLAP, en cambio, creariamos dos tablas separadas: una de ventas con sus montos, y otra de fechas. De esta forma podemos responder distintas preguntas de una manera optima desde el punto de vista de performance. Obviamente un OLAP es un sistema un poco mas rigido (porque a mas preguntas, mas tablas), pero el performance lo vale.

## Facts y Dimensions
Hay dos tipos de tablas en un OLAP, las facts y las dimensions.

Las tablas dimension son aquellas que guardan data auxiliar, y aunque si es un poco normalizado, lo que se busca es minimizar errores y mala data (no resguardar el espacio en disco).

Las tablas fact son aquellas que contiene por lo general las sumarizaciones.

Por ejemplo, asi seria un fact y un dimension:
```
Fact
=====
Year
Month
Day
Time
ProductId
SubAmount

Dimension
=========
ProductId
ProductNumber
ProductName
ProductPrice
```
Es discutible la necesidad de tener un dimension dado que complejiza la carga de data y actualizacion de la data, pero si es que tienes varias facts probablemente si tendria sentido. Por otro lado, tiene un costo de lectura. Se tendria que evaluar costo de lectura vs costo de escritura (complejidad).

# Comandos SQL para crear nueva tabla
Para crear nuevas tablas, se debe seguir los siguientes pasos:

1. Login con el usuario dbowner o superadmin

2. CREATE TABLE en PUBLIC

Crear la tabla usando ANSI SQL de PostgreSql. No olvidar de poner el esquema **public**.

```
CREATE TABLE public.my_fact (
...
);
```

3. Hypertable
```
SELECT create_hypertable('my_fact', 'time');
```
Esto convertira la tabla en un Hypertable que es el termino que TimeScale les da a las tablas manejadas por el.

4. Dar permisos

Para que el usuario `apprw`, que es el usuario con el que las aplicaciones deben usar para conectarse, pueda acceder a las tablas se debe dar GRANT.

Cuando una tabla nueva es creada, el mismo comando debe ser ejecutado asi se mantiene el acceso correcto.

```
GRANT SELECT ON ALL TABLES IN SCHEMA public TO apprw;
GRANT INSERT ON ALL TABLES IN SCHEMA public TO apprw;
GRANT UPDATE ON ALL TABLES IN SCHEMA public TO apprw;
GRANT DELETE ON ALL TABLES IN SCHEMA public TO apprw;
```
