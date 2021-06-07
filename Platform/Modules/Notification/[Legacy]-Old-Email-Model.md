El modulo de email templates vive dentro de api cms. En general, depende de Postmark, aunque se podria abstraer el servicio de envio y de mapeo de templates con otra implementacion. Por el momento no vemos esto como necesario ya que postmark funciona bien para el caso de uso.

## Data

Las tablas son:
- EmailServer: Hace un match a los servers en Postmark, asi como tiene la logica de que Pais o WL pertenece el servidor y su conjunto de templates. Por ej. Colombia es -170 y WL thebox 1034. Peru, es un caso raro debido a legacy, pero se encuentra ambos en el -1 y el -604.
El camino hacia adelante deberia ser: -604 para Peru, y el -1 para uso general de templates que se puedan compartir.
- EmailTemplate: Hace un match a los templates en Postmark. Para hacer la busqueda se usa el campo Type, el cual es un string con un key definido para ciertas acciones a enviar email.
El campo TemplateCode hace el mapeo a Postmark.
El campo IsMaster nos indica si es que es parte de los templates generales, ie. acciones genericas implementadas en toda la aplicacion para los cuales un WL o pais nuevo deberia tener una implementacion.
El Subject, HtmlBody y TextBody, son para lectura desde nuestro lado, la idea es que deberian ser igual a lo que esta en Postmark. Cuadno se actualiza un template, tambien se actualiza en postmark. Por eso si editamos directo en BD, o se cae la integracion a postmark, el template en realidad no se va actualizar. El template real sale de lo que esta guardado en postmark.

## Como llamar
Para usar el email templates y jalar la info de un template, instalar el nuget Eagle.Rest.CMS.Proxy y usar el comando GetEmailTemplate(siteId, type)

** Este modelo ya esta deprecado al 90% (hay algunas funcionalidades que no estan aun migradas porque ya no se usan del todo como Subscribe and Save). Asi que el nuevo modelo sera descrito en otro articulo. **