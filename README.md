 Django_tutorial

Este tutorial está basado en el tutorial oficial de [Django](https://docs.djangoproject.com/en/1.9/intro/tutorial01/), mucho más detallado.
Como paso previo se asume que se tiene ***python*** y ***Django*** instalados.
## 1. Crear un proyecto
En el directorio donde querramos almacenar nuestro proyecto ejecutar el comando:
```
$ django-admin startproyect mysite
```
Esto creará la estructura básica del proyecto, el cual incluye un servidor que nos servirá para la fase de desarrollo. Si queremos ver que el proyecto funciona correctamente, moverse al directorio mysite y ejecutar:
```
$ python manage.py runserver
```
Saldrá un mensaje por la consola indicando que el servidor está corriendo y con la dirección a visitar.
## 2. Crear una aplicación
Para crear una aplicación hay que asegurarse de que se está en el mismo directorio que manage.py y ejecutar:
```
$ python manage.py startapp nombreApp
```
lo que genera un directorio "nombreApp" (en los ejemplos a partid de ahora *polls*) con la estructura necesaria para poder desarrollar la aplicación. Podemos crear todas las que queramos.

## 3. Escribir una vista
El fichero views.py es donde indicamos las vistas que tiene nuestra aplicación. Para crear una vista de ejemplo:
* Abrir ***polls/views.py*** e insertar:
```python
from django.http import HttpResponse
def index(request):
    return HttpResponse("Hello, world. You´re at the nombreApp index.")
```
El código indica que cuando se visite la vista el servidor envíe el mensaje indicado.

* Ahora hay que crear el fichero ***polls/urls.py*** donde se indican las urls de las vistas de la aplicación, dentro del mismo escribimos:
```python
from django.conf.urls import url
from . import views
urlpatterns = [
    url(r'^$', views.index, name='index'),
]
```
lo que mapea la vista que acabamos de crear con la url correspondiente. 

* Por último en el fichero ***mysite/urls.py*** hay que indicar la url de la nueva aplicación que hemos creado, la url de *admin* ya viene por defecto:
```python
from django.conf.urls import include, url
from django.contrib import admin
urlpatterns = [
    url(r'^polls/', include('polls.urls')),
    url(r'^admin/', admin.site.urls),
```
Si ejecutamos ```$ python manage.py runserver ``` y visitamos http://localhost:8000/polls/ debe aparecernos el mensaje "Hello, world. You´re at the nombreApp index."
