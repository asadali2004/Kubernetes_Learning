# 🚀 Task 3: Multi-Node Kubernetes Cluster on Kind – NGINX with Replicas

> 🧠 Learn how to create a Kubernetes cluster with 1 master (control-plane) and 2 worker nodes using Kind, deploy NGINX with replicas, and explore how workloads are scheduled across nodes.

---

## 📋 What You’ll Learn

✅ How to create a multi-node cluster using Kind  
✅ Deploy an NGINX app with multiple replicas  
✅ Expose the app and access it locally  
✅ Understand how the control-plane manages worker nodes  
✅ Use `kubectl` to inspect node and pod behavior

---

## 🧰 Prerequisites

Ensure the following tools are installed and working:

```bash
docker --version
kind --version
kubectl version --client
````

Also, make sure Docker Desktop is **running** before you proceed.

---

## 🗂️ Step 1: Create Cluster Configuration File

Create a new file named `kind-multi-node.yaml`:

```yaml
# kind-multi-node.yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

💡 This config defines a 3-node cluster:
🟢 1 master (control-plane) and
🟠 2 workers (to run your applications)

---

## 🔧 Step 2: Create the Kubernetes Cluster

```bash
kind create cluster --name demo-cluster --config kind-multi-node.yaml
```

✅ This will spin up 3 Docker containers as K8s nodes.

---

## 🔍 Step 3: Check Cluster and Node Roles

```bash
kubectl get nodes -o wide
```

Expected output:

```
NAME                          STATUS   ROLES           AGE
demo-cluster-control-plane    Ready    control-plane   Xs
demo-cluster-worker           Ready    <none>          Xs
demo-cluster-worker2          Ready    <none>          Xs
```

💡 Workers have no labeled roles, but they are **schedulable** by default.

---

## 🌐 Step 4: Deploy NGINX App with Replicas

```bash
kubectl create deployment nginx-demo --image=nginx --replicas=3
```

🎯 This deploys 3 NGINX pods into the cluster.

Check:

```bash
kubectl get deployments
kubectl get pods
```

---

## 🚪 Step 5: Expose the App as a Service

```bash
kubectl expose deployment nginx-demo \
  --port=80 \
  --target-port=80 \
  --type=NodePort
```

Verify:

```bash
kubectl get services
```

You’ll see a `NodePort` service created.

---

## 🌍 Step 6: Access App via Port Forwarding

Since NodePorts aren’t accessible by default in Kind, run:

```bash
kubectl port-forward service/nginx-demo 8080:80
```

Then open in browser:

```
http://localhost:8080
```

✅ You’ll see the **"Welcome to nginx!"** page.

---

## 📊 Step 7: Check Pod Scheduling Across Nodes

```bash
kubectl get pods -o wide
```

Expected result:

```
NAME                 NODE
nginx-demo-xxxxx     demo-cluster-worker
nginx-demo-yyyyy     demo-cluster-worker2
nginx-demo-zzzzz     demo-cluster-worker
```

✅ This shows how the control-plane **only manages**, while workers run the pods.

---

## ⚙️ Optional: Allow Control-Plane to Run Workloads

To remove the master node taint:

```bash
kubectl taint nodes demo-cluster-control-plane node-role.kubernetes.io/control-plane-
```

💡 Now the master can also run pods — useful for testing.

---

## 🧼 Cleanup When Done

```bash
kubectl delete service nginx-demo
kubectl delete deployment nginx-demo
kind delete cluster --name demo-cluster
```

---

## 🧠 Key Concepts Practiced

* Control-plane vs worker nodes
* Pod scheduling across nodes
* Deployment scaling with replicas
* Port-forwarding for local testing
* Cluster creation with custom configuration

---

## 🗂️ Suggested Project Folder Structure

```
k8s-master-slave-demo/
├── kind-multi-node.yaml
├── 03_multi_node_cluster_nginx_replicas.md
```

---

✅ You’ve now deployed a scalable app on a **multi-node Kubernetes cluster** using Kind — great job!

