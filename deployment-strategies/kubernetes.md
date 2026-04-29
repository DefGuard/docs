---
metaLinks:
  alternates:
    - >-
      https://app.gitbook.com/s/e86iamwJVSYnIRsyVEAV/deployment-strategies/kubernetes
---

# Kubernetes

## Prerequisites

To deploy and use Defguard on your cluster, you'll need:

* A [Kubernetes cluster](https://kubernetes.io/docs/setup/)
* Kubernetes CLI [kubectl](https://kubernetes.io/docs/reference/kubectl/) installed on your machine
* Helm [binary](https://github.com/helm/helm/releases/latest)

{% hint style="warning" %}
The Helm charts currently support only **Traefik ingress – which is relevant and affects exposing gRPC services** (see below)
{% endhint %}

Kubernetes configuration can be found in the [git repository](https://github.com/DefGuard/deployment).

## Deployment

Add Defguard Helm chart repository:

```sh
helm repo add defguard https://defguard.github.io/deployment
```

In Kubernetes cluster, create a namespace for Defguard:

```sh
kubectl create namespace defguard
```

Examine available values for the Helm chart:

```sh
helm show values defguard/defguard
```

To set some values, either crate a file named **values.yaml**, or use `--set` option for `helm` command.

{% hint style="warning" %}
If use other ingress controller than [Traefik](https://doc.traefik.io/traefik/), the configuration for gRPC ingresses (which use HTTP/2) may need to be adjusted accordingly.
{% endhint %}

Install the Helm chart in the namespace:

```sh
helm install --wait=true --namespace defguard defguard defguard -f values.yaml
```

### Defguard Edge

If you want to deploy Defguard Edge service along with your Defguard instance, you also need to configure values related to the `defguard-proxy`subchart:

* `defguard-proxy.enabled`: enable the enrollment service
* `proxyUrl`: proxy gRPC endpoint URL (based on `defguard-proxy.ingress.grpc.host`)
* `defguard-proxy.publicUrl`: public URL of the enrollment service
* `defguard-proxy.ingress.web.host`: enrollment service web ingress address (the enrollment website)
* `defguard-proxy.ingress.grpc.host`: enrollment service gRPC ingress address (for communicating with core)

### Defguard Gateway

If you want to deploy Defguard Gateway service along with your Defguard instance, you need to configure values related to the `defguard-gateway` subchart:

* `defguard-gateway.enabled`: enable the VPN gateway service
