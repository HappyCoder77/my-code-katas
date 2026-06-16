[← Back to Docker Dojo](./README.md)
# 🥋 Basic Docker Container

This kata focuses on create a new Django project using Docker and adding PostgreSQL in a separate container as a database. The goal is to mechanize the creation of a PostgreSQL container.

---

## 🎯 The Objective
Add a Postegrsql container to a basic Django project.

---

## 🛠️ My Tech Stack for this Kata
| Tool / Library | Purpose |
| :--- | :--- |
| **Python** | Current stable runtime environment for running Django backend services. |
| **Django** | Web Framework (LTS focus) |
| **pipenv** | Dependencies and virtual environments management. |
| **Docker** | Containerization. |
| **Docker** | Database. |
  
---

## 🛠️ Phase 1: Create a basic Django project with Docker
See the kata [← Django Docker](./basic-django-docker.md)


## 🐳️ Phase 2: Dockerize the project
Create the container files for the project.

1. **Create the Dockerfile:**
   ```bash
   touch Dockerfile
   ```

2. **Add the Dockerfile content:**
   ```dockerfile
    FROM python:3.12
    
    ENV PYTHONDONTWRITEBYTECODE=1
    ENV PYTHONUNBUFFERED=1
    
    WORKDIR /code
    
    COPY Pipfile Pipfile.lock /code/
    RUN pip install pipenv && pipenv install --system
    
    COPY . /code/
   ```
3. **Run command to create the container image:**
    ```bash
    docker build .
    ```

## 🚀️ Phase 3: Orchestration
1. **Create a Docker Compose file:**
   ```bash
   touch docker-compose.yml
   ```
   
2. **Add docker compose file content:**
   ```yaml
    services:
      web:
        build: .
        command: python /code/manage.py runserver 0.0.0.0:8000
        volumes:
          - .:/code
        ports:
          - "8000:8000"

    ```

3. **Run the container:**
   ```bash
    docker compose up
   ```
4. **Go to localhost in the browser:**
   Open [http://localhost:8000](http://localhost:8000) or [http://127.0.0.1:8000](http://127.0.0.1:8000)

   
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
