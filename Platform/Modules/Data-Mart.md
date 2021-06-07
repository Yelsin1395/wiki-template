Una de las funcionalidades que es muy necesaria por los merchants, aunque muchas veces es subestimada, es la pantalla de Dashboard o Reportes Graficos. Su potencia puede ser muy grande dado que hoy los negocios viven de la data y si la data esta disponible lo mas rapido posible, entonces podran tomar mejores decisiones y mas rapidamente.

Sin embargo, una de las cosas que normalmente se sub-disena es que ejecutar queries para un dashboard puede tener mucho impacto sobre el performance de la aplicacion si es que se hace contra la data en vivo (la data transaccional del one-source-of-truth), y este fue uno de los grandes problemas de la plataforma legacy.

Y si a esto sumas que nuestra plataforma es un SAAS (una DB para todos), entonces el impacto es aun mucho mayor.

La solucion correcta es tener una base de datos separada de solo lectura sobre la que los queries del dashboard NO impacten en la transaccionalidad de la aplicacion. Teniendo esto y el mecanismo para que esta data sea lo mas 'en vivo' posible, entonces la potencia del dashboard se multiplica.

Esta seccion del wiki mostrara cual es el diseno de este modulo, su funcionalidad y mantenimiento.