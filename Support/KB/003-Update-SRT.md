**Paso 1 : UbigeoZone**

Según la cantidad de zonas distintas en la hoja “Zona”, se prepara un insert en la tabla UbigeoZone por cada uno.

![image.png](/.attachments/image-b5d6573a-a638-4830-a8e3-c4ca20b3461b.png)

Con un editor de texto o un excel, se prepara los insert de tipo:

![image.png](/.attachments/image-4bf27ce9-738f-4a3e-b540-ff1a23f79f83.png)

El resultado se observa de la siguiente forma:

![image.png](/.attachments/image-837e3f46-3ef3-4950-990f-93cdd8d80468.png)

**Paso 2 : UbigeoGrouping**

Según la cantidad de distintos ubigeos en la hoja “Zona”, se prepara un insert en la tabla UbigeoGrouping por cada uno. Es necesario contar con los ids generados en el paso anterior para cada Zona.

![image.png](/.attachments/image-b8bf962c-c838-41e8-bf37-918eb32fdf18.png)

Por ejemplo para el primer ubigeo : 150104 – Barranco

![image.png](/.attachments/image-151dee0c-bc23-4d3f-a9be-9f0008937b79.png)

Con un editor de texto o un excel, se prepara los insert de tipo:

![image.png](/.attachments/image-456feaad-2505-4f5f-9565-bda19410df81.png)

Para este ejemplo, se ha utilizado el id generado (85797) en los insert del Paso1, para la zona “LIMA URBANO”.
El resultado se observa de la siguiente forma:

![image.png](/.attachments/image-b787ca6d-b6e3-4b44-96ce-aca01753dfb4.png)

**Paso 3 : SupplierCost**

Finalmente en la hoja “SRT” se encuentra la informacion para inserts en la tabla SupplierCost. Es necesario contar con los ids generados en el Paso 1. 

![image.png](/.attachments/image-d35cc154-cebb-4cca-80af-5372031e1904.png)

Por ejemplo para el primer registro se prepara el insert:

![image.png](/.attachments/image-762f4dae-e978-44e3-ae95-630ade094d5c.png)

Para este ejemplo, se ha utilizado el id generado (85800) para la zona “LIMA URBANO”, el resto de datos se completan con la informacion del archivo (tarifa, horas, pesos, etc).

![image.png](/.attachments/image-4551b8a4-6734-4be0-846d-a311adb13ec0.png)

Tip: Es preferible utilizar una hoja de calculo para la creacion dinamica de este query. El fabuloso comando BUSCARV, te soluciona la vida.
