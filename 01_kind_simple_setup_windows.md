# 🐳 Kind (Kubernetes in Docker) - Simple Setup Guide for Windows

> 🚀 Perfect for beginners! Learn how to install and configure `Kind` and `kubectl` on Windows for local Kubernetes development.

---

## 📦 Prerequisites

- ✅ Windows 10/11 (64-bit)
- ✅ Docker Desktop (must be installed and running)
- ✅ PowerShell (run as Administrator)
- ✅ Internet connection

---

## 🔧 Step-by-Step Installation Guide

### 🔹 Step 1: Start Docker Desktop

1. Open Docker Desktop from the Start Menu or desktop.
2. Wait for it to fully start — you’ll see a green icon in the system tray.
3. Verify in PowerShell:
   ```powershell
   docker version
   docker ps
````

---

### 🔹 Step 2: Create a Folder for Kind

1. Open File Explorer → Go to `C:\`
2. Create a new folder named `kind`
3. Or use PowerShell:

   ```powershell
   New-Item -ItemType Directory -Path "C:\kind" -Force
   ```

---

### 🔹 Step 3: Download Kind Binary

Open **PowerShell as Administrator** and run:

```powershell
cd $env:USERPROFILE\Downloads
curl.exe -Lo kind-windows-amd64.exe https://kind.sigs.k8s.io/dl/v0.29.0/kind-windows-amd64
Move-Item .\kind-windows-amd64.exe C:\kind\kind.exe
```

---

### 🔹 Step 4: Add Kind to System PATH

1. Press `Win + R`, type `sysdm.cpl`, press Enter
2. Go to **Advanced** → Environment Variables
3. Under **System variables**, select `Path` → Edit → New → Add:

   ```
   C:\kind
   ```
4. Click **OK** on all windows

---

### 🔹 Step 5: Download kubectl

In the same PowerShell window:

```powershell
curl.exe -LO "https://dl.k8s.io/release/v1.28.0/bin/windows/amd64/kubectl.exe"
Move-Item .\kubectl.exe C:\kind\kubectl.exe
```

---

### 🔹 Step 6: Verify Everything Works

Close and re-open PowerShell as Administrator:

```powershell
kind version
kubectl version --client
```

If you see version outputs — you’re ready! 🎉

---

## 🚀 Your First Kind Cluster

### 🔹 Create Cluster

```powershell
kind create cluster
```

### 🔹 Verify Cluster

```powershell
kind get clusters
kubectl get nodes
kubectl cluster-info
```

---

## 📘 Basic kubectl Commands

| Task                 | Command                                         |
| -------------------- | ----------------------------------------------- |
| ✅ Create Pod         | `kubectl run test-pod --image=nginx`            |
| 🔍 List Pods         | `kubectl get pods`                              |
| ❌ Delete Pod         | `kubectl delete pod test-pod`                   |
| 🚀 Create Deployment | `kubectl create deployment nginx --image=nginx` |
| 🔄 Scale Deployment  | `kubectl scale deployment nginx --replicas=3`   |
| 🔍 Get Deployments   | `kubectl get deployments`                       |

---

## ❗ Troubleshooting

| Problem                   | Solution                                       |
| ------------------------- | ---------------------------------------------- |
| ❌ Docker not running      | Start Docker Desktop                           |
| ❌ kind: not recognized    | Check PATH & restart PowerShell                |
| ❌ kubectl: not recognized | Ensure `kubectl.exe` is in `C:\kind` & in PATH |

---

## ✅ Clean-Up & Advanced

### 🔹 Delete Cluster

```powershell
kind delete cluster
```

### 🔹 Multi-node Cluster Example

Create `kind-config.yaml`:

```yaml
kind: Cluster
apiVersion: kind.x-k8s.io/v1alpha4
nodes:
  - role: control-plane
  - role: worker
  - role: worker
```

Run:

```powershell
kind create cluster --config kind-config.yaml
```

---

## 🔗 Resources

* [Kind Docs](https://kind.sigs.k8s.io/)
* [Kubernetes Docs](https://kubernetes.io/docs/)
* [Docker Desktop](https://www.docker.com/products/docker-desktop/)
* [kubectl Cheat Sheet](https://kubernetes.io/docs/reference/kubectl/cheatsheet/)
---
