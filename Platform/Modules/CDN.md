# Que es una CDN?
Content Delivery Network. En estricto, es una red de entrega de contenido geo-distribuido y que su objetivo es entregar contenido lo mas rapido posible.

Por ejemplo, si es que tenemos una imagen que esta ubicada dentro de mi servidor web localizado en EEUU con un tag `<a href="myserver.com/myimage.jpg">` y un usuario en Espana pide esa imagen, la imagen es obtenida desde mi servidor, cruza a traves de la internet y aparece en el navegador.

Si esta imagen, en cambio, estuviera instalada en una CDN, y el tag diria `<a href="mycdn.com/myimage.jpg">`, el pedido llega a la CDN, la CDN ubica el servidor mas cercano (localizado en Portugal) y la imagen es enviada desde ahi y ya no desde mi servidor de origen.

Esto tiene varios beneficios:
1. Baja la carga a mi servidor web.
2. Sube la velocidad con la que la imagen es obtenida por mis usuarios.

Claro, tambien tiene sus desventajas:
1. Muchas CDNs trabaja con cache por lo tanto, si el archivo cambia muy seguido, el cache puede crear inconsistencias en la experiencia del usuario (o hasta fallas en el sistema).
2. El cache puede tardar en refrescarse.
3. En teoria, tienes un punto mas de mantenimiento y de configuracion, aunque las ganancias en velocidad son mucho mayores que el costo.

En la nueva plataforma, estamos sentando las bases para poder tener una CDN propia con la que podriamos tambien ofrecer el servicio como un producto separado.

# CDN de JzTech
El primer objetivo de tener una CDN es poder ofrecer un buen servicio y rendimiento a nuestros clientes con respecto a las imagenes o archivos que son ingresados en nuestra plataforma.

Entonces cuando un cliente sube una imagen de un producto (por ejemplo), la idea es que la plataforma le de acceso automatico a esta imagen a traves de la cdn y asi aprovechar todas las ventajas ya descritas (mas velocidad, mas cercania, etc).

La idea es que a traves de la CDN podamos tener acceso a varios de los archivos estaticos que ya estan siendo ingresados y ademas lograrlo de una manera rapida, eficiente y sin ninguna interrupcion.

Desde el punto de vista del usuario final (el que navega en el ecommerce) y del usuario cliente (quien contrata el servicio de la plataforma), esto es una solucion completamente invisible (y vamos a pasar a explicarlo para que se vea como se lograra).

## IMPORTANTE: CDN vs plataforma
Es muy importante entender esto: el producto de CDN de JZTECH **NO ESTA DENTRO** de la plataforma de ecommerce como tal. Se les debe considerar como productos hermanos.

Por lo tanto, y aqui esta la clave, la plataforma de ecommerce SOLAMENTE conocerá el ambiente de produccion de la CDN, aun desde el ambiente de staging. Analogia: en mi ambiente de staging, cuando llamo al API de google, uso el API del ambiente de produccion de google. Nadie externo tiene acceso al ambiente de staging de Google. Aqui es lo mismo.

Entonces, la pregunta inmediata es: entonces como logro tener la cadencia para tener una version nueva para staging (en progreso) y mantener la version anterior para produccion (estable).

Facil, eso se logra **versionando el contenido.**

Entonces si tienes un archivo que va a evolucionar muchas veces, lo que debes hacer es versionarlo. Por ejemplo, si tengo un archivo `misitio.css`, lo que tendria que hacer es siempre crear una version nueva p.e. 1.0.6 y en mi aplicacion que esta en pleno desarrollo llamo a esta version 1.0.6, mientras mi aplicacion en mi propio ambiente de produccion, seguira usando 1.0.5. Cuando mi aplicacion es promovida a produccion, mi codigo contendra la nueva referencia a 1.0.6 y ahi se cierra el ciclo.

# Arquitectura
Este es el modelo de arquitectura que hemos creado para poder lograr el objetivo de que los archivos esten mas cercanos a los usuarios y aliviar la carga sobre nuestros servidores y/o azure storage containers.

![cdn.png](/.attachments/cdn-5461829d-1470-4c41-a25e-e44e03fb8647.png)

