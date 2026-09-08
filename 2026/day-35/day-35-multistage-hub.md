# Day 35 – Multi-Stage Builds & Docker Hub

## Task
Build optimized images and share them with the world. Multi-stage builds are how real teams ship small, secure images. Docker Hub is how you distribute them. Both common interview topics.

---

## Task 1: The Problem with Large Images

1. Write a simple GO "Hello World" app
2. Build it in a **single stage**
3. Check the image size

**Result:** initial single-stage image size — **1.29GB**

<img width="1032" height="857" alt="image" src="https://github.com/user-attachments/assets/c739da6b-0975-481c-a73b-ade82ef7be7d" />

**`Dockerfile`:**
```dockerfile
# Base image 
FROM golang:1.21

# Current working directory
WORKDIR /app

# Copy required files
COPY main.go .

# Compile the application
RUN go build -o server main.go

# Ports
EXPOSE 8080

CMD ["./server"]
```


---

## Task 2: Multi-Stage Build

1. Rewrote the Dockerfile using multi-stage build:
   - **Stage 1** — build the app (install dependencies, compile)
   - **Stage 2** — copy only the built artifact into a minimal base image (`alpine`)
2. Rebuilt and compared sizes

<img width="569" height="95" alt="image" src="https://github.com/user-attachments/assets/8f134941-feba-4330-8924-3c8e0741c4bb" />
<img width="864" height="943" alt="image" src="https://github.com/user-attachments/assets/b915a163-8277-4b18-bcb4-c91a007cf86e" />


**Result:** size dropped from **1.5GB → 10.6MB**.

**Why multi-stage is so much smaller:**
1. **Compilation Tooling Removal:** The Go compiler, cache, and dependency manifests are left completely behind in the temporary `builder` layer.
2. **Zero Base OS Footprint:** Using `scratch` means there is no underlying Linux distribution kernel userland (no Ubuntu/Debian bloat, no package managers like `apt`, no bash).
3. **Attack Surface Reduction:** Discarding external binaries means vulnerability scanners have nothing to flag, creating a highly secure image.

---

## Task 3: Push to Docker Hub

1. Created a Docker Hub account
2. Logged in from the terminal
3. Tagged the image: `yourusername/image-name:tag`
4. Pushed to Docker Hub
5. Pulled it back (after removing locally) to verify

<img width="789" height="269" alt="image" src="https://github.com/user-attachments/assets/7e99788e-e684-4c64-a7ab-18834f507eeb" />
<img width="1572" height="337" alt="image" src="https://github.com/user-attachments/assets/78d5eb74-06b1-439a-8d7d-cab4be21c78c" />

<img width="1245" height="259" alt="image" src="https://github.com/user-attachments/assets/6b2a55cb-edba-41ff-872c-10ddd1af1c5c" />


---

## Task 4: Docker Hub Repository

1. Checked the pushed image on Docker Hub
2. Added a description to the repository
3. Explored the tags tab to understand versioning
4. Compared pulling a specific tag vs `latest`

**Tags used:**
titask74/go-optimized-app:v1.0.0 — specific version tag
titask74/go-optimized-app:latest — default rolling pointer tag

---

## Task 5: Image Best Practices

Applied to one image and rebuilt:
1. Minimal base image (Alpine vs Ubuntu — compared sizes)
2. **Non-root user** — added `USER` in the Dockerfile
3. Combined `RUN` commands to reduce layer count
4. **Specific version tags** for base images (not `latest`)

**Alpine `Dockerfile`:**(13MB)
```dockerfile
h# 1. Use a specific tag for the base build image (Rule #4)
FROM golang:1.21-alpine3.19 AS builder
WORKDIR /app
ENV GO111MODULE=off
COPY main.go .
RUN CGO_ENABLED=0 GOOS=linux go build -o server main.go

# 2. Use a specific minimal base image for production (Rule #1 & #4)
FROM alpine:3.19
WORKDIR /

# 3. Combine RUN commands to create a non-root user and group (Rule #2 & #3)
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

# Copy the compiled application binary from the builder stage
COPY --from=builder /app/server .

# Change ownership of the application file to our new non-root user
RUN chown appuser:appgroup /server

# 4. Enforce switching away from root to the non-root user (Rule #2)
USER appuser

EXPOSE 8080
CMD ["./server"]

```
<img width="820" height="141" alt="image" src="https://github.com/user-attachments/assets/bcbc5743-488e-4aba-a32e-fa3d7b639ffe" />

**But Scratch version is more optimized.**(10MB)
**Sratch `Dockerfile`:**
```dockerfile
# Stage : 1 : The build stage
FROM golang:1.21 AS builder

# Working directory
WORKDIR /app

# Copy required files
COPY main.go .

# Compile a statically linked binary (no outside OS libraries needed)
RUN CGO_ENABLED=0 GOOS=linux go build -o server main.go


# Stage : 2 : The final production stage
FROM scratch

WORKDIR /

# Copy only the compiled executable file from stage 1
COPY --from=builder /app/server .

# Ports
EXPOSE 8080

CMD ["/server"]

                        
```


**Result:** image size came down to **13MB** — not multi-stage this time, but with the added security of a non-root user and a minimal base.
