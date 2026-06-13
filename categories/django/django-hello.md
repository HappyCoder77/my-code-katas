[← Back to Django Dojo](./README.md)
# 🥋 Basic Django Setup

This kata focuses on building a basic development environment. The goal is to mechanize the creation of a basic Django project.

---

## 🎯 The Objective
Set up a basic Django project from scratch.

---

## 🛠️ My Tech Stack for this Kata
| Tool / Library | Purpose |
| :--- | :--- |
| **Python** | Current stable runtime environment for running Django backend services. |
| **Django** | Web Framework (LTS focus) |
| **pipenv** | Dependencies and virtual environments management. |
  
---

## 🛠️ Phase 1: Structure & Dependencies
Prepare the workspace and install dependencies.

1. **Initial Workspace:**
   ```bash
   mkdir hello && cd hello
   pipenv install 'django~=5.0.0'
   pipenv shell
   ```


## 🚀 Phase 2: Execution (The Ignite)
Run the commands to bootstrap the project.

1. **Initialize the Django project:**
   ```bash
   django-admin startproject config .
   ```

2. **Run migrations:**
   ```bash
   python manage.py migrate
   ```

3. **Verify the configuration (Sanity Check):**
   ```bash
   python manage.py check
   ```
   
4. **Start the environment:**
    ```bash
    python manage.py runserver
    ```

## 🛑 Phase 3: The Cool Down
Clean up and exit the virtual environment.

1. **Stop the server:** Press `Ctrl + C` in your terminal.

2. Exit the virtual environment:
   ```bash
   exit
   ```

## 🔄 Reset for Repetition (Optional)
Run these commands from your base terminal if you want to wipe the slate clean and start the kata again:

1. **Destroy the virtual environment:**
```bash
pipenv --rm
```

2. **Remove the project workspace:**
```bash
cd .. && rm -rf hello
```

---

## 📚 Credits & References
* This kata is based on the architectural patterns and production-ready concepts from the book **[Django for Professionals](https://djangoforprofessionals.com/)** by **William S. Vincent**.
