Para que las aplicaciones se conecten, hemos creado un usuario `apprw` para que pueda leer y escribir en la base de datos.

Para STG
```
CREATE USER apprw WITH PASSWORD 'r3dwr1t3u5r5tg';
GRANT CONNECT ON DATABASE tsdb TO apprw;
GRANT USAGE ON SCHEMA public TO apprw;
```

Para PROD
```
CREATE USER apprw WITH PASSWORD 'LdKB7bdowC';
GRANT CONNECT ON DATABASE tsdb TO apprw;
GRANT USAGE ON SCHEMA public TO apprw;
```

Para poder dar acceso a las tablas a este usuario, hay que ejecutar
```
GRANT SELECT ON ALL TABLES IN SCHEMA public TO apprw;
GRANT INSERT ON ALL TABLES IN SCHEMA public TO apprw;
GRANT UPDATE ON ALL TABLES IN SCHEMA public TO apprw;
GRANT DELETE ON ALL TABLES IN SCHEMA public TO apprw;
```

Si fuera necesario crear otro usuario, entonces hay que correr estos mismos comandos pero con otra contrasena.

