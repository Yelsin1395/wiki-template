# Asociar el cluster a un Azure Container Registry
Para poder usar las imagenes que estan en nuestro registry interno **juntozdockreg**, hay que conectar el AKS con el registry (ACR) ([info](https://docs.microsoft.com/en-us/azure/aks/cluster-container-registry-integration)).

Ir al Azure CLI (dentro del mismo portal o en tu maquina), y ejecutar este comando

```
az aks update -n juntoz-kub-api-staging -g juntoz-rg-staging-kub --attach-acr juntozdockreg
```
Donde: n (nombre del cluster destino) y g (resource group donde esta ese cluster)

# Asociar el cluster a GitHub Containers

1. Crear un PAT perteneciente al owner de la organizacion

2. Crear una trama base64 uniendo el org y el token.

```
echo -n "jztechpe:xxxxx" | base64
```

3. Crear un archivo json con el siguiente formato:

```
{
    "auths":
    {
        "ghcr.io":
            {
                "auth":"<token>"
            }
    }
}
```
Unificarlo como un string plano `{"auths":{....` y formarlo nuevamente como una trama base64

```
echo -n "{"auths":{...." | base64
>>> eyJhdXRocyI6eyJnaGNyLmlvIjp7Im...
```

4. Luego, usar esta trama mas grande, e insertarlo como un Secret de Kubernetes usando este manifest.

```
kind: Secret
type: kubernetes.io/dockerconfigjson
apiVersion: v1
metadata:
  name: <secret-name>
  namespace: <secret-namespace>
data:
  .dockerconfigjson: eyJhdXRocyI6eyJnaGNyLmlvIjp7Im...
```

Para aplicarlo, hay que guardar el manifest en un archivo y luego ejecutar:
```
kubectl apply -f mysecret.yml
```

NOTA: Este secret debe crearse para TODOS los namespaces desde donde se jalaran las imagenes.

5. Una vez que el secreto esta creado en ese namespace, hay que usarlo a nivel de los manifiestos de Deployment para que usen el secreto para poder obtener la imagen Docker. Esto se hace usando el atributo `imagePullSecrets`.

Ejemplo de manifiesto:
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: <deployname>
  namespace: <deploynamespace>
spec:
  replicas: 1
  selector:
    matchLabels:
      app: <containername>
  template:
    metadata:
      namespace: juntoz-api-core
      labels:
        app: <containername>
    spec:
      containers:
      - name: <containername>
        image: ghcr.io/jztechpe/<imagename>:<imagetag>
        imagePullPolicy: Always
        imagePullSecrets:
        - name: <secret-name>
        ports:
        - containerPort: <containerport>
```
