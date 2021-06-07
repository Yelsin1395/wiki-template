![Untitled Diagram.png](/.attachments/Untitled%20Diagram-89925c72-34ba-49b5-b522-301dcbc2afed.png)

Scrum, como siempre, es un marco de trabajo. En Juntoz, tratamos de seguirlo pero adaptandolo a nuestras necesidades (equipos cortos, muchos cambios de prioridades, etc).

**Sprint = 2 semanas**. Actualmente el sprint oficialmente es de 2 semanas, pero el planning se hace por cada semana. Esto ha mejorado el compromiso del equipo y la lista de lo planeado vs lo hecho tiene muchisima menos variacion.

**Backlog**. El backlog lo controla el Product Owner pero con la intervencion del CTO o CEO.

**Definition of Ready**. Solo items (bugs o stories) que tengan su scope estable pueden entrar al sprint. Aquello que "no este Ready" se difiere al siguiente sprint.

Ready = 
Necesidad de Negocio Estable +
Estimacion de Dev y Qa + 
Test cases Core identificados (y definidos).

En este punto es importante mencionar que los test cases core identificados significa que el Tester tiene que hacer el diseno de las pruebas y conversarlas con el equipo para poder tener un agreement, y este es el input **MANDATORIO** para que el desarrollador empiece el desarrollo y esas pruebas core (o acceptance o flujo ideal) DEBEN ser ejecutadas como **unit testing** por el desarrollador.

Luego de que se llego a un acuerdo con las pruebas core, el tester debe seguir disenando las pruebas adicionales y que, tambien puede pedir feedback del equipo, pero no son un input mandatorio que desbloquea a otros.

**Definition of Done**
Done = 
Desarrollo Terminado +
Test cases ejecutados
Instalado en Produccion.

**Demo**. Se hace demos al final del sprint. Normalmente de desarrollo DONE, pero puede ser desarrollo en progreso. La inclusion de una story en la demo es un consenso del equipo que lo desarrollo. Por lo tanto, normalmente deberia estar probada antes de mostrarlo. Usualmente dura 1h.

**Retrospectiva** Se hace al final de cada sprint. Usualmente dura 2h (por sprint de dos semanas). Se verifica lo que se hizo en la retro anterior y se hace seguimiento. Se obtiene informacion de lo bueno y lo malo hecho en el sprint, y entre todos buscamos soluciones a estos problemas. Estas soluciones son priorizadas y las que si se pueden realizar, se introducen en el backlog del siguiente sprint.
_Se ha revisado este tema, y actualmente (2019-6) estamos haciendo una retrospectiva cada dos sprints, osea cada cuatro semanas. Estamos iterando que tanto sirve esto._

**Planning**. Se hace al final de cada sprint y en el, el equipo termina las estimaciones necesarias, divide los stories si es necesario y asume el compromiso de lo qeu se hara.
Como historial, los equipos no estan cumpliendo lo comprometido (hasta ahora en 2019-02) por lo que se esta empujando para reducir aun mas el compromiso.

**Dev y Test paralelo**. El desarrollo y testing deben ir de la mano y debe tratarse de ejecutar ambos el mismo dia o no tener una separacion maxima de 1 dia. Es decir, de lo que desarrollo en la manana, el testing se haga en la tarde. Y en el peor de los casos, lo que desarrolle hoy, el testing se hace manana. Esto garantiza que el dev y el testing puedan terminar juntos dentro del sprint y el DoD se cumpla.
Para lograr esto, hay varios puntos necesarios:
- El Ready debe ser solo y solo si, los test cases ya estan identificados y asi el developer puede hacer las pruebas necesarias (al menos las core) antes de subir su codigo al servidor. Si esto se garantiza, las pruebas hechas por QA seran mucho mas limpias (dado que no habrian showstoppers). Notar que estas mismas pruebas son las que un dev haria al desarrollar, **NO** se estan agregando nuevas tareas.
- Los test cases deben poder identificarse maximo en un dia. O al menos los Core. Ese mismo dia cuando los devs investigan o preparan su ambiente. El desarrollo no debe retrasarse por esto asi que es necesario que dev y tester esten coordinados.
- El developer debe hacer entregas de codigo pequenas y que den valor testeable para que se puedan probar. Debe haber un cambio de mentalidad: ya no programar secuencialmente (desde bd hacia adelante) o por modulo. El desarrollo debe primar el END TO END dado que es lo unico que realmente puede hacer que haya algo testeable y de valor para el usuario lo antes posible.

**No hay codigo en produccion sin testear**. Este principio basico aunque parezca facil de hacer, no lo es. Dado que es la costumbre de desarrollar lo maximo posible y dejar el testing para el final (lo que alcance). Esto rompe todo concepto de desarrollo agil y ademas rompe al equipo en dos (por un lado los devs y por otro los testers que tienen que cumplir fechas imposibles).
Si el principio no se rompe y la fecha de fin de sprint no se cambia, entonces lo que queda es reducir el scope de desarrollo para que lo que se entregue si se garantice que este debidamente probado.
Si hay codigo que aplica para dos flujos, pero solo se puede probar un flujo, el desarrollo debe limitar el codigo solo para ese flujo (aunque pueda parecer raro poner un `IF THEN` para luego quitarlo despues de unos dias).

**Estimaciones**. Las estimaciones las hace el equipo completo (devs y tester) y buscan cerrar el DoD para cada item. Esta estimacion debe hacerse antes del planning o ahi mismo. Por ahora lo hacemos por numero de horas/hombre (cuanto demoraria la tarea hecha por una persona).