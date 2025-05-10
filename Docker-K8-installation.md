# Kubernetes Installation Guide

This guide provides the properly sequenced steps to install Kubernetes with Docker and containerd on an Ubuntu system.

## 1. System Requirements and Preparation

Before installing any components, we need to prepare the system with the required kernel modules and system parameters. These steps are essential prerequisites for both Docker and Kubernetes.

### Load Required Kernel Modules
```bash
# Create modules file
cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
overlay
br_netfilter
EOF

# Load modules immediately
sudo modprobe overlay
sudo modprobe br_netfilter
```

### Configure System Parameters
```bash
# Set required sysctl parameters
cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-iptables  = 1
net.bridge.bridge-nf-call-ip6tables = 1
net.ipv4.ip_forward                 = 1
EOF

# Apply sysctl parameters
sudo sysctl --system
```

### Disable Swap Space

Kubernetes requires swap to be disabled for proper functioning. This is because:

1. **Performance Predictability**: Kubernetes relies on precise CPU and memory resource allocation. Swap can introduce unpredictable performance variations and latency.

2. **Resource Management**: The Kubernetes scheduler makes pod placement decisions based on available CPU and memory resources. When swap is enabled, the system might over-commit resources, leading to unpredictable behavior.

3. **Out-of-Memory Handling**: With swap enabled, the Linux OOM (Out of Memory) killer may not work correctly with containerized applications, potentially causing the wrong processes to be terminated.

4. **Quality of Service (QoS)**: Kubernetes defines QoS classes for pods, which could be undermined if swap is available.

```bash
# Disable swap immediately
sudo swapoff -a

# Permanently disable swap in /etc/fstab
sudo sed -i '/ swap / s/^\(.*\)$/#\1/g' /etc/fstab

# Set swappiness to 0
sudo sysctl vm.swappiness=0
sudo sysctl -w vm.swappiness=0
```

## 2. Remove Previous Docker/Kubernetes Installations

Before installing new components, it's best to clean up any existing installations:

```bash
# Stop any running Docker containers
sudo docker stop $(sudo docker ps -aq)
# Remove all Docker containers
sudo docker rm $(sudo docker ps -aq)
# Remove all Docker images
sudo docker rmi $(sudo docker images -q)
# Remove Docker packages
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli containerd.io
sudo apt-get autoremove -y --purge docker-engine docker docker.io docker-ce containerd.io
# Remove Docker directories
sudo rm -rf /var/lib/docker /etc/docker
sudo rm -rf /var/run/docker.sock
sudo rm -rf /var/lib/containerd
sudo rm -rf /etc/containerd

# Reset any previous Kubernetes configuration
sudo kubeadm reset -f
sudo rm -rf /etc/kubernetes
sudo rm -rf ~/.kube
sudo rm -rf /var/lib/kubelet
sudo rm -rf /var/lib/etcd
sudo rm -rf /etc/cni/net.d
sudo iptables -F && sudo iptables -t nat -F && sudo iptables -t mangle -F && sudo iptables -X
```

## 3. Install Docker Engine

```bash
# Update package index
sudo apt-get update
# Install prerequisites
sudo apt-get install -y apt-transport-https ca-certificates curl software-properties-common gnupg
# Add Docker's official GPG key
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
# Set up the stable repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
# Update package index again after adding Docker repository
sudo apt-get update
# Install Docker Engine
sudo apt-get install -y docker-ce docker-ce-cli containerd.io
```

## 4. Configure Docker for Kubernetes

```bash
# Create daemon.json with required configurations for Kubernetes
cat <<EOF | sudo tee /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
EOF

# Restart Docker to apply changes
sudo systemctl daemon-reload
sudo systemctl start docker
sudo systemctl enable docker
sudo systemctl status docker

# Verify Docker installation
sudo docker run hello-world
```

## 5. Configure containerd

```bash
# Create the containerd config directory if it doesn't exist
sudo mkdir -p /etc/containerd
# Create a default configuration
sudo containerd config default | sudo tee /etc/containerd/config.toml > /dev/null
# Edit the configuration to use systemd cgroup driver
sudo sed -i 's/SystemdCgroup = false/SystemdCgroup = true/g' /etc/containerd/config.toml
# Restart containerd
sudo systemctl restart containerd
sudo systemctl status containerd
# Set correct permissions
sudo chmod 775 /var/run/containerd/containerd.sock
```

## 6. Install Kubernetes Components

```bash
# Add Kubernetes repository key
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
# Add Kubernetes repository
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF

# Update package index
sudo apt-get update
# Install Kubernetes components
sudo apt-get install -y kubelet kubeadm kubectl
# Pin Kubernetes package versions to prevent automatic upgrades
sudo apt-mark hold kubelet kubeadm kubectl
```

## 7. Configure Kubelet

```bash
# Create a default kubelet environment file
sudo mkdir -p /var/lib/kubelet
sudo bash -c 'cat > /var/lib/kubelet/kubeadm-flags.env << EOF
KUBELET_KUBEADM_ARGS="--container-runtime=remote --container-runtime-endpoint=unix:///var/run/containerd/containerd.sock"
EOF'

# Create a default Kubelet config
sudo mkdir -p /etc/default
sudo bash -c 'cat > /etc/default/kubelet << EOF
KUBELET_EXTRA_ARGS="--fail-swap-on=false"
EOF'

# Restart kubelet
sudo systemctl restart kubelet
```

## 8. Initialize Kubernetes Cluster

```bash
# Initialize the Kubernetes cluster
sudo kubeadm init --pod-network-cidr=10.244.0.0/16 --cri-socket=unix:///var/run/containerd/containerd.sock

# Set up kubeconfig for the user
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
chmod 600 $HOME/.kube/config
```

## 9. Install Network Plugin and Configure Cluster

```bash
# Install Flannel CNI plugin
kubectl apply -f https://raw.githubusercontent.com/flannel-io/flannel/master/Documentation/kube-flannel.yml

# Allow pod scheduling on the master node (for single-node clusters)
kubectl taint nodes --all node-role.kubernetes.io/control-plane-

# Check node status
kubectl get nodes

# Check system pods
kubectl get pods --all-namespaces
```

## 10. Deploy a Test Application

```bash
# Create a simple nginx deployment
kubectl create deployment nginx --image=nginx

# Expose the deployment
kubectl expose deployment nginx --port=80 --type=NodePort

# Check the service
kubectl get svc nginx

# Test the service (replace port number with the one from 'kubectl get svc nginx')
curl http://localhost:<nodePort>
```

## Troubleshooting Tips

- Check Kubelet status: `sudo systemctl status kubelet`
- View Kubelet logs: `sudo journalctl -u kubelet -n 50 --no-pager`
- Check Docker status: `sudo systemctl status docker`
- Check containerd status: `sudo systemctl status containerd`
- Verify kernel modules: `lsmod | grep br_netfilter` and `lsmod | grep overlay`
- Check IP forwarding: `sysctl net.ipv4.ip_forward`
---
