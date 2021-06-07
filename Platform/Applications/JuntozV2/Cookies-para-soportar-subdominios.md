## Nuestro cookie en juntoz es un cookie de tipo subdomain! ##

Esto significa que empieza con `.juntoz.com` o `.juntoz-staging.com`
Por ende, al manipular cookies, siempre se debe usar el nombre con el ` . ` adelante.

Ahora, en `localhost`, hacer lo del . adelante no funciona! Es por un tema de implementacion de los browsers. Osea que si quieren probar en local tienen que inventar un dominio local que no sea localhost y registrarlo en el `hosts` file.

## Como centralizamos la logica? ##
En el archivo de `appsettings` tenemos una propiedad que se llama `"SiteDomainCookie"` que es la que dicta el nombre del cookie. Obviamente esta debe hacer match segun el environment y dominio al que se despliegue. Por ej. STA debe tener el valor .juntoz-staging.com
