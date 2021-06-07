# Variables de entorno con el caracter $
En kubernetes se presenta un problema con las variables de entorno cuando el valor de la variable tiene caracteres especiales.

Por eso, en los ambientes de staging y produccion cuenta con 2 variables de entorno para **el connection string a la base de datos.**

En staging:
```
_juntoz-sql-cs-pwd : P@$$w0rd
_juntoz-sql-cs-pwd-for-kub : P@$$$$w0rd
```

En production no ocurre este error (porque la contrasena no tiene el $), pero se mantiene la paridad y se referencian entre si para no tener duplicidad sin ser necesario.

```
_juntoz-sql-cs-pwd : xxxx
_juntoz-sql-cs-pwd-for-kub : $(_juntoz-sql-cs-pwd)
```

Por lo tanto si usan para un cluster **Kubernetes** se debera usar la variable **_juntoz-sql-cs-pwd-for-kub** en los archivos de configuracion de sus proyectos.