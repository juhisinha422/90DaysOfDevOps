# Day 36 – Docker Project: Dockerize a Full Application

## Task 1: Pick Your App

### A Node.js Express app with MongoDB

# School Attendance API

A Node.js + Express + MongoDB backend for tracking school attendance, with JWT auth, role-based access (admin/teacher), daily/monthly reports, and CSV export.

## Setup

```bash
npm install
cp .env.example .env
# edit .env with your MongoDB URI and JWT secret

# create the first admin account
node seedAdmin.js

# start the server
npm run dev    # with nodemon
npm start      # plain node
```

Default seeded admin: `admin@school.com` / `ChangeMe123!` — change this immediately after first login.

## Roles

- **admin** — full access: manage classes, students, users, attendance, reports
- **teacher** — can view classes/students, mark and update attendance, view reports (cannot create classes/students/users or delete attendance)

## Auth

All routes except `/api/auth/login` require a `Authorization: Bearer <token>` header.

| Method | Route | Access | Description |
|---|---|---|---|
| POST | `/api/auth/register` | admin | Create a new user (admin or teacher) |
| POST | `/api/auth/login` | public | Log in, returns JWT |
| GET | `/api/auth/me` | any logged-in user | Get current user profile |

## Classes

| Method | Route | Access |
|---|---|---|
| GET | `/api/classes` | any |
| GET | `/api/classes/:id` | any |
| POST | `/api/classes` | admin |
| PUT | `/api/classes/:id` | admin |
| DELETE | `/api/classes/:id` | admin |

## Students

| Method | Route | Access |
|---|---|---|
| GET | `/api/students?classId=` | any |
| GET | `/api/students/:id` | any |
| POST | `/api/students` | admin |
| PUT | `/api/students/:id` | admin |
| DELETE | `/api/students/:id` | admin |

## Attendance

| Method | Route | Access |
|---|---|---|
| POST | `/api/attendance` | admin, teacher |
| GET | `/api/attendance?classId=&date=&studentId=` | any |
| PUT | `/api/attendance/:id` | admin, teacher |
| DELETE | `/api/attendance/:id` | admin |

**Mark attendance for a class** (bulk, one call per class per day):

```json
POST /api/attendance
{
  "classId": "664f...",
  "date": "2026-08-02",
  "records": [
    { "studentId": "664a...", "status": "present" },
    { "studentId": "664b...", "status": "absent" },
    { "studentId": "664c...", "status": "late" }
  ]
}
```

## Reports

| Method | Route | Access | Description |
|---|---|---|---|
| GET | `/api/reports/daily?classId=&date=` | any | Present/absent/late counts for one day |
| GET | `/api/reports/monthly?classId=&year=&month=` | any | Per-student totals for the month |
| GET | `/api/reports/export?classId=&startDate=&endDate=` | any | Downloads a CSV of attendance for the date range |

## Project structure

```
school-attendance-app/
├── config/db.js
├── models/          User, Class, Student, Attendance
├── middleware/       auth.js (JWT), role.js (RBAC)
├── controllers/       auth, class, student, attendance, report
├── routes/
├── server.js
├── seedAdmin.js       bootstrap first admin
└── .env.example
```

<img width="1363" height="728" alt="image" src="https://github.com/user-attachments/assets/6d7ea361-a6e7-4812-b1a5-6489cde1fd24" />

<img width="1365" height="682" alt="image" src="https://github.com/user-attachments/assets/cf4f125e-36bb-4242-8e19-1306a0d6bbb2" />

<img width="1362" height="728" alt="image" src="https://github.com/user-attachments/assets/611b8423-771b-43db-8456-884f6e996c9c" />

## Notes

- Attendance is unique per `(student, class, date)` — marking twice for the same day updates the existing record instead of duplicating it.
- Passwords are hashed with bcrypt; never stored in plain text.
- Swap the in-memory JWT secret in `.env` for a strong random value before deploying.

## Task 2: Write the Dockerfile

### Create a Dockerfile for your application
Docker file for backend

