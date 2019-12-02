# Harbor Scanner Aqua

Aqua CSP Scanner as a plug-in vulnerability scanner in the Harbor registry.

## TL;DR;

### Without TLS

```
$ helm install harbor-scanner-aqua . \
               --namespace harbor \
               --set scanner.aqua.user=$AQUA_USER \
               --set scanner.aqua.password=$AQUA_PASSWORD \
               --set scanner.aqua.host=http://csp-console-svc.aqua:8080
```

### With TLS

1. Generate certificate and private key files:
   ```
   $ openssl genrsa -out tls.key 2048
   $ openssl req -new -x509 \
                 -key tls.key \
                 -out tls.crt \
                 -days 365 \
                 -subj /CN=harbor-scanner-aqua.harbor
   ```
2. Install the `harbor-scanner-aqua` chart:
   ```
   $ helm install harbor-scanner-aqua . \
                  --namespace harbor \
                  --set service.port=8443 \
                  --set scanner.api.tlsEnabled=true \
                  --set scanner.api.tlsCertificate="`cat tls.crt`" \
                  --set scanner.api.tlsKey="`cat tls.key`" \
                  --set scanner.aqua.user=$AQUA_USER \
                  --set scanner.aqua.password=$AQUA_PASSWORD \
                  --set scanner.aqua.host=http://csp-console-svc.aqua:8080
   ```

## Introduction

This chart bootstraps a scanner adapter deployment on a [Kubernetes](http://kubernetes.io) cluster using the
[Helm](https://helm.sh) package manager.

## Prerequisites

- Kubernetes 1.12+
- Helm 2.11+ or Helm 3+

## Installing the Chart

To install the chart with the release name `my-release`:

```
$ helm install my-release .
```

The command deploys scanner adapter on the Kubernetes cluster in the default configuration. The [Parameters](#parameters)
section lists the parameters that can be configured during installation.

> **Tip**: List all releases using `helm list`.

## Uninstalling the Chart

To uninstall/delete the `my-release` deployment:

```
$ helm delete my-release
```

The command removes all the Kubernetes components associated with the chart and deletes the release.

## Parameters

The following table lists the configurable parameters of the scanner adapter chart and their default values.

|           Parameter          |                                Description                              |    Default     |
|------------------------------|-------------------------------------------------------------------------|----------------|
| `scanner.logLevel`           | The log level of `trace`, `debug`, `info`, `warn`, `warning`, `error`, `fatal` or `panic`. The standard logger logs entries with that level or anything above it | `info` |
| `scanner.aqua.user`          | Aqua management console username (required)                             | N/A            |
| `scanner.aqua.password`      | Aqua management console password (required)                             | N/A            |
| `scanner.aqua.host`          | Aqua management console address                                         | `http://aqua-web.aqua-security:8080` |
| `scanner.aqua.registry`      | The name of the Harbor registry configured in Aqua management console   | `Harbor`       |
| `scanner.api.tlsEnabled`     | The flag to enable or disable TLS for HTTP                              | `true`         |
| `scanner.api.tlsCertificate` | The absolute path to the x509 certificate file                          |                |
| `scanner.api.tlsKey`         | The absolute path to the x509 private key file                          |                |
| `scanner.api.readTimeout`    | The maximum duration for reading the entire request, including the body | `15s`          |
| `scanner.api.writeTimeout`   | The maximum duration before timing out writes of the response           | `15s`          |
| `scanner.api.idleTimeout`    | The maximum amount of time to wait for the next request when keep-alives are enabled | `60s` |
| `service.type`               | Kubernetes service type                                                 | `LoadBalancer` |
| `service.port`               | Kubernetes service port                                                 | `8443`         |
| `image.registry`             | Image registry                                                          | `docker.io`    |
| `image.repository`           | Image name                                                              | `aquasec/harbor-scanner-aqua` |
| `image.tag`                  | Image tag                                                               | `{TAG_NAME}`   |
| `image.pullPolicy`           | Image pull policy                                                       | `IfNotPresent` |
| `replicaCount`               | Number of scanner adapter Pods to run                                   | `1`            |

The above parameters map to the env variables defined in [harbor-scanner-aqua](https://github.com/aquasecurity/harbor-scanner-aqua#configuration).

Specify each parameter using the `--set key=value[,key=value]` argument to `helm install`.

```
$ helm install my-release . \
               --namespace my-namespace \
               --set scanner.aqua.user=$AQUA_USER \
               --set scanner.aqua.password=$AQUA_PASSWORD
```