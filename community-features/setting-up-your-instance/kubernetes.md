# Kubernetes

## Prerequisites

To deploy and use Defguard on your cluster you'll need:

* a [Kubernetes cluster](https://kubernetes.io/docs/setup/)
* Kubernetes CLI [kubectl](https://kubernetes.io/docs/reference/kubectl/) installed on your machine
* helm binary https://github.com/helm/helm/releases/latest

{% hint style="warning" %}
Our helm charts currently support only **Traefik ingress.**

**This only affects exposing GRPC services.**
{% endhint %}

## Deployment

We prepared a [git repository](https://github.com/DefGuard/deployment) with Kubernetes configuration, clone it:

```
git clone https://github.com/DefGuard/deployment.git && cd deployment/charts
```

Then create namespace for Defguard on your cluster:

```
kubectl create namespace defguard
```

Copy and fill in values file:

```
cp defguard/values.yaml ./
```

Required values (the rest should work if left as-is):

* `ingress.hosts.grpc`: GRPC ingress address - GRPC clients like defguard **gateway**, yubi-bridge

{% hint style="warning" %}
If you are configuring your gateway or yubi-bridge - please use this GRPC URL for communication.

If you have other ingress controller than traefik - you need to configure GRPC ingress manualy with corresponding to your setup.
{% endhint %}

* `ingress.hosts.web`: Web ingress address - Defguard webapp will be available here.
* `publicUrl`: Public URL your Defguard will be available under. Usually the same as ingress.hosts.web, but differ depending on your loadbalancer and/or reverse-proxy setup.

If you want to deploy the enrollment service along with your Defguard instance you also need to configure values related to the `defguard-prox`subchart:&#x20;

* `defguard-proxy.enabled`: enable the enrollment service
* `defguard-proxy.upstreamGrpcUrl`: defguard server gRPC endpoint URL
* `defguard-proxy.publicUrl`: public URL of the enrollment service
* `defguard-proxy.ingress.hosts.web`: enrollment service ingress address

And finally install the Helm chart in the namespace:

* `publicUrl`: Public URL your Defguard will be available under. Usually the same as ingress.hosts.web, but differ depending on your loadbalancer and/or reverse-proxy setup.

```
helm install --wait=true --namespace defguard defguard defguard -f values.yaml
```
