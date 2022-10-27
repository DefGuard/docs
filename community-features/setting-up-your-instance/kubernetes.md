# Deploy Defguard with Kubernetes using Helm

## Prerequisites

To deploy and use Defguard on your cluster you'll need:

* a [Kubernetes cluster](https://kubernetes.io/docs/setup/)
* Kubernetes CLI [kubectl](https://kubernetes.io/docs/reference/kubectl/) installed on your machine
* helm binary https://github.com/helm/helm/releases/latest

## Deployment

We prepared a [git repository](https://github.com/DefGuard/deployment) with Kubernetes configuration, clone it:

```
git clone git@github.com:DefGuard/deployment.git && cd deployment
```

Then create namespace for Defguard on your cluster:

```
kubectl create namespace defguard
```

And finally install the Helm chart in the namespace:

```
helm install --wait=true --namespace defguard defguard defguard
```
