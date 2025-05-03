# Docker Commands Comprehensive Cheat Sheet

## Container Lifecycle

| Command | Description |
|---------|-------------|
| `docker create [OPTIONS] IMAGE [CMD]` | Create a new container without starting it |
| `docker rename CONTAINER NEW_NAME` | Rename a container |
| `docker wait CONTAINER` | Block until a container stops, then print exit code |
| `docker update [OPTIONS] CONTAINER` | Update container resource limits |

## Advanced Container Operations

| Command | Description |
|---------|-------------|
| `docker diff CONTAINER` | Inspect changes to files on container's filesystem |
| `docker port CONTAINER` | List port mappings for a container |
| `docker commit [OPTIONS] CONTAINER [REPO[:TAG]]` | Create new image from container changes |
| `docker cp [OPTIONS] CONTAINER:SRC_PATH DEST_PATH` | Copy files/folders between container and host |
| `docker export [OPTIONS] CONTAINER` | Export container's filesystem as tar archive |
| `docker import [OPTIONS] FILE|URL|- [REPO[:TAG]]` | Import contents from tarball to create filesystem image |

## Image Operations

| Command | Description |
|---------|-------------|
| `docker history IMAGE` | Show history of an image |
| `docker image prune [OPTIONS]` | Remove unused images |
| `docker manifest COMMAND` | Manage Docker image manifests |
| `docker trust COMMAND` | Manage trust on Docker images |
| `docker scan IMAGE` | Scan an image for vulnerabilities (Docker Scout) |

## Network Operations

| Command | Description |
|---------|-------------|
| `docker network prune [OPTIONS]` | Remove all unused networks |
| `docker network inspect --format '{{.IPAM.Config}}' NETWORK` | Show network IP range |

## Volume Operations

| Command | Description |
|---------|-------------|
| `docker volume inspect --format '{{.Mountpoint}}' VOLUME` | Show volume host path |

## System & Debugging

| Command | Description |
|---------|-------------|
| `docker checkpoint COMMAND` | Manage checkpoints |
| `docker deploy [OPTIONS] STACK` | Deploy a new stack or update existing stack |
| `docker node COMMAND` | Manage Swarm nodes |
| `docker service COMMAND` | Manage services |
| `docker stack COMMAND` | Manage stacks |
| `docker swarm COMMAND` | Manage Swarm |
| `docker config COMMAND` | Manage Docker configs |
| `docker secret COMMAND` | Manage Docker secrets |
| `docker plugin COMMAND` | Manage plugins |
| `docker context COMMAND` | Manage contexts |
| `docker builder COMMAND` | Manage builds |
| `docker compose COMMAND` | Docker Compose (v2) commands |

## Logging & Monitoring

| Command | Description |
|---------|-------------|
| `docker events --filter 'event=die'` | Monitor container death events |
| `docker logs [OPTIONS] CONTAINER` | Show container logs. Options:<br>`-f`/`--follow`: Follow log output<br>`--tail N`: Show last N lines<br>`-t`/`--timestamps`: Show timestamps<br>`--since TIMESTAMP`: Show logs since timestamp (e.g., "2023-01-02T13:23:37")<br>`--until TIMESTAMP`: Show logs before timestamp<br>`--details`: Show extra details<br>`-n`/`--tail all`: Show all logs (default) |
| `docker stats --all --format "table {{.Container}}\t{{.CPUPerc}}\t{{.MemUsage}}"` | Custom stats output |

## Security

| Command | Description |
|---------|-------------|
| `docker scan IMAGE` | Scan image for vulnerabilities |
| `docker trust inspect IMAGE` | Show trust information for image |
| `docker image sign IMAGE` | Sign an image |
| `docker sbom IMAGE` | View software bill of materials |

## Buildx (Advanced Building)

| Command | Description |
|---------|-------------|
| `docker buildx build [OPTIONS] PATH` | Build with BuildKit |
| `docker buildx create --use` | Create and use builder instance |
| `docker buildx inspect` | Inspect current builder instance |
| `docker buildx bake [OPTIONS] [TARGET...]` | Build from compose file |

## Swarm Mode

| Command | Description |
|---------|-------------|
| `docker swarm init --advertise-addr <IP>` | Initialize swarm |
| `docker swarm join-token worker` | Show worker join token |
| `docker service scale SERVICE=REPLICAS` | Scale service |
| `docker service logs [OPTIONS] SERVICE` | Show service logs (supports same options as `docker logs`) |

## Useful Combinations

| Command | Description |
|---------|-------------|
| `docker ps -a --filter "status=exited"` | List stopped containers |
| `docker rm $(docker ps -aq)` | Remove all containers |
| `docker rmi $(docker images -q)` | Remove all images |
| `docker exec -it CONTAINER /bin/bash` | Get interactive shell |
| `docker inspect --format '{{.NetworkSettings.IPAddress}}' CONTAINER` | Get container IP |
| `docker stats $(docker ps --format '{{.Names}}')` | Monitor all running containers |
| `docker logs -f -t --tail 100 CONTAINER` | Follow logs with timestamps, showing last 100 lines |
| `docker logs --since 30m CONTAINER` | Show logs from last 30 minutes |
| `docker logs -f --until 2h CONTAINER` | Follow logs until logs from 2 hours ago |

## Docker Compose v2 (Modern)

| Command | Description |
|---------|-------------|
| `docker compose up -d --build` | Rebuild and restart containers |
| `docker compose config` | Validate and view compose file |
| `docker compose pull` | Pull service images |
| `docker compose push` | Push service images |
| `docker compose convert` | Convert compose file to platform format |
| `docker compose version` | Show version information |
| `docker compose logs [OPTIONS] [SERVICE]` | View service logs (supports same options as `docker logs`) |

## Registry Management

| Command | Description |
|---------|-------------|
| `docker search --limit 5 TERM` | Search with result limit |
| `docker logout REGISTRY` | Logout from specific registry |
| `docker image prune -a --filter "until=24h"` | Remove images older than 24h |

## Advanced Logging Examples

| Command | Description |
|---------|-------------|
| `docker logs --since $(date -d '1 hour ago' +%Y-%m-%dT%H:%M:%S) CONTAINER` | Show logs from last hour using date command |
| `docker logs -f --since 10m CONTAINER \| grep -i error` | Follow logs from last 10 minutes and filter for errors |
| `docker logs --tail 50 -t CONTAINER \| tee container.log` | Save last 50 logs with timestamps to file |
| `docker compose logs -f --tail=0` | Follow all compose service logs from current time |

## Useful Aliases

Add these to your `~/.bashrc` or `~/.zshrc`:
```bash
alias dps='docker ps --format "table {{.ID}}\t{{.Names}}\t{{.Status}}\t{{.Ports}}"'
alias dimg='docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}\t{{.Size}}"'
alias dstop='docker stop $(docker ps -q)'
alias drm='docker rm $(docker ps -aq)'
alias drmi='docker rmi $(docker images -q)'
alias dclean='docker system prune -a -f --volumes'
alias dlogs='docker logs -f -t --tail 100'
alias dlogserr='docker logs --since 1h | grep -i error'
alias dcomlogs='docker compose logs -f --tail=100'
