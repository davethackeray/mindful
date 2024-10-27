# Backend Development for Kokoro

## Introduction
This document provides instructions for developing the backend of the Kokoro app using Python with Django. It includes details on setting up user authentication, admin interface, integrating PostgreSQL, using Google Cloud Storage, and setting up OneSignal for notifications.

## Setting Up Django
1. Install Django:
   ```bash
   pip install django
   ```

2. Create a new Django project:
   ```bash
   django-admin startproject kokoro
   cd kokoro
   ```

3. Create a new Django app:
   ```bash
   python manage.py startapp main
   ```

4. Add the new app to the `INSTALLED_APPS` list in `kokoro/settings.py`:
   ```python
   INSTALLED_APPS = [
       ...
       'main',
   ]
   ```

## User Authentication and Admin Interface
1. Set up user authentication using Django's built-in authentication system:
   ```python
   # In main/models.py
   from django.contrib.auth.models import AbstractUser

   class CustomUser(AbstractUser):
       pass
   ```

2. Update the `AUTH_USER_MODEL` setting in `kokoro/settings.py`:
   ```python
   AUTH_USER_MODEL = 'main.CustomUser'
   ```

3. Create and apply migrations:
   ```bash
   python manage.py makemigrations
   python manage.py migrate
   ```

4. Create a superuser for accessing the admin interface:
   ```bash
   python manage.py createsuperuser
   ```

5. Register the custom user model in the admin interface:
   ```python
   # In main/admin.py
   from django.contrib import admin
   from django.contrib.auth.admin import UserAdmin
   from .models import CustomUser

   admin.site.register(CustomUser, UserAdmin)
   ```

## Integrating PostgreSQL
1. Install PostgreSQL and the psycopg2 library:
   ```bash
   sudo apt-get install postgresql postgresql-contrib
   pip install psycopg2
   ```

2. Update the database settings in `kokoro/settings.py`:
   ```python
   DATABASES = {
       'default': {
           'ENGINE': 'django.db.backends.postgresql',
           'NAME': 'kokoro_db',
           'USER': 'your_db_user',
           'PASSWORD': 'your_db_password',
           'HOST': 'localhost',
           'PORT': '5432',
       }
   }
   ```

3. Create the PostgreSQL database and user:
   ```bash
   sudo -u postgres psql
   CREATE DATABASE kokoro_db;
   CREATE USER your_db_user WITH PASSWORD 'your_db_password';
   ALTER ROLE your_db_user SET client_encoding TO 'utf8';
   ALTER ROLE your_db_user SET default_transaction_isolation TO 'read committed';
   ALTER ROLE your_db_user SET timezone TO 'UTC';
   GRANT ALL PRIVILEGES ON DATABASE kokoro_db TO your_db_user;
   \q
   ```

4. Apply migrations to the PostgreSQL database:
   ```bash
   python manage.py migrate
   ```

## Using Google Cloud Storage
1. Install the Google Cloud Storage library:
   ```bash
   pip install google-cloud-storage
   ```

2. Update the storage settings in `kokoro/settings.py`:
   ```python
   DEFAULT_FILE_STORAGE = 'storages.backends.gcloud.GoogleCloudStorage'
   GS_BUCKET_NAME = 'your_bucket_name'
   GS_CREDENTIALS = 'path/to/your/credentials.json'
   ```

3. Configure Google Cloud Storage:
   - Create a new bucket in the Google Cloud Console
   - Download the service account key and save it as `credentials.json`
   - Update the `GS_BUCKET_NAME` and `GS_CREDENTIALS` settings with your bucket name and the path to the credentials file

## Setting Up OneSignal for Notifications
1. Install the OneSignal library:
   ```bash
   pip install onesignal-sdk
   ```

2. Update the notification settings in `kokoro/settings.py`:
   ```python
   ONESIGNAL_APP_ID = 'your_onesignal_app_id'
   ONESIGNAL_API_KEY = 'your_onesignal_api_key'
   ```

3. Create a function to send notifications using OneSignal:
   ```python
   # In main/utils.py
   from onesignal_sdk.client import Client

   def send_notification(user, message):
       client = Client(app_id='your_onesignal_app_id', rest_api_key='your_onesignal_api_key')
       notification = {
           'contents': {'en': message},
           'include_player_ids': [user.onesignal_player_id],
       }
       client.send_notification(notification)
   ```

4. Call the `send_notification` function when needed, such as when sending reminders or milestone notifications.

## Conclusion
By following these instructions, you can set up the backend for the Kokoro app using Python with Django. This includes user authentication, admin interface, integrating PostgreSQL, using Google Cloud Storage, and setting up OneSignal for notifications.
