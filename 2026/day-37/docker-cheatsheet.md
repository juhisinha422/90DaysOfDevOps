# 🐳 Docker Cheat Sheet

A quick reference guide for commonly used Docker commands, organized by category.

---

# 📦 Container Commands

Containers are running instances of Docker images.

| Command | Description |
|---------|-------------|
| `docker run` | Creates and starts a container from a Docker image |
| `docker ps` | Lists running containers |
| `docker ps -a` | Lists all containers (running + stopped) |
| `docker stop` | Stops a running container |
| `docker start` | Starts a stopped container |
| `docker restart` | Restarts a container |
| `docker rm` | Removes a stopped container |
| `docker exec` | Executes commands inside a running container |
| `docker logs` | Displays logs of a container |
| `docker inspect` | Shows detailed information about a container |

### Examples

```bash
# Run a container in detached mode
docker run -d nginx

# Run an interactive container
docker run -it ubuntu bash

# List running containers
docker ps

# List all containers
docker ps -a

# Stop a container
docker stop container_id

# Remove a container
docker rm container_id

# Access a running container
docker exec -it container_id bash

# View container logs
docker logs container_id
```

---

# 🖼️ Image Commands

Docker images are templates used to create containers.

| Command | Description |
|---------|-------------|
| `docker build` | Builds a Docker image from a Dockerfile |
| `docker pull` | Downloads an image from Docker Hub or another registry |
| `docker push` | Pushes an image to a Docker registry |
| `docker tag` | Creates a tag/version for an image |
| `docker images` | Lists all Docker images |
| `docker image ls` | Lists all Docker images |
| `docker rmi` | Removes Docker images |
| `docker inspect` | Displays detailed image information |

### Examples

```bash
# Build an image
docker build -t myapp:v1 .

# Pull an image from Docker Hub
docker pull nginx

# List images
docker images

# Tag an image
docker tag myapp:v1 username/myapp:v1

# Push image to Docker Hub
docker push username/myapp:v1

# Remove an image
docker rmi image_id
```

---

# 💾 Volume Commands

Docker volumes are used to persist data outside containers.

| Command | Description |
|---------|-------------|
| `docker volume create` | Creates a Docker volume |
| `docker volume ls` | Lists Docker volumes |
| `docker volume inspect` | Shows detailed information about a volume |
| `docker volume rm` | Removes a Docker volume |

### Examples

```bash
# Create a volume
docker volume create my-volume

# List volumes
docker volume ls

# Inspect volume details
docker volume inspect my-volume

# Remove a volume
docker volume rm my-volume
```

---

# 🌐 Network Commands

Docker networks allow containers to communicate with each other.

| Command | Description |
|---------|-------------|
| `docker network ls` | Lists Docker networks |
| `docker network create` | Creates a custom network |
| `docker network inspect` | Shows network details |
| `docker network connect` | Connects a container to a network |
| `docker network disconnect` | Disconnects a container from a network |
| `docker network rm` | Removes a network |

### Examples

```bash
# Create a network
docker network create my-network

# List networks
docker network ls

# Run container with a custom network
docker run -d --name db --network my-network mongo

# Inspect network
docker network inspect my-network

# Remove network
docker network rm my-network
```

---

# 📝 Docker Compose Commands

Docker Compose is used to manage multi-container applications.

| Command | Description |
|---------|-------------|
| `docker compose up` | Creates and starts services defined in docker-compose.yml |
| `docker compose up -d` | Starts services in detached mode |
| `docker compose down` | Stops and removes containers and networks |
| `docker compose down -v` | Removes containers, networks, and volumes |
| `docker compose ps` | Lists Compose services |
| `docker compose logs` | Shows service logs |
| `docker compose build` | Builds images defined in Compose file |

### Examples

```bash
# Start application
docker compose up -d

# View running services
docker compose ps

# View logs
docker compose logs

# Stop application
docker compose down

# Remove volumes also
docker compose down -v
```

---

# 🔍 Docker Information & Troubleshooting Commands

| Command | Description |
|---------|-------------|
| `docker info` | Displays Docker system information |
| `docker version` | Shows Docker version details |
| `docker system df` | Shows Docker disk usage |
| `docker stats` | Shows container resource usage |
| `docker top` | Displays running processes inside a container |
| `docker events` | Shows real-time Docker events |

### Examples

```bash
# Check Docker disk usage
docker system df

# Detailed disk usage
docker system df -v

# Monitor container resources
docker stats

# Check Docker information
docker info
```

---

# 🧹 Docker Cleanup Commands

| Command | Description |
|---------|-------------|
| `docker container prune` | Removes stopped containers |
| `docker image prune` | Removes unused images |
| `docker volume prune` | Removes unused volumes |
| `docker system prune` | Removes unused containers, networks, images, and cache |

### Examples

```bash
# Remove stopped containers
docker container prune

# Remove unused images
docker image prune

# Clean unused Docker resources
docker system prune
```

---

# 🐳 Dockerfile Instructions

A Dockerfile contains instructions used to build a Docker image. Each instruction defines a step in the image creation process.

| Instruction | Description |
|------------|-------------|
| `FROM` | Defines the base image used to build the Docker image |
| `RUN` | Executes commands during the image build process, such as installing dependencies |
| `COPY` | Copies files and directories from the host machine into the Docker image |
| `WORKDIR` | Sets the working directory inside the container where commands are executed |
| `EXPOSE` | Documents the port on which the application runs inside the container |
| `CMD` | Defines the default command that runs when the container starts |
| `ENTRYPOINT` | Defines the main executable that runs when the container starts |

## Example Dockerfile

```dockerfile
FROM node:22-alpine

WORKDIR /app

COPY package.json .

RUN npm install

COPY . .

EXPOSE 3000

CMD ["npm", "start"]
