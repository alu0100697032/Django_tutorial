# Django_tutorial

## 1. Crear un proyecto 
En el directorio donde querramos almacenar nuestro proyecto ejecutar el comando:
```
$ django-admin startproyect mysite
```
Moverse al directorio mysite y ejecutar: 
```
$ python manage.py runserver
```
para ver que el proyecto funciona correctamente. Visita https://127.0.0.1:8000 en el navegador, aparecerá "Welcome to Django".

## 2. Crear una aplicación
Para crear una aplicación hay que asegurarse de que se está en el mismo directorio que manage.py y ejecutar: 
```
$ python manage.py startapp nombreApp
```
lo que genera un directorio "nombreApp" con la estructura necesaria para la aplicación.

## 3. Escribir una vista

Abrir nombreApp/views.pye insertar:
```python
from django.http import HttpResponse
def index(request):
  return HttpResponse("Hello, world. You´re at the nombreApp index.")
```
