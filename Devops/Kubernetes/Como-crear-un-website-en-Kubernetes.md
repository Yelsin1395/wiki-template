# Intro
Hoy la plataforma de Juntoz esta basada en Azure App Services que permiten hacer un deploy rapido y un manejo de la aplicacion a alto nivel.

Sin embargo, por temas de costos y no locked-in a ninguna plataforma, estamos moviendo todo a NodeJs y a AspnetCore usando Linux.

Para hacer esto aun mejor y yendo hacia las ultima tecnologias, se esta usando Docker y Kubernetes.

Sin embargo, ambos modelos aunque sea realmente el mismo codigo que se puede reutilizar por dentro, por fuera, hay varias cosas distintas que hay que hacer.

# Diferencias

|  | AAS | KUB |
|--|--|--|
| Web Server | IIS | N/A |
| Configuracion | json (netcore) < aas settings < env vars | json (netcore) < env vars |
| Autoscale | si, max 10 | si, "infinito" |
| Health Probe | ping | http |
| Ready Probe | no | http |
| TLS | Si, por UI | Si, por secrets |
| LetsEncrypt | No | Si, 3rd party |
| Slots | Si | No, pero por labels se puede lograr |

AAS siendo un PAAS ofrece management de alto nivel a costa de la falta de control en algunas cosas.

KUB no es un PAAS, es un orquestrador de contenedores por lo que ofrece varias funcionalidades tambien pero es mucho mas duro en la configuracion.

Las funcionalidades mas importantes que queremos con KUB es el autoscale que nos esta dando muchos problemas en AAS debido a que al parecer el probing de AAS es solamente por ping al servidor entonces mientras la aplicacion prende (que se puede demorar unos 10-30 segundos), las peticiones van entrando y se demoran. Por otro lado, cuando el autoscale decrece, igualmente al parecer AAS no corta la comunicacion progresivamente o hay fallas (en la aplicacion?) y por lo tanto quedan usuarios conectados a esas instancias que estan eliminandose.

En KUB tienes el health probe que si puede apuntar a un url de la aplicacion y ademas tienes el ready probe que permite distinguir cuando la aplicacion realmente "calento" y puede atender con normalidad.
Estos dos puntos nos permitiran tener una mejor estabilidad, ademas que con KUB logramos ser cloud-agnostic que es uno de los grandes puntos de mejora que queremos lograr con la plataforma.

# Modelo
Entonces como se ve en las diferencias, hay que cubrir algunas cosas para que funcione en KUB de una manera segura y flexible.

## Reglas
- El cluster es uno para todas las aplicaciones y se llama `juntoz-kub-web-production`.
- El cluster tendra instalado la aplicacion `cert-manager` que automaticamente maneja la renovacion de certificados (Lets Encrypt y CA).
- Cada web aplicacion tendra su propio namespace dado que necesitamos crear muchos servicios para cada una.
- Las aplicaciones web nunca deben mostrarse al usuario final directamente, esto es un riesgo de seguridad ademas de performance.
- Cada aplicacion tendra su propio Dockerfile y encima de esta, se creara un ingress `nginx`.
- El DNS es manejado por Cloudflare, por lo tanto, tambien hay una proteccion alli.

## Prerequisitos
* Instalar `kubectl` en tu computadora https://kubernetes.io/docs/tasks/tools/install-kubectl. Para poder acceder a los clusters en Azure, ver el articulo "Kubernetes en Juntoz".

* Instalar `helm` en tu computadora https://helm.sh/docs/intro/install/. Azure Kubernetes ya contiene helm en el servidor. Helm usara el contexto actual de kubectl.
 
* Instalar `cert-manager` https://cert-manager.io/docs/installation/kubernetes/#installing-with-helm.
1. Instalar los CRDs.
2. Crear namespace para cert-manager.
3. Insertar su helm repo.
2. Ejecutar `helm install`.
Cert-manager sera global para todo el cluster.

## Pasos

* Crear un dockerfile para la aplicacion. Para una aplicacion netcore sera algo parecido a esto:

```
FROM mcr.microsoft.com/dotnet/core/sdk:3.1 as builder
WORKDIR /_build
COPY . ./
# restore using nuget.config
RUN dotnet restore src/App.csproj --configfile src/nuget.config
# publish using the last restore done
RUN dotnet publish src/App.csproj -c Release -o out --no-restore

FROM mcr.microsoft.com/dotnet/core/aspnet:3.1
WORKDIR /app
COPY --from=builder /_build/out ./
# we will expose http only because https will be managed in nginx
ENV ASPNETCORE_URLS=http://*:5000
EXPOSE 5000
ENTRYPOINT ["dotnet", "App.dll"]
```

Primero lo compilamos, extraemos el compilado y luego lo ejecutamos.
NOTA: para obtener los nugets privados, usaremos el nuget.config que contiene la configuracion y el usuario (con su PAT) para entrar a Azure Artifacts.
Todas las aplicaciones web abriran el puerto 5000 y solo necesitan abrir http. Https lo hara el ingress nginx.

* Crear un manifest para kubernetes para la aplicacion.
Se crea el namespace que englobara toda esta instalacion.
Se crea un deployment para la aplicacion. Aqui es donde se configura los probes y la imagen docker.
Se crea un servicio para que obtenga un IP asociado al deployment.

```
# create namespace for this app
apiVersion: v1
kind: Namespace
metadata:
   name: juntoz-web-id
---
# deploy the application
apiVersion: apps/v1
kind: Deployment
metadata:
  name: juntoz-web-id-dpy
  namespace: juntoz-web-id
spec:
  replicas: __kub-pod-instancecount__
  selector:
    matchLabels:
      app: juntoz-web-id-app
  template:
    metadata:
      namespace: juntoz-web-id
      labels:
        app: juntoz-web-id-app
    spec:
      containers:
      - name: juntoz-web-id-app
        image: __juntoz-docker-reg-url__/juntoz-web-id:__kub-pod-tag__
        ports:
        - containerPort: 5000
        resources:
          limits:
            memory: "800Mi"
        # livenessProbe:
        #   httpGet:
        #     path: /tools/probe
        #     port: 3000
        #   initialDelaySeconds: 3
        env:
        - name: LOGLEVEL
          value: __logLevel__
        - name: ASPNETCORE_ENVIRONMENT
          value: __envName__
        - name: Services__JuntozApi__Default
          value: __juntoz-api-gateway__
        - name: Services__JuntozIdentityV1__BaseUrl
          value: __juntoz-api-tokenv1-url__
---
# create the service that contains the application
apiVersion: v1
kind: Service
metadata:
  name: juntoz-web-id-svc
  namespace: juntoz-web-id
spec:
  selector:
    app: juntoz-web-id-app
  ports:
    - port: 5000
      targetPort: 5000
```

3. Crear un manifest para la instalacion de certificado usando `cert-manager`, `Let's Encrypt` y `Cloudflare`.

*TODO: no siempre usaremos LE para los certificados, asi que falta hacer el manifest para cuando son certificados propios.*

```
# insert account secret to modify dns settings in cloudflare for juntoz-staging.com (stg) and juntoz.com (prod)
apiVersion: v1
kind: Secret
metadata:
  name: juntoz-cloudflare-api-token
  namespace: juntoz-web-id
type: Opaque
stringData:
  api-token: __juntoz-cloudflare-token-dns__
---
# create the certificate issuer that connects to ACME and generates a certificate
apiVersion: cert-manager.io/v1alpha2
kind: Issuer
metadata:
  name: juntoz-web-id-cert-issuer
  namespace: juntoz-web-id
spec:
  acme:
    email: __juntoz-acme-email__
    server: https://acme-v02.api.letsencrypt.org/directory
    privateKeySecretRef:
      name: juntoz-web-id-cert-issuer-privkey
    solvers:
    - dns01:
        cloudflare:
          email: katlim@ieholding.com
          apiTokenSecretRef:
            # this token need to be linked to the cloudflare.email account
            name: juntoz-cloudflare-api-token
            key: api-token
---
# using the cert issuer, it is downloaded and stored as a secret
apiVersion: cert-manager.io/v1alpha2
kind: Certificate
metadata:
  name: juntoz-web-id-cert-info
  namespace: juntoz-web-id
spec:
  secretName: juntoz-web-id-cert-tls
  duration: 2160h # 90d
  renewBefore: 24h # 1d
  organization:
  - juntoz.com
  dnsNames:
  - __juntoz-id-host__
  issuerRef:
    name: juntoz-web-id-cert-issuer
```