```bash

ubuntu@ip-172-31-7-36:~/school-attendance-app$ batcat Dockerfile
───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: Dockerfile
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ FROM node:22-alpine
   2   │
   3   │ WORKDIR /app
   4   │
   5   │ COPY package*.json ./
   6   │
   7   │ RUN npm install
   8   │
   9   │ COPY . .
  10   │
  11   │ EXPOSE 3000
  12   │
  13   │ CMD ["npm", "start"]
  14   │
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
ubuntu@ip-172-31-7-36:~/school-attendance-app$


```
```
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker build -t school-attendance-app:v1 .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  17.94MB
Step 1/7 : FROM node:22-alpine
22-alpine: Pulling from library/node
16da5a640377: Pulling fs layer
55afa1ecc21d: Pulling fs layer
efbef6f9e333: Pulling fs layer
a2980c1fee17: Pulling fs layer
a2980c1fee17: Download complete
16da5a640377: Download complete
55afa1ecc21d: Download complete
16a559d14b4b: Download complete
35d06909b2a1: Download complete
55afa1ecc21d: Pull complete
efbef6f9e333: Download complete
efbef6f9e333: Pull complete
a2980c1fee17: Pull complete
16da5a640377: Pull complete
Digest: sha256:c610fcdfb1d5b4740dd70c284ed3cb16bb857e0f7166196e36a5501df7a3aa32
Status: Downloaded newer image for node:22-alpine
 ---> c610fcdfb1d5
Step 2/7 : WORKDIR /app
 ---> Running in 8f2d2ae39601
 ---> Removed intermediate container 8f2d2ae39601
 ---> c4ae23f18368
Step 3/7 : COPY package*.json ./
 ---> 917cd1ef12e8
Step 4/7 : RUN npm install
 ---> Running in b927307e3963
npm warn deprecated lodash.get@4.4.2: This package is deprecated. Use the optional chaining (?.) operator instead.

added 137 packages, and audited 138 packages in 3s

23 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice
npm notice New major version of npm available! 10.9.8 -> 12.0.2
npm notice Changelog: https://github.com/npm/cli/releases/tag/v12.0.2
npm notice To update run: npm install -g npm@12.0.2
npm notice
 ---> Removed intermediate container b927307e3963
 ---> 0f8cc2678208
Step 5/7 : COPY . .
 ---> 0328447ff4c9
Step 6/7 : EXPOSE 3000
 ---> Running in f72e3abae852
 ---> Removed intermediate container f72e3abae852
 ---> 2183a254a665
Step 7/7 : CMD ["npm", "start"]
 ---> Running in 223807fdc9ed
 ---> Removed intermediate container 223807fdc9ed
 ---> 44d2dd9bf086
Successfully built 44d2dd9bf086
Successfully tagged school-attendance-app:v1
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                      ID             DISK USAGE   CONTENT SIZE   EXTRA
node:22-alpine             c610fcdfb1d5        232MB         58.1MB
school-attendance-app:v1   44d2dd9bf086        300MB         69.4MB
ubuntu@ip-172-31-7-36:~/school-attendance-app$
```
### "Follow Docker best practices by using multi-stage builds where applicable, running containers with a non-root user, and minimizing image size through lightweight base images like Alpine or Slim

```bash
 
 # Stage 1 - Install production dependencies
   2   │ FROM node:22-alpine AS deps
   3   │
   4   │ WORKDIR /app
   5   │
   6   │ COPY package*.json ./
   7   │
   8   │ RUN npm ci --omit=dev && npm cache clean --force
   9   │
  10   │
  11   │ # Stage 2 - Production image
  12   │ FROM node:22-alpine AS runner
  13   │
  14   │ WORKDIR /app
  15   │
  16   │ COPY --from=deps /app/node_modules ./node_modules
  17   │
  18   │ COPY server.js seedAdmin.js ./
  19   │ COPY config ./config
  20   │ COPY controllers ./controllers
  21   │ COPY middleware ./middleware
  22   │ COPY models ./models
  23   │ COPY routes ./routes
  24   │ COPY frontend ./frontend
  25   │
  26   │ # Create non-root user
  27   │ RUN addgroup -S appgroup && adduser -S appuser -G appgroup \
  28   │     && chown -R appuser:appgroup /app
  29   │
  30   │ USER appuser
  31   │
  32   │ EXPOSE 3000
  33   │
  34   │ CMD ["node", "server.js"]


```

