NOTA: Se asume haber leido y generado los archivos con [este link](/Home/Devops/Como-publicar-tu-website?-\(Manual-para-clientes-de-whitelabels\))

# Ir a Azure
1. Ir a azure y al app service (juntoz-asw-b2c-web-production) e ir a la seccion "SSL settings".
1. Ir al segundo tab Private Certificates.
1. Subir el archivo pfx con el password.
1. Esto sube el pfx a la lista.
1. Ir al primer tab Bindings.
1. Add Binding y escoger el certificado que lo acabas de subir con SNI Based SSL.
1. Usar para juntoz.com y *.juntoz.com.

Con esto el resultado final es que la web ya puede usar https. Yeee!

# NGINX

https://www.ssl2buy.com/wiki/how-to-install-ssl-certificate-on-nginx-server

1. Generar el archivo sslbundle
2. Modificar la configuracion para usar el archivo sslbundle y el archivo de privatekey.

# NodeJs
Para NodeJs hay que usar este codigo

```
const https = require('https');
const fs = require('fs');

// assume the cert folder is next to the current index file
var keypath = path.resolve(certfolder, 'server.key');
var crtpath = path.resolve(certfolder, 'server.sslbundle.crt');

var sslOptions = {
    key: fs.readFileSync(keypath),
    cert: fs.readFileSync(crtpath)
};

https.createServer(sslOptions, koaApp.callback()).listen(443);
```