* Crear el nginx-ingress controller
Para poder crear el ingress, el primer paso sera instalar el controlador de nginx ingress en el cluster. Esto sera por cada ingress instalado.

Para esto se usara como base esta documentacion: https://docs.nginx.com/nginx-ingress-controller/installation/installation-with-helm.

En resumen, lo que hay que ejecutar es este comando

```
helm install juntoz-web-id nginx-stable/nginx-ingress --namespace juntoz-web-id --set controller.ingressClass=nginx-juntoz-web-id --set controller.enableCustomResources=false --set controller.watchNamespace=juntoz-web-id --set rbac.create=true
```

** Para hacer un upgrade usar el isguiente comando. Notar el uso de `--reuse-values` para que no necesitar reescribir los parametros.
```
helm upgrade juntoz-web-id nginx-stable/nginx-ingress --namespace juntoz-web-id --reuse-values
```

* La primera palabra es el nombre de la instalacion. Tener en cuenta que automaticamente helm le anadira el sufijo `nginx-ingress` por lo tanto no es necesario agregarlo.
* Luego se asigna el namespace para que el ingress controller sea para este namespace nada mas.
* El campo `controller.ingressClass` es importante que haga match con el annotation `kubernetes.io/ingress.class` del **manifest debajo**. **Esta es la forma en la que el ingress controller se asocia con el ingress.**
* El ultimo parametro es `enableCustomResources=false` que desactivara los nuevos CRDs que NGINX presenta para mejorar la capacidad del ingress (como VirtualServer). Por ahora **no los estamos usando**. Esto campo se anadio tambien debido a que hay un bug que impide instalar un segundo ingress (https://github.com/nginxinc/kubernetes-ingress/issues/950).

Una vez instalado el ingress controller, el manifest puede ejecutarse.

* Crear el manifest para registrar la configuracion del ingress.
```
# set the ingress load balancer (nginx) and configure the rules here
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: juntoz-web-id-ing
  namespace: juntoz-web-id
  annotations:
    kubernetes.io/ingress.class: nginx-juntoz-web-id
    cert-manager.io/issuer: juntoz-web-id-cert-issuer
spec:
  rules:
  - host: __juntoz-id-host__
    http:
      paths:
      - backend:
          serviceName: juntoz-web-id-svc
          servicePort: 5000
        path: /
  tls:
  - hosts:
    - __juntoz-id-host__
    secretName: juntoz-web-id-cert-tls-use
```

* Una vez instalados los helm y ejecutados los manifests, modificar el DNS para que mapee con el ingress ip.

Para saber el IP debes ejecutar:
`kubectl get service,ingress -n juntoz-web-id`

Y el resultado debe ser:
```
NAME                                  TYPE           CLUSTER-IP     EXTERNAL-IP    PORT(S)                      AGE
service/juntoz-web-id-nginx-ingress   LoadBalancer   10.0.185.161   20.185.79.17   80:31201/TCP,443:31145/TCP   34d
service/juntoz-web-id-svc             ClusterIP      10.0.77.55     <none>         5000/TCP                     34d

NAME                                   HOSTS                   ADDRESS        PORTS     AGE
ingress.extensions/juntoz-web-id-ing   id.juntoz.com   20.185.79.17   80, 443   34d
```
NOTA: A veces, el ingress demora en obtener un IP, por lo tanto solo hay que esperar. Si es que nunca lo obtiene, es que hay un error en la configuracion.

Entonces en el DNS crear un registro `A` con el IP `20.185.79.17` para asociarlo al dominio segun sea necesario.




