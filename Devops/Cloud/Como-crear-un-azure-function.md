# Introducción

Un Azure Function es la nueva moda en cloud y es un componente buenísimo y muy barato.

En Juntoz, los usaremos basicamente para ejecutar tareas en background como colas o suscriptores de topics.

Tienen algunas caracteristicas que hay que considerar
- No pueden ejecutar por mucho tiempo. Como estándar estamos poniendo no más de 90 segundos.
- Escalan horizontalmente al infinito asi que hay que tener cuidado a los resources que acceden (como SQL Server o CosmosDb) dado que estos deben poder copar la demanda.
- Hay un atributo llamado `maxConcurrentCalls` que controla este escalamiento asi que puede controlarse de esta manera.

# Como crear uno?

Primero, considerar que uno crea en realidad un "módulo" de Azure Function y dentro de este módulo pueden coexistir varias funciones mientras mantengan el "diseño" (http, service bus, etc).

Ir a Azure Portal, en la sección Azure Function dar click en Crear.

Usar el resource group: `juntoz-rg-production-function` o `juntoz-rg-staging-function` según el ambiente.

El

IMPORTANTE: Cuando el wizard pide un storage donde guardar los archivos usar
`juntozazfuncstg` o `juntozazfuncprod`. NO CREAR OTRO.

Usar stack `NodeJs`

Usar plataforma `Windows`

NO activar Application Insights (por ahora)