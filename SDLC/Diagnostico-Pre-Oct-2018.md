# Diagnostico
El diagnostico hecho en los primeros dias de Octubre 2018 acerca del departamento de Tecnologia de Juntoz arrojo las conclusiones que siguen a continuacion.

## Equipo de Desarrollo
El equipo de desarrollo fue diezmado con las renuncias de 5-7 personas en un plazo de 6 meses (Fabricio, Brayan, Cristian Changa, Sikiuth, Cesar, X, Y). Esta fuga de talentos no solamente ocasiono la perdida de capacidad de desarrollo pero tambien la perdida de conocimiento valioso acerca de la plataforma.

Esto afecto especialmente el conocimiento sobre Catalog, Whitelabels, Mobile y Checkout. La velocidad de recuperacion de esa capacidad (incorporar nuevo personal) fue y es baja (Ene 2019).

El equipo encontrado estaba conformado de la siguiente manera:
- 1 solo equipo de 8 personas (1 team lead/developer, 1 semi-qa, 1 soporte, 6 developers). Ademas de 1 CTO.
- Cada developer era owner de un API (Catalog, Identity, Coupons, etc).
- El CTO se encarga del Azure y su mantenimiento.
- La unica persona que testeaba no tenia ni instruccion ni experiencia como QA (aunque cuando testeaba, todos coincidian que encontraba varios bugs).
- Una persona solamente veia el soporte (correccion de data, ejecucion de scripts) y esto le tomaba TODO el dia.

###Lo bueno
El equipo esta muy comprometido con la empresa, daban mas de 8 horas al dia y fines de semana.
El animo entre todos tambien es muy bueno, mucha camarederia y buen humor.
El apoyo mutuo tambien era palpable y les gustaba trabajar en equipo (a pesar que el proceso no lo promovia).

###Consecuencias
- Al especializar a todos los developers en una sola cosa, ocasionaba que el conocimiento este fragmentado y las tareas eran asignadas por persona y nadie era realmente responsable del end-to-end.
- Habia una sola persona encargada de front-end development lo que ocasiono que esa persona sea la unica disponible para hacer eso, creando un cuello de botella.
- El team lead no tenia el tiempo ni el empoderamiento para liderar el equipo (el CTO lideraba el equipo) dado que tenia tambien que desarrollar.
- La persona asignada a soporte no iba a crecer en su rol. Ademas, nadie conocia la real dimension del esfuerzo de lo que hacia.
- Todos ejecutaban tareas en solitario sin coordinacion.
- Al casi no haber testing, ocasionaba que los developers subian codigo a produccion sin las pruebas completas necesarias o sin todos los flujos necesarios.

## Proceso de Desarrollo
El proceso de desarrollo implantado era informal.
- No habia ningun issue tracker.
- Los requerimientos venian por email, en persona, telefono.
- Los requerimientos NO eran priorizados bajo un criterio unico. Cada desarrollador tomaba el requerimiento y lo empezaba a ejecutar. Muchas veces sin notificar a los demas.
- No habia planeamiento por sprint o periodo.
- Casi no habia rechazos de requerimientos dado que nunca pasaban por un proceso de analisis y diseño.
- El developer era el encargado de hacer el testing de su desarrollo y el pase a produccion se hacia "libremente".
- No habia retrospectivas (donde analicen el proceso).
- No habia reuniones diarias de sincronizacion (cada desarrollador iba por su lado).
- Los tickets se registraban post-desarrollo (!!!) y solo los trackeaba Sikiuth.
- No habia pruebas oficiales (no habia testers) ni menos un analisis de casos de prueba.
- Las pruebas que se hacian eran las que el developer podia pensar y su rigurosidad era bastante baja.
- El desarrollo se hacia bajo el metodo cascada: se acaba todo el desarrollo primero, y de ahi se hacen las pruebas (las que se puedan, se nos ocurran o las que alcance el tiempo).
- Dado que no habia rigurosidad en las pruebas, habia codigo que entraba a Produccion sin probarse (inaudito!).

###Consecuencias
- No se sabia el estado de un desarrollo.
- Los desarrolladores ejecutaban tareas que podrian no ser prioridad en su momento, solo porque 1) le dijeron en ese momento 2) era mas "facil" o "divertido" de lo que estaba haciendo.
- Esto ocasionaba esfuerzo perdido o que diverge versus los objetivos de la empresa que realmente son mas prioritarios.
- Al no haber equipo, ni filtro, ni priorizacion, cada desarrollador iba por su lado (como si cada uno tuviera una cola de atencion propia). Si habia otro desarrollador que este luchando por lograr un objetivo mayor o mas prioritario, esto no se atendia (probablemente ni siquiera se levantaba la bandera, ya que no habia sincronizacion diaria).
- Entraba codigo fuente sin probarse por lo que aun ocasiona bugs o casos de prueba que demostrarian que el codigo funciona bajo muchos escenarios (NOTA: hoy el codigo tiene varias partes hardcoded que no hubieran resistido pruebas mas exhaustivas).

## Prioridades del Equipo
Al llegar, todo el equipo estaba abocado en terminar los whitelabels de Billabong y Samsung Partners.
Sin embargo, estos whitelabels se han ido desarrollando desde Abril 2018 y aun no finalizaban completamente.
La gran consecuencia de esto es que todos los procesos y areas internas se quedaron abandonadas, igual que la web de juntoz.com y mobile, juntoz.com.co y juntoz.cl. Entonces el backlog se habia quedado congelado por meses sin avanzar y las areas tuvieron que resolverlo como podian.
Su solucion fue que el equipo de BI (Daniel Huerta) les provee la data o los reportes necesarios. Esto en si mismo tiene una consecuencia muy grave que es que 1) el equipo BI esta otorgando data en vivo, cuando ellos solo deben otorgar data offline. 2) no tienen conocimiento de la forma y fondo de la data lo que lleva a malas interpretaciones 3) al no estar atendidos por Tech, ellos repetian la logica de negocio que el sistema transaccional debe encapsular (BI debe obtener data "comidita" para que la exploten directamente).

