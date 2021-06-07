Implementar OpenIdConnect es mas o menos sencillo usando las extensiones de AspNetCore y IdentityServer.

Sin embargo, hay algunos errores que ocurren y aqui tratamos de documentar.

# Reverse Proxy y TLS Termination
Primero, notar que las aplicaciones web ecommerce correran siempre detras de un NGINX instalado como Kubernetes Ingress y actuando como un reverse proxy (con TLS termination).

Que es un reverse proxy? Basicamente un componente que se instala delante de un web server para poder dar funciones extras como balanceador de carga, tls termination, validacion de seguridad, etc. Uno de los mas famosos es NGINX. Apache tambien puede actuar como un reverse proxy.

Que es TLS Termination? El reverse proxy se encarga del trafico HTTPS que viene del browser. Este trafico HTTPS entonces es recortado y convertido en HTTP hacia la aplicacion web.

Es decir:
```
Usuario ----------HTTPS----------> Reverse Proxy ----------HTTP----------> Web Application
```
Esto implica que la desencriptacion del trafico se hace en el reverse proxy, y esto incrementa el performance entre 20-30% del Web Application.

Sin embargo, esto tiene algunas implicancias que deben ser atacadas en la configuracion del web application.

# ForwardHeaders
Dado que el usuario se comunica con el reverse proxy y no con el web application, entonces el web application deja de ver directamente piezas del request que normalmente si veria.






