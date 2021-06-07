# Problema
Cuando en un app service se ve el error SocketException (sockets exhausted), indica que los TCP sockets se han terminado.

Esto es por un "socket leak".

Principalmente, para nuestro caso, se debe al mal uso de la clase `HttpClient`.

HttpClient debe ser usada como un singleton o con la menor cantidad de instancias.

**MAL**
```
HttpClient c = new HttpClient();
await c.GetAsync(...)
c.Dispose();

using (HttpClient c = new HttpClient())
{
    await c.GetAsync(...)
}
```

**BIEN**
```
private static HttpClient httpClient = new HttpClient();

public async Task CallHttp()
{
    await httpClient.GetAsync(...);
}
```

HttpClient ya es thread-safe por lo que se pueden hacer varias llamadas concurrentes (ya si son muchas podrias hacer un pool).

En versiones mas avanzadas de NET (NetCore), existe la clase HttpClientFactory que es lo recomendado.

Ver:
https://josefottosson.se/you-are-probably-still-using-httpclient-wrong-and-it-is-destabilizing-your-software/
https://aspnetmonsters.com/2016/08/2016-08-27-httpclientwrong/

# Quick Fix
Tipicamente esto nos esta ocurriendo en web applications o en web jobs.

Si es un web application, reseteando la aplicacion se soluciona.

Pero si es un webjob, la clave es saber cual de todos.
Para encontrarlo, debemos ir al App Service > Advanced Tools > Go, esto abre la consola de administracion del app service.
Ir a Process Explorer, y esperar unos segundos.
Esto mostrara todos los procesos que estan corriendo en el servidor.

Handle Count: Para cada uno de los ejecutables corriendo, click en Properties y ver el dato "Handle Count". Tipicamente esto debe estar en un rango de 0-1000. Si se ve algo mas que eso, entonces ya es raro y es candidato a reiniciarlo.

Memory Set: Otro indicativo es la alta memoria (pero no es tan certero).

# Long Fix
Lo que ya hablamos, el reuso de HttpClient.