Como se dijo, la consecuencia mas grave de todo esto es el abandono de todas las aplicaciones propias de Juntoz y de las que dependen todos los white labels asimismo.

## Freelancers
Los freelancers (Fabricio, Cristian Ch y Brayan) tenian acceso indiscriminado al codigo fuente, a Azure y la base de datos y lo que hacian lo subian directamente a Produccion sin necesidad de pruebas o revision por parte del equipo oficial.

Esto traia consecuencias fatales:
- Codigo que nadie sabia que hacia entraba a Produccion. Y cuando habia fallas, el equipo tenia que solucionarlas sin saber que hacia.
- Codigo que no estaba testeado debidamente entraba a Produccion.
- La responsabilidad legal caia en el equipo cuando ellos no habian hecho el cambio.
- El diseno de las soluciones no correspondia a estandares establecidos por el equipo.
- Ademas que muchas veces los disenos tenian fallas y huecos de logica por falta de tiempo de analisis.
- Los freelancers eran contactados por las areas de negocio y hacian cambios sin consultarlos con Tech. A veces cambios grandes.
- Aunque suene mal, pero la intencion de los freelancers es hacer mas horas no es solucionar los problemas de los usuarios necesariamente. Entonces vimos tareas ya encomendadas de desarrollos que no tenian ningun sentido y que tampoco correspondian a las prioridades de Tech ni del negocio como un todo.

# Cambios realizados
Entonces en base a este diagnostico, los siguientes cambios fueron realizados:

## Product Owner
En este caso, despues de ver mas de cerca como se iba haciendo las cosas y los proyectos, plantee que Jean Diego Banon sea el PO. Hable con el primero para contarle que haria y el control y especialmente el impacto que tendria (que ya lo tenia pero solo para white labels). Se intereso luego de hacerme varias preguntas acerca de como actuaria, etc. Esto tambien lo converse con Fernando y estuvo de acuerdo.

El gran beneficio de esto fue que ahora si el equipo estaba coordinado y enfocado hacia la prioridad del negocio y no necesariamente lo que cada uno queria u opinaba.

## Scrum: Sprints
Antes la ejecucion era continua y sin cortes. Esto ocasionaba que las prioridades cambiasen a cada rato y sin un criterio unico.
Al enmarcarlo en sprints, podiamos tener un planning, una demo y una retro; un objetivo claro al final del sprint y una cadencia que el equipo necesitaba.
Se sentia (aun cuando yo no programaba) un desorden palpable y generalizado, y un caos que ocasionaba inseguridad en todos.
Definiendo una metodologia, ayudaba a que todos dejen de distraerse en coordinaciones y enfocarse en entregar valor.

Aunque al inicio fue dificil establecer los sprints y la cadencia debido a los compromisos previos (billabong, columbia y samsung), cuando terminamos pudimos ya empezar a dividir en stories y planear por sprint. Y las cosas empezaron a ordenarse mejor.

## Scrum: Retrospectivas y 1:1
Implamantando retrospectivas pudimos darle voz al equipo y un espacio para que se discuta los errores y soluciones para que todos puedan plantearlas y comprometerse.

Hacer reuniones 1:1 mensuales con cada uno del equipo tambien generaba un espacio de comunicacion que antes no habia y un lugar privado donde se podian discutir cuestiones mas privadas y feedback bi-direccional. De las reuniones 1:1 se saco informacion valiosa que normalmente no aparece en el dia a dia: incomodidades, feedback positivo tambien de los cambios que se estan haciendo.

## Creacion de equipos
Se crearon dos equipos, A (online) y B (offline). Los equipos mismos definieron los miembros bajo unas pautas y ha funcionado bien. Antes, al no tener equipos, no habia puntos de referencia ni relaciones pre-establecidas que haga que las conversaciones y preguntas fluyan mas rapidamente y mejor.

## Team Lead y Arquitecto
Se creo el puesto de Team Lead para ser lider de los dos equipos formados, para ser punto de referencia del proceso (Scrum), funcional y management.
Se creo el puesto de Arquitecto para ser lider tecnico de desarrollo.

Estos dos puestos eran necesarios para poder dar autonomia a los equipos y que hayan puntos de referencia establecidos oficialmente (y no haya dudas a quien preguntar).

Sin embargo, para que funcionen, el proceso de desarrollo tambien tenia que cambiar. El Team Lead ya no iba a tener responsabilidades de programacion solo liderazgo del equipo y la entrega de valor. De esa forma se podia concentrar en eso solamente y hacerlo eficazmente.

Igual el rol de arquitecto, tiene que salir del ciclo de desarrollo comun dado que los cambios que hara en el codigo son mas profundos y no pueden tener presion del tiempo.

## Control sobre Freelancers
Todo pedido a los FL se hace a traves de Tech. Tech decide si lo manda a ellos o lo hace el equipo oficial.
Los pedidos a los FL se hacen solamente para funcionalidad especifica y ya disenada. No se usaran FL para desarrollos grandes o riesgosos.
Los FL debera enviar un reporte a fin de mes con las tareas y horas realizadas.
CTO o Lider de equipo pueden aprobar este reporte.
Solo se pagara por desarrollo que ha sido finalizado y aprobado por code review y su testing se haya realizado satisfactoriamente. Bugs encontrados en las pruebas seran reportados y devueltos a los FL para su correccion.