[← Back to Django Dojo](./README.md)
# 🥋 Basic Django App

This kata focuses on making a simple homepage by creating a dedicated pages app for it. The goal is to mechanize the creation of a basic Django view.

---

## 🎯 The Objective
Add a pages app to a basic Django project.

---

## 🛠️ My Tech Stack for this Kata
| Tool / Library | Purpose |
| :--- | :--- |
| **Python** | Current stable runtime environment for running Django backend services. |
| **Django** | Web Framework (LTS focus) |
| **pipenv** | Dependencies and virtual environments management. |
  
---

## 🛠️ Phase 1: Create a basic Django project
See the kata [← Django Hello](./01-django-hello.md)


## 🚀 Phase 2: Add a new app (Pages)
Run the commands to add a new app to the project.

1. **Create the app:**
   ```bash
   python manage.py startapp pages
   ```

2. **Add the pages app to the INSTALLED_APPS config within the config/settings.py file:**
   ```python
   INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'pages', # add this
   ]
   ```


   
3. **Create the function view in the pages/views.py file:**
    ```python
    from django.http import HttpResponse


    def home_page_view(request):
      return HttpResponse("Hello, World!")

    ```

4. **Create a urls.py file within the pages app:**
   ```bash
   touch pages/urls.py
   ```
   
5. **Set the URL route for the home page view:**
   ```python
   from django.urls import path
   from .views import home_page_view

   urlpatterns = [path("", home_page_view, name="home")]
    ```

6. **Set the URL route for the pages app:**
   ```python
   from django.contrib import admin
   from django.urls import path, include

   urlpatterns = [
      path("admin/", admin.site.urls),
      path("", include("pages.urls")), #add this
   ]

   ```
7. **Start the server:**
   ```bash
   python manage.py runserver
   ```
   
## 🛑 Phase 3: The Cool Down
Clean up and exit the virtual environment.

1. **Stop the server:** Press `Ctrl + C` in your terminal.

2. **Exit the virtual environment:**
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
