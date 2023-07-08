settings.py

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    
    'demotest',
    
]

DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'employees',
        'USER': 'root',
        'PASSWORD': '',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
------------------
Application- admin.py
from django.contrib import admin
from .models import Employee

# Register your models here.
admin.site.register(Employee)
------------------------
application - models.py
from django.db import models

# Create your models here.

class Employee(models.Model):
    name = models.CharField(max_length=50)
    email = models.CharField(max_length=30)
    phone = models.IntegerField(default=0)
    country = models.CharField(max_length=50)

    # class Meta:
    #     verbose_name_plural = "Employee"

python manage.py makemigrations demotest

python manage.py migrate
---------------------------------

project- url.py

from django.contrib import admin
from django.urls import path,include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('demotest.urls')),
]
----------------------------------
application- url.py

from django.urls import path
from . import views

urlpatterns = [
   
    path('', views.Index),
    path('getemployees/', views.getEmployee),
    path('create/', views.Create),
    path('edit_employee/<int:id>/', views.Edit),
    path('update_employee/<int:id>/', views.Update_emp),
    path('delete_employee/<int:id>/', views.Delete_emp),
]
    ------------------------

    
