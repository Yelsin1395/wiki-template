# Que hacer cuando hay un nuevo ingreso?

## Unos dias antes
1. Definir el dia y la hora de arribo, por ejemplo Lunes 9am.
1. Definir una persona que la reciba y la acompañe.
1. Pedir computadora y sitio

## Un dia util antes
1. Coordinar training inicial con arquitecto y team lead

## El mismo dia
1. Crear email e.g. juan@ieholding.com > Pedir a Oscar Arevalo o Kat Lim Ruiz
    - Agregar al grupo devteam@ieholding.com
1. Crear usuario en Jira
    - Invitar con su email
    - Agregar al grupo jira-software-users (por defecto)
1. Invitar a Slack: Project Eagle Peru
    - Invitar con su email.
    - Agregar a grupos "devteam" y "#bugreporting".
1. Admin: Crear member user en Portal Azure
    - Entrar a portal.azure.com > Azure Active Directory > Users
    - Invitar con su email xx@ieholding.com o xx@juntoz.com
    - Agregar a Azure Group: Juntoz-Dev-Team
    - Si tiene un rol de liderazgo, agregar a grupo Juntoz-Azure-Prod-Contributors
1. Admin: crear usuario en Azure DevOps (source code) **>> IMPORTANTE: el usuario en Azure debe estar creado y con su password ya estable.**
    - Invitar con su email (https://dev.azure.com/eagleperu/_settings/users)
    - Configurar cuenta como Basic y agregar paquete Package Management (para poder acceder y bajar los azure artifacts).
    - Agregar a grupo: Juntoz Developers
1. NO CREAR UNA CUENTA MICROSOFT para encapsular la cuenta de ieholding. Esto empeora las cosas.
1. Agregar al grupo de Whatsapp EagleTech

### Dependiendo de la necesidad:
1. Crear usuario en algolia.com
2. Crear usuario en postmarkapp.com
3. Crear usuario en papertrail.com
4. Asociar usuario a https://developers.facebook.com/apps/486126978242899 (facebook login, separado de la cuenta de marketing de Juntoz en fb)

### Para poder bajar el codigo fuente
Actualmente 03/2019 ya todas las cuentas fueron migradas al Azure AD Juntoz Peru y la org eagleperu de AzureDevops tambien fue migrada y conectada a este Azure AD. De esta forma en teoria bajar el codigo debe ser mas facil y los nuget packs tambien seran posible acceder a ellos.

Sin embargo, posiblemente debido a como esta diseñado Azure Devops (y todo el mess que es las cuentas de Microsoft personal, work, azure, etc), aun hay algunos conflictos para bajar el codigo.
Esto se debe a que hay dos capas de seguridad, 1. AzureDevops y luego 2. Git.

Entonces para 1, se usa el usuario de AzureDevops como siempre, pero para el segundo este password no funciona. Para que puedas bajar el codigo, hay que crear un Personal Access Token que cada usuario debe crear.

Para crear un PAT debes:
1. Ir a Azure Devops (dev.azure.com/eagleperu)
1. Esquina superior derecha, icono de usuario > Security
1. Personal Access Tokens, generar
1. Escribir una fecha de vencimiento (a un año?)
1. Guardar ese PAT dado que es tu contraseña de git.

Entonces al hacer un `git clone`, va a ocurrir lo siguiente:
1. Aparece una ventana de Azure Devops para meter tu usuario y password de ADevops.
2. Luego aparecera un input box **feo** para introducir tu mismo usuario, pero usando tu PAT como password.
3. El codigo bajara.
4. Lo mismo para hacer `git push`.

NOTA: Es probable que debido a esto ya no se puede usar Visual Studio 2017 como cliente git. Otros funcionan mucho mejor como Visual Studio Code, TortoiseGit.

En teoria este proceso de doble password, se elimina si se usa el Git Credential Manager (setting por defecto cuando instalas Git y Visual Studio). Personalmente (Kat Lim) a mi nunca me ha funcionado. GCM crea un PAT automaticamente.

Sin embargo, si usaramos GitLab o otro servicio que este mejor hecho, no tendriamos estos problemas. Es un tema de Azure Devops, desde mi punto de vista.

NOTA: ahora que ya tenemos un solo Azure AD (antes teniamos dos y por eso GCM no funcionaba), ya GCM funciona bien segun lo que vi. Eso lo puedes configurar usando TortoiseGit.

### Insertar a grupos
[Estructura de seguridad de Azure](/Home/Devops/Estructura-de-seguridad-de-Azure)
[Estructura de seguridad para Azure Devops (codigo fuente)](/Home/Devops/Estructura-de-seguridad-para-Azure-Devops-\(codigo-fuente\))

## Onboarding
- Leer [Proceso de Desarrollo](/Home/Proceso-de-Desarrollo).
- Training:
  - Que es Juntoz? Juntoz Tech vs Juntoz Mall
  - Empresa: RRHH, Marketing, SAC, etc.
  - Sobre Proceso de Desarrollo (especialmente interaccion Dev y QA)
  - Sobre Equipos
  - Sobre Arquitectura Global de la plataforma


# Dar de baja
El proceso lo inicia HR.

Usar la misma lista de ingreso pero en sentido contrario.

2018-12: hasta hoy no hay ningun paso adicional necesario