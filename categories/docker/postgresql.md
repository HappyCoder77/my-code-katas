[← Back to Docker Dojo](./README.md)
# 🥋 Basic Docker Container

This kata focuses on creating a new Django project using Docker and adding PostgreSQL in a separate container as a database. The goal is to mechanize the creation of a PostgreSQL container.

---

## 🎯 The Objective
Add a PostgreSQL container to a basic Django project.

---

## 🛠️ My Tech Stack for this Kata
| Tool / Library | Purpose |
| :--- | :--- |
| **Python** | Current stable runtime environment for running Django backend services. |
| **Django** | Web Framework (LTS focus) |
| **pipenv** | Dependencies and virtual environments management. |
| **Docker** | Containerization. |
| **Postgresql** | Database. |
  
---

## 🛠️ Phase 1: Create a basic Django project with Docker
See the kata [← Basic Django Dockerization](./basic-django-docker.md)
(omit from short reset step onwards)

## 🦸️ Phase 2: Create a superuser in detached mode
Create a superuser for the project.

1. **Run the container in detached mode:**
   ```bash
   docker compose up -d
   ```

2. **Create a superuser in Docker:**
   ```bash
    docker compose exec web python manage.py createsuperuser
   ```
3. **Navigate to localhost and log in**


## 🐘️ Phase 3: Switch to PostgreSQL
1. **Stop the container:**
   ```bash
   docker compose down
   ```
   
2. **Add a new db service to docker-compose.yml and make the web service depend on db:**
   ```yaml
    services:
      web:
        build: .
        command: python manage.py runserver 0.0.0.0:8000
        volumes:
          - .:/code
        ports:
          - 8000:8000
        depends_on: #new
          - db
      db: #new
        image: postgres:16 #new
        environment: #new
          - POSTGRES_HOST_AUTH_METHOD=trust
    ```

3. **Run the container:**
   ```bash
    docker compose up -d
   ```

4. **Update the database config in the settings.py file:**
   ```python
      DATABASES = {
      "default": {
          "ENGINE": "django.db.backends.postgresql",
          "NAME": "postgres",
          "USER": "postgres",
          "PASSWORD": "postgres",
          "HOST": "db",
          "PORT": 5432,
          }
      }

   ```

5. **Install psycopg in the container:**
  ```bash
    docker compose exec web pipenv install psycopg2-binary==2.9.12
  ```

6. **Stop the container:**
  ```bash
    docker compose down
  ```

7. **Force an image rebuild:**
  ```bash
    docker compose up -d --build
  ```
8. **Run migrations**
   ```bash
    docker compose exec web python manage.py migrate
   ```

9. **Create superuser**
   ```bash
    docker compose exec web python manage.py createsuperuser
   ```

10. **Navigate to the admin page and login**

   
## 🛑 Phase 4: The Cool Down
Keep your workspace clean and prepare the environment for the next training session.

### 🔌 1. Cool Down (Stop the current run)
When you finish testing, gracefully stop the containers and remove the networks created by Docker Compose:
```bash
docker compose down
```

## 🔄 2. Short Reset (Repeat Dockerization from the base project)
Use this to wipe the slate clean and practice Phase 2 and 3 again from scratch, keeping your base Django project and Pipfile intact.

1. **Stop containers y remove all images:**
```bash
docker compose down --rmi all
```
2. **Delete only the Docker configuration files:**
```bash
rm Dockerfile docker-compose.yml
```

## 🪓 3. The "Full Wipe" (Optional - Absolute Scratch)
If you want to practice the entire pipeline from the very beginning (including Phase 1: Django Setup from the previous kata)

1. **Purge Docker assets for this project:**
```bash
docker compose down --rmi all -v
```
2. **Remove Pipenv virtual environment and all files:**
```bash
pipenv --rm
cd .. && rm -rf hello
```
---

## 📚 Credits & References
* This kata is based on the architectural patterns and production-ready concepts from the book **[Django for Professionals](https://djangoforprofessionals.com/)** by **William S. Vincent**.
