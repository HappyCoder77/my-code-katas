
# 🥋 Kata 01: Professional Django-Docker Setup

This kata focuses on building a professional-grade development environment. The goal is to mechanize the creation of a secure, containerized Django project using best practices like non-root users and database health checks.

---

## 🎯 The Objective
Set up a Django project from scratch using **Docker Compose**, **PostgreSQL**, and **Pipenv**, without installing any local dependencies on your host machine.

---

## 🛠️ My Tech Stack for this Kata
| Tool / Library | Purpose |
| :--- | :--- |
| **Python** | Core Runtime (Slim image for smaller footprint) |
| **Django** | Web Framework (LTS focus) |
| **PostgreSQL** | Relational Database (Alpine for efficiency) |
| **Pipenv** | Deterministic dependency management | Latest |
| **django-environ** | 12-Factor app env management (Database URLs) |
| **WhiteNoise** | Efficient static file serving for containers |
| **psycopg2-binary** | PostgreSQL adapter for Python |
  
---

## 🛠️ Phase 1: Structure & Dependencies
Prepare the workspace and define the environment's requirements.

1. **Initial Workspace:**
   
   `bash`
   ``` 
   mkdir django-kata && cd django-kata
   touch Dockerfile .dockerignore docker-compose.yml .env Pipfile
   ```
2. **.gitignore** (Git Sanitization):

   ```
   __pycache__/
   *.py[cod]
   .env
   .venv/
   db.sqlite3
   staticfiles/
   postgres_data/  # Muy importante: no versionar la data binaria de la DB
   .vscode/
   ```


3. **.dockerignore** (Context Optimization):
   
   ```
   .git
   .gitignore
   __pycache__/
   *.py[cod]
   .env
   .venv
   env/
   venv/
   ```

4. **Pipfile** (Core Stack):
   
    ```toml
    [[source]]
    url = "https://pypi.org/simple"
    verify_ssl = true
    name = "pypi"

    [packages]
    django = ">=5.0,<6.0"
    django-environ = "*"
    psycopg2-binary = "*"
    whitenoise = "*"

    [dev-packages]
    djlint = "*"
    black = "*"

    [requires]
    python_version = "3.12"
    ```
## 🐳 Phase 2: Professional Dockerization

Build a secure Dockerfile optimized for speed and security.

`Dockerfile`
```
FROM python:3.12-slim

# Prevent .pyc files and enable real-time logging
ENV PYTHONDONTWRITEBYTECODE=1
ENV PYTHONUNBUFFERED=1

WORKDIR /app

# Security: Create and use a non-root user
RUN groupadd -g 1000 djangouser && \
    useradd -m -u 1000 -g djangouser djangouser

# Install Pipenv
RUN pip install --no-cache-dir pipenv

# Dependency Layer (Cached for faster builds)
COPY Pipfile Pipfile.lock /app/
RUN pipenv install --system --deploy

# Copy code and assign ownership to djangouser
COPY --chown=djangouser:djangouser . /app/

USER djangouser

CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"]
```

## 🏗️ Phase 3: Orchestration

Define the services in docker-compose.yml.

`docker-compose.yml`
```
services:
  db:
    image: postgres:16-alpine
    volumes:
      - postgres_data:/var/lib/postgresql/data/
    environment:
      - POSTGRES_DB=postgres
      - POSTGRES_USER=postgres
      - POSTGRES_PASSWORD=postgres_password
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 5s
      timeout: 5s
      retries: 5

  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/app
    ports:
      - "8000:8000"
    environment:
      - DATABASE_URL=postgres://postgres:postgres_password@db:5432/postgres
      - DJANGO_DEBUG=True
      - DJANGO_SECRET_KEY=dev-secret-key-$$change-me$$
    depends_on:
      db:
        condition: service_healthy
    user: "1000:1000"

volumes:
  postgres_data:
```

## 🚀 Phase 4: Execution (The Ignite)

Run the commands to bootstrap the project.

  1. Initialize the Django project:
     ```
     docker compose run --rm web django-admin startproject config .
     ```

  2. Start the environment:
     ```
     docker compose up -d
     ```

  3. Run migrations:
     ```
     docker compose exec web python manage.py migrate
     ```
  4. Git Initialization:
     ```
     git init
     git add .
     git commit -m "feat: initial professional django-docker setup"
     ```

   5. Verify Ownership (Host):
      ```
      sudo chown -R $USER:$USER .
      ```
## ✅ Mastery Checklist

   - [ ] Non-root User? Confirmed via USER djangouser in Dockerfile.

   - [ ] Healthcheck? The web service correctly waits for db readiness.

   - [ ] Env Escaping? Used $$ for special characters in Docker Compose.

   - [ ] Data Persistence? Postgres volume is correctly mapped.

   - [ ] Build Time? Under 20 minutes?
         
   - [ ] Git sanitized?
         
   - [ ] First commit
         
   - [ ] Non-root user? confirmed via whoami


## ⏱️ Training Log

   * Attempt #1: __ min.

   * Target: < 15 minutes.
