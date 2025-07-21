# 🔧 Task 6: Deploy a Node.js Web App with MongoDB on Kind Kubernetes Cluster

> 🚀 In this task, you'll deploy a Node.js app that connects to a MongoDB database inside a Kubernetes cluster. You'll use YAML files to define deployments and services for both components.

---

## 📁 Folder Structure

```

task6/
├── mongo-deployment.yml
├── mongo-service.yml
├── webapp-deployment.yml
└── webapp-service.yml

````

---

## 🧰 Prerequisites

- Kind cluster running (`kind create cluster`)
- `kubectl` installed
- Docker running

---

## 🛠️ Step-by-Step Setup

### 1️⃣ Create the Project Folder

```bash
mkdir ~/task6
cd ~/task6
touch mongo-deployment.yml mongo-service.yml webapp-deployment.yml webapp-service.yml
````

---

### 2️⃣ Define MongoDB Deployment

**📄 `mongo-deployment.yml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mongo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: mongo
  template:
    metadata:
      labels:
        app: mongo
    spec:
      containers:
        - name: mongo
          image: mongo:4.4
          ports:
            - containerPort: 27017
```

---

### 3️⃣ Define MongoDB Service

**📄 `mongo-service.yml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: mongo-service
spec:
  selector:
    app: mongo
  ports:
    - port: 27017
```

This exposes the MongoDB deployment internally within the cluster.

---

### 4️⃣ Define Web App Deployment

**📄 `webapp-deployment.yml`**

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp
spec:
  replicas: 1
  selector:
    matchLabels:
      app: webapp
  template:
    metadata:
      labels:
        app: webapp
    spec:
      containers:
        - name: webapp
          image: kodekloud/webapp-color
          ports:
            - containerPort: 8080
          env:
            - name: DB_HOST
              value: "mongo-service"
```

The environment variable `DB_HOST` allows the web app to connect to MongoDB.

---

### 5️⃣ Define Web App Service (External Access)

**📄 `webapp-service.yml`**

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  type: NodePort
  selector:
    app: webapp
  ports:
    - port: 8080
      targetPort: 8080
      nodePort: 30009
```

This allows you to access the app at `localhost:30009`.

---

## 🚀 Step 6: Deploy to Kubernetes

```bash
# Deploy MongoDB
kubectl apply -f mongo-deployment.yml
kubectl apply -f mongo-service.yml

# Deploy Web App
kubectl apply -f webapp-deployment.yml
kubectl apply -f webapp-service.yml
```

---

## ✅ Step 7: Verify Resources

```bash
kubectl get pods
kubectl get deployments
kubectl get svc
```

You should see:

* Pods: `mongo-*` and `webapp-*`
* Services: `mongo-service` (internal), `webapp-service` (NodePort)

---

## 🌐 Step 8: Access Web App

Open your browser or terminal:

```
http://localhost:30009
```

Or test with curl:

```bash
curl http://localhost:30009
```

---

## 🧹 Step 9: Clean Up

```bash
kubectl delete -f webapp-service.yml
kubectl delete -f webapp-deployment.yml
kubectl delete -f mongo-service.yml
kubectl delete -f mongo-deployment.yml
```

---

## 📘 Summary

| Resource                | Description                                    |
| ----------------------- | ---------------------------------------------- |
| `mongo-deployment.yml`  | Deploys MongoDB pod                            |
| `mongo-service.yml`     | Makes MongoDB accessible to other pods         |
| `webapp-deployment.yml` | Deploys a Node.js app that connects to MongoDB |
| `webapp-service.yml`    | Exposes the app on NodePort for browser access |

