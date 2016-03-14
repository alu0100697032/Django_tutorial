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

## 4. Creando modelos

* Debemos definir los modelos dentro de ***polls/models.py***, como ejemplo definiermos dos modelos: Question y Choice:

  ```python
  from django.db import models

  class Question(models.Model):
      question_text = models.CharField(max_length=200)
      pub_date = models.DateTimeField('date published')

  class Choice(models.Model):
      question = models.ForeignKey(Question, on_delete=models.CASCADE)
      choice_text = models.CharField(max_length=200)
      votes = models.IntegerField(default=0)
  ```
* El segundo paso es activar los modelos para ello hay que decirle a Djanfo que tenemos instalada nuestra app, escribimos en ***mysite/settings.py***:
```python
INSTALLED_APPS = [
   'polls.apps.PollsConfig', # línea nueva
   'django.contrib.admin',
   'django.contrib.auth',
   'django.contrib.contenttypes',
   'django.contrib.sessions',
   'django.contrib.messages',
   'django.contrib.staticfiles',
]
```

* Para decirle a Django que hemos hecho cambios en nuestro modelo ejecutar: ```$ python manage.py makemigrations polls```
* Por último ejecuar ```$ python manage.py migrate```para crear las tablas en la base de datosself.

## 5. Crear un administrador

* Ejecutar ***python manage.py createsuperuser*** y seguir las instrucciones.
* Una vez creado el usuario en ***/admin*** podemos logearnos con el usuario creado.
* Para hacer que los modelos de nuestra aplicación ***polls*** sean modificables en la página de administración debemos modificar ***polls/admin.py***:

  ```python
  from django.contrib import admin

  from .models import Question, Choice

  admin.site.register(Question)
  admin.site.register(Choice)
  ```

## 6. Templates

Creamos un directorio templates donde guardaremos todos los templates de nuestra aplicación, por defecto Django buscará en esta carpeta en el momento de renderizar las vistas, entonces creamos el directorio: ***polls/templates/polls*** y dentro creamos el fichero ***index.html*** con el siguiente código:
```html
{% if latest_question_list %}
    <ul>
    {% for question in latest_question_list %}
        <li><a href="/polls/{{ question.id }}/">{{ question.question_text }}</a></li>
    {% endfor %}
    </ul>
{% else %}
    <p>No polls are available.</p>
{% endif %}
```

* En ***polls/views.py*** importamos el shortcut ***render()*** y cargamos el template:

```python
from django.shortcuts import render

from .models import Question


def index(request):
    latest_question_list = Question.objects.order_by('-pub_date')[:5]
    context = {'latest_question_list': latest_question_list}
    return render(request, 'polls/index.html', context)
```

## 7. Testing

* Los tests los escribiremos en ***polls/tests.py***, por ejemplo:

```python
import datetime

from django.utils import timezone
from django.test import TestCase

from .models import Question


class QuestionMethodTests(TestCase):

    def test_was_published_recently_with_future_question(self):
        """
        was_published_recently() should return False for questions whose
        pub_date is in the future.
        """
        time = timezone.now() + datetime.timedelta(days=30)
        future_question = Question(pub_date=time)
        self.assertEqual(future_question.was_published_recently(), False)

    def test_was_published_recently_with_old_question(self):
        """
        was_published_recently() should return False for questions whose
        pub_date is older than 1 day.
        """
        time = timezone.now() - datetime.timedelta(days=30)
        old_question = Question(pub_date=time)
        self.assertEqual(old_question.was_published_recently(), False)

    def test_was_published_recently_with_recent_question(self):
        """
        was_published_recently() should return True for questions whose
        pub_date is within the last day.
        """
        time = timezone.now() - datetime.timedelta(hours=1)
        recent_question = Question(pub_date=time)
        self.assertEqual(recent_question.was_published_recently(), True)
```

* Para ejecutar los tests de nuestra aplicación debemos ejecutar: ***$ python manage.py test polls***

## 8. Hojas de estilo

*
Al igual que en los templates, creamos un directorio donde guardaremos las hojas de estilo de nuestra aplicación. Creamos es directorio ***polls/static/polls*** y dentro nuestra hoja de estilo, por ejemplo ***style.css***:

```css
li a {
    color: green;
}
```

* Una vez creada la hoja de estilo hay que cargarla en el template que querramos. Por ejemplo en ***polls/templates/polls/index.html***:

```html
{% load staticfiles %}

<link rel="stylesheet" type="text/css" href="{% static 'polls/style.css' %}" />
```
