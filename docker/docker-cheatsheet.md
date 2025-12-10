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

### Installation

```bash
# Install Docker Compose (standalone)
# Linux
sudo curl -L "https://github.com/docker/compose/releases/latest/download/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose

# macOS
brew install docker-compose

# Verify installation
docker-compose --version
docker compose version  # Docker Compose V2 (plugin)
```

### Basic Commands

```bash
# Start services
docker-compose up
docker-compose up -d          # Detached mode (background)
docker-compose up --build    # Build images before starting
docker-compose up --force-recreate  # Recreate containers

# Stop services
docker-compose down
docker-compose down -v       # Remove volumes too
docker-compose down --remove-orphans  # Remove orphaned containers
docker-compose down -t 30    # Timeout in seconds

# View logs
docker-compose logs
docker-compose logs -f       # Follow logs
docker-compose logs <service>
docker-compose logs --tail=100  # Last 100 lines
docker-compose logs --since 10m  # Last 10 minutes

# Execute command
docker-compose exec <service> <command>
docker-compose exec web bash
docker-compose exec -T web npm test  # Non-interactive

# Run one-off command
docker-compose run <service> <command>
docker-compose run web npm install
docker-compose run --rm web bash  # Remove after execution

# Scale services
docker-compose up --scale web=3
docker-compose up --scale web=3 --scale worker=2

# Build services
docker-compose build
docker-compose build --no-cache
docker-compose build <service>  # Build specific service
docker-compose build --parallel  # Build in parallel

# List services
docker-compose ps
docker-compose ps -a          # All services

# Start/stop/restart
docker-compose start
docker-compose start <service>
docker-compose stop
docker-compose stop <service>
docker-compose restart
docker-compose restart <service>

# Pause/unpause
docker-compose pause
docker-compose pause <service>
docker-compose unpause
docker-compose unpause <service>
```

### Advanced Commands

```bash
# Validate configuration
docker-compose config
docker-compose config --services  # List services
docker-compose config --volumes   # List volumes

# Pull images
docker-compose pull
docker-compose pull <service>

# Push images (if using build)
docker-compose push
docker-compose push <service>

# View service status
docker-compose top
docker-compose top <service>

# Port mapping
docker-compose port <service> <port>
docker-compose port web 80

# Events stream
docker-compose events
docker-compose events --json

# Kill services
docker-compose kill
docker-compose kill -s SIGTERM  # Send specific signal
docker-compose kill <service>

# Remove stopped containers
docker-compose rm
docker-compose rm -f           # Force remove
docker-compose rm -v           # Remove volumes

# Use specific compose file
docker-compose -f docker-compose.prod.yml up
docker-compose -f docker-compose.yml -f docker-compose.override.yml up

# Use specific project name
docker-compose -p myproject up
```

### docker-compose.yml Examples

#### Basic Example

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

#### Advanced Example

```yaml
version: '3.8'

services:
  web:
    build:
      context: .
      dockerfile: Dockerfile
      args:
        - NODE_ENV=production
    image: myapp:latest
    container_name: myapp-web
    ports:
      - "8080:80"
      - "8443:443"
    volumes:
      - ./app:/app
      - ./config:/config:ro  # Read-only
      - web-data:/data
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgresql://user:pass@db:5432/mydb
    env_file:
      - .env
      - .env.production
    depends_on:
      - db
      - redis
    networks:
      - frontend
      - backend
    restart: unless-stopped
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:80/health"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
    deploy:
      resources:
        limits:
          cpus: '0.5'
          memory: 512M
        reservations:
          cpus: '0.25'
          memory: 256M

  db:
    image: postgres:15-alpine
    container_name: myapp-db
    environment:
      POSTGRES_DB: myapp
      POSTGRES_USER: user
      POSTGRES_PASSWORD: password
    volumes:
      - db-data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb.d/init.sql
    networks:
      - backend
    restart: always
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U user"]
      interval: 10s
      timeout: 5s
      retries: 5

  redis:
    image: redis:7-alpine
    container_name: myapp-redis
    command: redis-server --appendonly yes
    volumes:
      - redis-data:/data
    networks:
      - backend
    restart: always

  nginx:
    image: nginx:alpine
    container_name: myapp-nginx
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf:ro
      - ./ssl:/etc/nginx/ssl:ro
    depends_on:
      - web
    networks:
      - frontend
    restart: always

volumes:
  db-data:
  redis-data:
  web-data:

networks:
  frontend:
    driver: bridge
  backend:
    driver: bridge
```

#### Development Override Example

```yaml
# docker-compose.override.yml (auto-loaded in dev)
version: '3.8'

services:
  web:
    build:
      target: development
    volumes:
      - .:/app
      - /app/node_modules  # Anonymous volume to prevent overwrite
    environment:
      - NODE_ENV=development
      - DEBUG=true
    ports:
      - "9229:9229"  # Node.js debugger
    command: npm run dev
```

### Compose File Features

#### Build Options

```yaml
services:
  web:
    build:
      context: ./dir
      dockerfile: Dockerfile.prod
      args:
        - BUILDKIT_INLINE_CACHE=1
        - NODE_ENV=production
      target: production
      cache_from:
        - myapp:latest
```

#### Health Checks

```yaml
services:
  web:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost"]
      interval: 30s
      timeout: 10s
      retries: 3
      start_period: 40s
```

#### Resource Limits

```yaml
services:
  web:
    deploy:
      resources:
        limits:
          cpus: '1.0'
          memory: 1G
        reservations:
          cpus: '0.5'
          memory: 512M
```

#### Networks

```yaml
services:
  web:
    networks:
      - frontend
      - backend

networks:
  frontend:
    driver: bridge
    ipam:
      config:
        - subnet: 172.20.0.0/16
  backend:
    external: true  # Use existing network
```

#### Volumes

```yaml
services:
  web:
    volumes:
      - ./data:/app/data           # Bind mount
      - app-data:/app/storage      # Named volume
      - /var/lib/mysql             # Anonymous volume

volumes:
  app-data:
    driver: local
    driver_opts:
      type: none
      o: bind
      device: /path/on/host
```

#### Environment Variables

```yaml
services:
  web:
    environment:
      - NODE_ENV=production
      - DATABASE_URL=postgres://...
    env_file:
      - .env
      - .env.production
    environment:
      - DEBUG=${DEBUG:-false}  # Default value
```

#### Dependencies

```yaml
services:
  web:
    depends_on:
      - db
      - redis
    # Or with conditions
    depends_on:
      db:
        condition: service_healthy
      redis:
        condition: service_started
```

### Useful One-Liners

```bash
# Rebuild and restart specific service
docker-compose up -d --build web

# View logs for specific service
docker-compose logs -f --tail=100 web

# Execute command in running service
docker-compose exec web npm test

# Run one-off command in new container
docker-compose run --rm web npm install

# Scale specific service
docker-compose up -d --scale web=3

# Stop all services and remove volumes
docker-compose down -v

# Validate compose file
docker-compose config

# List all services
docker-compose config --services

# Pull latest images
docker-compose pull

# Build without cache
docker-compose build --no-cache

# View resource usage
docker-compose top

# Copy file from container
docker-compose cp web:/app/file.txt ./

# Copy file to container
docker-compose cp ./file.txt web:/app/
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
| Docker Compose logs | `docker-compose logs -f` |
| Docker Compose exec | `docker-compose exec <service> bash` |
| Docker Compose build | `docker-compose build` |
| Docker Compose scale | `docker-compose up --scale web=3` |
| Docker Compose config | `docker-compose config` |
| Docker Compose ps | `docker-compose ps` |
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

