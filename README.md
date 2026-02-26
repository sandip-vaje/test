# Terms Module - Django API

This document outlines the creation of a Terms module with database integration for the Django property management system.

## Files Created

### 1. Terms Module Structure
```
terms/
├── __init__.py          # Module initialization
├── apps.py             # Django app configuration
├── models.py           # Database model for Terms
├── views.py            # API views (GET and POST)
└── urls.py             # URL routing
```

### 2. Configuration Files
- `requirements.txt` - All project dependencies
- `setup.bat` - Automated setup script

## File Contents

### terms/__init__.py
```python
# Empty file for module initialization
```

### terms/apps.py
```python
from django.apps import AppConfig


class TermsConfig(AppConfig):
    default_auto_field = 'django.db.models.BigAutoField'
    name = 'terms'
```

### terms/models.py
```python
from django.db import models


class Terms(models.Model):
    key = models.CharField(max_length=100)
    title = models.CharField(max_length=200)
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)

    def __str__(self):
        return self.title
```

### terms/views.py
```python
from django.http import JsonResponse
from django.views.decorators.csrf import csrf_exempt
from .models import Terms
import json


@csrf_exempt
def terms_api(request):
    if request.method == 'GET':
        terms = Terms.objects.all()
        content = [{
            "key": term.key,
            "title": term.title,
            "description": term.description
        } for term in terms]
        
        response_data = {
            "content": content,
            "message": "",
            "status": 200
        }
        return JsonResponse(response_data)
    
    elif request.method == 'POST':
        try:
            data = json.loads(request.body)
            
            term = Terms.objects.create(
                key=data.get('key'),
                title=data.get('title'),
                description=data.get('description')
            )
            
            response_data = {
                "content": {
                    "key": term.key,
                    "title": term.title,
                    "description": term.description
                },
                "message": "Terms inserted successfully",
                "status": 201
            }
            return JsonResponse(response_data, status=201)
            
        except json.JSONDecodeError:
            return JsonResponse({
                "content": None,
                "message": "Invalid JSON data",
                "status": 400
            }, status=400)
```

### terms/urls.py
```python
from django.urls import path
from . import views

urlpatterns = [
    path('', views.terms_api, name='terms_api'),
]
```

### requirements.txt
```
Django>=4.0,<6.0
django-cors-headers
django-celery-beat
django-sslserver
celery
phonenumbers
django-phonenumber-field
Pillow
boto3
PyJWT
cryptography
requests
```

### setup.bat
```batch
@echo off
echo Installing dependencies...
pip install -r requirements.txt

echo Creating migrations...
python manage.py makemigrations
python manage.py makemigrations terms

echo Running migrations...
python manage.py migrate

echo Setup complete! You can now run: python manage.py runserver
pause
```

## API Endpoints

### GET/POST `/terms/`
Single endpoint for both operations:

**GET** - Returns all terms from database
```json
{
    "content": [
        {
            "key": "key1",
            "title": "Sample title",
            "description": "Description text"
        }
    ],
    "message": "",
    "status": 200
}
```

**POST** - Inserts new term into database
**Request:**
```json
{
    "key": "new_key",
    "title": "New Title",
    "description": "New description"
}
```
**Response:**
```json
{
    "content": {
        "key": "new_key",
        "title": "New Title",
        "description": "New description"
    },
    "message": "Terms inserted successfully",
    "status": 201
}
```

## Setup Instructions

### Quick Setup
```cmd
setup.bat
python manage.py runserver
```

### Manual Setup
```cmd
pip install -r requirements.txt
python manage.py makemigrations terms
python manage.py migrate
python manage.py runserver
```

## Configuration Changes

### settings.py
Added `'terms'` to `INSTALLED_APPS`

### property_management/urls.py
Added terms URL routing:
```python
path('terms/', include(terms_urls)),
```

## Dependencies Added
- Django
- django-cors-headers
- django-celery-beat
- django-sslserver
- celery
- phonenumbers
- django-phonenumber-field
- Pillow
- boto3
- PyJWT
- cryptography
- requests

## Usage
1. Run `setup.bat` for first-time setup
2. Start server: `python manage.py runserver`
3. Access APIs at `http://127.0.0.1:8000/terms/`