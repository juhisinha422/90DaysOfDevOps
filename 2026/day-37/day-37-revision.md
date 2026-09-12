#  Day 37 – Docker Revision & Cheat Sheet

##  Self-Assessment Checklist

Mark yourself honestly — **can do**, **shaky**, or **haven't done**.

## ✅ Docker Skills Checklist

- [x] Run a container from Docker Hub (interactive + detached)
- [x] List, stop, remove containers and images
- [x] Explain image layers and how caching works
- [x] Write a Dockerfile from scratch with `FROM`, `RUN`, `COPY`, `WORKDIR`, `CMD`
- [ ] Explain `CMD` vs `ENTRYPOINT`
- [x] Build and tag a custom image
- [x] Create and use named volumes
- [ ] Use bind mounts
- [x] Create custom networks and connect containers
- [x] Write a `docker-compose.yml` for a multi-container app
- [x] Use environment variables and `.env` files in Compose
- [x] Write a multi-stage Dockerfile
- [x] Push an image to Docker Hub
- [x] Use `healthcheck` and `depends_on`

---

# ⚡ Quick-Fire Docker Questions

## 1. What is the difference between an image and a container?

A Docker **image** is a blueprint or template that contains the application, dependencies, libraries, and configuration required to run an application.

A Docker **container** is a running instance of that image.

We can create multiple containers from the same image, and each container runs independently.

Example:

```
Docker Image  →  Docker Container

nginx image   →  Running nginx container
```

---

## 2. What happens to data inside a container when you remove it?

Containers are **ephemeral**, which means their data is temporary.

When a container is removed:

- Data stored inside the container writable layer is deleted.
- Data remains only if it is stored using:
  - Docker volumes
  - Bind mounts

Example:

```
Container removed ❌
Data inside container ❌

Volume data ✅
Bind mount data ✅
```

---

## 3. How do two containers on the same custom network communicate?

Two containers connected to the same custom Docker network communicate using **container names as DNS names**.

Docker provides built-in DNS resolution inside custom networks, allowing containers to communicate without using IP addresses.

Example:

```
Backend Container
        |
        |
        ↓
mongodb://database:27017
        |
        |
Database Container
```

Here:

```
database = container name
```

---

## 4. What does `docker compose down -v` do differently from `docker compose down`?

### `docker compose down`

Removes:

- Containers
- Networks

Keeps:

- Volumes

---

### `docker compose down -v`

Removes:

- Containers
- Networks
- Volumes

The `-v` flag deletes named volumes, which means persistent data stored inside those volumes will also be removed.

---

## 5. Why are multi-stage builds useful?

Multi-stage builds help:

- Reduce Docker image size
- Improve security
- Remove unnecessary build dependencies

The build process is separated into multiple stages:

1. Build stage:
   - Installs dependencies
   - Compiles application
   - Creates build artifacts

2. Runtime stage:
   - Copies only required files
   - Runs the application

Benefits:

- Smaller images
- Fewer packages
- Reduced attack surface

---

## 6. What is the difference between `COPY` and `ADD`?

### COPY

Used to copy files and directories from the build context into the Docker image.

Example:

```dockerfile
COPY package.json /app/
```

### ADD

Has the same functionality as COPY but provides additional features:

- Extract compressed archives
- Download files from URLs

Example:

```dockerfile
ADD app.tar.gz /app/
```

Docker recommends using **COPY** unless you specifically need ADD features.

---

## 7. What does `-p 8080:80` mean?

Port mapping syntax:

```bash
-p host_port:container_port
```

Example:

```bash
docker run -p 8080:80 nginx
```

Meaning:

```
Host Machine Port 8080
          |
          ↓
Container Port 80
```

It allows users to access the application running inside the container through the host machine.

---

## 8. How do you check how much disk space Docker is using?

Use:

```bash
docker system df
```

It shows disk usage for:

- Images
- Containers
- Volumes
- Build cache

For detailed information:

```bash
docker system df -v
```

---

# 📚 Docker Documentation

Docker commands and Dockerfile instructions:

➡️ [View Docker Cheat Sheet & Dockerfile Instructions](./docker-cheatsheet.md)

---

# 🔁 Revisit Weak Spots

## 1. Explain CMD vs ENTRYPOINT

Both `CMD` and `ENTRYPOINT` define what command runs when a container starts.

### Simple Difference:

| Instruction | Purpose |
|---|---|
| `CMD` | Default command (can be overridden) |
| `ENTRYPOINT` | Main command (usually fixed) |

Example:

```dockerfile
FROM ubuntu

ENTRYPOINT ["echo"]

CMD ["Hello Docker"]
```

Run:

```bash
docker run image_name
```

Output:

```
Hello Docker
```

Run:

```bash
docker run image_name DevOps
```

Output:

```
DevOps
```

Here:

```
ENTRYPOINT → echo
CMD        → Default argument
```

---

## 2. Use Bind Mounts

A **bind mount** maps a file or directory from the host machine directly into a container.

It is commonly used for:

- Development environments
- Sharing source code
- Testing changes without rebuilding images

### Syntax:

```bash
docker run -v <host-path>:<container-path> <image>
```

Example:

```bash
docker run -v /home/user/project:/app node-app
```

Mapping:

```
Host Machine              Container

/home/user/project  --->  /app
```

Changes made on the host are immediately available inside the container.

---

# 🎯 Day 37 Summary

Topics revised:

✅ Docker images and containers  
✅ Container lifecycle  
✅ Docker networking  
✅ Docker volumes  
✅ Docker Compose  
✅ Multi-stage builds  
✅ Dockerfile instructions  
✅ CMD vs ENTRYPOINT  
✅ Bind mounts  
✅ Docker troubleshooting commands  
