# Day 31 – Dockerfile: Build Your Own Images

## Task
Write Dockerfiles and build custom images — the skill that separates someone who *uses* Docker from someone who actually *ships* with Docker.

---

## Task 1: Your First Dockerfile

1. Create folder `my-first-image`
2. Inside, create a `Dockerfile` that:
   - Uses `ubuntu` as the base image
   - Installs `curl`
   - Sets a default command to print `"Hello from my custom image!"`
3. Build the image, tag it `my-ubuntu:v1`
4. Run a container from it

**Verify:** message prints on `docker run`

**`Dockerfile`:**
```dockerfile
FROM ubuntu
RUN apt update && apt install -y curl
CMD ["echo", "THIS IS CUSTOM DOCKERFILE"]
```

**Commands:**
```bash
docker build -t my-ubuntu:v1 .
docker run my-ubuntu:v1
```
<img width="881" height="363" alt="image" src="https://github.com/user-attachments/assets/18b227b8-d1e2-4042-8954-fe8ff568c8fb" />

---

## Task 2: Dockerfile Instructions

Build a Dockerfile using all of:
- `FROM` — base image
- `RUN` — execute commands during build
- `COPY` — copy files from host into image
- `WORKDIR` — set working directory
- `EXPOSE` — document the port
- `CMD` — default command

<img width="889" height="949" alt="image" src="https://github.com/user-attachments/assets/d01995b6-42d4-48a7-b428-0128209471c9" />


**Instruction reference:**
| Instruction | Purpose |
|---|---|
| `FROM` | Sets the base image |
| `RUN` | Installs stuff / executes commands at build time |
| `WORKDIR` | Sets the working directory inside the image |
| `COPY` | Copies files from host into the image |
| `EXPOSE` | Documents which port the app uses |
| `CMD` | Sets the default command when the container runs |

---

## Task 3: CMD vs ENTRYPOINT

**CMD example:**
```dockerfile
FROM ubuntu
CMD ["echo", "hello"]
```
```bash
docker run image-name        # prints "hello"
docker run image-name ls     # runs "ls" instead — CMD gets fully overridden, not appended
```

**ENTRYPOINT example:**
```dockerfile
FROM ubuntu
ENTRYPOINT ["echo"]
```
```bash
docker run image-name hello
# output: hello
```

**When to use which:**
- **CMD** → sets a default that can be overridden entirely by the user
- **ENTRYPOINT** → fixed, non-negotiable behavior; arguments passed at runtime get appended to it instead of replacing it

---

## Task 4: Build a Simple Web App Image

1. Create a static `index.html` with any content
2. Write a Dockerfile that:
   - Uses `nginx:alpine` as base
   - Copies `index.html` into the Nginx web directory
3. Build and tag as `my-website:v1`
4. Run with port mapping, access via browser

<img width="1004" height="903" alt="image" src="https://github.com/user-attachments/assets/29dcebb6-569d-48df-b4a0-48cc3e04e2fd" />
<img width="1004" height="903" alt="image" src="https://github.com/user-attachments/assets/62a001ae-426e-4fce-8186-47c2c68fe758" />
<img width="1832" height="1012" alt="image" src="https://github.com/user-attachments/assets/738b0a70-133b-42ab-a444-da03c00f0b90" />




---

## Task 5: `.dockerignore`

1. Create `.dockerignore` in a project folder
2. Add entries:
   ```
   node_modules
   .git
   *.md
   .env
   ```
3. Build — verify ignored files aren't included

**Steps:**
```bash
touch .dockerignore
vim .dockerignore
```

These entries get excluded during the build's `COPY` step — reduces image size and avoids leaking sensitive/unnecessary files.

---

## Task 6: Build Optimization

1. Build once, change one line, rebuild — notice Docker's **cache** behavior
2. Reorder the Dockerfile so frequently-changing lines come **last**
3. Why does layer order matter for build speed?

**Observed:**
- First build → slow (nothing cached yet)
- Second build → fast (unchanged layers reused from cache)

**Why order matters:**
Docker caches each layer. Changing a layer invalidates the cache for that layer *and everything after it* — but layers before it stay cached. Putting stable, rarely-changing instructions (like `FROM`, `RUN apt install`) early, and frequently-changing ones (like `COPY` of source code) last, maximizes cache reuse and keeps rebuilds fast.
