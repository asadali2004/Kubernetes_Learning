# 📊 Task 4: Kubernetes Dashboard on Kind (with Token Login + Pod via UI)

> 💡 Learn how to set up the Kubernetes Dashboard on a Kind cluster, access it via browser using a login token, and deploy a Pod using YAML — all visually!

---

## 🎯 Goal

- Setup a multi-node Kubernetes cluster using Kind  
- Install and access Kubernetes Dashboard  
- Login using a secure token  
- Deploy and manage a Pod using YAML in the Dashboard UI  

---

## 🧰 Prerequisites

Ensure the following are installed and working:

```bash
docker --version
kind --version
kubectl version --client
````

---

## 🧱 Step 1: Create a Multi-Node Kind Cluster

Create a file named `kind-multi-node.yaml`:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

Create the cluster:

```bash
kind create cluster --name dashboard-cluster --config kind-multi-node.yaml
```

---

## 📥 Step 2: Install Kubernetes Dashboard

Apply the official Kubernetes Dashboard manifest:

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

---

## 🔐 Step 3: Create Dashboard Admin User (RBAC)

Create a file named `admin-user.yaml`:

```yaml
# admin-user.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
subjects:
  - kind: ServiceAccount
    name: admin-user
    namespace: kubernetes-dashboard
```

Apply it:

```bash
kubectl apply -f admin-user.yaml
```

---

## 🔑 Step 4: Get Login Token

Run this command and copy the output:

```bash
kubectl -n kubernetes-dashboard create token admin-user
```

---

## 🌐 Step 5: Access Dashboard in Browser

Start the proxy server:

```bash
kubectl proxy
```

Now open this in your browser:

```
http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

Login Steps:

* Select **"Token"**
* Paste the token you copied earlier
* Click **Sign In**

✅ You’re now inside the **Kubernetes Dashboard**!

---

## ➕ Step 6: Deploy a Pod via the Dashboard

1. Click the **+ Create** button in the top-right
2. Paste the following YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-dashboard-pod
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:latest
      ports:
        - containerPort: 80
```

3. Click **Create**

🔍 Go to **Workloads → Pods** to see the pod status.

✅ The pod should show **Running**

---

## 🚪 (Optional) Step 7: Access NGINX Pod in Browser

Run:

```bash
kubectl port-forward pod/nginx-dashboard-pod 8080:80
```

Now visit:

```
http://localhost:8080
```

✅ You’ll see the NGINX welcome page in your browser.

---

## 🧼 Cleanup (Optional)

```bash
kind delete cluster --name dashboard-cluster
```

---

## ✅ Summary of Commands

| Task                | Command                                                                      |
| ------------------- | ---------------------------------------------------------------------------- |
| Create Kind cluster | `kind create cluster --name dashboard-cluster --config kind-multi-node.yaml` |
| Install Dashboard   | `kubectl apply -f [dashboard-url]`                                           |
| Admin RBAC          | `kubectl apply -f admin-user.yaml`                                           |
| Get Token           | `kubectl -n kubernetes-dashboard create token admin-user`                    |
| Access Dashboard    | `kubectl proxy`                                                              |
| Deploy Pod via UI   | Use YAML in the Dashboard                                                    |
| Access Pod via port | `kubectl port-forward pod/nginx-dashboard-pod 8080:80`                       |
| Delete Cluster      | `kind delete cluster --name dashboard-cluster`                               |

---

## 🧠 Key Concepts Practiced

* Multi-node Kind cluster creation
* Installing Kubernetes Dashboard
* Using RBAC for secure login
* YAML-based deployment via UI
* Port-forwarding and accessing pods
* Visual monitoring of workloads


