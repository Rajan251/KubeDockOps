# Docker Advanced Commands Cheat Sheet

## Container Deep Inspection

| Command | Description |
|---------|-------------|
| `docker container inspect --format '{{.HostConfig.LogConfig.Type}}' CONTAINER` | Show container's logging driver |
| `docker inspect --format '{{.Config.Healthcheck}}' CONTAINER` | Check healthcheck configuration |
| `docker inspect --format '{{json .State}}' CONTAINER | jq` | Container state in JSON (requires jq) |
| `docker stats --no-stream --format "table {{.Container}}\t{{.MemPerc}}\t{{.NetIO}}"` | Custom resource stats |

## Network Diagnostics

| Command | Description |
|---------|-------------|
| `docker network disconnect -f NETWORK CONTAINER` | Force disconnect container from network |
| `docker run --rm --net container:CONTAINER nicolaka/netshoot` | Debug network using another container's namespace |
| `docker run --rm --net host nicolaka/netshoot` | Debug host network stack |
| `docker exec CONTAINER curl -s http://169.254.169.254/latest/meta-data/` | Access cloud metadata from container |

## Storage & Filesystem

| Command | Description |
|---------|-------------|
| `docker system df -v` | Detailed disk usage breakdown |
| `docker diff CONTAINER | grep -E '^(A|C|D)'` | Filter changed files (Added/Changed/Deleted) |
| `docker export CONTAINER | tar tvf -` | Inspect exported container contents |
| `docker run -it --rm --privileged --pid=host alpine nsenter -t 1 -m -u -n -i sh` | Host filesystem inspection |

## Security & Hardening

| Command | Description |
|---------|-------------|
| `docker run --read-only --tmpfs /run CONTAINER` | Run container with read-only rootfs |
| `docker run --security-opt no-new-privileges IMAGE` | Prevent privilege escalation |
| `docker run --cap-drop ALL --cap-add NET_BIND_SERVICE IMAGE` | Minimal capabilities |
| `docker scan --dependency-tree IMAGE` | Show dependency tree with vulnerabilities |
| `docker trust sign IMAGE:TAG` | Digitally sign an image |

## BuildKit Advanced Features

| Command | Description |
|---------|-------------|
| `DOCKER_BUILDKIT=1 docker build --ssh default --no-cache .` | SSH agent forwarding in builds |
| `docker build --secret id=mysecret,src=./secret.txt .` | Secure secret injection |
| `docker build --output type=local,dest=./output .` | Build artifacts to host |
| `docker buildx build --platform linux/arm64,linux/amd64 .` | Multi-architecture builds |

## Swarm Advanced Operations

| Command | Description |
|---------|-------------|
| `docker service update --force --update-parallelism 0 SERVICE` | Zero-downtime rolling update |
| `docker node update --availability drain NODE` | Prepare node for maintenance |
| `docker secret create SECRET_FILE - < file.txt` | Create secret from stdin |
| `docker config inspect --pretty CONFIG` | Human-readable config output |

## Runtime Constraints

| Command | Description |
|---------|-------------|
| `docker run --memory-swappiness=0 IMAGE` | Disable swap usage |
| `docker run --device-read-bps /dev/sda:1mb IMAGE` | Throttle device I/O |
| `docker run --ulimit nofile=1024:1024 IMAGE` | Set file descriptor limits |
| `docker run --cpuset-cpus="0-3" IMAGE` | Restrict to specific CPUs |

## Debugging & Troubleshooting

| Command | Description |
|---------|-------------|
| `docker run -it --rm --cap-add SYS_PTRACE IMAGE strace -p 1` | Trace system calls |
| `docker events --filter 'event=die' --format '{{.ID}} {{.Actor.Attributes.exitCode}}'` | Monitor failed containers |
| `docker run --rm -v /var/run/docker.sock:/var/run/docker.sock docker:latest sh` | Docker-in-Docker troubleshooting |
| `docker checkpoint create CONTAINER CHECKPOINT_NAME` | Create container checkpoint |

## GPU & Hardware Acceleration

| Command | Description |
|---------|-------------|
| `docker run --gpus all nvidia/cuda:11.0-base nvidia-smi` | NVIDIA GPU access |
| `docker run --device /dev/kfd --device /dev/dri --group-add video IMAGE` | AMD GPU access |
| `docker run --device /dev/dri:/dev/dri IMAGE` | Intel QuickSync access |

## CI/CD & Automation

| Command | Description |
|---------|-------------|
| `docker buildx bake --set *.platform=linux/amd64` | Build for specific platform |
| `docker compose convert --format json` | Convert compose to JSON |
| `docker run --env-file .env.production IMAGE` | Load env vars from file |
| `docker context create ecs myecs --from-env` | Create ECS context |

## Expert Aliases

```bash
# Show container IPs
alias dip="docker inspect --format '{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}'"

# Follow logs of newest container
alias dlogf="docker logs -f \$(docker ps -ql)"

# Interactive debug container
alias ddebug="docker run -it --rm --cap-add=SYS_PTRACE --security-opt seccomp=unconfined"

# Show container resource usage
alias dtop="docker stats --format 'table {{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}\t{{.NetIO}}'"

# Prune everything with force
alias dnuke='docker system prune -a -f --volumes'
