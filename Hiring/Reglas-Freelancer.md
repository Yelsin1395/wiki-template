# Reglas

Los freelancers son personas que NO estan dentro de Juntoz pero estan contratadas para ejecutar tareas de desarrollo o testing en pos de los productos de Juntoz.

# Workflow

```
Freelancer Flow
Kat Lim <katlim@ieholding.com>	Thu, Oct 11, 2018 at 9:09 AM
To: Christian Changa <christianempire@hotmail.com>, vitokofabri@hotmail.com, brayan.hpe@outlook.com
Cc: Andres Acosta <andres.acosta@juntoz.com>, Oscar Adrian Arevalo <oscar@ieholding.com>, Fernando D'Alessio <fernando@ieholding.com>
Hola

Durante mi poco tiempo aqui, he visto un poco de desorden en general de quienes pueden acceder al codigo y hacer checkins, etc.

Como entienden, el gran problema es el governance del codigo y de cual codigo esta entrando a PROD, sin revisiones, ni testing, etc. Como les dije, esto no es un tema personal contra uds, es simplemente un tema de administracion de codigo basica en donde personas externas a la empresa no deberian acceder a el ni tampoco a los servidores y etc.

Hay temas de accountability (responsabilidad ante bugs en PROD, que sera del equipo oficial) y de propiedad intelectual. Si quieren podemos discutir eso mas a fondo.

Entonces, la idea es ordenar el tema. Reconozco la necesidad que estamos cortos en el equipo, pero no puede hacerse como sea.

Entonces el flujo que vamos a empezar a seguir es:

1. Solo tecnologia tiene acceso a uds y uds solo tienen acceso a tecnologia. Pedidos, emails del negocio, los derivan a Andres o a mi y nosotros lo verificamos primero. Asimismo, en algun momento creo que JD nos ayudara a hacer el triage de pedidos tambien.

2. Una vez que esta asentado el requerimiento de negocio, nosotros lo bajaremos a lo tecnico y le enviaremos un email con el requerimiento tecnico y de negocio, y en algun momento, los criterios de aceptacion minimo que el codigo debe cumplir (en terminos de usuario o negocio).

3. Por lo general seran cosas muy puntuales para poder minimizar el intercambio de comunicaciones necesarias.

4. Uds hacen el codigo, lo testean, cumplen con esos criterios de aceptacion y nos lo envian.

5. Haremos code review de su codigo. Si no pasa, lo corrigen y lo envian de nuevo. Hasta que pase.

6. Nosotros haremos la integracion y el testing del codigo. Solo se hara el pago si es que el testing pasa correctamente.

7. A final del mes, nos deberan entregar un reporte detallado de las tareas hechas (requerimiento, tarea realizada, fecha de inicio y fecha de fin, horas tomadas "exactamente").

8. Lo revisamos, aprobamos y lo mandamos a Oscar con las horas que se cobraran.

Este obviamente es un proceso perfectible. Suena medio parametrizado o duro, pero es lo tipico en un ambiente controlado de codigo. Empezaremos a usar mas herramientas como Jira, pull requests, y esto tambien fluira mejor, etc.

Andres sera el encargado de "liderar el equipo de freelancers". Como freelancers entonces implica que no pueden participar de la ruta critica del codigo ni de requerimientos grandes que tengan fechas limite duras. Espero que entiendan que no podemos tener una dependencia ni mediana sobre lo que uds harian (dado que nos bloquearia mucho).

Esto significa que se encapsularia a proof of concepts, bugs no criticos, mejoras pequeñas, etc.

Hoy borre los accesos desde ieholding para uds (que asimismo creo que no deberian tener un correo de ieholding ni de juntoz) y los he registrado como stakeholders con sus correos personales oficiales.

Si esto impide acceso al codigo, me avisan para corregirlo. Tendran acceso al codigo en modo read only solamente o les daremos un branch separado para poder hacer luego un pull request o el code review.

Cualquier pregunta o duda me avisan. De nuevo no es cuestion de cortar el tema ni un tema personal, sino solo de ordenarlo y que fluya mejor.

Gracias!

--
Kat Lim Ruiz
CTO
Juntoz.com
```

# Accesos
IMPORTANTE: Esta seccion tiene que estar coordinada con este link: [Nuevos Ingresos - Checklist](/Hiring/Nuevos-Ingresos-%2D-Checklist)

1. Coordinar training inicial con arquitecto y team lead
1. NO TENDRAN ACCESO A AZURE PORTAL
1. NO TENDRAN ACCESO A JIRA (a evaluar)
1. Crear usuario en Azure DevOps (source code)
    - Invitar con su email
    - Configurar cuenta como Basic y agregar paquete Package Management (para poder acceder y bajar los azure artifacts).
    - Agregar a grupo: Juntoz Freelancers
1. Se evaluara si invitar a Slack: Project Eagle Peru?
