# Como publicar tu website?

**Importante: lee la guia completa primero**

##Intro
Este articulo describe de manera detallada y facil de entender (espero) como publicar un website con su propio dominio y certificado digital.
Estoy asumiendo que el contenido de tu website (html, css, imagenes, javascript, etc) ya esta alojado en un servidor y listo para ser publicado.

## Paso 1: Comprar un dominio (ignorar si ya lo tienes comprado y eres dueño de el)
En este articulo, usaremos el manejador de dominios (domain registrar) llamado namecheap.com, que tiene precios muy competitivos y ofrecen seriedad y soporte.

### Abre una cuenta
Ir a namecheap.com y crear tu propio usuario.

_Nota que este sera tu usuario raiz con el que se hara toda la administracion de tu dominio y certificado, por lo tanto, si es que hay un equipo detras responsable, hay que crear la cuenta y el email de tal manera que todos puedan entrar (password compartido, email compartido, etc)._

### Busca tu dominio
- Ir a Domains, Domain Name Search y ingresar tu dominio deseado.
- Por ejemplo, jtzapi.com. **El dominio debe aparecer como disponible.**
- Click en Add To Cart y luego View Cart.

Hay algunas opciones que se pueden agregar. Por ejemplo
- WhoIsGuard: Este servicio permite OCULTAR el nombre del dueño del dominio.
- AutoRenew: El dominio se renovara cada año automaticamente.

Hacer el pago. Una vez hecho esto, el nuevo dominio aparecera en tu cuenta de namecheap.

## Paso 2: Comprar un certificado digital (si tienes ya un certificado digital pasar al paso 5)
Hasta hace unos pocos años, la web en general se manejaba en http y solo las zonas donde ponias tu usuario y password o tu tarjeta de credito se hacian por https.

Pero ahora ultimo la buena practica es que TODO el sitio web este en https y para hacer https, necesitas comprar un certificado digital.

Para nuestras necesidades, necesitamos comprar un certificado wildcard que soporte subdominios *.misitio.com y dominios raiz misitio.com.

Recomendamos comprar el Comodo Essential Wildcard que cuesta 79.99$/año en namecheap.
https://www.namecheap.com/security/ssl-certificates/comodo/essentialssl-wildcard/

Una vez comprado, va a aparecer en tu dashboard de namecheap (si no aparece, click en View All Domains, o en la seccion Product List).

![Items.png](/.attachments/Items-077dd516-a339-432c-bf52-9dba1602eafa.png)

O aqui

![Items (1).png](/.attachments/Items%20(1)-077aed6a-0829-4e53-8ea5-2dcacda77014.png)

## Paso 3: Generar los archivos CSR basicos del certificado

Para activar el certificado, te va a pedir crear un CSR.

Namecheap te da algunos ejemplos para generarlo para Apache, NGINX, etc. Si no esta el servidor web usado, puedes ir a: https://decoder.link/csr_generator.

Aqui debes poner los datos correctos de tu empresa.

![Items (2).png](/.attachments/Items%20(2)-a7925bcf-8ce4-47c1-bc70-235b7d38cd6f.png)

IMPORTANTE:
- El CSR debe ser hecho con el dominio wildcard (*.misitio.com), si no lo haces asi, solo funcionara para el dominio naked (misitio.com). Pero no te preocupes, puedes generarlo nuevamente.
- Para el email sugiero poner un email de una persona que casi o siempre estara en la empresa (un fundador, un gerente, etc) o un email generico como de soporte. El email es solo representativo de la empresa, no sirve directamente para este tramite o configuracion.

Click en Generate, y te va a aparecer una ventana popup con tres secciones: CSR, PRIVATE KEY, CERTIFICATE.

![Items (3).png](/.attachments/Items%20(3)-9ee0cd0a-3a5c-45f1-a409-e2d056358126.png)

Abrir cada seccion y copiar el contenido de cada una en un archivo distinto.

CSR: Abrir Notepad y grabarlo en un archivo misitio.csr.txt
```
-----BEGIN CERTIFICATE REQUEST-----
MIIDKTCCAhECAQAwgYExFjAUBgNVBAMMDSoubWlzaXRpby5jb20xDTALBgNVBAcM
BFBlcnUxDTALBgNVBAgMBExpbWExGDAWBgNVBAoMD01pIEVtcHJlc2EgUy5BLjEi
....
-----END CERTIFICATE REQUEST-----
```

PRIVATE KEY: Abrir Notepad y grabarlo en un archivo misitio.key.txt
```
-----BEGIN RSA PRIVATE KEY-----
MIIEpQIBAAKCAQEA1pbIhbY9PF7cUszWw9uwD470ST/nSG823/cnE9rlmHW4EooF
C2xRJXeEabBXjAYNdb75lzzYFipX2KcadnUGmM1OJKojye+wpSa7siyQmcMehk3J
...
-----END RSA PRIVATE KEY-----
```


CERTIFICATE: igual y grabarlo como misitio.cert.txt
```
-----BEGIN CERTIFICATE-----
MIIDmDCCAoCgAwIBAgIJAMIak6Sn8EH8MA0GCSqGSIb3DQEBCwUAMGExFjAUBgNV
BAMMDSoubWlzaXRpby5jb20xDTALBgNVBAcMBFBlcnUxDTALBgNVBAgMBFBlcnUx
....
-----END CERTIFICATE-----
```

