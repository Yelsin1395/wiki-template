Esta es la raiz para la documentacion de todas las APIs que se crearan para la nueva plataforma V3.

### Porque V3?

Porque Juntoz primero se hizo como un monolitico y llevo a tener Juntoz.com desktop = V1.

Luego, se dividio en macroservicios = V2.

Ahora se dividira en microservicios = V3.

# Cual es el problema de la arquitectura actual?

La arquitectura actual aunque es buena tiene algunas fallas de diseno y de ejecucion.

1. La principal es que el API se hizo para sostener Juntoz.com, un solo marketplace. Todas las apis se hicieron por lo tanto para poder llamarse bajo ese nivel de granularidad (1 site > merchant > user).
Luego se quiso extender el modelo para whitelabels. Las APIs la aguantaron, pero crearon una dualidad en la arquitectura que hoy nos cuesta muchisimo mantener.
Algunos ejemplos:
- Tenemos dos indices, uno en algolia y otro en azure. Ambos se actualizan por caminos distintos con estructuras de datos distintos y para colmo, con distintas capacidades (uno no tiene campanas de precios, el otro si, pero a medias).
- Al estar todo adjunto a juntoz.com, los catalogos y tiendas de Juntoz.com y whitelabel son la misma. Esto aunque por un lado puede ser beneficioso (menos trabajo de mantenimiento para el merchant), ocasiono distorsiones en el modelo de datos que son bastante costosas de mantener: CategoriesJuntoz, CategoriesWhiteLabels, BoostJuntoz, BoostWhiteLabel, dos indices de busqueda, etc.
2. Las APIs se hicieron para soportar la web y merchant central. Esto lo que ocasiono es que NO FUERAN REUTILIZABLES ni usadas por el exterior. Osea las Apis se adaptaron a la web. Cuando lo correcto es al reves. **El API debe ser un producto separado, y las webs se adaptan al API**.
3. Las APIs hoy son macroservicios. Tienen demasiado codigo escrito junto.
4. Se uso MSSQL como storage principal, cuando muchos objetos son realmente mejor adaptados a un modelo NOSQL. Esto necesito mucho mas codigo del necesario para hacer el mapping (ORM) y ademas resulto en la necesidad de crear stored procedures para soportar queries complejos que el ORM arrojaba mal. Por ejemplo, Product, Categories, Brands y etc, tienen una mejor adaptacion a NoSQL que a tablas. La Order en realidad deberia estar en SQL, pero esta en NoSQL :).
5. Se uso el patron CQRS en las APIs adentro y esto creo aun mas codigo del necesario. Se duplicaron los DTO (ahora habian RP y DTO, y DTO por Task y DTO por Query), cuando un patron de Repository era mas adecuado, ademas que el patron CQRS internamente dentro de un mismo espacio de memoria no tiene ningun sentido (el C y el Q apuntan a la misma data fisica y el patron fue creado para el caso contrario).
6. Adicionalmente se hicieron en .NET (y una en NETCore). NET y C# son lenguajes de tipado estricto. Y aunque exista el tipo `dynamic`, todo el codigo en general busca que sea tipado y que haya una estructura fija. Esto es bueno para ciertos sistemas, pero para ecommerce donde los objetos son muy variables y la UI ademas tambien es muy especial, esto no ayuda a que el desarrollo escale.
7. NET es una tecnologia de hace 20 anhos y que ha evolucionado pero se ha mantenido parecida durante todo este tiempo. NO esta adaptado a Cloud environments (dado que es muy pesado correr NET xq necesita IIS, ASPNET y Windows Server) y requiere correrlo en Azure App Services. Es muy caro el hosting.
8. El codigo de integraciones se ha insertado DENTRO de las APIs. Esto perjudica muchisimo el performance y ademas del control y la facilidad de mantemiento.
9. El esquema de seguridad de las API se basa en roles del usuario dentro del token de usuario. Sin embargo, hay un cruce muy feo actualmente debido a que 1) no hay suficientes roles para cubrir todas los aspectos funcionales 2) los endpoints han sido copy/paste y hay demasiados endpoints para poder ordenarlos y verificar que realmente el usuario pueda hacer lo que necesita. Al no tener suficientes roles, eso hace que realmente los roles sean demasiado "gordos" y puedan hacer demasiadas cosas juntas (role kamadmin, te da acceso a la seccion del Site, cuando ser kamadmin no tiene ninguna relacion con eso estrictamente).

# Nueva plataforma
Entonces ya desde hace tiempo hemos querido cambiar estas APIs, pero no ha habido la oportunidad de hacerlo.

Esperamos que las nuevas APIs se sigan los siguientes principios para "aprender de nuestros errores" y con la siguiente arquitectura:

1. Las APIs estaran hechas en NodeJs. Node es un lenguaje dinamico muy adaptable para la web y permite creacion de objetos y modelos de datos muy rapidamente. Actualmente hay dos APIs creadas con KOA y otras en TypeScript con NestJS. Las ultimas probablemente quedaran como las oficiales. Typescript nos da tipos de datos estrictos, pero puede soportar tipos dinamicos muy facilmente.
2. NodeJs ademas es multiplataforma (NetCore tambien, pero sigue siendo un lenguaje tipado) puede correr en Linux y el hosting y las distintas formas de deployarlo lo hace muy flexible.
3. La plataforma correra en imagenes Linux en Docker y Kubernetes. Esto lograra que el hosting sea mucho mas barato y mas eficiente. Un bit: una imagen ASPNET ocupa alrededor de 2gb (Windows Server, IIS, Aspnet), una imagen ASPNETCORE ocupa entre 200 y 300mb (pero sigue siendo un lenguaje tipado), una imagen NodeJs puede ocupar entre 50 y 200mb dependiendo cuantos modulos importamos.
4. Abran tres APIs separadas: public, core e integracion. Esto se explicara mas adelante. Pero basicamente se hace para que realmente haya una estandarizacion para las aplicaciones web y que no necesitan conocer el core para evitar problemas de ataques de seguridad. Las de integracion de igual manera estaran para separar la logica del core.
5. Ahora si seran microservicios. Un microservicio debe poder encapsular un tema especifico o entidad. Creo que antes no se hizo asi por un tema de costos (es muy caro hostear un API en Azure para juntoz) y ademas por "flojera" debido a que es mas facil crear un endpoint que todo un API.
6. Las APIs desde el saque seran multi-nivel: multiples site, merchants, logistics.
7. El esquema de seguridad esta migrando hacia tokens JWT que permiten un mejor performance dado que son auto-contenidos.
8. El esquema de roles fue expandido y mejorado para que permita multiples niveles y ademas roles para cada funcion del sistema.
9. Merchant Central sera deprecado y abriremos un nuevo backoffice con Site Central que desde el inicio permite la co-existencia de multiples sites.






