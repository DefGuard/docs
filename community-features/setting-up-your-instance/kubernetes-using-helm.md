# Kubernetes using Helm

To deploy and use Defguard on your cluster you'll need a [Kubernetes cluster](https://kubernetes.io/docs/setup/),
Kubernetes CLI [kubectl](https://kubernetes.io/docs/reference/kubectl/) installed on your machine

1. Install NGINX ingress controller you can find detailed instruction [here](https://kubernetes.github.io/ingress-nginx/deploy/)

2. Clone frontend, core and gateway repositiories

Frontend
```
git clone git@github.com:DefGuard/frontend.git 
```
Core server
```
git clone git@github.com:DefGuard/core.git 
```
Gateway
```
git clone git@github.com:DefGuard/wireguard-gateway.git 
```

3. In Frontend and Core repositories create directories named k8s

##  Frontend k8s setup

in k8s directory first create frontend-deployment.yaml file it'll configure our deployment	

**frontend/k8s/frontend-deployment.yaml**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: frontend
  labels:
    app: defguard
    service: frontend
spec:
  replicas: 1
  selector:
    matchLabels:
      app: defguard
      service: frontend
  template:
    metadata:
      labels:
        app: defguard
        service: frontend
    spec:
      containers:
        - name: defguard
          image: registry.teonite.net/defguard/frontend:latest
          ports:
            - containerPort: 80
```
Now lets create our pod service and name it **frontend-service.yaml**

**frontend/k8s/frontend-deployment.yaml**
```yaml
apiVersion: v1
kind: Service
metadata:
  labels:
    app: defguard
    service: frontend
  name: frontend
spec:
  ports:
    - name: http
      port: 80
      targetPort: 80
  selector:
    app: defguard
    service: frontend
```

Now we'll create our ingress file which will redirect our Api calls to core server and name it **ingress.yaml**

**frontend/k8s/ingress.yaml**
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: defguard-frontend-ingress
  namespace: defguard
spec:
  rules:
  - host: defguard.example.net
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: frontend
            port:
              name: http
      - path: /api
        pathType: Prefix
        backend:
          service:
            name: core
            port:
              name: http
```

Last step in frontend k8s setup is to create our **kustomization.yaml** file where we link our resources

**frontend/k8s/kustomization.yaml**
```
resources:
  - frontend-deployment.yaml
  - frontend-service.yaml
  - ingress.yaml
```
Before this step make sure you are conencted to your Kubernetes cluster
After creating our k8s folder we can create Defguard namespace

`kubectl create ns defguard`

then apply our changes

```
kubectl apply -n defguard -f frontend/k8s
```
## Core server k8s setup

