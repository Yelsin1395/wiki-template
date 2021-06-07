Los precios NO estan asociados al producto, sino a la variacion. De esta manera podemos tener una variacion que cuesta 100 soles y otra que cuesta 80 soles.

El precio tiene tres componentes: precio base, precios especiales y precio urgente. El ultimo no existe en la plataforma legacy.

## Precio Base
El precio base es aquel que es el precio original del producto (a veces se usa para crear la diferencia con el precio especial y crear un efecto de una oferta mayor). En ingles se llama "List Price".

## Precio Especial
El precio especial es el precio final o real que el usuario pagaria por el producto. Los precios especiales se registran con un nombre (para asignar una forma de recordatorio o la razon por la que el precio especial fue registrado como una campaña VERANO2020). 

Los precios especiales pueden tener una fecha de inicio y fin (siendo opcional esta ultima).

El sistema solo permite registrar o modificar precios especiales con fechas en el futuro. Si el precio ya esta corriendo pero no termina, se puede modificar la fecha de fin, pero si ya paso la fecha de fin, ese precio no se puede modificar.

Cuando el usuario registra un nuevo precio especial, el sistema automaticamente modificara la fecha de fin del anterior rango, para darle termino.

Cuando el usuario busca un producto en el catalogo o lo ve en el product page, el sistema automaticamente mostrara el precio especial vigente en ese momento. Y esto lo hace el api-public de producto. El front NO necesita hacerlo.

Los precios especiales son opcionales siempre.

### Insertar un precio especial
Para insertar un precio especial, el precio base debe ser mayor a cero.

Ante cualquier accion, **la fecha de fin DEBE SER mayor que la fecha de inicio**.

Al insertar un precio especial, se debe validar que este nuevo precio este siempre en el futuro (fecha de inicio y fecha de fin en el futuro).

1. Si para el precio anterior (ultimo), la fecha de inicio Y fin estan en el futuro, no permitir la insercion de la nueva fecha, y decirle al usuario que debe modificar ese precio anterior. Solo puede haber UN precio en el futuro.
2. Si para el precio ultimo, la fecha de inicio ya paso y la fecha de fin ya paso, entonces esta fecha ultima es ignorada y se realiza la insercion.
3. Si para el precio ultimo, la fecha de inicio ya paso pero la fecha de fin aun esta vigente, entonces esta fecha de fin debe ser modificada automaticamente por el sistema con un segundo antes que la nueva fecha de inicio.
4. El precio especial si es que ya no esta corriendo, el precio especial debe ser siempre menor al precio base.

### Modificar precio especial
1. Si la fecha de inicio AUN NO HA PASADO, permito modificar la fecha de inicio, la fecha de fin, el nombre y el precio. Y las fechas siempre en el futuro.
2. Si la fecha de inicio YA PASO, no se puede modificar la fecha de inicio, ni el nombre ni el precio. Solo la fecha de fin (y que tiene que asignarse un valor en el futuro, y que puede ser a 1 minuto de ahora).
3. Si la fecha de fin YA PASO, entonces NO se puede modificar ni la fecha de inicio, fin, precio ni el nombre.
4. El precio especial si es que ya no esta corriendo, el precio especial debe ser siempre menor al precio base.

### Eliminar un precio especial
1. Solo se puede eliminar aquellos que estan en el futuro y que no han pasado. Fecha de inicio y fecha de fin en el futuro.
2. Si la fecha de inicio ya esta corriendo, NO se puede eliminar.
3. Si la fecha de fin ya paso, entonces obviamente ese precio ya esta obsoleto y por tanto debe quedarse como historia.

## Precios especiales vs Precio Base
Hay algunas condiciones especiales dado que se debe cumplir la regla que el basePrice >= [vigente.specialPrices].

Entonces cuando modificas el basePrice, se debe comparar contra todos los precios especiales vigentes (startDate > now && endDate > now o endDate null).

Y cuando modificas el precio especial tambien hay que verificar que sea siempre menor al base price.

## Precio Urgente
Y hay un nuevo concepto llamado Precio Urgente. Este es un precio especial tambien registrado de la misma manera con una fecha de inicio y fin (aqui la fecha final si es mandatoria y no debe pasar mas de 7 dias). Este precio sirve para sobreescribir cualquier campaña que este corriendo actualmente y de manera temporal.

Entonces el sistema revisara si hay precio urgente vigente, si hay uno lo usa e ignora todo lo demas. Si no hay uno vigente, usa el precio especial vigente.

En este caso, el precio urgente puede modificarse a voluntad.