Actualmente, la plataforma tiene varios contenedores de Azure Storage cada uno con distintos objetivos. Los relacionados a la CDN son 3 contenedores:
- **jzprodstocatalog**: donde se insertan las imagenes de brands, products, stores. Estas imagenes son subidas por SiteCentral.
- **jzprodstocms**: donde se insertan los archivos de media library por site/store. Estas imagenes son subidas por SiteCentral.
- **jzprodstocdn**: este es un contenedor especial que usamos para poder subir archivos de la plataforma o tambien de UX de cada site. No tiene una UI a traves de SiteCentral y son mas relacionados al desarrollo directamente. Para subir archivos aqui, se debe seguir un procedimiento especial.

Entonces la idea es que todos estos archivos puedan ser accesibles a traves de la CDN.

Lo tipico seria tener un proceso en background que subiria estos archivos automaticamente a la CDN (que funciona como una base de datos o un folder compartido de archivos publicado en internet). Sin embargo, vamos a usar otra solucion.

Desde hace tiempo, Juntoz usa un producto llamado Cloudflare que es basicamente un operador de DNS y ademas un sistema de proteccion DDOS. Ademas de esto, CF tiene tambien un producto llamado Cloudflare Workers que permite crear una funcion serverless que se le conecta a una entrada DNS del dominio y te permite capturar el request, inspeccionarlo y hasta modificarlo o redirigirlo a otros servidores.

Esto es una funcionalidad muy poderosa, dado que puede funcionar como balanceador de carga, hosting de paginas estaticas, etc.

En nuestro caso, usaremos un CF Worker en conjunto con el cache de Cloudflare para lograr tener una CDN muy facilmente (y muy barata tambien).

Dado que un CFW es una funcion serverless, dentro de la funcion se puede manipular como el request como uno quiera y eso es lo que haremos.

## CF Worker
Lo primero que hay que hacer es crear un CFW en Cloudflare asignado a nuestro dominio principal (jzte.ch).

Este CFW se le debe asignar una ruta que en nuestro caso seria `cdn.jzte.ch/*`.

![image.png](/.attachments/image-2dff46f2-6ea0-4de3-aee0-c3666db516d7.png)

Para que funcione el subdominio tambien se tendria que configurar en la seccion DNS del dominio, un CNAME llamado `cdn` con target `192.0.2.1` y `Proxied` (que es un IP que siempre representa localhost y lo necesitamos dado que el worker esta corriendo dentro de CF, y no hay un IP externo asociado). 

![image.png](/.attachments/image-4bc96f03-548f-4045-bcc3-c7b9eeac2c39.png)

