# What is use of rollout command ?

The command:
```sh
kubectl create deployment myd1 --image=vimal/apache-webserver
```
creates a **Kubernetes Deployment** named `myd1` using the Docker image `vimal/apache-webserver`.

---

## **📌 What Does This Command Do?**
1. **Creates a Deployment (`myd1`)**  
   - A **Deployment** manages a set of identical pods (containers) running your application.
   - Ensures high availability by maintaining the desired number of replicas.

2. **Uses the Docker Image `vimal/apache-webserver`**  
   - Kubernetes pulls this image from **Docker Hub** (default container registry).
   - Runs an Apache web server inside a pod.

3. **Default Settings:**
   - Creates **1 replica (pod)** by default.
   - Uses the **latest** image tag if not specified.

---

## **📌 Related Commands (With Examples)**
### **1️⃣ Check the Deployment Status**
```sh
kubectl get deployments
```
**Output:**
```
NAME   READY   UP-TO-DATE   AVAILABLE   AGE
myd1   1/1     1            1           10s
```
- `READY`: `1/1` means 1 pod is running out of 1 desired.
- `UP-TO-DATE`: 1 pod has the latest update.
- `AVAILABLE`: 1 pod is ready to serve traffic.

---

### **2️⃣ See the Pods Created by the Deployment**
```sh
kubectl get pods
```
**Output:**
```
NAME                    READY   STATUS    RESTARTS   AGE
myd1-5f6d8c4b6d-abc12   1/1     Running   0          30s
```
- The pod name follows the pattern: `<deployment-name>-<random-id>`.

---

### **3️⃣ Scale the Deployment (Increase Replicas)**
```sh
kubectl scale deployment myd1 --replicas=3
```
**Now check pods again:**
```sh
kubectl get pods
```
**Output:**
```
NAME                    READY   STATUS    RESTARTS   AGE
myd1-5f6d8c4b6d-abc12   1/1     Running   0          1m
myd1-5f6d8c4b6d-def34   1/1     Running   0          10s
myd1-5f6d8c4b6d-ghi56   1/1     Running   0          10s
```
- Now **3 pods** are running for high availability.

---

### **4️⃣ Update the Deployment (Change Image)**
```sh
kubectl set image deployment/myd1 apache-webserver=vimal/apache-webserver:new-version
```
- This triggers a **rolling update** (Kubernetes replaces pods one by one to avoid downtime).

---

### **5️⃣ Delete the Deployment**
```sh
kubectl delete deployment myd1
```
- This removes the deployment **and all associated pods**.

---

## **📌 Alternative: `kubectl apply` (Using YAML)**
Instead of `create`, you can define a Deployment in a YAML file (`deployment.yaml`):
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myd1
spec:
  replicas: 1
  selector:
    matchLabels:
      app: apache
  template:
    metadata:
      labels:
        app: apache
    spec:
      containers:
      - name: apache-webserver
        image:
```

---

# What is surge in K8s ?

# **What is "Surge" in Kubernetes?**  

In Kubernetes, **"Surge"** refers to the number of **extra pods** that can be created **above the desired replica count** during a **rolling update** of a Deployment. It helps ensure zero-downtime deployments by allowing new pods to be spun up before old ones are terminated.  

## **📌 Key Points About Surge**
1. **Part of RollingUpdate Strategy**  
   - Defined in a Deployment’s `spec.strategy.rollingUpdate.maxSurge`.  
   - Works alongside `maxUnavailable` (how many pods can be down during an update).  

2. **Default Value**  
   - If not set, Kubernetes uses a default of **25%**.  
   - Example: If you have 4 replicas, maxSurge allows 1 extra pod (25% of 4 = 1).  

3. **Why Use Surge?**  
   - Ensures **smooth updates** (no downtime).  
   - Helps maintain **service availability** during deployments.  

---

## **📌 Example: How Surge Works**
### **Scenario:**
- You have a Deployment with **4 replicas**.  
- You update the container image.  
- Kubernetes needs to replace old pods with new ones.  

### **Without Surge (Problem)**
- If Kubernetes kills an old pod **before** starting a new one, your app may experience **downtime**.  

### **With Surge (Solution)**
- Kubernetes **first creates new pods** (up to `maxSurge` limit).  
- **Then removes old pods**, ensuring **at least 4 pods are always running**.  

---

## **📌 How to Configure Surge?**
### **1. In a Deployment YAML**
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 4
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1    # Allows 1 extra pod during update
      maxUnavailable: 0  # Ensures no pods are down during update
  template:
    spec:
      containers:
      - name: nginx
        image: nginx:1.25
```
- `maxSurge: 1` → **1 extra pod allowed** (total 5 pods during update).  
- `maxUnavailable: 0` → **No pods are killed** until new ones are ready.  

### **2. Using `kubectl patch` (Update Existing Deployment)**
```sh
kubectl patch deployment/nginx-deployment -p '{"spec":{"strategy":{"rollingUpdate":{"maxSurge":"1","maxUnavailable":"0"}}}}'
```

---

## **📌 Surge vs. maxUnavailable**
| Setting | Controls | Example | Effect |
|---------|----------|---------|--------|
| **`maxSurge`** | Extra pods allowed during update | `maxSurge: 1` | Allows **1 extra pod** (total = desired + 1) |
| **`maxUnavailable`** | Pods that can be down during update | `maxUnavailable: 1` | Allows **1 pod to be unavailable** during update |

---

## **🎯 When Should You Adjust Surge?**
- **✔ High-Traffic Apps** → Increase `maxSurge` for smoother updates.  
- **✔ Low Resources** → Reduce `maxSurge` to avoid overloading the cluster.  
- **✔ Zero-Downtime Requirement** → Set `maxUnavailable: 0` and `maxSurge: 1`.  

### **Example Workflow**
1. **Initial State:** 4 pods running (`nginx:1.24`).  
2. **Update Triggered:** `kubectl set image deploy/nginx-deployment nginx=nginx:1.25`.  
3. **Kubernetes:**
   - Creates **1 new pod** (`maxSurge: 1` → now 5 pods).  
   - Waits for the new pod to be **Ready**.  
   - Deletes **1 old pod** (back to 4 pods, but now running `nginx:1.25`).  
   - Repeats until all pods are updated.  

---

## **🚀 Summary**
- **Surge (`maxSurge`)** = Extra pods allowed during updates.  
- **Default** = 25% of replicas.  
- **Used for** zero-downtime deployments.  
- **Configured in** `Deployment.spec.strategy.rollingUpdate`.  
