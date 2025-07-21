## Deploy NGINX Web Server on Kind Kubernetes Cluster (Beginner Friendly)

> 📘 Learn how to deploy a simple NGINX app on your local Kind Kubernetes cluster — step by step.

---

## 📋 What You’ll Learn

✅ How to create a Kubernetes deployment  
✅ How to scale the app using replicas  
✅ How to expose the app using a Service  
✅ How to access the app from your browser  
✅ How to monitor, log, and clean up  

---

## 🧰 Prerequisites

- ✅ A **Kind cluster** is running  
  👉 Check using: `kind get clusters`  
  👉 If not, run: `kind create cluster`
  
- ✅ `kubectl` is installed and working  
  👉 Check: `kubectl version --client`

- ✅ PowerShell or terminal is open

---

## 🔧 Step-by-Step Deployment Guide

### 🟢 Step 1: Check Your Cluster Status

```bash
kubectl get nodes
````

✅ You should see a `kind-control-plane` node in **Ready** state.

---

### 🌐 Step 2: Create NGINX Deployment

```bash
kubectl create deployment nginx-deployment --image=nginx:latest
```

🔍 Verify:

```bash
kubectl get deployments
kubectl get pods
```

Expected output (example):

```
NAME                 READY   STATUS    AGE
nginx-deployment     1/1     Running   30s
```

---

### 📈 Step 3: Scale Deployment (Optional)

```bash
kubectl scale deployment nginx-deployment --replicas=2
```

Verify multiple pods:

```bash
kubectl get pods
```

💡 *Scaling means running multiple copies of your app for better performance.*

---

### 🚪 Step 4: Expose the App via Service

```bash
kubectl expose deployment nginx-deployment \
  --type=NodePort \
  --port=80 \
  --name=nginx-service
```

🔍 Check service:

```bash
kubectl get services
```

Example output:

```
NAME            TYPE       PORT(S)        AGE
nginx-service   NodePort   80:32000/TCP   1m
```

💡 *Services act as doors to access your app from outside the cluster.*

---

### 🔄 Step 5: Access NGINX via Port-Forward

```bash
kubectl port-forward service/nginx-service 8080:80
```

➡ Keep this terminal open while accessing.

---

### 🌐 Step 6: View in Browser

Open your browser and visit:

```
http://localhost:8080
```

✅ You should see the **"Welcome to nginx!"** page.

🎉 Congrats! Your app is running in Kubernetes 🎉

---

## 🧠 Useful Monitoring Commands

| Task                   | Command                                        |
| ---------------------- | ---------------------------------------------- |
| View all K8s resources | `kubectl get all`                              |
| NGINX logs             | `kubectl logs deployment/nginx-deployment`     |
| Describe deployment    | `kubectl describe deployment nginx-deployment` |
| Describe service       | `kubectl describe service nginx-service`       |
| Watch pod health       | `kubectl get pods --watch`                     |

---

## 🧹 Cleanup Commands

| Task              | Command                                      |
| ----------------- | -------------------------------------------- |
| Stop port-forward | Press `Ctrl + C`                             |
| Delete service    | `kubectl delete service nginx-service`       |
| Delete deployment | `kubectl delete deployment nginx-deployment` |
| Verify cleanup    | `kubectl get all`                            |

---

## 🛠 Troubleshooting

| Issue                         | Solution                               |
| ----------------------------- | -------------------------------------- |
| ❌ Pod stuck in "Pending"      | Wait, image is downloading             |
| ❌ Can't open `localhost:8080` | Ensure port-forward is still running   |
| ❌ Command not found           | Check if `kubectl` is installed        |
| ❌ Browser error               | Port 8080 may be in use by another app |

---

## ✅ Summary – What You Learned

* Deploy apps in Kubernetes using `kubectl create deployment`
* Scale apps using replicas
* Expose apps via `kubectl expose`
* Access apps with `kubectl port-forward`
* Monitor and troubleshoot your app

---

## 🚀 What’s Next?

* Try deploying Apache or Redis
* Practice scaling up and down
* Start using YAML files instead of CLI
* Learn about namespaces, config maps, and secrets