```bash

Multi Stage Dockerfile for backend

ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                      ID             DISK USAGE   CONTENT SIZE   EXTRA
node:22-alpine             c610fcdfb1d5        232MB         58.1MB
school-attendance-app:v1   44d2dd9bf086        300MB         69.4MB
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker build --no-cache -t school-attendance-app .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  132.1kB
Step 1/18 : FROM node:22-alpine AS deps
 ---> c610fcdfb1d5
Step 2/18 : WORKDIR /app
 ---> Running in 77ad8e13a81d
 ---> Removed intermediate container 77ad8e13a81d
 ---> af6ad13f8ff7
Step 3/18 : COPY package*.json ./
 ---> 4cf45debc4e7
Step 4/18 : RUN npm ci --omit=dev && npm cache clean --force
 ---> Running in 926adf41e5cc
npm warn deprecated lodash.get@4.4.2: This package is deprecated. Use the optional chaining (?.) operator instead.

added 110 packages, and audited 111 packages in 3s

18 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
npm notice
npm notice New major version of npm available! 10.9.8 -> 12.0.2
npm notice Changelog: https://github.com/npm/cli/releases/tag/v12.0.2
npm notice To update run: npm install -g npm@12.0.2
npm notice
npm warn using --force Recommended protections disabled.
 ---> Removed intermediate container 926adf41e5cc
 ---> b3d78825a955
Step 5/18 : FROM node:22-alpine AS runner
 ---> c610fcdfb1d5
Step 6/18 : WORKDIR /app
 ---> Running in 52b5168054ea
 ---> Removed intermediate container 52b5168054ea
 ---> a2bfc32c6c62
Step 7/18 : COPY --from=deps /app/node_modules ./node_modules
 ---> 4f30678320ee
Step 8/18 : COPY server.js seedAdmin.js ./
 ---> 2b963d726fa7
Step 9/18 : COPY config ./config
 ---> 6fa6a1bb240c
Step 10/18 : COPY controllers ./controllers
 ---> f806aa922ec3
Step 11/18 : COPY middleware ./middleware
 ---> bd9bece1202f
Step 12/18 : COPY models ./models
 ---> 3b8528156645
Step 13/18 : COPY routes ./routes
 ---> 531cd2e17f19
Step 14/18 : COPY frontend ./frontend
 ---> d25ac80b5af2
Step 15/18 : RUN addgroup -S appgroup && adduser -S appuser -G appgroup     && chown -R appuser:appgroup /app
 ---> Running in c59b4bbfdaf5
 ---> Removed intermediate container c59b4bbfdaf5
 ---> b6f5ad51fe07
Step 16/18 : USER appuser
 ---> Running in 2883c63126a1
 ---> Removed intermediate container 2883c63126a1
 ---> 98f439aeafac
Step 17/18 : EXPOSE 3000
 ---> Running in 72cf5098131b
 ---> Removed intermediate container 72cf5098131b
 ---> a1589ac6f57d
Step 18/18 : CMD ["node", "server.js"]
 ---> Running in 685bed026660
 ---> Removed intermediate container 685bed026660
 ---> 8b59882a086b
Successfully built 8b59882a086b
Successfully tagged school-attendance-app:latest
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                          ID             DISK USAGE   CONTENT SIZE   EXTRA
node:22-alpine                 c610fcdfb1d5        232MB         58.1MB
school-attendance-app:latest   8b59882a086b        280MB         64.4MB
school-attendance-app:v1       44d2dd9bf086        300MB         69.4MB
ubuntu@ip-172-31-7-36:~/school-attendance-app$


ubuntu@ip-172-31-7-36:~/school-attendance-app$
```

### Added a .dockerignore file

```bash

ubuntu@ip-172-31-7-36:~/school-attendance-app$ cat .dockerignore
node_modules
.git
.gitignore
Dockerfile
README.md
.env
npm-debug.log
*.log

ubuntu@ip-172-31-7-36:~/school-attendance-app$
```

Builded and tested it locally.

Docker image for Frontend
```bash
      │ File: Dockerfile
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ FROM nginx:1.25-alpine
   2   │
   3   │ COPY . /usr/share/nginx/html
   4   │
   5   │ EXPOSE 80
   6   │
   7   │ CMD ["nginx", "-g", "daemon off;"]
   8   │

```

