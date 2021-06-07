Para que las aplicaciones tengan TLS activado es necesario comprar un certificado digital de un CA prominente (Certificate Authority).

Nosotros usamos Namecheap.com.

**Al comprarlo no olvidar crear el CSR con el wildcard: `*.midominio.com`.**

Entonces ahora que usaremos mas Kubernetes, debemos introducir los certificados para que los ingress (load balancers al frente de las aplicaciones web) puedan hostear https.

Lo primero es obtener el certificado de namecheap. Esto despues de llenar el CSR y enviar los datos necesarios de ownership (DNS validation), se reciben dos archivos:
- micert.crt
- micert.ca-bundle

El archivo `micert.key` fue generado con el CSR y no se puede perder. Si se pierde, se tendria que recuperar el certificado.

Entonces para poder subir el certificado a Kubernetes, primero hay que preparar estos archivos para poder subirlos.

1. Entrar a `bash` y ejecutar este comando
```
cat micert.crt|base64 -w0>micert.b64.txt
cat micert.key|base64 -w0>mikey.b64.txt
```
Estos dos archivos seran usados luego.

2. Crear un manifest para un `Secret` que contendra este certificado de manera segura dentro de Kubernetes.

```
apiVersion: v1
kind: Secret
metadata:
  name: mi-web-secret
  namespace: mi-web
type: kubernetes.io/tls
data:
  tls.crt: LS0tLS1CRUdJTi....
  tls.key: LS0tLS1CRUdJTiBSU0EgUFJJV...
```
Para llenar el campo `tls.crt`, abrir el archivo micert.b64.txt y copiar y pegar todo el string y tal cual pegarlo en el manifest. 

**NO PUEDE HABER SALTOS DE LINEA, DEBE SER EL STRING COMPLETO**

No se puede tampoco usar comandos Yaml Multiline, porque siempre meten un espacio al final.

Para llenar el campo `tls.key`, seguir las mismas instrucciones con el archivo mikey.b64.txt.

3. En el manifest del `Ingress`, se debe entonces configurar el secret que se debe usar para obtener el certificado.

```
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: mi-web-ing
  namespace: mi-web
  annotations:
    kubernetes.io/ingress.class: nginx-mi-web
spec:
  rules:
  - host: miweb.com
    http:
      paths:
      - backend:
          serviceName: mi-web-svc
          servicePort: 5000
        path: /
  - host: "*.miweb.com"
    http:
      paths:
      - backend:
          serviceName: mi-web-svc
          servicePort: 5000
        path: /
  tls:
  - hosts:
    - "miweb.com"
    - "*.miweb.com"
    secretName: mi-web-secret
```


## Referencias
https://kubernetes.io/docs/concepts/services-networking/ingress/#tls
