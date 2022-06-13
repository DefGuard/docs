# Kubernetes using Helm

To deploy and use Defguard on your cluster you'll need a [Kubernetes cluster](https://kubernetes.io/docs/setup/),
Kubernetes CLI [kubectl](https://kubernetes.io/docs/reference/kubectl/) installed on your machine

## Before we start

1. Install NGINX ingress controller you can find detailed instruction [here](https://kubernetes.github.io/ingress-nginx/deploy/).

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

First step is to k8s folder in core repository in this folder create file named **deployment.yaml**

**core/k8s/deployment.yaml**
```
apiVersion: apps/v1
kind: Deployment
metadata:
  name: core
  labels:
    app: defguard
    service: core
spec:
  strategy:
    type: Recreate
  replicas: 1
  selector:
    matchLabels:
      app: defguard
      service: core
  template:
    metadata:
      labels:
        app: defguard
        service: core
    spec:
      containers:
        - name: core
          image: registry.teonite.net/defguard/core:latest
          imagePullPolicy: Always
          envFrom:
            - configMapRef:
                name: web
            - secretRef:
                name: web
          ports:
            - name: http
              containerPort: 8000
            - name: grpc
              containerPort: 50055
          volumeMounts:
            - name: data
              mountPath: /storage
      securityContext:
        runAsUser: 1000
        runAsGroup: 1000
        fsGroup: 1000
      volumes:
        - name: data
          persistentVolumeClaim:
            claimName: core-data
```

Next we need to create [**ingress.yaml**]("https://kubernetes.io/docs/concepts/services-networking/ingress/") to handle our GRPC client 

**core/k8s/ingress.yaml**
```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  annotations:
    ingress.kubernetes.io/protocol: h2c
  name: defguard-grpc-ingress
spec:
  rules:
    - host: defguard-grpc.example.net
      http:
        paths:
          - path: /
            pathType: ImplementationSpecific
            backend:
              service:
                name: core
                port:
                  name: grpc
```

Next create our service file and name it **service.yaml** here we bind our ports

```
apiVersion: v1
kind: Service
metadata:
  name: core
  labels:
    app: defguard
    service: core
spec:
  ports:
    - name: http
      port: 8000
      targetPort: "http"
      protocol: TCP
    - name: grpc
      port: 50055
      targetPort: "grpc"
      protocol: TCP
  selector:
    app: defguard
    service: core

```

Next step create persistent volume claim to store our Sqlite database file let's name it **storage.yaml**

**core/k8s/storage.yaml**	

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: core-data
  labels:
    app: defguard
    service: core
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 128Mi
```

Next create **config.env** file where we put our configuration 

**core/k8s/config.env**

You can find all configurable thing [here]("https://github.com/DefGuard/docs/blob/docs/in-depth/environmental-variables-configuration.md")

```
DEFGUARD_DATABASE_URL=sqlite:/storage/defguard.db
DEFGUARD_LDAP_URL=ldap://oldap:389
DEFGUARD_WG_SERVICE_URL=http://wireguard:50051
```
**Note** we recommend to store DEFGUARD_JWT_SECRET in 
[Kubernetes secrets]("https://kubernetes.io/docs/concepts/configuration/secret/")

Last step is to create our **kustomization.yaml** to point all our files

```
resources:
  - storage.yaml
  - service.yaml
  - deployment.yaml
  - ingress.yaml
configMapGenerator:
  - name: web
    envs:
      - config.env
```
then apply our changes

```
kubectl apply -n defguard -f core/k8s
```