```bash
ubuntu@ip-172-31-7-36:~/school-attendance-app/frontend$ vim Dockerfile
ubuntu@ip-172-31-7-36:~/school-attendance-app/frontend$ docker build -t school-attendance-app-fe .
DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  31.74kB
Step 1/4 : FROM nginx:1.25-alpine
1.25-alpine: Pulling from library/nginx
0d0c16747d2c: Pulling fs layer
4abcf2066143: Pulling fs layer
fc21a1d387f5: Pulling fs layer
e6ef242c1570: Pulling fs layer
13fcfbc94648: Pulling fs layer
d4bca490e609: Pulling fs layer
5406ed7b06d9: Pulling fs layer
8a3742a9529d: Pulling fs layer
8a3742a9529d: Download complete
e6ef242c1570: Download complete
13fcfbc94648: Download complete
d4bca490e609: Download complete
5406ed7b06d9: Download complete
4abcf2066143: Download complete
fc21a1d387f5: Download complete
0d0c16747d2c: Download complete
fc44f5562637: Download complete
30d1be61be14: Download complete
4abcf2066143: Pull complete
fc21a1d387f5: Pull complete
e6ef242c1570: Pull complete
13fcfbc94648: Pull complete
d4bca490e609: Pull complete
8a3742a9529d: Pull complete
5406ed7b06d9: Pull complete
0d0c16747d2c: Pull complete
Digest: sha256:516475cc129da42866742567714ddc681e5eed7b9ee0b9e9c015e464b4221a00
Status: Downloaded newer image for nginx:1.25-alpine
 ---> 516475cc129d
Step 2/4 : COPY . /usr/share/nginx/html
 ---> f2db3134125b
Step 3/4 : EXPOSE 80
 ---> Running in 816c6b5aac3d
 ---> Removed intermediate container 816c6b5aac3d
 ---> 082f97641aca
Step 4/4 : CMD ["nginx", "-g", "daemon off;"]
 ---> Running in 5ce9dbf9605e
 ---> Removed intermediate container 5ce9dbf9605e
 ---> b7dcdb213c8a
Successfully built b7dcdb213c8a
Successfully tagged school-attendance-app-fe:latest
ubuntu@ip-172-31-7-36:~/school-attendance-app/frontend$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                             ID             DISK USAGE   CONTENT SIZE   EXTRA
nginx:1.25-alpine                 516475cc129d       75.4MB         21.7MB
node:20-alpine                    fb4cd12c85ee        194MB         48.8MB
node:22-alpine                    c610fcdfb1d5        232MB         58.1MB
school-attendance-app-fe:latest   b7dcdb213c8a       74.2MB         20.5MB
school-attendance-app:latest      8b59882a086b        280MB         64.4MB
school-attendance-app:v1          44d2dd9bf086        300MB         69.4MB
ubuntu@ip-172-31-7-36:~/school-attendance-app/frontend$
```


## Task 3: Add Docker Compose

### Configured Docker Compose with an app service (built from a Dockerfile), a database service, persistent database volumes, a custom network, environment variables from a .env file, and database health checks

```bash

   │ File: docker-compose.yml
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ version: "3.8"
   2   │ services:
   3   │   frontend:
   4   │     build: ./frontend
   5   │     container_name: school-attendance-frontend
   6   │     ports:
   7   │       - "80:80"
   8   │     depends_on:
   9   │       - backend
  10   │     restart: unless-stopped
  11   │
  12   │   backend:
  13   │     build:
  14   │       context: .
  15   │     container_name: school-attendance-backend
  16   │     ports:
  17   │       - "5000:5000"
  18   │     env_file:
  19   │       - .env
  20   │     command: sh -c "node seedAdmin.js && node server.js"
  21   │     depends_on:
  22   │       - mongo
  23   │     restart: unless-stopped
  24   │
  25   │   mongo:
  26   │     image: mongo:7
  27   │     container_name: attendance-mangodb
  28   │     ports:
  29   │       - "27017:27017"
  30   │     volumes:
  31   │       - mongo-data:/data/db
  32   │     restart: unless-stopped
  33   │
  34   │ volumes:
  35   │   mongo-data:

```

