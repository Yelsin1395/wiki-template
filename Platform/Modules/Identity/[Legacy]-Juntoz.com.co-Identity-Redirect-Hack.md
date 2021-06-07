## Sobre el fix temporal en IdentityServer para el flujo de Registro con social login

El problema, era que el redirect estaba yendo al mismo account.juntoz.com en vez de a juntoz.com.co. 

Lo que se hizo fue agregar un redirect en cloudflare en duro a esta ruta que lleve a juntoz.com.co/account/login?returnurl=es-co 

Ojo que te manda a login para que haga el flujo de login del usuario, el cual ya esta autorizado en identity server en ese momento.

**TODO: Revisar si es que realmente esto sigue vigente?**