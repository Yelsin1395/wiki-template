Esta api es la encargada de loguear todos los movimientos que se realizan durante un proceso masivo, notifica solo el comienzo y fin del proceso masivo.

Para realizar logueo o el seguimiento del proceso masivo contamos con un logueo para el proceso principal y para los items, estos logueos son guardados en distintos contenedores en cosmos.

![image.png](/.attachments/image-d13b5cb7-078d-4132-ac7e-4334b7f007ec.png)

## Endpoints
Contamos con 6 endpoints para cubrir los distintos sucesos durante el proceso masivo, 3 para el proceso principal y 3 para el proceso de los items.

###LongProcess
1. *startProcess* : Este endpoint es el encargado de iniciar el proceso masivo, recibe el Excel a procesar y lo carga en Azure storage, luego  inserta en cosmos los datos del proceso y genera un processId que es devuelto como respuesta.

2. *endProcess* : Este endpoint se usa cuando el proceso a finalizado ,setea el proceso en estado "Completed" o "Completed_error" si este finalizo con errores.

3. *errorProcess* : Se usa cuando el proceso no pudo si quiera empezar con su procesamiento y setea el proceso con el estado "error".

###LongProcessDetail
1. *startItemProcess* :  Se usa cuando inicia el procesamiento de un item.
2. *endItemProcess* : Se usa cuando termina satisfactoriamente el procesamiento de un item, tambien actualiza el contador de items procesados correctamente del proceso principal "successItemsProcessed".
3. *errorItemProcess* : Se usa cuando ocurrió un error al procesar el item, tambien actualiza el contador de items procesador con error del proceso principal "errorItemsProcessed".

