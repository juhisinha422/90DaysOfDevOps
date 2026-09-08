# Day 33 – Docker Compose: Multi-Container Basics

## Task
Run multi-container applications with a single command.

Yesterday, networks and volumes were created manually and containers run one by one. Docker Compose does all of that from a single YAML file.

---

## Task 1: Install & Verify

Checked Docker Compose availability and version:
```
docker compose version
```
**Output:** <img width="465" height="78" alt="image" src="https://github.com/user-attachments/assets/eea5b142-4c14-4208-91ff-fd2735a9ad81" />


---

## Task 2: First Compose File

1. Create folder `compose-basics`
2. Write a `docker-compose.yml` running a single Nginx container with port mapping
3. Start with `docker compose up`
4. Access via browser
5. Stop with `docker compose down`

**`docker-compose.yml`:**
```yaml
services:
  nginx:
    image: nginx
    ports:
      - "8080:80"
```
<img width="1840" height="673" alt="image" src="https://github.com/user-attachments/assets/c3b57976-a3de-4bab-a9ab-a7a28acb2758" />
<img width="1591" height="607" alt="image" src="https://github.com/user-attachments/assets/d41a3f7e-c780-43ab-b6bf-d6ca884160c5" />
<img width="755" height="107" alt="image" src="https://github.com/user-attachments/assets/8445cca0-1b4b-43c3-a37a-6d2bef0719b1" />

---

## Task 3: Two-Container Setup (WordPress + MySQL)

Compose file running:
- A **WordPress** container
- A **MySQL** container

Both on the same auto-created network, MySQL with a named volume for persistence, WordPress connecting to MySQL via service name.

**`docker-compose.yml`:**
```yaml
version: "3.8"
services:
  db:
    image: mysql:5.7
    container_name: mysql-db
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: rootpass
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wpuser
      MYSQL_PASSWORD: wppass
    volumes:
      - db_data:/var/lib/mysql

  wordpress:
    image: wordpress:latest
    container_name: wordpress-app
    restart: always
    ports:
      - "8081:80"
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_USER: wpuser
      WORDPRESS_DB_PASSWORD: wppass
      WORDPRESS_DB_NAME: wordpress
    depends_on:
      - db

volumes:
  db_data:
```
<img width="882" height="569" alt="image" src="https://github.com/user-attachments/assets/8086f1be-a14d-4598-9a3e-2baa9a886d81" />

<img width="1850" height="701" alt="image" src="https://github.com/user-attachments/assets/cd678bb7-2c8a-4b4e-b7bd-df7b38d6005e" />



**Verified persistence:** stopped and restarted with `docker compose down` + `docker compose up` — WordPress data was still there.

<img width="1849" height="617" alt="image" src="https://github.com/user-attachments/assets/cae58e32-899c-46c9-b3d9-01e6a095fa9d" />


---

## Task 4: Compose Commands

| Action | Command |
|---|---|
| Start in detached mode | `docker compose up -d` |
| View running services | `docker compose ps` |
| View logs (all services) | `docker compose logs -f` |
| View logs (specific service) | `docker compose logs -f wordpress` |
| Stop without removing | `docker compose stop` |
| Remove everything (containers, networks) | `docker compose down` |
| Rebuild after a change | `docker compose up -d --build` |

---

## Task 5: Environment Variables

1. Added environment variables directly in `docker-compose.yml`
2. Created a `.env` file, referenced its variables from the compose file
3. Verified the variables were picked up correctly
   <img width="690" height="148" alt="image" src="https://github.com/user-attachments/assets/e331c5fe-f4d1-419a-a314-26c8e68f6809" />
   <img width="943" height="615" alt="image" src="https://github.com/user-attachments/assets/f13561a3-6e49-41e5-81bf-a39a3204d4e4" />
   <img width="978" height="941" alt="image" src="https://github.com/user-attachments/assets/e676c157-896d-4713-9b53-43dfa0691e99" />
