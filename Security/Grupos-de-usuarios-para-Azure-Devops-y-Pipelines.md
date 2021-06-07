# Grupos de Usuario para Azure Devops y Pipelines

Una vez creado el usuario, hay que insertarlo en un grupo para poder empezar a trabajar.

El grupo por defecto debe ser `Juntoz Developers`.

Si es un freelancer, agregar a `Juntoz Freelancers`.

Estos grupos ya estan configurados en los respectivos proyectos y con los permisos correctos.

Definiciones:

* Juntoz Owners: Grupo que es owner de toda la organizacion y proyectos.

* Juntoz Staging Approvers: Grupo que permite deployar a Staging. Juntoz Developers esta incluido dentro.

* Juntoz Production Approvers: Grupo que permite deployar a Production. Juntoz Owners esta incluido dentro ademas de algunos usuarios sueltos (lideres de equipo y arquitectos). Este grupo es administrador de los proyectos, builds, releases, etc.

Para cada STAGE (Staging, Production) de un nuevo release, debe setearse como owner y approver a Juntoz XX Approvers (segun cada ambiente). Esto es importante para que no se pierda el control de acceso de cambios a Produccion.

***TO DO: A mejorar la definicion del team por defecto del proyecto (Juntoz Team, WhiteLabel Team).***

# FAQ

## Nuevo developer?
Agregar a Juntoz Developers

## Nuevo freelancer
Agregar a Juntoz Freelancers

## Nuevo build?
No hay configuraciones necesarias

## Nuevo release?
Para Staging, owner y approver deben ser Juntoz Staging Approvers
Para Prod, owner y approver deben ser Juntoz Production Approvers