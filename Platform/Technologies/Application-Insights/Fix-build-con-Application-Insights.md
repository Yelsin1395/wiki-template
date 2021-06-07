Si reciben durante un build el mensaje de error:

```
2017-06-08T22:31:52.4342685Z ##[error]Failed to deploy web package to App Service.
2017-06-08T22:31:52.4352691Z ##[error]Error: (6/8/2017 10:31:52 PM) An error occurred when the request was processed on the remote computer.
Error: An error was encountered when processing operation 'Delete Directory' on 'D:\home\site\wwwroot\App_Data\jobs'.
Error: The error code was 0x80070091.
```

o algo similar. Basta con agregar:

```
-skip:Directory='.\App_Data\jobs\continuous\ApplicationInsightsProfiler.' - skip:skipAction=Delete,objectname='dirPath',absolutepath='.\App_Data\jobs\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.\App_Data\jobs$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\App_Data$'`
```
En el paso Azure App Service Deploy del build correspondiente:

![Items.png](/.attachments/Items-29983b59-1089-46f0-96a6-51ea9857e9b3.png)

En la sección Additional Deployment Options:

![Items2.png](/.attachments/Items2-f02f17c0-d3e3-43b4-b603-34da345be310.png)

Esto es para que no haga el delete de esos directorios dado que son jobs que deben mantenerse.

**Recordar que no es recomendado agregar AI a la aplicacion web. Mejor usarlo por fuera. Solo se usaria por dentro si es que lo amerita y se necesita hacer un tracking mas fino.**