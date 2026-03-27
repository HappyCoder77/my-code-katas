
# 🥋 Kata 01: Professional Django-Docker Setup

This kata focuses on building a professional-grade development environment. The goal is to mechanize the creation of a secure, containerized Django project using best practices like non-root users and database health checks.

---

## 🎯 The Objective
Set up a Django project from scratch using **Docker Compose**, **PostgreSQL**, and **Pipenv**, without installing any local dependencies on your host machine.

---

## 🛠️ Phase 1: Structure & Dependencies
Prepare the workspace and define the environment's requirements.

1. **Initial Workspace:**
   ```bash
   mkdir django-kata && cd django-kata
   touch Dockerfile .dockerignore docker-compose.yml .env Pipfile
   ```
    Pipfile (Core Stack):
    Ini, TOML

    [[source]]
    url = "[https://pypi.org/simple](https://pypi.org/simple)"
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

## ✅ Mastery Checklist

   - [ ] Non-root User? Confirmed via USER djangouser in Dockerfile.

   - [ ] Healthcheck? The web service correctly waits for db readiness.

   - [ ] Env Escaping? Used $$ for special characters in Docker Compose.

   - [ ] Data Persistence? Postgres volume is correctly mapped.

   - [ ] Build Time? Under 20 minutes?
