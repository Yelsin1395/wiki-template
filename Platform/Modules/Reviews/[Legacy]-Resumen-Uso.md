Con respecto a los KAM:

- En el MC como admin merchant.juntoz.com/#/admin/ encontrarán una sección nueva llamada Reviews.
* Resumen: muestra métricas generales por país y juntoz de lo que va del año actual.
* Tienda / Productos: son las calificaciones ponderadas que ha obtenido una tienda y producto
* Opiniones: son todas las calificaciones que se han obtenido y se podrán aprobar/rechazar según sea el caso
  * Deberían aprobarse las opiniones que tengan la naturaleza de ser una opinión ya sea positiva, neutral o negativa.
  * Deberían rechazarse los que sean del tipo pregunta (NO ES UN SISTEMA DE PREGUNTA/RESPUESTA)
  * Rechazar todo lo que sea SPAM (a criterio del responsable)
  * Por defecto el API de azure aprueba las opiniones cuando encuentra en la opinión palabras positivas.

Con respecto a los MC
- Para los MC tienen el mismo módulo solo que esta filtrado por su tienda
- Ellos no pueden aprobar/rechazar opiniones; De esta manera, evitamos que se beneficien solo con calificaciones positivas

Con respecto a las calificiones
- La escala de medición es de 1 a 5
- Positivos: >= 4
- Neutral: < 4 y >= 3
- Negativo: < 3

Validaciones
- Un usuario solo puede calificar un producto/tienda una sola vez
- Deberán esperar 1 minuto para volver a calificar

NOTA:
- Para verificar si hay un nuevo review para aprobar lo hacen desde el MC, pueden usar los filtros para hacerlo más sencillo.
https://merchant.juntoz.com/#/admin/reviews/all

