# Kubernetes Architecture Overview

This document provides a high-level overview of the key components and concepts in a Kubernetes cluster.

---

## 1. Control Plane Components (Master Nodes)

These components manage the Kubernetes cluster and make global decisions about the cluster (e.g., scheduling).

- **kube-apiserver**  
  Exposes the Kubernetes API; acts as the front-end of the control plane.

- **etcd**  
  A distributed key-value store used to hold all cluster data and configuration.

- **kube-scheduler**  
  Watches for newly created Pods and assigns them to Nodes based on resource availability and scheduling policies.

- **kube-controller-manager**  
  Runs various controllers such as:
  - Node Controller
  - ReplicaSet Controller
  - Deployment Controller  
  Ensures the desired state of cluster resources is maintained.

- **cloud-controller-manager (optional)**  
  Integrates the cluster with cloud provider APIs (e.g., AWS, GCP) for managing infrastructure-specific tasks.

---

## 2. Node Components (Worker Nodes)

These components run on each node and are responsible for running application workloads.

- **kubelet**  
  An agent that ensures containers are running in Pods and communicates with the kube-apiserver.

- **kube-proxy**  
  Maintains network rules for Pods to allow communication inside or outside the cluster, and performs load balancing.

- **Container Runtime**  
  Software that is responsible for running containers (e.g., Docker, containerd, CRI-O).

---

## 3. Addons (Optional Plugins)

These extend cluster functionality and are commonly deployed in production environments.

- **CNI (Container Network Interface)**  
  Provides networking for Pods using plugins such as Calico, Flannel, or WeaveNet.

- **DNS (CoreDNS or kube-dns)**  
  Provides internal DNS-based service discovery within the cluster.

- **Dashboard**  
  A web-based user interface to manage and monitor Kubernetes resources.

- **Ingress Controller**  
  Manages external HTTP/S access to services (e.g., Nginx, Traefik).

- **Metrics Server**  
  Collects resource usage data (CPU, memory) for features like Horizontal Pod Autoscaling (HPA).

---

## 4. Other Key Concepts

- **Pods**  
  The smallest deployable unit in Kubernetes; may contain one or more containers.

- **Controllers**  
  Ensure that the current state of the cluster matches the desired state defined by resources like Deployments, StatefulSets, etc.

- **Services**  
  Provide a stable endpoint (IP/DNS) to access a group of Pods.

---

> This document serves as a quick reference to understand the core structure and components of Kubernetes architecture.
