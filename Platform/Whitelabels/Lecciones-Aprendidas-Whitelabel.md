# Lessons Learned

## mantenimiento y responsabilidades
- Todo whitelabel debe tener un contrato de mantenimiento que debe cubrir:
Costo de Azure + markup
Costo de Algolia + markup
Horas de soporte incluidas por mes maximo
Horas de desarrollo incluidas por mes maximo
Canal de comunicacion oficial (solo product/comercial)
Costo de SAC (llamadas, atencion especial, etc) + markup
Costo de Operaciones (shipping, devoluciones, estados) + markup
1 mes de mantenimiento para bugs solamente (no nuevas funcionalidades)
15 dias calendario minimo de Beta/Friends and Family

Si no es posible obtenerlo aislado para este WL, obtener una proporcion de # transacciones de WL / # transacciones totales dentro de Juntoz.
Esto se reconsiderara cuando el site tenga mas de X (?) transacciones por mes.

- Compra de dominio y certificados va por el cliente, no Juntoz (ver [SSL para WhiteLabels](/Home/Devops/Manejo-de-certificados-SSL/SSL-para-Whitelabels))
- El cliente se responsabiliza de subir imagenes en el formato y tamaño correcto para desktop y mobile.

## Criterios de Aceptación en QA
El happy path se define como un flujo completo sin buscar ninguna funcionalidad extra, o prueba negativa; dicho lo anterior, una prueba "Happy Path" consiste en los siguientes flujos de pruebas básicas:
- **Para Whitelabels y cuaquier página de compras en cualquier país**
1. Home Page
1. Búsqueda
1. Catálogo
1. Product Page
1. Carrito
1. Log In (previo a checkout)
1. Checkout
1. Orden Generada


## tecnico
- Todos los whitelabels deben compartir el mismo identity y profile. Actualmente profile esta semi-separado, hay que juntarlo para todos >> GAP
- Todos los whitelabels deben compartir el mismo core y estructura, y solo debe cambiar la data >> GAP
- Todo el whitelabel debe ser configurable (CMS) >> GAP
- Todos los whitelabel comparten el mismo checkout, Sin excepciones.
- Cada whitelabel tiene que tener su propio web y app insights (el codigo fuente si debe ser compartido).
- Cada whitelabel tiene que tener su propio Google Analytics

##CheckList previo a pasar a Producción

- Contenido:
1. Lista total de productos, con imagenes, precios y stock actualizados
1. Terminos y Condiciones
1. Privacidad
1. Cambios y Devoluciones
1. Vincular correctamente banners en Home Page (una vez creados)
1. Configurar Correos transaccionales
1. Verificar categorizacion correcta.
1. Verificar que mínimo se deberá tener un método de pago habilitado, ver si se Incluye Cash contra Entrega?
1. Verificar que mínimo se debará tener un Operador Logistico asignado.

- Funcionalidad en la WEB:
1. Configurar sender de correos transaccionales
1. Categorias dinamicas en menu (cuando no hay productos en una categoria, no se muestra)
1. Verificar flujo de Recuperacion de contraseña
1. Verificar flujo de Edicion de contraseña
1. Log-out desde Mis Datos + Log-in llegar al Home
1. Verificar flujo de compra completo con los diferentes metodos de pago
1. Asegurar envio de correo de Merchant por confirmacion de pedido
1. Verificar buen funcionamiento de todos correos transaccionales
1 Mail Transaccional Pago Efectivo configurado?
1. Enlace legal en Checkout correctamente vinculado
1. Adaptar todos los botones en checkout a linea grafica de WL
1. En caso tengra integración realizar las configuraciones respectivas para la activación.
**(Hasta este punto se puede comprar)**
1. Libro de Reclamaciones destinatarios
1. Enlace de Terminos y Condiciones broken en Pagina de Confirmacion de Pedido
1. Enlace de Mis Ordenes reenvia a Home en Pagina de Confirmacion de Pedido
1. Configuracion de Remitente en Label de los correos transaccionales
1. Configuracion de Google Analytics

- SEO
1. Robots habilitado para las paginas que se deben indexar por el buscador
1. Robots inhabilitado para pagina CARRITO, X, Y (especificar)
1. Todas las paginas deben tener un TITLE y un META DESCRIPTION
1. Categorias deben tener un META DESCRIPTION apropiado y un TITLE apropiado
1. Product View deben tener un META DESCRIPTION apropiado y un TITLE apropiado
1. META KEYWORDS deben estar presentes
1. La estructura de los enlaces de la pagina deben poder llegar a todos los rincones de la aplicacion.
1. Las imagenes deben tener ALT (cuando corresponda).
1. La estructura del HTML debe ser SEMANTICO (H1, H2, Section, Article, Header, Body, Footer, etc).
1. Sitemap ?? NOTE: revisar como.
1. Microdata + OpenGraph.
1. Localizable URLs (cultures/languages)
1. Breadcrumbs
1. URL Canonicalization

Emails minimos que se deben enviar
- User Register
- Order Received
- Order Confirmed
- Order In Transit
- Order Delivered
- Product Review (ReviewDb SiteSetting)
