## Esquema de uso de SSL en Juntoz

### Para la comunicacion de browser a Cloudflare (puerta de entrada de Juntoz.com), necesitamos un SSL certificate.

Cloudflare nos da un Universal SSL Cert (compartido con otras 50 empresas) esto gracias a nuestro Plan Pro.

Este certificado se renueva automaticamente mientras mantenganmos nuestra cuenta en CF (cosa que es probable siempre lo usaremos y pagaremos).

### Para la comunicacion Cloudflare hacia nuestros servidores ("trafico interno")

Comprarlo en namecheap para *.juntoz.com. **Se necesita un certificado oficial SSL wildcard.**

Usar las instrucciones aqui: [Namecheap: Que hacer al comprar un certificado SSL?](/Home/Devops/Manejo-de-certificados-SSL/Namecheap:-Que-hacer-al-comprar-un-certificado-SSL?)

IMPORTANTE: cuando juntoz.com se renueva, hay que actualizar TODAS LAS API y LAS WEB de PERU