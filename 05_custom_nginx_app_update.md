# 🌐 Task 5: Deploy and Update a Custom NGINX Web App on Kind (From Scratch)

> 🚀 In this task, you’ll learn to containerize your own HTML app with NGINX, deploy it on Kubernetes using Kind, and update the web content using rolling updates.

---

## 🧰 Prerequisites

- Docker (running)
- Kind
- kubectl

Test commands:

```bash
docker --version
kind --version
kubectl version --client
````

---

## 📁 Project Structure

```
webapp-k8s/
├── app/
│   ├── index.html        # Your custom webpage
│   └── Dockerfile        # To build a custom NGINX image
├── manifests/
│   ├── deployment.yaml   # K8s deployment file
│   └── service.yaml      # K8s service file
```

---

## 🧱 Step 1: Create a Kind Cluster

```bash
kind create cluster --name webapp-cluster
kubectl get nodes
```

---

## ✏️ Step 2: Create Your Custom Web Page

Create a file: `app/index.html`

```html
<!DOCTYPE html>
<html>
<head>
  <title>My Custom WebApp</title>
</head>
<body>
  <h1>🚀 Welcome to My Kubernetes-Powered Web App!</h1>
  <p>Deployed using KIND + Custom NGINX</p>
</body>
</html>
```

---

## 🐳 Step 3: Create a Dockerfile

Create a file: `app/Dockerfile`

```Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

---

## 🔨 Step 4: Build Docker Image & Load into Kind

```bash
cd app
docker build -t custom-nginx:1.0 .
kind load docker-image custom-nginx:1.0 --name webapp-cluster
```

---

## 📄 Step 5: Create Kubernetes Manifests

### `manifests/deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: webapp-deployment
spec:
  replicas: 2
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
          image: custom-nginx:1.0
          ports:
            - containerPort: 80
```

### `manifests/service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: webapp-service
spec:
  selector:
    app: webapp
  ports:
    - port: 80
      targetPort: 80
```

---

## 🚀 Step 6: Deploy to Kubernetes

```bash
kubectl apply -f manifests/
kubectl get pods
kubectl get svc
```

---

## 🌐 Step 7: Access Web App in Browser

```bash
kubectl port-forward svc/webapp-service 8080:80
```

Then open:

```
http://localhost:8080
```

✅ You should see your custom welcome page!

---

## 🔁 Step 8: Update the App Content

### 🧾 Step 1: Edit `app/index.html`

Change the content (e.g., update the welcome message).

### 🔨 Step 2: Rebuild Docker Image with New Tag

```bash
docker build -t custom-nginx:1.1 .
kind load docker-image custom-nginx:1.1 --name webapp-cluster
```

### 📝 Step 3: Update the Deployment YAML

Edit `deployment.yaml`:

```yaml
image: custom-nginx:1.1
```

### 🚀 Step 4: Re-apply the Deployment

```bash
kubectl apply -f manifests/deployment.yaml
kubectl rollout status deployment/webapp-deployment
```

### 🔄 Step 5: Access Updated Web App

```bash
kubectl port-forward svc/webapp-service 8080:80
```

Open: `http://localhost:8080`
✅ You’ll see the updated content.

---

## 🧹 Step 9: Cleanup (When Done)

```bash
kubectl delete -f manifests/
kind delete cluster --name webapp-cluster
```

---

## ⚙️ Bonus: Automation Script (Optional)

Create a `deploy.sh` file:

```bash
#!/bin/bash

TAG=$1
if [ -z "$TAG" ]; then
  echo "Usage: ./deploy.sh <tag>"
  exit 1
fi

docker build -t custom-nginx:$TAG ./app
kind load docker-image custom-nginx:$TAG --name webapp-cluster
sed "s|custom-nginx:.*|custom-nginx:$TAG|" manifests/deployment.yaml > manifests/tmp-deployment.yaml
kubectl apply -f manifests/tmp-deployment.yaml
rm manifests/tmp-deployment.yaml
```

Usage:

```bash
chmod +x deploy.sh
./deploy.sh 1.2
```

---

## 🧠 Concepts Practiced

* Build custom Docker image
* Load image into Kind
* Deploy web app using Deployment + Service
* Port-forwarding to access apps locally
* Rolling updates using image tags
* Automating deployments with bash scripts


