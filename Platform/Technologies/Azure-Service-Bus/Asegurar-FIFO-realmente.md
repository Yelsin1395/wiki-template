# Introduccion
En la nueva plataforma estamos usando la combinacion de Azure Service Bus y Azure Functions para el procesamiento de queues y topics.

Los topics sabemos que queremos que sean en paralelo completamente, y las colas queremos que sean seriales (siguiendo un orden especifico).

Sin embargo, Azure Functions permite procesar colas tambien en paralelo, lo que nos ayudara mucho en la velocidad. Sin embargo, hay casos en donde aun asi queremos mantener el orden de ejecucion de la cola.

Por ejemplo:

Si la cola tiene tres mensajes

```
[M3, M2, M1] => Azure Function
```

Tipicamente hubieramos esperado que se procese en orden M1, M2, M3.

Pero como AF puede escalar "al infinito", la plataforma podria procesar M2, M1, M3 y esta bien.

(Aqui lo que esta pasando es que AF1 coge M1 y le pone lock, AF2 coge M2 y le pone lock. Esto esta manteniendo el "orden de una cola" pero la ejecucion podria terminar siendo en desorden si es que M1 es mas grande que M2 por ejemplo).

Esto nos sirve mucho si es que M1, M2, M3 contienen objetos distintos (como productos distintos). Sin embargo, el problema ocurre si es que M1 y M2 contienen el producto P1 (dos updates casi simultaneos). El paralelismo haria que se procese la data en orden incorrecto y podria terminar habiendo data corrupta.

# Solucion 
Para solucionar esto, se usa un feature de ASB llamado Sessions. ASB usa el parametro sessionId del mensaje para garantizar que un solo ejecutante procese esos mensajes.

Por ejemplo, en este caso tenemos 5 mensajes y cada uno con un sessionId asignado (productId).

```
[M5(P3), M4(P1), M3(P2), M2(P1), M1(P1)] => { AF1, AF2, AF3 }

donde AF1, AF2... son las multiples instancias del mismo Azure Function que escalan para cubrir la demanda.
```

Lo que hace el engine de ASB y AF es que AF1 procese todos los P1, AF2 procese los P2 y AF3 procese los P3.

Asi:
```
[M4, M2, M1] => AF1
[M3] => AF2
[M5] => AF3
```

De esta forma se garantiza el orden FIFO dentro del mismo SessionId.

# Implementacion
El **primer paso** es que cuando se ingrese el mensaje en la cola, se debe enviar el sessionId.

```
        await this.azureBusSender.sendToQueue(
            'order_to_ready',
            {
                sessionId: order.id,
                body: {
                    subOrderNumber: suborder.subOrderNumber
                },
                userProperties: {
                    event: 'update_status_ready',
                    bearerToken: this.bearerToken,
                    siteId: this.inSite
                }
            }
        );
```

En este caso por ejemplo, el sessionId es el orderId. De esta manera los mensajes de la misma orden, ejecutaran en orden FIFO.

Si por ejemplo, asignaria `sessionId: suborder.subOrderNumber`, ASB y AF tomarian que son distintos sessionId y ejecutaria los mensajes en paralelo.

El **segundo paso** es que el **queue** o el **subscription** tenga el flag `Enable Sessions` activado. Esto se hace al momento de la CREACION. Por lo tanto, habria que recrear la cola o subscripcion si no lo tiene activado.

Y el **tercer paso** es que el Azure Function lo tenga activado tambien. Esto se hace en el archivo `function.json`.

```
{
  "bindings": [
    {
      "name": "inMsg",
      "type": "serviceBusTrigger",
      "direction": "in",
      "queueName": "order_to_ready",
      "connection": "inServiceBusEndpoint",
      "isSessionsEnabled": true
    }
  ]
}
```

## Troubleshooting
* Si es que el Azure Function tiene `enableSessions=false` o no esta definido y la cola o subscripcion si lo tiene, la **COLA NO SE PROCESARA**.
* Si es que el Azure Function tiene `enableSessions=true` o no esta definido y la cola o subscripcion no lo tiene, la **COLA NO SE PROCESARA**.