El contenido de la funcion puedes subirlo adentro del CF Worker, pero lo mejor es subirlo a un repo git, y desde ahi con un pipeline, subirlo a CF. El repo es `Juntoz.Cdn`. Para crear el worker es necesario usar el cli `wrangler` de CF y seguir un estandar determinado (ver https://developers.cloudflare.com/workers).

### Logica del worker

Lo que hara entonces el worker es recibir todos los requests hacia el url `cdn.jzte.ch` y usando solo ese url, rutearemos hacia el azure storage correcto donde se guarda el archivo.

Por ejemplo, si es que el url es `https://cdn.jztec.ch/site/{siteid}/0.1.1/main.css`, dentro del worker identificamos que la primera seccion del path es `site`, por lo tanto, convertiremos el url a `https://jzprodstocdn/site/{siteid}/0.1.1/main.css`. De esta manera tenemos una ruta valida de cdn muy facilmente y sin necesidad de cambiar nada de nuestro backend (claro, lo unico necesario es mantener las convenciones a traves de los modulos).

Y lo mismo hariamos para las imagenes de los productos.
```
https://cdn.jztec.ch/products/{siteid}/{merchantid}/{storeid}/{productid}/myimage.jpg
```
a
```
https://jzprodstocatalog/products/{siteid}/{merchantid}/{storeid}/{productid}/myimage.jpg
```

NOTA: esto no hace un redirect 302, sino que cambia el request para rutearlo al lugar correcto. Esto es muy importante dado que si fuera un 302, estaria al final igual exponiendo el url origen y no bajariamos el trafico.

### Y el cache?

Luego de lograr este primer hito, el segundo hito ya seria utilizar el cache y esto se hace tambien dentro del worker.

Basicamente, verificariamos el url de entrada si es que esta ya dentro del cache. Si esta, se saca el contenido de ahi. Si no lo esta, entonces se va a la ruta origen y luego de obtener el contenido, se ingresa en el cache (aun no se define el TTL exacto, pero se puede manipular en base a distintas reglas ya que es codigo puro).

https://developers.cloudflare.com/workers/learning/how-the-cache-works

### Endpoints Disponibles
Como se menciono antes, la CDN tendra varios endpoints y que estaran conectados a los distintos storage accounts que tenemos en Azure.

(hasta ahora)
|Request|Origen|
|----|-----|
|`cdn.jzte.ch/site/{siteid}/*`|`jzprodstocdn/site/{siteid}/*`|
|`cdn.jzte.ch/ux-jz-tech/{siteid}/*`|`jzprodstocdn/ux-jz-tech/{siteid}/*`|
|`cdn.jzte.ch/scweb/{siteid}/*`|`jzprodstocdn/scweb/{siteid}/*`|
|`cdn.jzte.ch/products/*`|`jzprodstocatalog/products/*`|
|`cdn.jzte.ch/brands/*`|`jzprodstocatalog/brands/*`|
|`cdn.jzte.ch/stores/*`|`jzprodstocatalog/stores/*`|
|`cdn.jzte.ch/media/*`|`jzprodstocms/media/*`|

## Publicar las rutas CDN
Con el CFW solo tenemos la primera parte del modulo dado que por ahora, nadie conoce esos endpoints de CDN.

Entonces para que estos endpoints se usen, esta la forma mas obvia que seria publicarlo a traves de SC o a traves de nuestra documentacion publica de que estos endpoints funcionan de esta manera y que los pueden usar.

Y la otra forma, es automatizar su uso.

### On the Fly
Una forma de automatizar es hacerlo "on the fly" en las apis publicas de la plataforma. Las apis publicas son aquellas que le permite a un app o una web ecommerce interactuar con la plataforma. Estas apis estan optimizadas para su uso en la web ecommerce (vs las core que son mas para Site Central y CRUDs) por lo tanto, podemos adaptarlas con seguridad de que se usaran por las webs ecommerce y no interferiran con la actualizacion o uso de la plataforma en Site Central (que solo usa apis core).

Entonces por ejemplo, en el endpoint para leer la data del producto para mostrarlo en la web ecommerce (product view), podemos tocar la data on the fly y empujar los urls de la cdn en vez de los urls reales del azure storage.

```
{
  id: "1456999",
  images: [
     {
        imageOriginal: "jzprodstocatalog/products/1456999.jpg"
     }
  ]
}
```
a

```
{
  id: "1456999",
  images: [
     {
        imageOriginal: "cdn.jzte.ch/products/1456999.jpg"
     }
  ]
}
```

De esta manera las webs automaticamente obtendrian el cambio sin necesidad de cambiar su codigo o alguna configuracion.

Efectivamente esta solucion viene con un hit en la lectura de la data. Aunque anticiparia que sera imperceptible.

### Incrustado
Otra forma de trabajo seria incrustar directamente los urls en la data. Por ejemplo, al publicar al indice de busqueda ahi se cambian los urls o al subirlos a traves de SC.
Sin embargo, desde mi punto de vista, aunque es mucho mas veloz, le quita lo liviano a la solucion actual de CDN. Creo que por ahora (2021-03), podemos seguir optando por la opcion "on the fly" y ver como nos va.

## Upload to CDN: jzcdncli
Como ya mencionamos, la CDN para catalog y cms es "alimentada" a traves de los storage accounts. Pero para el caso del storage cdn, el caso es distinto.

No hay una UI en SiteCentral para alimentarlo y esta mas relacionado al desarrollo de las webs que al uso del site owner o merchants.

Por lo tanto para ingresar archivos alli, se ha creado un `jzcdncli`. Este CLI permitira ingresar archivos hacia la CDN desde una computadora o desde un pipeline y permitira ingresar archivos solo a tres endpoints o buckets (como los llamamos dentro del cli): site, ux-jz-tech y scweb.

Para asegurar que el uso del cli este controlado y ejecutado solo por las personas correctas, se ha instaurado la necesidad de ingresar un api key.

Entonces, para `scweb` y `jz-ux-tech` se usara un API KEY distinto por cada uno y son basicamente llaves internas para que los developers de JzTech puedan publicar las versiones de los componentes de la plataforma al cdn y asi poder ser usado por las webs facilmente.

Para el caso de los `site`, el objetivo es que cada site tenga un API KEY asignado propio y con el pueda subir archivos a la seccion `site` propia.

# Futuro
El objetivo a corto plazo de todo esto es tener un producto separado de CDN que nos permita tener muy buen performance a nivel de frontend y dar un buen servicio a nuestros clientes.

El objetivo a largo plazo, por que no, podria ser lanzarlo como un producto separado real y obtener clientes por esto y un revenue stream separado.
