# Como instalar?

Ir a Bitnami.com, y crear una imagen Sonarqube en Azure.

Instalarlo como una VM
- sonarqube 
- username admin
- password VbRvkb2yELm6
- port 80, 443
- VM: STANDARD_A2_V2

Actualmente la VM es: bitnami-sonarqube-828d-ip.eastus.cloudapp.azure.com

PEM
[bitnami-azure-27f35131-c5c7-4217-a8f4-dd36ab5263ed.pem.txt](/.attachments/bitnami-azure-27f35131-c5c7-4217-a8f4-dd36ab5263ed.pem-1b705e66-6bac-482f-9f0b-3650f9bfaf33.txt)

PPK
[bitnami-azure-27f35131-c5c7-4217-a8f4-dd36ab5263ed.ppk.txt](/.attachments/bitnami-azure-27f35131-c5c7-4217-a8f4-dd36ab5263ed.ppk-2bd30319-d312-42ca-9fee-1e6b76c41e88.txt)

![image.png](/.attachments/image-7426a5a8-392c-4654-b707-3b6e0375b233.png =350x150)

ssh -i [your-private-key].pem bitnami@52.149.215.94

# Analysis token for JuntozPlatformV3
jzpv3-token: dbabee182738e091b2f86f76e1e1bfa65b8b868c (de usuario admin)

# Configuraciones


# Usar Azure Active Directory
Para poder manejar los usuarios a traves de AAD, hay que primero instalar el plugin dentro de Sonarqube.

Luego ir a Azure y registrar en Azure Active Directory un "App Registration".

Crear el App Registration como "juntoz-sonarqube" (o cualquier nombre).
Crear un Client Secret (apuntarlo que luego se ocultara).
Adicionalmente se pide un redirect uri pero tiene que ser https. Sin embargo, la VM no viene con certificado por defecto, por lo tanto hay que crear uno.

## HTTPS
Lo bueno de usar Bitnami es que tiene muchos utilitarios. Entonces en este link:
https://docs.bitnami.com/general/apps/sonarqube/administration/generate-configure-certificate-letsencrypt/ podemos generar un certificado gratis por LetsEncrypt y renovarlo todos los meses.

Para tener esto claro esta, primero necesitas un dominio. En nuestro caso usaremos un subdominio. 

En Cloudflare, insertaremos:

```
Un registro A que apunte a un subdominio de juntoz.com

A, sq, <IP de la maquina virtual>, DNS Only
```

Esperando unos minutos para que replique, entonces seguimos las instrucciones de Bitnami.

Al llegar a la parte de dominio, escribir `sq.juntoz.com`.

Preguntara si `www.sq.juntoz.com` se quiere registrar, responder "No".

Si dice que `sq.juntoz.com` no corresponde con el IP de la maquina, es que el registro A aun no se propaga del todo, hay que esperar (lo que hace el registro A es asociar el subdominio sq con el IP que uno inserte).

Una vez que el A hace match con el IP, pide "reiniciar el sonarqube y la maquina", hay que responder que "Si".

Si pregunta "quieres hacer redirect HTTP a HTTPS?", responder "No" (aunque de repente si valdria la pena?).

El utilitario preguntara por un email como administrador del dominio, usar `techsupport@juntoz.com`. (aunque en la VM actual use katlim@ieholding.com y no se puede cambiar facilmente, asi que se quedara con este).

El proceso dura unos pocos segundos, y luego resulta satisfactorio.

## Retomando el App Registration
Hay que entonces una vez que el servidor esta corriendo como HTTPS, vamos a Azure Active Directory y  a nuestro App Registration y configurar un Redirect Uri.

Este tiene que ser HTTPS.

Entonces seria `https://sq.juntoz.com/oauth2/callback/aad`, click en Save para guardar los cambios.

## Configurar en Sonarqube
Hay que insertar en Administracion, Azure Active Directory y llenar el ClientId, ClientSecret y TenantId.

Asimismo, tener activado la opcion SignUp. Esto es muy importante.

En Administration/General/Base Url, escribir `https://sq.juntoz.com`, sin slash al final.

## Probar la instalacion del AAD
Luego de esto se supone ya funcionaria.

- Ir a sq.juntoz.com.
- Login
- Login with Microsoft.
- Ingresar tus credenciales de Azure.
- Exito!

# Configurar Emails
SMTP host: smtp.gmail.com
User: tech-bot@ieholding.com
Pwd: t3chb0t999
Ir a la cuenta de tech-bot (que esta en Gmail) y configurar Access for Less Secure Apps ON.
De esta manera se habilita el SMTP.
