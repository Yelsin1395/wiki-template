Dado que ahora en la nueva plataforma, el esquema de tarifas de envio es por el punto de vista de la venta, entonces saber QUIEN hara el envio ya no es tan importante, y por ende, podemos demorar esa decision lo mas posible.

El diseno que se ha creado para esta parte es tener tres formas de asignacion:
1. MANUAL. Cuando la suborden esta en estado `Confirmed`, el `sitelogistic` (o el `sitelogisticadmin`) pueden asignar manualmente el carrier a la suborden. Esto es para manejar situaciones muy excepcionales en donde la asignacion debe hacerse en base a la decision de la persona responsable.
En este caso la idea es que la asignacion manual NO valide la cobertura del carrier para que realmente sea puramente la decision del usuario responsable. Pero pueden haber variaciones donde segun el rol se ignore la cobertura, pero para otros si sea mandatorio, etc.

2. SEMI-AUTOMATICO. Se le dara la posibilidad al rol `sitelogistic` de definir `Carrier Preferido` para que cuando el merchant asigna el estado `Ready`, la suborden se le es asignado un carrier automaticamente. Esta preferencia sera configurada a nivel de warehouse, dado que se quiere contemplar el caso tipico en donde si un merchant tiene distintos almacenes en lugares distintos o con necesidades distintas, se pueda configurar p.e. un camion refrigerado o un envio express, etc.
Aqui se asume que OPS del site, SABE que ese carrier si tendra cobertura a donde el merchant quiere vender.

3. AUTOMATICO. En este caso, se contemplaria un modelo para hacer match orden vs carriers, segun distintos criterios como cobertura, rendimiento y etc.
La idea final aqui es que podamos por ejemplo usar un modelo de Machine Learning, en donde podamos incorporar un rating del carrier (la asignacion manual indicaria una preferencia tambien) y asi retroalimentar el proceso.

![shipping selection.png](/.attachments/shipping%20selection-e3b7fa92-0158-4020-836e-4676e480490f.png)