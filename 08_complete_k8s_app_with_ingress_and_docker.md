# 🚀 Deploying a Custom HTML App and Apache on Kubernetes with Ingress (Beginner-Friendly Guide)

This guide walks you through deploying a **custom HTML app** and an **Apache web server** on Kubernetes using Docker and NGINX Ingress. It includes all Dockerfiles, YAMLs, and commands to go from scratch to a fully functional setup.

---

## 📁 Project Structure

```

custom-html-app/
├── index.html
├── Dockerfile
├── custom-deployment.yaml
├── custom-service.yaml
├── apache-deployment.yaml
├── apache-service.yaml
└── ingress.yaml

````

---

## 🔹 Step 1: Create the HTML App

📄 `index.html`

```html
<!DOCTYPE html>
<html>
<head>
    <title>Hello</title>
</head>
<body>
    <h1>Hello World from Custom App!</h1>
</body>
</html>
````

---

## 🔹 Step 2: Create Dockerfile

📄 `Dockerfile`

```Dockerfile
FROM nginx:alpine
COPY index.html /usr/share/nginx/html/index.html
```

### 🔍 Explanation:

* `FROM nginx:alpine` – Uses lightweight NGINX image
* `COPY index.html` – Places HTML inside NGINX web root

---

## 🔹 Step 3: Build and Push Docker Image

Replace `<your-dockerhub-username>` with your Docker Hub username.

```bash
cd custom-html-app
docker login
docker build -t <your-dockerhub-username>/custom-html-app:v1 .
docker push <your-dockerhub-username>/custom-html-app:v1
```

---

## 🔹 Step 4: Kubernetes Deployment + Service for Custom App

📄 `custom-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: custom-app-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: custom
  template:
    metadata:
      labels:
        app: custom
    spec:
      containers:
        - name: custom
          image: <your-dockerhub-username>/custom-html-app:v1
          ports:
            - containerPort: 80
```

📄 `custom-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: custom-service
spec:
  selector:
    app: custom
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

## 🔹 Step 5: Apache Deployment + Service

📄 `apache-deployment.yaml`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: apache-deployment
spec:
  replicas: 2
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
        - name: apache
          image: httpd:2.4
          ports:
            - containerPort: 80
```

📄 `apache-service.yaml`

```yaml
apiVersion: v1
kind: Service
metadata:
  name: apache-service
spec:
  selector:
    app: apache
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

---

## 🔹 Step 6: Set Up Ingress

Install Ingress Controller (for kind users):

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/controller-v1.10.0/deploy/static/provider/kind/deploy.yaml
```

📄 `ingress.yaml`

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: demo-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  ingressClassName: nginx
  rules:
    - http:
        paths:
          - path: /apache
            pathType: Prefix
            backend:
              service:
                name: apache-service
                port:
                  number: 80
          - path: /custom
            pathType: Prefix
            backend:
              service:
                name: custom-service
                port:
                  number: 80
```

---

## 🔹 Step 7: Apply to Kubernetes

```bash
kubectl apply -f apache-deployment.yaml
kubectl apply -f apache-service.yaml
kubectl apply -f custom-deployment.yaml
kubectl apply -f custom-service.yaml
kubectl apply -f ingress.yaml
```

Check resources:

```bash
kubectl get deployments
kubectl get services
kubectl get pods
kubectl get ingress
```

---

## 🔹 Step 8: Access the Application

For local Kubernetes (kind):

```bash
kubectl port-forward --namespace ingress-nginx service/ingress-nginx-controller 8080:80
```

Open in browser:

* 🌐 Apache: `http://localhost:8080/apache`
* 🌐 Custom HTML: `http://localhost:8080/custom`

---

## ✅ Summary

| App      | URL                                                          | Replicas |
| -------- | ------------------------------------------------------------ | -------- |
| Apache   | [http://localhost:8080/apache](http://localhost:8080/apache) | 2        |
| HTML App | [http://localhost:8080/custom](http://localhost:8080/custom) | 2        |

You’ve successfully:

* Built and pushed a Docker image
* Created Kubernetes deployments/services
* Configured NGINX Ingress
* Accessed apps via browser

🎉 Great job!

