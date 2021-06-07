# Deploy
Para deployar archivos hacia Azure Storage, es necesario hacer esta configuracion:

Asumiendo este task en tu azure-pipelines.yml

```
  - task: AzureFileCopy@4
    inputs:
      sourcePath: $(Pipeline.Workspace)\drop\*
      azureSubscription: Microsoft Azure CSP 1 (27f35131-c5c7-4217-a8f4-dd36ab5263ed)
      destination: azureBlob
      storage: $(jz-storage)
      containerName: $(jz-storage-container)
```

Ir a ese Service Connection (Azure Devops/(tu proyecto)/Configuracion/Service Connections) y hacer click en "Manage Service Principal", esto llevara al portal de azure para obtener el nombre interno de ese service principal, que seguramente es algo asi como eagleperu-juntoz-(subscription-id). Guarda ese nombre interno.

Ir al otro link "Manage roles", esto llevara al control de accesos configurados para ese Azure Subscription. Alli tienes que agregar un nuevo "role assignment" a este service principal.

Tienes que asignar estos dos roles: Azure Storage Blob Owner y Azure Storage Blob Contributor a ese service principal.

Con esto, la operacion ya funciona!

Y por eso es que segun la definicion del task AzureFileCopy@4 no habia forma de configurar la seguridad. Y esto es porque lo obtiene directamente del service principal.

Todo esto gracias a este link
https://brettmckenzie.net/2020/03/23/azure-pipelines-copy-files-task-authentication-failed/

# Runtime

Una vez insertado en el Azure Storage, ahora ya se podria acceder al archivo por ejemplo:

`https://[storageaccount].blob.core.windows.net/[container]/myfile.js`

*NOTA: Azure storage te da otras rutas tambien para distintos casos de uso, ver documentacion.*

Yendo mas alla, si es que ahora quieres tener un dominio asignado a este storage, el primer paso es configurar el CNAME de tu dominio.

En nuestro caso es Cloudflare. Ir a tu cuenta de Cloudflare, seleccionar tu dominio e ir a la seccion de DNS.

Configura un CNAME asi:
```
Name: mystrg
Host: [storageaccount].blob.core.windows.net
DNS Only <<< esto es muy importante
TTL: Auto
```

Luego ir a Azure Portal, Storage Account, Custom Domain e introducir el dominio que enmascarara el storage: `mystrg.mydomain.com`. Esto validara el CNAME y si todo esta bien, entonces se graban tus cambios.

Como esta la configuracion, esto funciona bien y ahora tus archivos puedes acceder como

`http://mystrg.mydomain.com/[container]/myfile.js`

Nota que por ahora solo podras usar HTTP y no HTTPS, dado que Azure Storage no soporta HTTPS de por si.

Aqui tienes el camino que te da Azure que es crear una Azure CDN con el costo adicional respectivo.

Como ya tenemos Cloudflare, aprovechemos sus capacidades!

Cloudflare te da un certificado gratis "on the edge" osea como primer punto de entrada desde el browser. Por lo tanto, en teoria ya deberia poder soportar https por defecto si es que todo mi dominio `mydomain.com` (y por tanto mi subdominio `mystrg` incluido) ya esta registrado en Cloudflare. Sin embargo, no es asi.

Para activar el https para este subdominio, lo que hay que hacer es ir a mi registro CNAME y cambiar `DNS Only` to `Proxy`.

De esta forma, tu url `https://mystrg.mydomain.com/[container]/myfile.js` ya tiene soporte https!.



