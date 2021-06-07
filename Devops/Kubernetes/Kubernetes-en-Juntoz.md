# Como usar kubernetes en Juntoz?

Este articulo tiene varios comandos y procedimientos para el uso tipico de kubernetes.

# Como controlar kubernetes desde mi maquina local?

Primero lo que hay que hacer es configurar el context de `kubectl`.

```
1: az login (solo si es que no estas ya autenticado o si tu token local ya expiro)
2: az aks get-credentials --resource-group=juntoz-rg-staging-kub --name=juntoz-kub-api-staging
```

Con esto, `az` configuro automaticamente que los comandos de `kubectl` se ejecuten contra el cluster `juntoz-kub-api-staging`.

Para cambiar de cluster, hay que hacer los pasos de nuevo.

## Namespace
Nuestros pods todos tienen un namespace asignado, por lo tanto para acelerar el uso de kubectl, puedes setear el namespace por defecto

```
kubectl config set-context --current --namespace=mynamespace
```

# Como ver el dashboard de un cluster?
~~https://docs.microsoft.com/en-us/azure/aks/kubernetes-dashboard~~
Usar https://octant.dev/

# Como ver los logs de un pod?
```
> kubectl get pods

NAME                                 READY     STATUS    RESTARTS   AGE
simple-deployment-4098151155-494nl   1/1       Running   0          1m
simple-deployment-4098151155-n8bqr   1/1       Running   0          1m

> kubectl logs -f simple-deployment-4098151155-n8bqr
```

# Como reiniciar un pod?
Hay dos formas, borrar el pod o bajar el numero de instancias

```
kubectl delete pod foo
```

```
kubectl scale --replicas=0 rs/foo
```

# Como acceder a un servicio para ver si esta corriendo bien?
Especialmente para poder separar si el ingress esta mal o el servicio.

```
kubectl port-forward -n wl-web-lifemiles svc/wl-web-lifemiles-svc 5001:5000
```
Donde 5001 sera un puerto local, y 5000 es el puerto en el que corre el svc

Una vez hecho esto, usar localhost:5001 y deberia funcionar.