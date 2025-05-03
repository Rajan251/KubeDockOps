# 🐳 Docker Concepts - Complete Guide

## 📘 Core Concepts

### 1. 🚀 Containers
- Lightweight, standalone, executable packages of software  
- Include everything needed to run an application  
- Share the host OS kernel  
- Isolated from other containers and the host  

### 2. 🧱 Images
- Read-only templates used to create containers  
- Built from Dockerfiles  
- Composed of layered filesystem  
- Stored in registries (Docker Hub, private registries)  

### 3. ⚙️ Docker Engine
- Client-server application with three components:
  - **Docker Daemon (`dockerd`)**: Background service managing containers  
  - **REST API**: Interface for interacting with the daemon  
  - **CLI (`docker`)**: Command line interface  

### 4. 📝 Dockerfile
- Text document containing commands to assemble an image  
- Each instruction creates a new layer in the image  
- Common instructions:

  ```dockerfile
  FROM, RUN, COPY, ADD, CMD, ENTRYPOINT, 
  ENV, ARG, WORKDIR, EXPOSE, VOLUME, USER


## 🏗️ Architecture Components

### 1. 🧩 Docker Objects

| Object     | Description                               |
|------------|-------------------------------------------|
| **Images**     | Immutable templates for containers         |
| **Containers** | Runnable instances of images              |
| **Networks**   | Configurable communication channels       |
| **Volumes**    | Persistent data storage                   |
| **Plugins**    | Extend Docker functionality               |
| **Services**   | Swarm mode workload units                |
| **Tasks**      | Individual container instances in Swarm  |
| **Secrets**    | Secure credential management              |
| **Configs**    | Non-sensitive configuration data          |


### 2. 🗂️ Docker Registry

Docker Registry stores and distributes Docker images.

- **Docker Hub**: Default public registry  
- **Private Registries**: Self-hosted solutions (e.g., Artifactory, Harbor, etc.)

#### 📦 Common Registry Commands


- docker pull
- docker push
- docker login
- docker search



## 🌐 Networking

### 1. 🔌 Network Drivers

| Driver       | Description                            |
|--------------|----------------------------------------|
| **bridge**   | Default network driver                 |
| **host**     | Removes network isolation              |
| **overlay**  | Connects multiple Docker daemons       |
| **ipvlan**   | Provides IPv4/IPv6 addressing          |
| **macvlan**  | Assigns MAC address to container       |
| **none**     | Disables networking                    |

### 2. 🛠️ Network Types

| Type                 | Description                                       |
|----------------------|---------------------------------------------------|
| **Default networks** | Automatically created (bridge, host, none)        |
| **User-defined networks** | Custom networks created by users             |
| **Ingress network**  | Special overlay network for swarm services        |


## Orchestration

### 1. Kubernetes Integration

- Docker as container runtime

**Kubernetes Concepts:**

- **Pods**
- **Deployments**
- **Services**
- **Ingress**

### 2. Docker Swarm vs Kubernetes

| Feature         | Docker Swarm | Kubernetes  |
|-----------------|--------------|-------------|
| Setup           | Simpler      | Complex     |
| Scaling         | Faster       | More robust |
| Learning Curve  | Lower        | Steeper     |
| Community       | Smaller      | Larger      |

---

## Monitoring & Logging

### 1. Monitoring Tools

- **Docker stats**: Basic container metrics  
- **cAdvisor**: Container metrics collector  
- **Prometheus**: Time-series monitoring  
- **Grafana**: Metrics visualization  

### 2. Logging Drivers

| Driver      | Description                     |
|-------------|---------------------------------|
| json-file   | Default logging driver          |
| syslog      | System logging                  |
| journald    | Systemd journal                 |
| gelf        | Graylog Extended Log Format     |
| fluentd     | Fluentd logging                 |

---

## CI/CD Integration

### 1. Pipeline Integration

- Docker in build pipelines  
- Image scanning in CI  
- Automated builds with Docker Hub  
- Deployment strategies  

### 2. Best Practices

- Immutable images  
- Semantic versioning  
- Build context optimization  
- Layer caching strategies  

---

## Advanced Concepts

### 1. Docker Internals

- **Namespaces** (pid, net, ipc, mnt, uts, user)  
- **Control Groups (cgroups)**  
- **Union File Systems**  
- **Container Runtime Interface (CRI)**  
- **Open Container Initiative (OCI)**  

### 2. Plugins

- Authorization plugins  
- Volume plugins  
- Network plugins  
- Logging plugins  

---

## Troubleshooting

### 1. Common Commands

```bash
docker inspect CONTAINER
docker logs CONTAINER
docker stats
docker events
docker diff CONTAINER
docker exec -it CONTAINER /bin/sh
```

## Ecosystem Tools

| Tool         | Purpose                         |
|--------------|---------------------------------|
| Docker Desktop | Local development environment |
| Portainer    | GUI management                  |
| Rancher      | Container management platform   |
| Trivy        | Vulnerability scanner           |
| Hadolint     | Dockerfile linter               |


## Container Operations

### Process Management
- `docker top CONTAINER`                  # View running processes
- `docker exec -it CONTAINER /bin/bash`   # Interactive shell
- `docker attach CONTAINER`               # Attach to running process

### Monitoring & Inspection
- `docker ps`                            # List running containers
- `docker ps -a`                         # List all containers
- `docker inspect CONTAINER`             # Detailed container info
- `docker stats`                         # Live performance metrics
- `docker logs CONTAINER`                # View container logs

## Container Networking

### Network configuration examples
- `docker run -p 8080:80 nginx`          # Port mapping
- `docker run --network=host nginx`      # Host network mode
- `docker run --network=none nginx`      # No networking

### Network inspection
- `docker network ls`                    # List networks
- `docker network inspect NETWORK`       # Network details

## Container Storage

### Volume operations
- `docker volume create myvol`           # Create volume
- `docker run -v myvol:/data nginx`      # Mount volume
- `docker run -v /host/path:/data nginx` # Bind mount

### Temporary filesystem
- `docker run --tmpfs /tmp nginx`       # In-memory tmpfs

## Container Security

### Security configurations
- `docker run --read-only nginx`         # Read-only filesystem
- `docker run --user 1000 nginx`        # Run as non-root
- `docker run --cap-drop=ALL nginx`     # Drop all capabilities

## Container Resource Management

### Resource constraints
- `docker run --memory=512m nginx`      # Memory limit
- `docker run --cpus=1.5 nginx`         # CPU limit
- `docker run --blkio-weight=500 nginx` # Block IO weight

## Multi-Container Applications

### docker-compose

#### docker-compose.yml example

```yaml
version: '3.8'
services:
  web:
    image: nginx
    ports:
      - "80:80"
  db:
    image: postgres
    environment:
      POSTGRES_PASSWORD: example