```bash
ubuntu@ip-172-31-7-36:~/school-attendance-app/frontend/js$ docker compose up -d
WARN[0000] /home/ubuntu/school-attendance-app/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 11/11
 ✔ mongo Pulled                                                                                                                             14.4s
   ✔ 15a369849dbf Pull complete                                                                                                              0.3s
   ✔ 39a945af8df2 Pull complete                                                                                                              5.1s
   ✔ 9edaf47f313b Pull complete                                                                                                              0.3s
   ✔ bb94a6d99c0a Pull complete                                                                                                              5.3s
   ✔ 8932aff54341 Pull complete                                                                                                              5.3s
   ✔ 4b0954780896 Pull complete                                                                                                              0.4s
   ✔ dff0d25b5d25 Pull complete                                                                                                              0.4s
   ✔ 9fc668cfba29 Pull complete                                                                                                             13.3s
   ✔ 0da68b64f60d Download complete                                                                                                          0.0s
   ✔ b3c1ad30c086 Download complete                                                                                                          0.1s
WARN[0014] Docker Compose is configured to build using Bake, but buildx isn't installed
[+] Building 12.8s (27/27) FINISHED                                                                                                docker:default
 => [backend internal] load build definition from Dockerfile                                                                                 0.0s
 => => transferring dockerfile: 686B                                                                                                         0.0s
 => [backend internal] load metadata for docker.io/library/node:22-alpine                                                                    0.6s
 => [backend internal] load .dockerignore                                                                                                    0.0s
 => => transferring context: 116B                                                                                                            0.0s
 => [backend internal] load build context                                                                                                    0.0s
 => => transferring context: 14.81kB                                                                                                         0.0s
 => [backend deps 1/4] FROM docker.io/library/node:22-alpine@sha256:c610fcdfb1d5b4740dd70c284ed3cb16bb857e0f7166196e36a5501df7a3aa32         0.0s
 => => resolve docker.io/library/node:22-alpine@sha256:c610fcdfb1d5b4740dd70c284ed3cb16bb857e0f7166196e36a5501df7a3aa32                      0.0s
 => CACHED [backend deps 2/4] WORKDIR /app                                                                                                   0.0s
 => CACHED [backend deps 3/4] COPY package*.json ./                                                                                          0.0s
 => CACHED [backend deps 4/4] RUN npm ci --omit=dev && npm cache clean --force                                                               0.0s
 => CACHED [backend runner  3/11] COPY --from=deps /app/node_modules ./node_modules                                                          0.0s
 => CACHED [backend runner  4/11] COPY server.js seedAdmin.js createUser.js ./                                                               0.0s
 => CACHED [backend runner  5/11] COPY config ./config                                                                                       0.0s
 => CACHED [backend runner  6/11] COPY controllers ./controllers                                                                             0.0s
 => CACHED [backend runner  7/11] COPY middleware ./middleware                                                                               0.0s
 => CACHED [backend runner  8/11] COPY models ./models                                                                                       0.0s
 => CACHED [backend runner  9/11] COPY routes ./routes                                                                                       0.0s
 => [backend runner 10/11] COPY frontend ./frontend                                                                                          0.1s
 => [backend runner 11/11] RUN addgroup -S appgroup && adduser -S appuser -G appgroup     && chown -R appuser:appgroup /app                  8.6s
 => [backend] exporting to image                                                                                                             2.0s
 => => exporting layers                                                                                                                      1.0s
 => => exporting manifest sha256:22b548af71a24dd188976ffb6f58ee53ce25a1a68310dd51faed64c4c160f441                                            0.0s
 => => exporting config sha256:fbb31b25f2559f40561bfbe4c7e6842e5a5eaf2087c4dd8c73689a4daaebd1bd                                              0.0s
 => => exporting attestation manifest sha256:3683220173bde2e13bf15b159c5de91bae31d4055c8edde5eac77432e9d6b946                                0.0s
 => => exporting manifest list sha256:d9879542a347828e087092abea9e36ba117afe563125c58688b7cca0026a7372                                       0.0s
 => => naming to docker.io/library/school-attendance-app-backend:latest                                                                      0.0s
 => => unpacking to docker.io/library/school-attendance-app-backend:latest                                                                   0.8s
 => [backend] resolving provenance for metadata file                                                                                         0.0s
 => [frontend internal] load build definition from Dockerfile                                                                                0.0s
 => => transferring dockerfile: 138B                                                                                                         0.0s
 => [frontend internal] load metadata for docker.io/library/nginx:1.25-alpine                                                                0.3s
 => [frontend internal] load .dockerignore                                                                                                   0.0s
 => => transferring context: 2B                                                                                                              0.0s
 => [frontend internal] load build context                                                                                                   0.0s
 => => transferring context: 13.70kB                                                                                                         0.0s
 => CACHED [frontend 1/2] FROM docker.io/library/nginx:1.25-alpine@sha256:516475cc129da42866742567714ddc681e5eed7b9ee0b9e9c015e464b4221a00   0.0s
 => => resolve docker.io/library/nginx:1.25-alpine@sha256:516475cc129da42866742567714ddc681e5eed7b9ee0b9e9c015e464b4221a00                   0.0s
 => [frontend 2/2] COPY . /usr/share/nginx/html                                                                                              0.1s
 => [frontend] exporting to image                                                                                                            0.3s
 => => exporting layers                                                                                                                      0.1s
 => => exporting manifest sha256:caec2a03e0c08a68893d793658f6c84b4f33aed9453d28f9592d099da9744dab                                            0.0s
 => => exporting config sha256:91862896c486343d78895d7304bf674ad34d30c7c4f2630ff57790196fa27100                                              0.0s
 => => exporting attestation manifest sha256:53436b7e6d8b5f38942431225f64e9a18305d74952404277b4486e4c4dc952a7                                0.0s
 => => exporting manifest list sha256:147fa44818601f0d9b402b3f3eb79aa442224bc0a1618337db51375429f25aea                                       0.0s
 => => naming to docker.io/library/school-attendance-app-frontend:latest                                                                     0.0s
 => => unpacking to docker.io/library/school-attendance-app-frontend:latest                                                                  0.0s
 => [frontend] resolving provenance for metadata file                                                                                        0.0s
[+] Running 6/6
 ✔ backend                                Built                                                                                              0.0s
 ✔ frontend                               Built                                                                                              0.0s
 ✔ Network school-attendance-app_default  Created                                                                                            0.1s
 ✔ Container attendance-mangodb           Started                                                                                            0.5s
 ✔ Container school-attendance-backend    Started                                                                                            0.7s
 ✔ Container school-attendance-frontend   Started                                                                                            1.0s

ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED          STATUS          PORTS                                                   NAMES
eb13d50e4235   school-attendance-app-frontend   "/docker-entrypoint.…"   56 minutes ago   Up 56 minutes   0.0.0.0:80->80/tcp, [::]:80->80/tcp                     school-attendance-frontend
0077813c0368   school-attendance-app-backend    "docker-entrypoint.s…"   56 minutes ago   Up 56 minutes   3000/tcp, 0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp   school-attendance-backend
c2302d551ad1   mongo:7                          "docker-entrypoint.s…"   56 minutes ago   Up 56 minutes   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp         attendance-mangodb
ubuntu@ip-172-31-7-36:~/school-attendance-app$

```

