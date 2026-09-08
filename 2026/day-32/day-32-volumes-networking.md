# Day 32 – Docker Volumes & Networking

## Task
Solve two real problems: data persistence and container communication.

Containers are ephemeral — they lose data when removed. And by default, containers can't easily talk to each other. Fixing both today.

---

## Task 1: The Problem

1. Run a Postgres or MySQL container
2. Create some data inside it (table, rows — anything)
3. Stop and remove the container
4. Run a new one — is the data still there?

<img width="923" height="1003" alt="image" src="https://github.com/user-attachments/assets/477fb01e-3954-4489-a126-e05b398671ef" />


Created a table and inserted data successfully.

<img width="1208" height="267" alt="image" src="https://github.com/user-attachments/assets/b2ae1a4c-58e1-4cc4-afd2-2e510dfdfc9c" />


After re-creating the same image, all data was lost — the data was never persisted anywhere outside the container itself.

**Why:** Containers are ephemeral — anything stored inside a container's own filesystem is lost the moment the container is removed.

---

## Task 2: Named Volumes

1. Create a named volume
2. Run the same database container, this time with the volume attached
3. Add data, stop and remove the container
4. Run a new container with the same volume
5. Is the data still there?

**Verify with:** `docker volume ls`, `docker volume inspect`

<img width="882" height="406" alt="image" src="https://github.com/user-attachments/assets/f3b2b145-a610-4e51-a4c6-b5a0b7d6a996" />


Data was still there — even after fully re-creating the container — because it was written to the mounted volume, not the container's own filesystem.

<img width="1650" height="504" alt="image" src="https://github.com/user-attachments/assets/439e4e82-5170-491d-ab64-f09d6f6f885d" />

<img width="855" height="226" alt="image" src="https://github.com/user-attachments/assets/fa0d42dd-e265-403d-a5ff-d75cfb2a12b2" />


**Key insight:**
- The **container** thinks it's storing data at `/var/lib/postgresql`
- **Docker** actually stores that data on the host at `/var/lib/docker/volumes/my-db-data/_data`

That host-side location is where the data truly lives — not inside the container.

---

## Task 3: Bind Mounts

1. Create a folder on the host with an `index.html`
2. Run an Nginx container, **bind mount** that folder to the Nginx web directory
3. Access the page in browser
4. Edit `index.html` on the host, refresh browser

<img width="1270" height="95" alt="image" src="https://github.com/user-attachments/assets/5e532835-8b2a-4d6f-a8ca-c9fe9df8ff29" />
<img width="1383" height="289" alt="image" src="https://github.com/user-attachments/assets/387221d8-424b-417c-bea6-167a16111c42" />


Edited `index.html` (added "part 2" at the end), saved — change reflected immediately on refresh.

<img width="1281" height="126" alt="image" src="https://github.com/user-attachments/assets/f9535279-bb39-4b13-8def-a4b3d3a69c1d" />
<img width="1119" height="257" alt="image" src="https://github.com/user-attachments/assets/6f86d9f4-7ce8-4248-a714-7b1eae7f626f" />



**Named volume vs bind mount:**
- **Named volume** → managed entirely by Docker
- **Bind mount** → direct link to a specific host folder

**When to use which:**
- **Bind mount** → local dev work, live editing
- **Named volume** → databases, persistent production storage

---

## Task 4: Docker Networking Basics

1. List all Docker networks
2. Inspect the default `bridge` network
3. Run two containers on the default bridge — can they ping each other by **name**?
4. Can they ping each other by **IP**?
<img width="615" height="97" alt="image" src="https://github.com/user-attachments/assets/d05aa017-ab3d-4aba-88c9-ad8c1bd44eeb" />

<img width="945" height="438" alt="image" src="https://github.com/user-attachments/assets/cc381703-87e0-4bf2-bda9-8509aa1f9a5c" />



```bash
docker run -dit --name c1 ubuntu
docker run -dit --name c2 ubuntu
```

<img width="912" height="170" alt="image" src="https://github.com/user-attachments/assets/61e025c5-e4dd-435f-8fd7-abb6b1a196d5" />

`ping` wasn't installed initially — installed it inside the containers first.

<img width="782" height="934" alt="image" src="https://github.com/user-attachments/assets/62ba8dc7-55a9-4cca-a08b-631270c81776" />


**Result:** pinging by IP worked; pinging by container name did not, on the default bridge network.

---

## Task 5: Custom Networks

1. Create a custom bridge network `my-app-net`
2. Run two containers on it
3. Can they ping each other by name now?

<img width="819" height="264" alt="image" src="https://github.com/user-attachments/assets/6e5010c1-e1bc-4025-8b76-15d1788b33e1" />
<img width="708" height="289" alt="image" src="https://github.com/user-attachments/assets/f3901bb8-82b4-4f29-b27b-c423d3705daf" />
<img width="984" height="336" alt="image" src="https://github.com/user-attachments/assets/edd216a4-58b5-4aab-9290-fe32ed503a67" />


**Result:** name-based ping worked once containers were placed on the custom network.

**Default bridge vs custom network:**
- **Default bridge** → IP-based ping works, name-based doesn't
- **Custom network** → both work

**Why:** custom Docker networks provide built-in DNS resolution between containers; the default bridge network does not.

---

## Task 6: Put It Together

1. Create a custom network
2. Run a database container (MySQL/Postgres) on it, with a volume for data
3. Run an app container on the same network
4. Verify the app container can reach the database by name

<img width="1072" height="967" alt="image" src="https://github.com/user-attachments/assets/de222452-d73f-4816-9ea4-093057963797" />
<img width="925" height="184" alt="image" src="https://github.com/user-attachments/assets/3f133ebe-1e96-4c3f-a2e2-429dc46f4c7c" />
<img width="967" height="284" alt="image" src="https://github.com/user-attachments/assets/71dbde2b-773e-4701-b945-6b439adf5ef2" />
<img width="1382" height="127" alt="image" src="https://github.com/user-attachments/assets/5532f2bc-dbb9-472a-800d-f7b4251f51df" />


Successfully pinged from an Ubuntu container to a Postgres container named `db`, purely by container name.

**Note:** the default bridge network could technically work too, but only by IP — using a custom network is what enables name-based service discovery.
