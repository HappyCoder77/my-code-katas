[← Back to Docker Dojo](./README.md)
# 🥋 Django PostgreSQL Container

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
See the kata [← Basic Django Dockerization](./basic-django-docker.md) inside a dir called postgresql
(omit from short reset step onwards)

## 🐘️ Phase 2: Switch to PostgreSQL
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

3. **Update the database config in the settings.py file:**
Comment out the original config and add this:
```python
  DATABASES = {
  "default": {
      "ENGINE": "django.db.backends.postgresql",
      "NAME": "postgres",
      "USER": "postgres",
      "HOST": "db",
      "PORT": 5432,
      }
  }
```
1. **Restart the container:**
```bash
docker compose up -d
```
4. **Install psycopg in the container:**
```bash
docker compose exec web pipenv install psycopg2-binary==2.9.12
```

5. **Force an image rebuild:**
```bash
docker compose up -d --build
```

6. **Delete the sqlite3 file:**
```bash
rm db.sqlite3
```

7. **Run migrations**
 ```bash
 docker compose exec web python manage.py migrate
 ```

8. **Create superuser**
 ```bash
 docker compose exec web python manage.py createsuperuser
 ```

9. **Navigate to the admin page and login**

   
## 🛑 Phase 4: The Cool Down
Keep your workspace clean and prepare the environment for the next training session.

## 🔄 2. Short Reset (Repeat Dockerization from the base project)
Use this to wipe the slate clean and practice Phase 2 and 3 again from scratch, keeping your base Django project and Pipfile intact.

1. **Stop containers y remove all images:**
```bash
docker compose down --rmi all -v
```
2. **Restore the original docker-compose file:**
```yaml
services:
  web:
    build: .
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/code
    ports:
      - 8000:8000
    depends_on: #DELETE FROM THIS LINE ONWARDS
      - db
  db: #new
    image: postgres:16
    environment:
      - POSTGRES_HOST_AUTH_METHOD=trust
```
3. **Restore original Pipfile:**
```
[source]]
url = "https://pypi.org/simple"
verify_ssl = true
name = "pypi"

[packages]
django = "*"
psycopg2-binary = "==2.9.12" #REMOVE THIS LINE
[dev-packages]

[requires]
python_version = "3.12"
```
4. **Recreate the Pipfile.lock:**
```bash
rm -f Pipfile.lock
pipenv lock
```
5. **Restore original DATABASES settings in settings.py:**
Remove the current DATABASE settings and uncomment the original config
```python
DATABASES = {
    "default": {
        "ENGINE": "django.db.backends.sqlite3",
        "NAME": BASE_DIR / "db.sqlite3",
    }
}
```
6. **Activate the local environment:**
```bash
pipenv shell
python manage.py runserver
```
7. **Navigate to localhost in your browser**


## 🪓 3. The "Full Wipe" (Optional - Absolute Scratch)
If you want to practice the entire pipeline from the very beginning (including Phase 1: Django Setup from the previous kata)

1. **Purge Docker assets for this project:**
```bash
docker compose down --rmi all -v
```
2. **Remove Pipenv virtual environment and all files:**
```bash
pipenv --rm
cd .. && rm -rf postgresql
```
---

## 📚 Credits & References
* This kata is based on the architectural patterns and production-ready concepts from the book **[Django for Professionals](https://djangoforprofessionals.com/)** by **William S. Vincent**.