# 🎥 Demo Video

Click the image below to watch the demo.

[![Watch Demo](https://img.shields.io/badge/▶-Watch%20Demo-red?style=for-the-badge)](https://github.com/user-attachments/assets/3e452e8a-b382-418f-a02b-8e36024da481)

---

## Task 4: Ship It

### Tagged the application image, pushed it to Docker Hub, shared the Docker Hub repository link, and created a README.md containing the application overview, Docker Compose setup instructions, and the required environment variables.

```bash

ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                                   ID             DISK USAGE   CONTENT SIZE   EXTRA
mongo:7                                 35a5926f71f8       1.19GB          297MB    U
school-attendance-app-backend:latest    d9879542a347        280MB         64.4MB    U
school-attendance-app-frontend:latest   147fa4481860       74.2MB         20.5MB    U
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker tag school-attendance-app-backend:latest sufiyn/school-attendance-app-backend:latest
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker tag school-attendance-app-frontend:latest sufiyn/school-attendance-app-frontend:latest
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                                          ID             DISK USAGE   CONTENT SIZE   EXTRA
mongo:7                                        35a5926f71f8       1.19GB          297MB    U
school-attendance-app-backend:latest           d9879542a347        280MB         64.4MB    U
school-attendance-app-frontend:latest          147fa4481860       74.2MB         20.5MB    U
sufiyn/school-attendance-app-backend:latest    d9879542a347        280MB         64.4MB    U
sufiyn/school-attendance-app-frontend:latest   147fa4481860       74.2MB         20.5MB    U
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker push sufiyn/school-attendance-app-backend:latest
The push refers to repository [docker.io/sufiyn/school-attendance-app-backend]
842c3fc9f2c5: Pushed
efbef6f9e333: Mounted from library/node
beb9729922f5: Pushed
be16ae6b4cd2: Pushed
6951cbbb7e11: Pushed
16da5a640377: Mounted from library/node
c64ba2efec29: Pushed
161e4946a9d0: Pushed
ee17a423126a: Pushed
6562fb7cefa4: Pushed
763c9d5d2a6a: Pushed
55afa1ecc21d: Mounted from library/node
be0192bfee09: Pushed
a2980c1fee17: Mounted from library/node
b6fce451218a: Pushed
latest: digest: sha256:d9879542a347828e087092abea9e36ba117afe563125c58688b7cca0026a7372 size: 856
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker push sufiyn/school-attendance-app-frontend:latest
The push refers to repository [docker.io/sufiyn/school-attendance-app-frontend]
897c40d76d77: Pushed
fc21a1d387f5: Mounted from library/nginx
5406ed7b06d9: Mounted from library/nginx
b57cadcf5a05: Pushed
4abcf2066143: Mounted from library/nginx
e6ef242c1570: Mounted from library/nginx
13fcfbc94648: Mounted from library/nginx
d4bca490e609: Mounted from library/nginx
8a3742a9529d: Mounted from library/nginx
0d0c16747d2c: Mounted from library/nginx
latest: digest: sha256:147fa44818601f0d9b402b3f3eb79aa442224bc0a1618337db51375429f25aea size: 856
ubuntu@ip-172-31-7-36:~/school-attendance-app$
```
## Docker Hub Repository

You can find all the Docker images for this project here:

[Docker Hub Repositories](https://hub.docker.com/repositories/sufiyn)

## 📄 DevOps Documentation

The complete Docker, Docker Compose, and DevOps implementation is available in the **DevOps** branch.

👉 **View the DevOps README**

[![DevOps Documentation](https://img.shields.io/badge/📖-Open%20DevOps%20README-blue?style=for-the-badge)](https://github.com/sk7652183-rgb/school-attendance-app/blob/DevOps/README.md)


Or browse the **devops** branch directly:

🔗 https://github.com/sk7652183-rgb/school-attendance-app/tree/DevOps

## Test the Whole Flow

### Remove all local images and containers

```bash

ubuntu@ip-172-31-7-36:~$
ubuntu@ip-172-31-7-36:~$
ubuntu@ip-172-31-7-36:~$ docker ps
CONTAINER ID   IMAGE                            COMMAND                  CREATED       STATUS       PORTS                                                   NAMES
eb13d50e4235   school-attendance-app-frontend   "/docker-entrypoint.…"   3 hours ago   Up 3 hours   0.0.0.0:80->80/tcp, [::]:80->80/tcp                     school-attendance-frontend
0077813c0368   school-attendance-app-backend    "docker-entrypoint.s…"   3 hours ago   Up 3 hours   3000/tcp, 0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp   school-attendance-backend
c2302d551ad1   mongo:7                          "docker-entrypoint.s…"   3 hours ago   Up 3 hours   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp         attendance-mangodb
ubuntu@ip-172-31-7-36:~$
ubuntu@ip-172-31-7-36:~$
ubuntu@ip-172-31-7-36:~$ ls
school-attendance-app
ubuntu@ip-172-31-7-36:~$ cd school-attendance-app
ubuntu@ip-172-31-7-36:~/school-attendance-app$ ls
Dockerfile  controllers         frontend    node_modules       routes        {config,models,middleware,controllers,routes,utils}
README.md   createUser.js       middleware  package-lock.json  seedAdmin.js
config      docker-compose.yml  models      package.json       server.js
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker compose down
WARN[0000] /home/ubuntu/school-attendance-app/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 4/4
 ✔ Container school-attendance-frontend   Removed                                                                                            0.5s
 ✔ Container school-attendance-backend    Removed                                                                                           10.2s
 ✔ Container attendance-mangodb           Removed                                                                                            0.3s
 ✔ Network school-attendance-app_default  Removed                                                                                            0.1s
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE                                          ID             DISK USAGE   CONTENT SIZE   EXTRA
mongo:7                                        35a5926f71f8       1.19GB          297MB
school-attendance-app-backend:latest           d9879542a347        280MB         64.4MB
school-attendance-app-frontend:latest          147fa4481860       74.2MB         20.5MB
sufiyn/school-attendance-app-backend:latest    d9879542a347        280MB         64.4MB
sufiyn/school-attendance-app-frontend:latest   147fa4481860       74.2MB         20.5MB
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker rmi mongo:7 \
school-attendance-app-backend:latest \
school-attendance-app-frontend:latest \
sufiyn/school-attendance-app-backend:latest \
sufiyn/school-attendance-app-frontend:latest
Untagged: mongo:7
Deleted: sha256:35a5926f71f8b6cb19206bee928c5a85f241a8be99f20c81abe35ae78a73415d
Untagged: school-attendance-app-backend:latest
Untagged: school-attendance-app-frontend:latest
Untagged: sufiyn/school-attendance-app-backend:latest
Deleted: sha256:d9879542a347828e087092abea9e36ba117afe563125c58688b7cca0026a7372
Untagged: sufiyn/school-attendance-app-frontend:latest
Deleted: sha256:147fa44818601f0d9b402b3f3eb79aa442224bc0a1618337db51375429f25aea
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker images
                                                                                                                              i Info →   U  In Use
IMAGE   ID             DISK USAGE   CONTENT SIZE   EXTRA
```

### Pull from Docker Hub and run using only your compose file
```bash
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker compose up -d
WARN[0000] /home/ubuntu/school-attendance-app/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion
[+] Running 26/26
 ✔ frontend Pulled                                                                                                                           1.8s
   ✔ 897c40d76d77 Pull complete                                                                                                              0.1s
   ✔ b57cadcf5a05 Download complete                                                                                                          0.1s
 ✔ backend Pulled                                                                                                                            3.7s
   ✔ 763c9d5d2a6a Pull complete                                                                                                              2.5s
   ✔ b6fce451218a Pull complete                                                                                                              0.1s
   ✔ c64ba2efec29 Pull complete                                                                                                              0.9s
   ✔ 161e4946a9d0 Pull complete                                                                                                              0.1s
   ✔ 842c3fc9f2c5 Pull complete                                                                                                              1.2s
   ✔ ee17a423126a Pull complete                                                                                                              0.1s
   ✔ beb9729922f5 Pull complete                                                                                                              0.1s
   ✔ be16ae6b4cd2 Pull complete                                                                                                              1.3s
   ✔ be0192bfee09 Pull complete                                                                                                              1.4s
   ✔ 6951cbbb7e11 Pull complete                                                                                                              0.1s
   ✔ 6562fb7cefa4 Download complete                                                                                                          0.0s
 ✔ mongo Pulled                                                                                                                             15.6s
   ✔ 15a369849dbf Pull complete                                                                                                             14.4s
   ✔ 39a945af8df2 Pull complete                                                                                                              5.9s
   ✔ 9edaf47f313b Pull complete                                                                                                              6.0s
   ✔ bb94a6d99c0a Pull complete                                                                                                              6.1s
   ✔ 8932aff54341 Pull complete                                                                                                              6.3s
   ✔ 4b0954780896 Pull complete                                                                                                              0.3s
   ✔ dff0d25b5d25 Pull complete                                                                                                              0.3s
   ✔ 9fc668cfba29 Pull complete                                                                                                             14.3s
   ✔ b3c1ad30c086 Download complete                                                                                                          0.1s
   ✔ 0da68b64f60d Download complete                                                                                                          0.0s
[+] Running 4/4
 ✔ Network school-attendance-app_default  Created                                                                                            0.1s
 ✔ Container attendance-mongodb           Started                                                                                            0.7s
 ✔ Container school-attendance-backend    Started                                                                                            0.8s
 ✔ Container school-attendance-frontend   Started                                                                                            1.0s
ubuntu@ip-172-31-7-36:~/school-attendance-app$ docker ps
CONTAINER ID   IMAGE                                   COMMAND                  CREATED         STATUS         PORTS                                                   NAMES
5251986db833   sufiyn/school-attendance-app-frontend   "/docker-entrypoint.…"   5 seconds ago   Up 5 seconds   0.0.0.0:80->80/tcp, [::]:80->80/tcp                     school-attendance-frontend
ca862a1176b5   sufiyn/school-attendance-app-backend    "docker-entrypoint.s…"   5 seconds ago   Up 5 seconds   3000/tcp, 0.0.0.0:5000->5000/tcp, [::]:5000->5000/tcp   school-attendance-backend
9e2e3583c111   mongo:7                                 "docker-entrypoint.s…"   6 seconds ago   Up 5 seconds   0.0.0.0:27017->27017/tcp, [::]:27017->27017/tcp         attendance-mongodb
ubuntu@ip-172-31-7-36:~/school-attendance-app$
```

### Does it work fresh? If not — fix it until it does

Yes, it is working fine. The images were successfully pulled from Docker Hub after updating the Docker Compose file to use the images from the registry.
