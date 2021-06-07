# Introduccion
Como startup, necesitamos un proceso de desarrollo que no entorpezca ni demore la entrega de valor hacia los usuarios.

En ese sentido, la industria esta adoptando los metodos de trabajo AGILES como Scrum o Lean/Kanban, y en Juntoz seguimos esa misma tendencia debido especialmente a la alta cantidad de cambios que son introducidos todas las semanas a Produccion (en otras palabras, es un ambiente de constante cambio).

# Como funciona?

1. Las areas de negocio asi como el Product Owner que esta en contacto con los clientes, inician los pedidos de cambios al traer una necesidad de usuario.
--- A veces podria ser un Epic si es que la funcionalidad es muy grande.
--- Otras veces es una tarea de soporte como modificacion de data o subida de archivos, etc. Esto se describira en la seccion Soporte Tecnico.
1. Estas necesidades las analiza o el Product Owner o el CTO y de esta forma se van refinando o rechazando. Asimismo se evalua el tipo (epic, bug, story, support) y su prioridad.
1. Segun su prioridad intrinseca y a la prioridad del negocio, cada item es reservado para su implementacion en un sprint dado.
--- Cambios en prioridades pueden ocurrir y el PO y/o CTO pueden decidir esto.
1. El proceso de desarrollo se basa en Scrum que inicia los Lunes y termina al segundo Viernes (sprint de dos semanas). Para mas detalle ver [aqui](/Home/Proceso-de-Desarrollo/Scrum-en-Juntoz).
1. Todos los fin de sprint se hace una demo donde se presenta que se hizo y se recibe feedback.
En detalle ver el link [Scrum](/Home/Proceso-de-Desarrollo/Scrum-en-Juntoz).

# Tipos de Issue
- Epic: cuando una necesidad es una lista de necesidades y que no puede entregarse en una sola Story.
- Story: funcionalidad nueva que el usuario necesita para poder lograr su objetivo de negocio a traves del sistema.
- Bug: error de procesamiento o ejecucion del sistema. Que es un error? El sistema ejecuta una logica (o una falta de) pero que no coincide con lo esperado. "Lo esperado" es decision de PO, buenas practicas, buenas costumbres, etc.
- Support: tareas que llevan a mejorar o corregir el sistema pero que no necesita de cambios en el codigo fuente, solo en la configuracion o en la data.

# Prioridades
- Highest/Showstopper: cuando una funcionalidad o bug es parte critica del flujo basico del sistema y que no se puede continuar sin ella. O cuando la necesidad de entrega es muy alta frente a un compromiso con algun cliente que no se puede tener un workaround.
- High: Cuando aquello que se ha pedido, tiene una alta importancia, pero o puede esperar o tiene un workaround plausible.
- Medium, Baja: importancia decreciente o los tiempos de espera pueden ser mas altos sin causar daño al usuario.
