# Kubernetes Architecture Overview (Expanded)

This document provides a high-level overview of the key components and concepts in a Kubernetes cluster, including advanced and optional elements.

## 1. Control Plane Components (Master Nodes)
These components manage the Kubernetes cluster and make global decisions about the cluster (e.g., scheduling).

### Core Components:
- **kube-apiserver**  
  Exposes the Kubernetes API; acts as the front-end of the control plane. Supports horizontal scaling for high availability.

- **etcd**  
  A distributed key-value store used to hold all cluster data and configuration. Can be run in a clustered mode for fault tolerance.

- **kube-scheduler**  
  Watches for newly created Pods and assigns them to Nodes based on resource availability, constraints, and policies (e.g., affinity/anti-affinity).

- **kube-controller-manager**  
  Runs various controllers such as:
  - **Node Controller** (monitors node status)
  - **ReplicaSet Controller** (maintains desired Pod count)
  - **Deployment Controller** (manages rolling updates)
  - **Endpoint Controller** (updates Service endpoints)
  - **ServiceAccount Controller** (manages default accounts)
  - **PersistentVolume Controller** (binds PVs to PVCs)

### cloud-controller-manager (optional)
Integrates the cluster with cloud provider APIs (e.g., AWS, GCP, Azure) for managing:
- Node lifecycle (auto-scaling)
- Load balancers
- Persistent volumes
- Route configurations

## 2. Additional Control Plane Components:
- **API Aggregation Layer**  
  Allows extending the Kubernetes API with custom APIs (e.g., using Custom Resource Definitions or API extensions).

- **Cluster Autoscaler**  
  Automatically adjusts the number of worker nodes based on resource demands.

- **kube-state-metrics**  
  Generates metrics about the state of Kubernetes objects (Deployments, Pods, Nodes, etc.) for monitoring.

# 2. Node Components (Worker Nodes)
These components run on each node and are responsible for running application workloads.

### Core Components:
- **kubelet**  
  An agent that ensures containers are running in Pods. Reports node status, enforces Pod security policies, and handles container health checks.

- **kube-proxy**  
  Maintains network rules (using iptables, IPVS, or eBPF) for Pod-to-Pod and external communication. Supports Service abstraction (ClusterIP, NodePort, LoadBalancer).

- **Container Runtime**  
  Software responsible for running containers (e.g., Docker, containerd, CRI-O). Implements the Kubernetes Container Runtime Interface (CRI).

### Additional Node Components:
- **Node Problem Detector**  
  Monitors and reports node issues (e.g., kernel crashes, disk pressure).

- **Logging Agents**  
  Collects and forwards container logs (e.g., Fluentd, Filebeat).

- **CSI (Container Storage Interface) Drivers**  
  Enables dynamic provisioning of storage volumes (e.g., AWS EBS, GCE Persistent Disk).

- **Device Plugins**  
  Manages hardware resources (e.g., GPUs, FPGAs) for specialized workloads.

---

# 3. Addons (Optional Plugins)
These extend cluster functionality and are commonly deployed in production environments.

### Networking:
- **CNI (Container Network Interface) Plugins**  
  Provides networking for Pods (e.g., Calico, Flannel, Cilium, WeaveNet). Supports features like Network Policies for security.

- **Service Meshes**  
  Manages service-to-service communication (e.g., Istio, Linkerd). Provides traffic management, observability, and mTLS.

### Storage:
- **CSI Drivers**  
  Enables integration with external storage systems (e.g., NFS, Ceph, Portworx).

- **Local Storage Provisioner**  
  Manages dynamically provisioned local volumes.

### Observability:
- **Metrics Server**  
  Collects resource usage data (CPU, memory) for features like Horizontal Pod Autoscaling (HPA).

- **Prometheus + Grafana**  
  Monitors cluster and application metrics with alerting and dashboards.

- **OpenTelemetry / Jaeger**  
  Provides distributed tracing for microservices.

### Security:
- **RBAC (Role-Based Access Control)**  
  Manages fine-grained permissions for users and services.

- **Pod Security Policies (PSP) / OPA Gatekeeper**  
  Enforces security policies for Pods (deprecated in favor of PSA and OPA).

- **Falco / Aqua Security**  
  Provides runtime security and anomaly detection.

### Application Delivery:
- **Ingress Controllers**  
  Manages external HTTP/S access to services (e.g., Nginx, Traefik, HAProxy, Istio Gateway).

- **GitOps Tools**  
  Manages deployments using Git workflows (e.g., ArgoCD, Flux).

- **Helm / Kustomize**  
  Package managers for Kubernetes manifests.

### Other Addons:
- **Cluster DNS (CoreDNS)**  
  Provides internal DNS-based service discovery.

- **Dashboard**  
  Web-based UI for cluster management.

- **Backup Tools**  
  (e.g., Velero) for disaster recovery and migration.

# 4. Other Key Concepts

### Workloads:
- **Pods**  
  The smallest deployable unit in Kubernetes, consisting of one or more containers that share storage and networking resources.

### Controllers:
- **Deployment**  
  Manages stateless applications and ensures the desired number of Pods are running. Supports rolling updates and rollbacks.

- **StatefulSet**  
  Used for stateful applications that require stable storage and unique network identifiers (e.g., databases).

- **DaemonSet**  
  Ensures that a specific Pod is running on all (or selected) Nodes in the cluster. Typically used for node-level agents (e.g., logging, monitoring).

- **Job/CronJob**  
  Manages batch processing workloads. A Job creates Pods that run to completion, and a CronJob runs Jobs on a scheduled basis.

- **ReplicaSet**  
  Ensures a specified number of Pod replicas are running at any given time. ReplicaSets are usually managed by Deployments.

---

### Networking:

#### Services:
- **ClusterIP**  
  The default service type that exposes the service on an internal IP in the cluster.

- **NodePort**  
  Exposes the service on a static port on each Node’s IP address, allowing external access.

- **LoadBalancer**  
  Creates an external load balancer (typically used with cloud providers) to distribute traffic to the service.

- **ExternalName**  
  Maps a service to an external DNS name, allowing services to access resources outside the cluster.

#### Ingress:
- **Ingress**  
  Routes HTTP/S traffic to Services with path-based rules. Typically used for exposing web applications.

#### Network Policies:
- **Network Policies**  
  Defines how Pods communicate with each other, similar to firewall rules. Can control ingress and egress traffic between Pods.

---

### Storage:

- **PersistentVolume (PV)**  
  A cluster-wide resource that represents storage in the Kubernetes ecosystem.

- **PersistentVolumeClaim (PVC)**  
  A user request for storage, which is bound to an available PersistentVolume.

- **StorageClass**  
  Defines how storage is dynamically provisioned, specifying the type of storage (e.g., SSD, HDD) and parameters.

---

### Advanced Concepts:

- **Custom Resource Definitions (CRDs)**  
  Allows extending the Kubernetes API to manage custom resources, enabling custom workloads like Operators.

- **Horizontal Pod Autoscaler (HPA)**  
  Automatically scales the number of Pods in a Deployment or ReplicaSet based on CPU utilization, memory, or custom metrics.

- **Vertical Pod Autoscaler (VPA)**  
  Dynamically adjusts resource requests and limits for Pods to optimize resource usage.

- **Cluster API**  
  Manages Kubernetes clusters using Kubernetes-style APIs, enabling automation of cluster creation, scaling, and management.
