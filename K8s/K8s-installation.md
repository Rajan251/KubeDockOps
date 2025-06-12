## Prerequisites and System Requirements

**System Requirements:**
- Ubuntu 18.04+ (20.04 LTS or 22.04 LTS recommended)
- 2+ CPU cores
- 2GB+ RAM (4GB recommended)
- 20GB+ free disk space
- Network connectivity between nodes

## Installation Components

### 1. Container Runtime (containerd)
**What it is:** The low-level container runtime that actually runs containers
**Why needed:** Kubernetes needs a container runtime to manage containers

```bash
# Update system
sudo apt update && sudo apt upgrade -y

# Install containerd
sudo apt install -y containerd

# Configure containerd
sudo mkdir -p /etc/containerd
containerd config default | sudo tee /etc/containerd/config.toml

# Enable SystemdCgroup
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/' /etc/containerd/config.toml

# Restart containerd
sudo systemctl restart containerd
sudo systemctl enable containerd
```

### 2. Kubernetes Components

**kubeadm:** Tool to bootstrap Kubernetes clusters
**kubelet:** Node agent that runs on each node
**kubectl:** Command-line tool to interact with Kubernetes

```bash
# Add Kubernetes apt repository
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl gpg

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Install Kubernetes components
sudo apt update
sudo apt install -y kubelet kubeadm kubectl

# Hold packages to prevent automatic updates
sudo apt-mark hold kubelet kubeadm kubectl
```

### 3. System Configuration

**Disable swap:** Kubernetes requires swap to be disabled
**Enable IP forwarding:** Required for pod networking

```bash
# Disable swap
sudo swapoff -a
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Load required kernel modules
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

sudo modprobe overlay
sudo modprobe br_netfilter

# Configure sysctl parameters
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

sudo sysctl --system
```

## Initialize Kubernetes Cluster

### Single-Node Setup (Master Node)

```bash
# Initialize cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16

# Configure kubectl for your user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

# Remove taint from master node (for single-node setup)
kubectl taint nodes --all node-role.kubernetes.io/control-plane-
```

### 4. Install Pod Network (CNI)

**What it is:** Container Network Interface for pod communication
**Why needed:** Pods need to communicate across nodes

**Option 1: Flannel (Simple)**
```bash
kubectl apply -f https://github.com/flannel-io/flannel/releases/latest/download/kube-flannel.yml
```

**Option 2: Calico (More features)**
```bash
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/tigera-operator.yaml
kubectl create -f https://raw.githubusercontent.com/projectcalico/calico/v3.26.1/manifests/custom-resources.yaml
```

## Verification

```bash
# Check node status
kubectl get nodes

# Check system pods
kubectl get pods -n kube-system

# Check cluster info
kubectl cluster-info
```

## Optional: Install Dashboard

```bash
# Install Kubernetes Dashboard
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

# Create admin user
cat <<EOF | kubectl apply -f -
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
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
EOF

# Get dashboard token
kubectl -n kubernetes-dashboard create token admin-user
```

## Summary of What Each Component Does

- **containerd:** Runs your containers
- **kubeadm:** Sets up the cluster initially
- **kubelet:** Manages containers on each node
- **kubectl:** Your command-line interface to Kubernetes
- **CNI (Flannel/Calico):** Enables pod networking
- **Dashboard:** Web UI for cluster management

The entire setup should take about 20-30 minutes. After completion, you'll have a working single-node Kubernetes cluster ready for deploying applications.
