# Crear un AKS Cluster en Azure

**Para Staging**
1. Nombre de cluster: `juntoz-kub-xxx`
1. Resource Group `juntoz-rg-staging-kub`
1. Nodo `Standard_B2ms` *(EVALUANDO, B2 son burstable, por lo que no necesariamente te garantizan capacidad, y para kubernetes eso es importante. Deberia ser o Standard_B2ms o Standard_B2)*
1. Nodes `3`
1. Scale Virtual Nodes `Disabled`
1. Scale VM Scale Sets `Enabled`
1. Service Principal `New`
1. Enable RBAC `Yes`
1. Networking Http Application Routing `No`
1. Network Configuration `Basic`
1. Container Monitoring `Yes`
1. Log Analytics Workspace `Reuse or New?` (*Reuse if it's a small cluster and it is not worth the separation with other clusters*)

**Para Produccion**
Usar los mismos settings de Staging, excepto por:
1. Resource Group `juntoz-rg-production-kub`
1. Nodo `Standard_D2s_v3`

**IMPORTANTE: Los tamanos de las maquinas deben tener ser consistentes a las guias dadas arriba dado que se compraran instancias reservadas y tienen que hacer match exacto en el tamano.**

# Asociar el juntozdockreg al cluster
Para poder usar las imagenes que estan en nuestro registry interno **juntozdockreg**, hay que conectar el AKS con el registry (ACR) ([info](https://docs.microsoft.com/en-us/azure/aks/cluster-container-registry-integration)).

Ir al Azure CLI (dentro del mismo portal o en tu maquina), y ejecutar este comando

```
az aks update -n juntoz-kub-api-staging -g juntoz-rg-staging-kub --attach-acr juntozdockreg
```
Donde: n (nombre del cluster destino) y g (resource group donde esta ese cluster)
