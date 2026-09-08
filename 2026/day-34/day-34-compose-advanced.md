# Day 34 – Docker Compose: Real-World Multi-Container Apps

## Task
Build more complex, production-like setups with Docker Compose. Yesterday was basics — today: app + database + cache, healthchecks, restart policies, and service dependencies.

---

## Task 1: Build a 3-Service App Stack

- **Web app** — Python Flask
- **Database** — Postgres
- **Cache** — Redis
<img width="765" height="131" alt="image" src="https://github.com/user-attachments/assets/9543f8a0-3455-4a8d-b52d-c4f62a401366" />

<img width="872" height="373" alt="image" src="https://github.com/user-attachments/assets/2fcbccbb-4ccc-4841-a99f-c6c7f71df194" />

<img width="1349" height="1008" alt="image" src="https://github.com/user-attachments/assets/d686b4e9-0dac-486c-b8c0-921ac9388617" />

<img width="1617" height="308" alt="image" src="https://github.com/user-attachments/assets/0cef73d4-7fc6-4017-b40e-9baabefe41ca" />


**`docker-compose.yml`:**
```yaml
services:
  web:
    build: ./app
    ports:
      - "5000"
    depends_on:
      db:
        condition: service_healthy
      cache:
        condition: service_started
    networks:
      - frontend
      - backend
    labels:
      com.example.service: "web"
      com.example.tier: "app"

  db:
    image: postgres:16
    restart: always
    environment:
      POSTGRES_DB: appdb
      POSTGRES_USER: appuser
      POSTGRES_PASSWORD: apppass
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U appuser -d appdb"]
      interval: 5s
      timeout: 5s
      retries: 5
    volumes:
      - db_data:/var/lib/postgresql/data
    networks:
      - backend
    labels:
      com.example.service: "db"
      com.example.tier: "data"

  cache:
    image: redis:7
    networks:
      - backend
    labels:
      com.example.service: "cache"
      com.example.tier: "data"

networks:
  frontend:
  backend:

volumes:
  db_data:

```

**`Dockerfile`:**
```dockerfile
FROM python:3.9-slim
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
CMD ["python", "app.py"]
```

**`app.py`:**
```import os, time
from flask import Flask
import psycopg2
import redis

app = Flask(__name__)

def get_db():
    return psycopg2.connect(
        host="db", dbname="appdb", user="appuser", password="apppass"
    )

r = redis.Redis(host="cache", port=6379, decode_responses=True)

@app.route("/")
def hello():
    conn = get_db()
    cur = conn.cursor()
    cur.execute("SELECT version();")
    db_version = cur.fetchone()[0]
    conn.close()

    hits = r.incr("hits")
    return f"Hello! DB says: {db_version[:30]}... | Redis hits: {hits}"

if __name__ == "__main__":
    app.run(host="0.0.0.0", port=5000)
```

---

## Task 2: `depends_on` & Healthchecks

1. Added `depends_on` so the app starts after the database
2. Added a healthcheck on the database service
3. Used `depends_on` with `condition: service_healthy` — app waits for the DB to be truly ready, not just "started"

**Result:** confirmed — the web service now waits until the database passes its healthcheck before starting (see Task 1's compose file for the exact config).
<img width="1689" height="578" alt="image" src="https://github.com/user-attachments/assets/ed9556d4-64c0-44a0-87fc-4a8db75f4563" />

---

## Task 3: Restart Policies

Added `restart: always` to the database service.

**Test:**
```
docker ps
docker kill <db-container-id>
```

**Observation:** the container did *not* auto-restart from a manual kill — restart policies only kick in when the container exits due to an actual error/crash.

**Difference between policies:**
- **`always`** → restarts regardless of exit status, even after a clean/manual stop
- **`on-failure`** → restarts only when the container exits with a non-zero code (an actual crash/error)

---

## Task 4: Custom Dockerfiles in Compose

Used `build:` instead of a pre-built image:
```yaml
build: ./app
```
Made a code change, rebuilt and restarted with a single command (`docker compose up -d --build`).

---

## Task 5: Named Networks & Volumes

Already in place from Task 1:
```yaml
networks:
  app-net:

volumes:
  db_data:
```

**Result:**
- DB data persists across restarts
- Containers communicate with each other by service name

---

## Task 6: Scaling (Bonus)

```
docker compose up --scale web=3
```
<img width="791" height="939" alt="image" src="https://github.com/user-attachments/assets/3e7e98fc-90cf-42d6-9229-31b06dd99134" />
<img width="1574" height="290" alt="image" src="https://github.com/user-attachments/assets/e5d26596-979b-4cc0-ba1a-be03ff9d6536" />


**Problem:** port conflict — `5000` already bound.

**Why scaling fails with static port mapping:** multiple container replicas can't all bind to the same host port simultaneously — a fixed `host:container` port mapping only works for a single instance.

---

## What I Learned
- Multi-container apps using Docker Compose
- `depends_on` + healthcheck ensures proper startup order
- Restart policies manage container failure recovery
- Custom Dockerfiles can be built directly via Compose
- Networks enable service-to-service communication by name
- Volumes persist database data across restarts

## Observations
- App correctly waits for DB readiness thanks to the healthcheck
- DB does *not* auto-restart on a manual kill (only on actual failure, depending on policy)
- Scaling breaks due to static port conflicts