**IMPORTANTISIMO!: Estos archivos son claves para poder regenerar tu archivo de certificado en caso se pierda. Guardarlos en un lugar seguro pero facilmente ubicable. Estos son activos sensibles de su empresa.**

## Paso 4: Registrar certificado en COMODO

Ir a la misma pantalla dentro de namecheap desde donde partimos a generar el CSR y llenarla asi:
CSR = ingresar el contenido del archivo misitio.csr.txt que creamos en el paso anterior.
Email = email address que sera asociado al certificado digital dentro de COMODO. **Este email es muy importante dado que si necesitaras regenerar el certificado en el futuro, se reenviara a este mismo email**.
Escoger Web Server = Other.

![Items.jpg](/.attachments/Items-fbb9ecc6-5c35-4bde-a8fc-cac84de99212.jpg)

A partir de aca hay dos formas de validar el ownership (**usa una u otra, no las dos**).

1. Escoger DVC Method = DNS-based
Este caso se usa cuando puedes tener acceso a tu configuracion DNS y poder agregar un registro CNAME (NAMECHEAP o COMODO pide esto) o TXT (godaddy pide esto).

Aparecera esta pantalla al confirmar este paso, y lo siguiente es ir al boton "See Details" asociado al certificado que estas generando.

![Items (5).png](/.attachments/Items%20(5)-90dc9a32-bdd4-41b8-a51f-41cdc0ff030a.png)

Aparecera esta segunda pantalla con los detalles del certificado, y aqui debes dar click en la flecha del boton Edit Methods y debe aparecer un submenu llamado tipo Get CNAME o Get DNS.

![Items (8).png](/.attachments/Items%20(8)-8a9c26dd-2af2-474f-9ae1-50e12c1e43d5.png)

La pagina te dara algunos datos parecidos a estos:
```
Host _21CF421C333BF58352D6B35D078968BA.misitio.com
Target 397CFE7CADEDD59471E5E0D282046759.3CBD78097220B0F975375B72E9853xxx.5ce706c9efxxx.comodoca.com
```

Ir a la consola DNS de tu dominio (esto seria namecheap.com o goddady.com o cloudflare.com, etc) y debes añadir un registro CNAME con los datos generados en el paso anterior.

_* Los cambios hechos en el DNS pueden tomar entre segundos a horas (dependiendo de la velocidad de tu controlador de dominio). Para verificar que el cambio ya se replico alrededor del mundo, puedes usar dnschecker.org y introducir misitio.com._

2. Ir al servidor web y introducir un archivo en una ruta especial.
Por ejemplo, godady pide agregar un archivo en la ruta .well-known/pki.validation/godaddy.html y dentro poner el valor del registro TXT que dicen en las instrucciones.
Una vez que se genere el certificado, el archivo se puede borrar.

Con este paso hecho, COMODO verificara automaticamente que tu empresa es dueña del dominio del certificado (infiriendo que si tienen acceso al DNS del dominio, entonces es porque son dueños del mismo), y enviara un email automatico ya con el certificado dentro de un zip.

_El email llegara aprox 15-30 minutos despues que haya podido detectar el cambio en el DNS._

## Paso 5: Generar archivo pfx
Una vez recibido el email de COMODO, atachado estara un zip con los archivos necesarios para generar el archivo de certificado final.

Este zip contiene dos archivos: misitio.ca-bundle, misitio.crt

(en caso el certificado es de otro proveedor, probablemente recibiras otros archivos y las instrucciones siguientes no aplicaran directamente).

Para generar el archivo pfx,
1. Ir a https://decoder.link/converter
1. Pestaña PEM to PKCS#12 (PEM es el formato generado por Namecheap, y PKCS es el formato necesario para Azure)
1. Certificate file: introducir el archivo misitio.crt (que esta dentro del zip recibido)
1. Private file: introducir el archivo misitio.key.txt (generado al inicio).
1. Introducir un password que desees (escribelo en un nuevo archivo llamado misitio.pfx.pass.txt).
1. Click en Convert.
1. Esto generara un zip con un archivo pfx dentro (y es el que luego lo referenciamos como misitio.pfx).

## Paso 6: Enviar a Juntoz
Para poder registrar el certificado con Juntoz dentro de un azure website deben enviarse:
1. misitio.pfx
2. misitio.pfx.pass.txt

Todos los demas archivos NO son necesarios enviarlos.

Un output final **recomendado** es tener un zip con los siguientes archivos:
```
(generados por el csr)
misitio.csr.txt
misitio.key.txt
misitio.cert.txt

(generados por COMODO)
misitio.ca-bundle
misitio.crt

(generados por ti)
misitio.pfx
misitio.pfx.pass.txt
```
Y esto guardarlo en un lugar seguro pero conocido para ser utilizados en el futuro (si es que hay que re-crear el servidor, el certificado sera necesario instalarlo).

## Paso 6.1: Generar SSLBundle
Si es que en vez de Azure Website, se usara NGINX o NodeJs, no necesitamos generar el archivo pfx. Necesitamos dos archivos distintos: un sslbundle y el archivo misitio.key.txt.

El sslbundle se genera usando este comando (en resumen, es la union de los archivos ca-bundle y crt otorgados por COMODO):

```
cat misitio.crt misitio.ca-bundle > misitio.ssl-bundle.crt
```

## Finalmente
Espero que esta guia haya servido, si hay algo que mejorar o cambiar, por favor contactar a katlim@ieholding.com.
