# Kubernetes Storage Explained Simply (With Examples) 🍪

Imagine your Kubernetes cluster is a toy city, and your apps are toys (Pods) that need shelves (storage) to keep their stuff (data).

Here’s how storage works in Kubernetes—explained like you’re 5!

---

## 1. Why Do We Need Storage?

**Problem:**  
If a Pod crashes, all its data disappears (like a sandcastle washed away by waves).

**Solution:**  
Use persistent storage (like a toy box that survives even if the toy breaks).

---

## 2. Key Storage Concepts

### 1) Volumes (Toy Boxes for Pods)

A Volume is like a shared toy box that Pods can use.

**Types:**

- `emptyDir` – Temporary storage (deleted when Pod dies).
- `hostPath` – Uses a folder on the Node (risky, not for production).
- **Cloud Volumes (AWS EBS, GCE PD)** – Like a USB stick for the cloud.

---

### Example (`emptyDir`):

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: toy-pod
spec:
  containers:
  - name: toy-container
    image: nginx
    volumeMounts:
    - name: toy-box
      mountPath: /data  # Mounts inside the container
  volumes:
  - name: toy-box
    emptyDir: {}        # Temporary storage
```
→ If the Pod dies, /data disappears.


### 2) PersistentVolume (PV) – The Big Storage Shelf

A PV is like a big bookshelf in your city (cluster-wide storage).

**Examples:**

- AWS EBS (Elastic Block Store)  
- NFS (Network File System)  
- Ceph (Distributed storage)  

---

### Example (PV using AWS EBS):

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: big-shelf
spec:
  capacity:
    storage: 10Gi       # 10 GB storage
  accessModes:
    - ReadWriteOnce     # Only 1 Pod can use it at a time
  awsElasticBlockStore:
    volumeID: vol-123   # AWS EBS volume ID
    fsType: ext4
```

### 3) PersistentVolumeClaim (PVC) – "I Need a Shelf!"

A PVC is like a request note from a Pod: "I need 5GB of storage!"

Kubernetes finds a matching PV (big shelf) and assigns it.

---

### Example (PVC):

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-claim
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi      # Pod asks for 5GB

```

### How to Use in a Pod:

```yml
apiVersion: v1
kind: Pod
metadata:
  name: toy-pod
spec:
  containers:
  - name: toy-container
    image: nginx
    volumeMounts:
    - name: toy-box
      mountPath: /data
  volumes:
  - name: toy-box
    persistentVolumeClaim:
      claimName: my-claim  # Uses the PVC
```
→ Now /data survives even if the Pod dies!

### 4) StorageClass (Automatic Shelf Delivery)

A StorageClass is like an IKEA catalog—it defines types of shelves (fast SSD vs. slow HDD).

Automatically creates PVs when a PVC asks for storage (dynamic provisioning).

---

### Example (AWS gp2 StorageClass):

```yaml
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: fast-shelf
provisioner: kubernetes.io/aws-ebs
parameters:
  type: gp2             # AWS General Purpose SSD
  fsType: ext4
```

### Now, when you create a PVC:
```yml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-claim
spec:
  storageClassName: fast-shelf  # Uses the StorageClass
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
```

### 3. Real-World Examples

---

### Example 1: Database (MySQL) with Persistent Storage

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:5.7
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "password"
        volumeMounts:
        - name: mysql-data
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-data
        persistentVolumeClaim:
          claimName: mysql-pvc
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 10Gi
```

### Example 2: Shared Storage (ReadWriteMany – NFS)

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: shared-shelf
spec:
  capacity:
    storage: 100Gi
  accessModes:
    - ReadWriteMany       # Many Pods can use it
  nfs:
    server: 192.168.1.100
    path: "/mnt/data"
```

### 4. Commands to Manage Storage

| Command                             | What It Does                     | Example                               |
|-------------------------------------|-----------------------------------|---------------------------------------|
| `kubectl get pv`                    | List PersistentVolumes            | `kubectl get pv`                      |
| `kubectl get pvc`                   | List PersistentVolumeClaims       | `kubectl get pvc`                     |
| `kubectl get storageclass`          | List StorageClasses               | `kubectl get sc`                      |
| `kubectl describe pvc my-claim`     | Check PVC status                  | `kubectl describe pvc mysql-pvc`      |
| `kubectl delete pvc my-claim`       | Delete a PVC                      | `kubectl delete pvc mysql-pvc`        |

### 5. When to Use What?

| Use Case                          | Storage Type                      | Example                           |
|------------------------------------|-----------------------------------|-----------------------------------|
| Temporary cache                    | `emptyDir`                        | Scratch space for a Pod           |
| Single-Pod storage                 | `ReadWriteOnce` (PVC + PV)        | Database (MySQL)                  |
| Multi-Pod shared storage           | `ReadWriteMany` (NFS)             | Shared file uploads               |
| Cloud storage                      | `StorageClass` (AWS EBS/GCE PD)   | Auto-provisioned volumes          |

