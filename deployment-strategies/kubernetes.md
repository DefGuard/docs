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
* Helm binary https://github.com/helm/helm/releases/latest

{% hint style="warning" %}
Our helm charts currently support only **Traefik ingress - which is relevant and affects exposing GRPC services (see below** `ingress.hosts.grpc`**`).`**
{% endhint %}

## Deployment

We prepared a [git repository](https://github.com/DefGuard/deployment) with Kubernetes configuration, clone it with:

```
git clone https://github.com/DefGuard/deployment.git && cd deployment/charts
```

Then create a namespace for Defguard on your cluster:

```
kubectl create namespace defguard
```

Copy and fill in values file:

```
cp defguard/values.yaml ./
```

Required values (the rest should work if left as-is):

* `ingress.hosts.grpc`: GRPC ingress address - GRPC clients like Defguard **gateway**, yubi-bridge

{% hint style="warning" %}
If you are configuring your gateway or yubi-bridge - please use this GRPC URL for communication.

If you have other ingress controller than traefik - you need to configure GRPC ingress manually with corresponding to your setup.
{% endhint %}

* `ingress.hosts.web`: Web ingress address - Defguard web app will be available here.
* `publicUrl`: Public URL your Defguard will be available under. Usually the same as ingress.hosts.web, but differs depending on your load balancer and/or reverse-proxy setup.

And finally, install the Helm chart in the namespace:

```
helm install --wait=true --namespace defguard defguard defguard -f values.yaml
```

### Proxy service

If you want to deploy the enrollment service along with your Defguard instance, you also need to configure values related to the `defguard-proxy`subchart:

* `defguard-proxy.enabled`: enable the enrollment service
* `proxyUrl`: proxy gRPC endpoint URL (based on `defguard-proxy.ingress.grpc.host`)
* `defguard-proxy.publicUrl`: public URL of the enrollment service
* `defguard-proxy.ingress.web.host`: enrollment service web ingress address (the enrollment website)
* `defguard-proxy.ingress.grpc.host`: enrollment service gRPC ingress address (for communicating with core)

### VPN gateway service

If you want to deploy the VPN gateway service along with your Defguard instance, you need to do it in two steps:

* first deploy the core service and use the web UI to [setup a VPN location](../tutorials/step-by-step-setting-up-a-vpn-server/adding-additional-vpn-locations.md)
* copy the gateway token and proceed to deploying the gateway itself

To deploy the gateway service, configure values related to the `defguard-gateway`subchart:

* `defguard-gateway.enabled`: enable the VPN gateway service
* `defguard-gateway.token`: the gateway token generated in Web UI
* `defguard-gateway.grpcUrl`: URL where the core gRPC server is available (based on `defguard.ingress.grpc.host`)
