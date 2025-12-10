# 🐳 The Ultimate Docker Cheatsheet

### *Because containers are just fancy boxes, but you still need to know how to use them*

---

## 🎬 Act I: Getting Started (a.k.a. "Hello, Container")

### Installation

```bash
# Install Docker (Linux - Ubuntu/Debian)
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh

# Install Docker (macOS)
brew install docker

# Install Docker (Windows)
# Download Docker Desktop from docker.com

# Verify installation
docker --version
docker info
```

### Basic Commands

```bash
# Check Docker status
docker ps                    # Running containers
docker ps -a                 # All containers (including stopped)
docker ps -l                 # Last container
docker ps -q                 # Only container IDs

# Docker version and info
docker version
docker info

# System-wide information
docker system df             # Disk usage
docker system events         # Real-time events
docker system prune          # Clean up everything
```

---

## 📦 Act II: Images (The Blueprint)

### Image Operations

```bash
# List images
docker images
docker image ls
docker images -a              # All images (including intermediate)

# Pull image
docker pull <image-name>
docker pull nginx:latest
docker pull ubuntu:22.04
docker pull node:20-alpine

# Search for images
docker search <term>
docker search nginx

# Inspect image
docker inspect <image-name>
docker inspect nginx:latest

# Remove image
docker rmi <image-name>
docker rmi <image-id>
docker rmi -f <image-id>     # Force remove

# Remove all unused images
docker image prune
docker image prune -a        # Remove all unused images (not just dangling)

# Build image
docker build -t <name:tag> .
docker build -t myapp:latest .
docker build -t myapp:v1.0 -f Dockerfile.prod .

# Build with no cache
docker build --no-cache -t myapp:latest .

# Build with build args
docker build --build-arg VERSION=1.0 -t myapp:latest .

# Tag image
docker tag <source> <target>
docker tag myapp:latest myapp:v1.0
docker tag myapp:latest registry.com/myapp:latest

# Save/load image
docker save -o image.tar <image-name>
docker load -i image.tar

# Export/import image
docker export <container-id> > container.tar
docker import container.tar myapp:latest
```

### Dockerfile Basics

```dockerfile
# Basic Dockerfile
FROM ubuntu:22.04

# Set working directory
WORKDIR /app

# Copy files
COPY . .

# Install dependencies
RUN apt update && apt install -y python3

# Expose port
EXPOSE 8080

# Set environment variable
ENV NODE_ENV=production

# Run command
CMD ["python3", "app.py"]

# Or use ENTRYPOINT
ENTRYPOINT ["python3"]
CMD ["app.py"]
```

---

## 🚀 Act III: Containers (The Running Instance)

### Container Lifecycle

```bash
# Run container
docker run <image-name>
docker run nginx
docker run -d nginx          # Detached mode (background)
docker run -it nginx          # Interactive + TTY
docker run --rm nginx        # Auto-remove when stopped

# Run with name
docker run --name my-nginx nginx

# Run with port mapping
docker run -p 8080:80 nginx
docker run -p 127.0.0.1:8080:80 nginx  # Bind to localhost only

# Run with volume
docker run -v /host/path:/container/path nginx
docker run -v $(pwd):/app nginx

# Run with environment variables
docker run -e VAR=value nginx
docker run --env-file .env nginx

# Run with network
docker run --network bridge nginx
docker run --network host nginx

# Start container
docker start <container-name>
docker start <container-id>

# Stop container
docker stop <container-name>
docker stop <container-id>
docker stop $(docker ps -q)  # Stop all running containers

# Restart container
docker restart <container-name>

# Pause/Unpause
docker pause <container-name>
docker unpause <container-name>

# Remove container
docker rm <container-name>
docker rm -f <container-name>  # Force remove (stop first)
docker rm $(docker ps -aq)      # Remove all stopped containers
```

### Container Interaction

