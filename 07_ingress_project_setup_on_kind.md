# 🌐 Task 7: Kubernetes Ingress Demo on Kind — Complete Setup Guide

> 🚀 This project teaches you how to deploy two apps (Echo Server and NGINX) and expose them via **Ingress** using a custom hostname (`demo.local`) on a local **Kind** Kubernetes cluster.

---

## 📁 Folder Structure

```

k8s-ingress-demo/
├── kind-config.yaml
└── manifests/
├── app1-deployment.yaml
├── app2-deployment.yaml
└── ingress.yaml

````

---

## 🧰 Prerequisites

- Docker & Docker Desktop ✅
- Kind installed ✅
- kubectl installed ✅
- Windows admin access (to edit hosts file) ✅

Test tools:

```bash
docker --version
kind --version
kubectl version --client
````

---

## 🧱 Step 1: Create Kind Cluster with Port Mappings

📄 `kind-config.yaml`

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
    extraPortMappings:
      - containerPort: 80
        hostPort: 8080
      - containerPort: 443
        hostPort: 8443
```

Create the cluster:

```bash
kind create cluster --name ingress-demo --config kind-config.yaml
```

Check status:

```bash
kubectl cluster-info --context kind-ingress-demo
```

---

## 🌐 Step 2: Install Ingress Controller

Install NGINX ingress controller:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.1/deploy/static/provider/kind/deploy.yaml
```

Wait for the controller to be ready:

```bash
kubectl get pods -n ingress-nginx -w
```

You should see `ingress-nginx-controller` in **Running** state.

---

## 🚀 Step 3: Deploy Applications

### 🔹 App 1 – Echo Server

📄 `manifests/app1-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app1-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app1
  template:
    metadata:
      labels:
        app: app1
    spec:
      containers:
        - name: app1
          image: ealen/echo-server
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app1-service
spec:
  selector:
    app: app1
  ports:
    - port: 80
      targetPort: 80
```

---

### 🔹 App 2 – NGINX

📄 `manifests/app2-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: app2-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: app2
  template:
    metadata:
      labels:
        app: app2
    spec:
      containers:
        - name: app2
          image: nginx
          ports:
            - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: app2-service
spec:
  selector:
    app: app2
  ports:
    - port: 80
      targetPort: 80
```

---

## 🌍 Step 4: Create Ingress Resource

📄 `manifests/ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  ingressClassName: nginx
  rules:
    - host: demo.local
      http:
        paths:
          - path: /app1(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: app1-service
                port:
                  number: 80
          - path: /app2(/|$)(.*)
            pathType: Prefix
            backend:
              service:
                name: app2-service
                port:
                  number: 80
```

---

## 📦 Step 5: Apply All Resources

```bash
kubectl apply -f manifests/
```

---

## 🧾 Step 6: Add Host Mapping

Edit your hosts file:

📍 Windows path: `C:\Windows\System32\drivers\etc\hosts`

Add this line at the bottom:

```
127.0.0.1 demo.local
```

💡 You may need **Notepad (Run as Administrator)** to save this file.

---

## 🌐 Step 7: Test the Ingress Routes

### Test via Browser:

* App1: [http://demo.local:8080/app1](http://demo.local:8080/app1)
* App2: [http://demo.local:8080/app2](http://demo.local:8080/app2)

### Test via curl:

```bash
curl.exe -H "Host: demo.local" http://localhost:8080/app1
curl.exe -H "Host: demo.local" http://localhost:8080/app2
```

✅ If successful:

* App1 returns JSON request metadata
* App2 shows NGINX welcome page

---

## 🧹 Cleanup (Optional)

```bash
kind delete cluster --name ingress-demo
```

---

## ✅ What You Practiced

| Feature                 | Description                      |
| ----------------------- | -------------------------------- |
| Kind multi-node cluster | Custom config with port mappings |
| Ingress controller      | Installed using official NGINX   |
| Ingress resource        | Routes traffic to apps           |
| Hostname routing        | Simulated using hosts file       |
| Real-world use          | Two apps behind one endpoint     |

