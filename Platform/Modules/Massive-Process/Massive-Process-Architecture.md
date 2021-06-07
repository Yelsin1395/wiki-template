Este apartado describe como debe ser el funcionamiento y arquitectura de un proceso masivo.

![Upload Massive Engine - Juntoz.png](/.attachments/Upload%20Massive%20Engine%20-%20Juntoz-c63a6457-6111-4038-bace-d28a7b2b6f6a.png)

## MicroSiteMaster
Su papel en este escenario es recibir las notificaciones del **LongProcess**, recibe tres tipos de mensajes start ,error y endprocess, también contiene el microSite de la entidad que cargara el Excel, en este ejemplo es el micrositeProduct.


##MicroSite{Entity}
- ###FrontEnd
1. Desde aquí se iniciará el proceso masivo subiendo un archivo Excel desde la UI. Cada proceso masivo tendrá su **témplate de Excel** para que lo usuarios sepan como realizar sus cargas de la manera correcta y el proceso sepa como mapear los datos que necesita.
2. Luego de cargar el Excel el front consume un endpoint del backend.

- ###BackEnd
1. A través de un endpoint recibe el Excel y los datos necesarios para su procesamiento, este envía el Excel al **LongProcess** a través de su método **startProcess** el cual le devuelve un **processId** este processId es necesario para poder dar seguimiento al proceso hasta su finalización.
2. Luego de obtener el processId , el Excel es convertido a Json y es enviado a su azure function correspondiente.

##Azure Function
Es encargado de mapear y procesar el jsonExcel, durante el procesamiento o iteracion se debe comunicar con su **ApiCore** correspondiente para ejecutar la acción que desee, también se comunica con el **LongProcess**.

##ApiCore{Entity}
Esta api es encargada de ejecutar la acción por el cual fue creado el proceso masivo, se deben reutilizar los mismos métodos que se usan para la misma acción en el SiteCentral.

##LongProcess
Esta api es la encargada de loguear todos los pasos durante el procesamiento desde inicio a fin, esta usa también el motor de notificaciones para notificar al micrositeMaster el inicio y termino del proceso masivo.