# Octopod

### This is beta version of a chart!

[Octopod](https://octopod.site/) is a fully open-source self-hosted solution for managing multiple deployments in a Kubernetes cluster with a user-friendly web interface. Managing deployments does not require any technical expertise.

## TL;DR
TBD (install chart from typeable's chart repository)

## Introduction

This chart bootstraps an Octopod deployment on a [Kubernetes](http://kubernetes.io) cluster using the [Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.12+
- Helm 3.1.0
- PV support (for postgresql persistense)
- nginx-ingress controller

## Installing the Chart
This chart will not create or delete any namespaces for you.
You'll need to create 2 namespaces before installing:
First in which octopod will be installed
```console
$ kubectl create namespace octopod
```
Second in which Octopod will deploy all it's deployments (configured in octopod.deploymentNamespace)
```console
$ kubectl create namespace octopod-deployments
```
Also you need to generate certificates for octo client<->octopod server communication.

Generate certificates
```bash
mkdir certs
cd certs && \
openssl req -x509 -newkey rsa:4096 -keyout server_key.pem -out server_cert.pem -nodes -subj "/CN=localhost/O=Server" && \
openssl req -newkey rsa:4096 -keyout client_key.pem -out client_csr.pem -nodes -subj "/CN=Client" && \
openssl x509 -req -in client_csr.pem -CA server_cert.pem -CAkey server_key.pem -out client_cert.pem -set_serial 01 -days 3650

```
Create configmap from generated certificates
```console
kubectl create configmap octopod-certs -n octopod --from-file=./certs
```
Name for configmap is cofigured in octopod.certsConfigMapName  
To install the chart with the release name `my-release` from current directory execute:

```console
$ helm dependency build
$ helm -n octopod install my-release .
```

The command deploys Octopod on the Kubernetes cluster in the default configuration inside octopod namespace. The [Parameters](#parameters) section lists the parameters that can be configured during installation.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```console
$ helm -n octopod delete my-release
```

The command removes all the Kubernetes components but PVC's associated with the postgres chart and deletes the release.

## Parameters

The following tables lists the configurable parameters of the Octopod chart and their default values.

| Key | Type | Default | Description |
|-----|------|---------|-------------|
| affinity | object | `{}` | Octopod deployment affinity |
| controlScripts.image.pullPolicy | string | `"IfNotPresent"` | control scripts image pull policy |
| controlScripts.image.repository | string | `"typeable/octopod-helm-example"` | Control scripts image repository (you probably want to use your own control scripts) |
| controlScripts.image.tag | float | `1.1` | Control scitpts image tag |
| fullnameOverride | string | `""` | Override chart full name  |
| image.pullPolicy | string | `"IfNotPresent"` | Octopod image pull policy |
| image.repository | string | `"typeable/octopod"` | Octopod image repository |
| image.tag | string | `""` | Octopod image tag (default values is taken from chart metadata) |
| imagePullSecrets | list | `[]` | Pull secrets if you want to use private registry  |
| ingress.app.annotations | object | `{}` | Additional ingress annotations for app service |
| ingress.app.host | string | `"octopod-app.example.com"` | Hostname for app service |
| ingress.enabled | bool | `true` | Create ingress objects or not |
| ingress.ingressClass | string | `"nginx"` | Ingress class |
| ingress.powerApp.annotations | object | `{}` | Additional ingress annotations for power-app service  |
| ingress.powerApp.host | string | `"octopod-powerapp.example.com"` | Hostname for power-app |
| ingress.tls.clusterIssuer | string | `"letsencrypt"` | Name of cluster issuer you want to use |
| ingress.tls.enabled | bool | `true` | Use https for all services |
| ingress.ui.annotations | object | `{}` | Additional ingress annotations for ui service |
| ingress.ui.host | string | `"octopod.example.com"` | Hostname for main UI |
| ingress.ws.annotations | object | `{}` | Additional ingress annotations for ws service |
| ingress.ws.host | string | `"octopod-ws.example.com"` | Hostname for websockets ingress |
| nameOverride | string | `""` | Name ovveride (default is Release.Name) |
| nodeSelector | object | `{}` | Node selector if you want octopod to use specific nodes onn your cluster |
| octopod.archiveRetention | int | `1209600` |  |
| octopod.baseDomain | string | `""` | Domain that will be used as a ase for Octopod deploymets |
| octopod.certsConfigMapName | string | `"octopod-certs"` | Config map with self-signed certificates |
| octopod.deploymentNamespace | string | `"octopod-deployment"` | Name of a namespace which will be used for all Octopod deployments (you need to create it yourself) |
| octopod.migrations.enabled | bool | `true` | Enable or not automatic DB schema migrations |
| octopod.projectName | string | `"Octopod"` | Project name |
| octopod.statusUpdateTimeout | int | `600` | Time to wait before deployment is marked as failed |
| podAnnotations | object | `{}` | Additional pod annotations |
| podSecurityContext | object | `{}` | Additional security context |
| postgresql.enabled | bool | `true` | Use bitnami postgres chart |
| postgresql.postgresqlDatabase | string | `"octopod"` | Database name for octopod |
| postgresql.postgresqlUsername | string | `"octopod"` | Octopod DB username |
| rbac.create | bool | `true` | Creates ClusterRoles and Bindings for Octopod service account |
| replicaCount | int | `1` | Number of Octopod replicas |
| resources.limits.cpu | string | `"200m"` | CPU limits |
| resources.limits.memory | string | `"512Mi"` | Memory limits |
| resources.requests.cpu | string | `"200m"` | CPU requests |
| resources.requests.memory | string | `"256Mi"` | Memory requests |
| securityContext.runAsGroup | int | `1000` | Use non-root GID |
| securityContext.runAsUser | int | `1000` | Use non-root UID |
| service.ports.app | int | `4000` | App service port |
| service.ports.powerApp | int | `4443` | Power app service port |
| service.ports.ui | int | `80` | UI service port |
| service.ports.ws | int | `4020` | WebSockets service port  |
| service.type | string | `"ClusterIP"` | Octopod service type |
| serviceAccount.annotations | object | `{}` | Additional anotations for Octopod's SA |
| serviceAccount.create | bool | `true` | Create ServiceAccount |
| serviceAccount.name | string | `""` | ServiceAccount name override |
| sqitch.image.pullPolicy | string | `"IfNotPresent"` | squitch image pull policy |
| sqitch.image.repository | string | `"typeable/sqitch"` | squitch image repository |
| sqitch.image.tag | string | `"v2.0.0"` | squitch image tag |
| tolerations | list | `[]` | Octpod deploymet tolerations |


Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`. For example,

```console
$ helm -n octopod install my-release \
  --set controlScripts.image.repository=registry.example.com/control,controlScripts.image.tag=0.0.1 \
    .
```

The above command sets control scripts image to your custom repository

Alternatively, a YAML file that specifies the values for the parameters can be provided while installing the chart. For example,

```console
$ helm install my-release -f values.yaml .
```

> **Tip**: You can use the default [values.yaml](values.yaml)

## Configuration and installation details

If you want to have authentication for Octopod UI you can use project like [Oauth2-Proxy](https://github.com/oauth2-proxy/oauth2-proxy) or directly use your oauth provider.
After that you can add following annotations to UI ingress (considering that your oauth provider installed on oath.example.com):
```yaml
ingress:
  ui:
    annotations:
      nginx.ingress.kubernetes.io/auth-url: "https://oauth.example.com/oauth2/auth"
      nginx.ingress.kubernetes.io/auth-signin: "https://oauth.example.com/oauth2/start?rd=/redirect/$http_host$request_uri"
```