```bash
# Execute command in running container
docker exec <container-name> <command>
docker exec my-nginx ls -la
docker exec -it my-nginx /bin/bash  # Interactive shell

# Attach to container
docker attach <container-name>

# View logs
docker logs <container-name>
docker logs -f <container-name>    # Follow logs
docker logs --tail 100 <container-name>
docker logs --since 10m <container-name>

# Copy files
docker cp <container>:<path> <host-path>
docker cp <host-path> <container>:<path>
docker cp my-nginx:/etc/nginx/nginx.conf ./nginx.conf

# Inspect container
docker inspect <container-name>
docker inspect --format='{{.NetworkSettings.IPAddress}}' <container-name>

# Container stats
docker stats
docker stats <container-name>
docker stats --no-stream       # One-time stats
```

---

## 🌐 Act IV: Networking (Connect the Dots)

### Network Operations

```bash
# List networks
docker network ls

# Create network
docker network create <network-name>
docker network create my-network
docker network create --driver bridge my-network

# Inspect network
docker network inspect <network-name>

# Connect container to network
docker network connect <network> <container>
docker network disconnect <network> <container>

# Remove network
docker network rm <network-name>
docker network prune          # Remove unused networks

# Network types
# bridge (default), host, none, overlay
```

### Port Mapping

```bash
# Map ports
docker run -p 8080:80 nginx           # host:container
docker run -p 127.0.0.1:8080:80 nginx # Bind to specific interface
docker run -P nginx                   # Publish all exposed ports

# List port mappings
docker port <container-name>
```

---

## 💾 Act V: Volumes (Persistent Storage)

### Volume Operations

```bash
# List volumes
docker volume ls

# Create volume
docker volume create <volume-name>
docker volume create my-data

# Inspect volume
docker volume inspect <volume-name>

# Remove volume
docker volume rm <volume-name>
docker volume prune          # Remove unused volumes

# Use volume in container
docker run -v my-data:/data nginx
docker run -v /host/path:/container/path nginx  # Bind mount
docker run --mount type=volume,source=my-data,target=/data nginx
```

### Volume Examples

```bash
# Named volume
docker run -v my-data:/app/data nginx

# Bind mount
docker run -v $(pwd):/app nginx

# Read-only mount
docker run -v my-data:/app/data:ro nginx

# Tmpfs (in-memory)
docker run --tmpfs /tmp nginx
```

---

## 🏗️ Act VI: Docker Compose (Orchestration Made Easy)

### Docker Compose Basics

```bash
# Start services
docker-compose up
docker-compose up -d          # Detached mode
docker-compose up --build    # Build images first

# Stop services
docker-compose down
docker-compose down -v       # Remove volumes too

# View logs
docker-compose logs
docker-compose logs -f       # Follow logs
docker-compose logs <service>

# Execute command
docker-compose exec <service> <command>
docker-compose exec web bash

# Scale services
docker-compose up --scale web=3

# Build services
docker-compose build
docker-compose build --no-cache

# List services
docker-compose ps

# Start/stop/restart
docker-compose start
docker-compose stop
docker-compose restart
```

### docker-compose.yml Example

```yaml
version: '3.8'

services:
  web:
    build: .
    ports:
      - "8080:80"
    volumes:
      - .:/app
    environment:
      - NODE_ENV=production
    depends_on:
      - db

  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - db-data:/var/lib/postgresql/data

volumes:
  db-data:
```

---

## 🔧 Act VII: Advanced Operations

### Multi-stage Builds

```dockerfile
# Stage 1: Build
FROM node:20 AS builder
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

# Stage 2: Production
FROM node:20-alpine
WORKDIR /app
COPY --from=builder /app/dist ./dist
COPY package*.json ./
RUN npm ci --only=production
CMD ["node", "dist/index.js"]
```

### Health Checks

```dockerfile
# In Dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl -f http://localhost:8080/health || exit 1
```

```bash
# Check health
docker inspect --format='{{.State.Health.Status}}' <container>
```

### Resource Limits

```bash
# Run with memory limit
docker run -m 512m nginx

# Run with CPU limit
docker run --cpus="1.5" nginx

# Run with both
docker run -m 512m --cpus="1.5" nginx
```

---

## 🧹 Act VIII: Cleanup (The Spring Cleaning)

### Cleanup Commands

