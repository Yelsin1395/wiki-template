## Preguntas
- Que fw de css has usado? Bulma, Bootstrap. Si necesitas cambiarlo, que harias? y para hacer un upgrade?.
- Tienes tu portafolio de websites?
- Cual ha sido la arquitectura mas compleja que has visto?
- Sabes que es una aplicacion isomorfica?
- Sabes .NET?
- Que consideraciones al diseñar o implementar una web debemos tener para el SEO?
- Que consideraciones al diseñar o implementar una web debemos tener para el buen performance?
- Que es un buen UX para ti? Que pagina actual te impacto mas su UX?
- conoces Google analytics?

## Prueba Tecnica

### Archivo
https://drive.google.com/file/d/1H0uXv8iqhfK5vsqNw8HXtf7tB6hU_0sc/view?usp=sharing
(El test esta en drive xq gmail bloquea zips con .js)

###Instrucciones
Modificar este web y reformar la UI como mejor te parezca.
- Deben usar Bulma y Vue.js
- Pueden usar extensiones de Bulma o componentes de Vue.js
- Si necesitas modificar Bulma, lo haras a traves de un css adicional, sin tocar Bulma original.
- Para los archivos de data (.json), no debes modificar su estructura pero si podrias agregar mas paises si asi lo quieres. Recuerda que en esta "foto" aparece un pais o dos, pero podria aparecer hasta tres paises.
- La UI la puedes modificar como tu quieras PERO la data debe mantener su misma estructura (osea el backend siempre va a devolver esa estructura y eso no lo podras cambiar).
- Si los componentes UI se pueden mejorar o si deseas usar webpack o etc, eres libre de hacerlo. De nuevo, lo importante es que el backend quedara fijo y las tecnologias base tambien. Todo lo demas puedes cambiarlo.

Tienes maximo 3 horas y mandarlo al final del tiempo, este finalizado o no (y si no esta finalizado, al menos deberia poderse ver a traves del codigo como iba a quedar).
TIP: Prioriza y arma una estructura base para que al final del tiempo igual se pueda ver un esquema de trabajo.

NOTA: Al desarrollar en local, el CORS impedira que cargues los archivos. Para lograrlo, tienes que subir tus archivos a un servidor web local. Puedes usar IIS o alguno de preferencia.

TIP: En Chrome instala una extension llamada Web Server for Chrome que es un servidor web local. Al abrir la extension, configurar la ruta local de donde se servira los archivos y con eso ya puedes acceder a http://127.0.0.1:8887/.

### Explicacion de la Web
Es un dashboard para visualizar algunos de los KPI de Juntoz.

Seccion 1:
GMV: ventas brutas por pais
NMV: ventas netas por pais (luego de retirar las canceladas)

Seccion 2:
Resumen de los estados de las ordenes por pais

Seccion 3:
Resumen de las ordenes canceladas por pais

Los paises son 604 Peru (PEN), 170 Colombia (COP), 152 Chile (CLP).

Sera usado en todos los tipos de dispositivo.

### Privacidad
El test es privado solo para Juntoz.com. No podras utilizarlo como parte de tu portafolio. La data representada no es real.