```

## Container Images

### Image management

```bash
docker build -t myimage .           # Build from Dockerfile
docker pull nginx                   # Download image
docker push myrepo/myimage          # Upload image
docker image prune                  # Clean unused images
```

## Container Orchestration

### Swarm mode commands

```bash
docker swarm init                   # Initialize swarm
docker service create nginx         # Create service
docker stack deploy -c compose.yml mystack # Deploy stack
```

## Debugging Containers

### Troubleshooting commands

```bash
docker diff CONTAINER               # Changed files
docker export CONTAINER > file.tar  # Export filesystem
docker cp CONTAINER:/path ./local   # Copy files out
```

## Advanced Container Features

### Advanced configurations

```bash
docker run --device /dev/sda:/dev/sda nginx # Device access
docker run --security-opt=no-new-privileges nginx # Security
docker run --ulimit nofile=1024 nginx      # Resource limits
```
## Container Best Practices

### Sample optimized Dockerfile

```dockerfile
FROM alpine:3.14
RUN apk add --no-cache nginx && \
    rm -rf /var/cache/apk/*
COPY --chown=nginx:nginx app /app
USER nginx
EXPOSE 8080
CMD ["nginx", "-g", "daemon off;"]
```

## Container Ecosystem Tools

| Tool      | Command Example                                | Purpose              |
|-----------|------------------------------------------------|----------------------|
| cAdvisor  | `docker run -p 8080:8080 google/cadvisor`      | Container monitoring |
| Portainer | `docker run -d -p 9000:9000 portainer/portainer` | Web UI               |
| Trivy     | `trivy image nginx:latest`                     | Vulnerability scanning|