```bash
# Remove stopped containers
docker container prune

# Remove unused images
docker image prune
docker image prune -a        # All unused images

# Remove unused volumes
docker volume prune

# Remove unused networks
docker network prune

# Remove everything (nuclear option)
docker system prune -a --volumes

# Remove specific resources
docker rm $(docker ps -aq -f status=exited)
docker rmi $(docker images -q -f dangling=true)
```

### Disk Usage

```bash
# Check disk usage
docker system df
docker system df -v          # Verbose

# Inspect what's using space
docker system events
```

---

## 🐛 Act IX: Debugging & Troubleshooting

### Common Debugging Commands

```bash
# Container logs
docker logs <container>
docker logs --tail 100 -f <container>

# Container processes
docker top <container>

# Container stats
docker stats <container>

# Inspect container
docker inspect <container>

# Execute into container
docker exec -it <container> /bin/bash
docker exec -it <container> /bin/sh

# Check container events
docker events

# Check Docker daemon logs
# Linux: journalctl -u docker
# macOS: ~/Library/Containers/com.docker.docker/Data/log
```

### Common Issues

```bash
# Container won't start
docker logs <container>      # Check logs
docker inspect <container>   # Check configuration

# Port already in use
# Find what's using the port
lsof -i :8080                # macOS/Linux
netstat -ano | findstr :8080  # Windows

# Permission denied
# Run with --privileged (not recommended for production)
docker run --privileged <image>

# Out of disk space
docker system prune -a --volumes

# Network issues
docker network inspect <network>
docker network ls
```

---

## 🎭 Act X: Useful One-Liners

```bash
# Run and remove container
docker run --rm <image>

# Run with current directory mounted
docker run -v $(pwd):/app -w /app <image>

# Run with environment from file
docker run --env-file .env <image>

# Build and tag
docker build -t myapp:latest -t myapp:v1.0 .

# Remove all stopped containers
docker rm $(docker ps -aq -f status=exited)

# Remove all images
docker rmi $(docker images -q)

# Stop all containers
docker stop $(docker ps -q)

# Copy file from container
docker cp <container>:/path/to/file ./

# View container IP
docker inspect -f '{{range.NetworkSettings.Networks}}{{.IPAddress}}{{end}}' <container>

# Execute command in all containers
docker ps -q | xargs -I {} docker exec {} <command>

# Clean build (no cache)
docker build --no-cache -t myapp:latest .

# Save all images
docker save $(docker images -q) -o all-images.tar

# Load all images
docker load -i all-images.tar
```

---

## 🆘 Emergency Commands (When Everything is on Fire)

```bash
# Stop all containers
docker stop $(docker ps -q)

# Remove all containers
docker rm -f $(docker ps -aq)

# Remove all images
docker rmi -f $(docker images -q)

# Nuclear option - remove everything
docker system prune -a --volumes --force

# Restart Docker daemon
# Linux
sudo systemctl restart docker

# macOS
# Restart Docker Desktop

# Windows
# Restart Docker Desktop

# Check Docker daemon
docker info

# Reset Docker (macOS)
# Docker Desktop > Troubleshoot > Reset to factory defaults
```

---

## 📝 Quick Reference Card

| What You Want | Command |
|---------------|---------|
| List containers | `docker ps` |
| List images | `docker images` |
| Run container | `docker run <image>` |
| Stop container | `docker stop <container>` |
| Remove container | `docker rm <container>` |
| View logs | `docker logs <container>` |
| Execute command | `docker exec -it <container> bash` |
| Build image | `docker build -t <name> .` |
| Remove image | `docker rmi <image>` |
| Port mapping | `docker run -p 8080:80 <image>` |
| Volume mount | `docker run -v /path:/path <image>` |
| Docker Compose up | `docker-compose up -d` |
| Docker Compose down | `docker-compose down` |
| Clean up | `docker system prune` |

---

## 🎬 The End Credits

Remember:
- Containers are ephemeral (use volumes for data)
- `docker run` creates a new container each time
- Use `docker-compose` for multi-container apps
- Clean up regularly or your disk will hate you
- Read the logs when things break
- Don't run as root in production (use USER directive)

**Now go containerize something awesome!** 🐳

---

*Made with ☕ and the frustration of "it works on my machine"*

*Last updated: When you finally got that container to start*

