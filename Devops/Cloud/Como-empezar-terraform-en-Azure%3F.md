- Ir a Azure Directory
- Crear App Registration llamado como `jz-hosted-terraform` con este service principal permite que terraform se registre y pueda aplicar los cambios en Azure.
- Dentro de ese app registration, crear un client secret (que no expire). Copiar ese secret asap xq se borrara luego de salir de la pantalla.

Con estos tres pasos, el App Registration te da los siguientes datos:

```
clientid           cadac438-558b-4fdb-bcde-798096eb0cd3
client secret      LLyB_pnHK7GMD~7MM0~398yxs~.XCeh7of
tenantid           1d2696b0-d159-47cf-9176-51a7dd7d161d
subscriptionid     27f35131-c5c7-4217-a8f4-dd36ab5263ed
```

El subscriptionid es donde se aplicara todos los cambios de terraform.

- El siguiente paso es ir a la subscripcion en el portal de Azure, ir a la opcion Access Management, Add Role, y poner Contributor a ese app registration llamado jz-hosted-terraform.

- De esta forma, le estas dando permisos realmente a ese app reg sobre esa subscripcion.

Una vez hecha esta configuracion, ya todo se puede aplicar en terraform y se inicia con el azure_rm.