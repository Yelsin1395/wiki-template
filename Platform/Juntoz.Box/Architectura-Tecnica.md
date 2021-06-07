# Introduccion
Juntoz.Box tiene varios componentes que interactuan entre si.

1. box-app: La caja web que es una aplicacion php con el framework Laravel como base.

2. box-sync: Una aplicacion de consola que permite sincronizar los temas que se guardan en SiteCentral hacia el box para que se apliquen y se muestren al usuario final.

3. Una imagen docker llamada jzbox que contiene un servidor web Apache, la aplicacion box-app corriendo dentro del mismo y un cronjob (tareas programadas por tiempo) que ejecuta box-sync cada 1' o 5' (a definir).

Esta imagen docker se puede utilizar asimismo para hacer desarrollo local.

4. Un helm chart para su instalacion dentro de un cluster kubernetes y que instala basicamente los siguientes componentes:
  - Un deployment con un pod jzbox.
  - Un service que se conecta al pod.
  - Un ingress nginx que funciona como reverse proxy y que se conecta al service.
  - La configuracion de cert-manager (ya instalado en el cluster) para la emision del certificado digital automatico con Let's Encrypt.

Utilizando el helm chart permitira crear o eliminar una aplicacion rapidamente en un cluster kubernetes para que este online. 

5. Es probable que requeriramos un servidor commander que permita ejecutar los comandos de helm sobre el cluster de kubernetes. Este servidor master recibiria la peticion por una cola y los ejecutaria. Por ejemplo, helm install, helm uninstall, helm upgrade.

## Proximamente
Para lograr una actualizacion rapida de los temas (themes) se quiere implementar un modelo pub/sub con un servidor redis, que permitira generar un evento cuando los templates fueron modificados y asi, la caja puede ejecutar la sincronizacion de los temas en ese mismo momento.

# Como interactuan los componentes?

## Onboarding y creacion de la aplicacion
Para que la aplicacion sea creada, los siguientes datos ya deberian estar disponibles:
- Site Id
- Site Code: Codigo corto del site
- Site Domain: Dominio naked del site (ej: google.com, juntoz.com). www.google.com NO ES NAKED.
- Site Client Id Interactive: Id del openid client marcado como interactive.
- Site Client Secret: Secret del openid client.
- Site Email Tech: email del site para recibir notificaciones acerca del dominio

1. (En teoria) Una peticion entra al box commander con la informacion del site, lo que ejecutara el comando `helm install`.
2. Con esto, se creara los componentes declarados dentro del chart de helm.
3. El mas importante es el ingress dado que sera la puerta de entrada a la aplicacion. El box commander debe poder retornar el IP publico de este ingress para poder mostrarselo al administrador del site.
4. (Offline), el administrador debe usar este IP para que el DNS del siteDomain se configure y apunte a este IP.
5. Se supone que cuando el DNS sea configurado, el certificado deberia poderse generar automaticamente (que toma a veces un poco de tiempo) y el dominio ya se deberia poder acceder (https://midominio.com).

Sin embargo en este punto, el site estara online pero NO mostrara ningun contenido dado que no hay un theme escogido.

*** NOTAS: Aqui tambien deberia ser posible asignar un dominio por defecto automatico para que el usuario pueda hacer pruebas antes de comprar el dominio final.

## Visualizar el theme
A traves de SiteCentral, el usuario podra crear su propio theme o subir uno ya hecho (y en el futuro podria comprarlo desde un Marketplace que algun third-party developer ha creado).

Cuando el usuario sube los archivos, entra el cronjob del box (o el pub/sub de redis) y que obtendra la lista de archivos a bajar y los bajara a la carpeta correcta del box donde se guardan los themes.

Cuando los themes se actualizan, la aplicacion web no necesita reiniciarse dado que una aplicacion php siempre ejecuta todo el flujo cada request y va al folder fisico de themes para ejecutar los archivos.

## Reinicio del contenedor Docker
Hay que recordar que el box es un container Docker STATELESS por lo tanto cuando la caja se reinicia por algun motivo, todos los archivos son borrados y se debe bajar de nuevo los archivos del template.
Para evitar downtime, se debe implementar lo siguiente:
1. Ejecutar el sync ANTES de que la caja se prenda
2. Habilitar el probe READY para que Kubernetes verifique esto y el box verificara si esta listo para ejecutarse realmente.

# Como se manejan los upgrades?
Como ya vimos, los themes solo necesitan actualizarse y estos entra a la caja automaticamente. Pero los cambios al proyecto jzbox y el chart de helm deben si generar un upgrade de version.

Cuando algun componente ha sido actualizado (boxapp o boxsync), la imagen de Docker debe ser actualizada. 

Cuando una imagen de Docker es actualizada, se le debe incrementar su semver para alguno de los tres numeros major (cambios mayores y/ breaking changes), minor (cambios medianos pero que no cambian la firma de entrada o salida) y revision (cambios menores o bugs). 

La buena practica en Docker es que se publique con tres tags distintos: jzbox:0, jzbox:0.1, jzbox:0.1.3. De esta forma, el tag major siempre apuntara a la ultima version de la imagen con major 0.

En el chart de Helm se apunta a la ultima version major estable. Y de esta forma se encadena 

Y adicionalmente, dentro del chart de Helm se debe usar solamente la ultima version major estable (no las minor ni revision, al menos en un chart oficial), de esta manera siempre apuntara a la ultima version de la imagen.




Y dentro del chart de Helm se usara siempre la ultima version major estable, por lo tanto ahora esta como jzbox:0. De esta manera cada vez que el pod reinicia, sacara siempre el ultimo version minor y revision de la version 0.

Cuando se haga una nueva version major 1, se actualizara todas las instalaciones de Helm usando `helm upgrade`

1. El chart de Helm: cuando algo tiene que cambiarse sobre la estructura de los componentes fisicos (resources de Kubernetes) o algun nuevo parametro de entrada, el chart de Helm debe modificarse, subirlo de version y publicarse.

Un 












