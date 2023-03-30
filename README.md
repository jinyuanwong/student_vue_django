# App - student (vue + django) 

1. To launch django backend, run
`python .\manage.py runserver`

2. To launch vue frontend, run
`npm run serve`


## guideline 
---

`django-admin startproject django_project`

rename the django_project to backend
~~~~
### package management for python
``pipenv shell``

``python .\manage.py migrate``

``npm install -g @vue/cli``

``vue create frontend``

``cd frontend ``

``npm run serve``

To create an app in Django:

``python manage.py startapp students``

Add students in ./backend/django_project/settings.py
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'students',
]
```

Include students urlpatterns (like adding routes in rails)
``` django_project/urls.py
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include('students.urls')),
]
```

```students/urls.py
urlpatterns = [
    path('students/', index)
]
```

Add views for students 
```
from django.http import JsonResponse

# Create your views here.
def index(request):
    students = [] 
    return JsonResponse(students, safe=False)
```

Add model for students
```students/models.py
from django.db import models

# Create your models here.
class Student(models.Model):
    name = models.CharField(max_length=255)
    course = models.CharField(max_length=255)
    rating = models.IntegerField()

    class Meta: 
        ordering = ['name']
```

make migration
```
python manage.py makemigrations
python manage.py migrate
```

Register students model in admin
```in students/admin.py
from .models import Student

admin.site.register(Student)
```

Create adminuser
`python manage.py createsuperuser`
login from here `http://127.0.0.1:8000/admin/`
 
Add string method to student, if not, it will show __student object(1)__ 
``` Student/model
def __str__(self):
    return self.name
```


modify view of student to present the api result 
```students/views.py
from .models import Student
# Create your views here.
def index(request):
    students = [] 

    for student in Student.objects.all():
        students.append({
            'name': student.name,
            'course': student.course,
            'rating': student.rating,
        })

    return JsonResponse(students, safe=False)
```

Install Django REST Framework
```
pip install djangorestframework
```
Modify the install app in settings.py
```
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'students',
]
```

create and modify student urls
``` student/urls.py
from django.urls import path 
from rest_framework import routers
from .views import StudentsViewSet

router = routers.DefaultRouter()
router.register('students', StudentsViewSet)

urlpatterns = router.urls
```

create StudentsViewSet in students/views.py
```
from django.shortcuts import render

from rest_framework.viewsets import ModelViewSet
from .models import Student
from .serializers import StudentSerializer

class StudentsViewSet(ModelViewSet):
    queryset = Student.objects.all() 
    serializer_class = StudentSerializer

```

create a serializers.py
`touch students/serializers.py`
modify it
```
from rest_framework import ModelSerializer 

from .models import Student

class StudentSerializer(ModelSerializer):
    class Meta:
        model = Student 
        fields = ['id', 'name', 'course', 'rating']
```

install __Emmet Keybindings__ extensions for writing html code easier

adding bootstrap style in frontend\public\index.html
```
<link href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha2/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-aFq/bzH65dt+w6FI2ooMVUpc+21e0SRygnTpmBvdBgSdnuTN7QbdgL+OapgHtvPp" crossorigin="anonymous">

<div class="container">
    <div class="row">
    <div class="col-8 mx-auto mt-5">
        <div id="app"></div>
    </div>
    </div>
</div>
```


install django-cors-headers to prevent error in the next step.
`pip install django-cors-headers`

modify settings.py, add
```
INSTALLED_APPS = [
    'corsheaders',
]

MIDDLEWARE = [
    'corsheaders.middleware.CorsMiddleware',
]

CORS_ORIGIN_ALLOW_ALL = True 
```

modify frontend\src\App.vue
```
<template>
  <div id="app">
    {{ msg }}
    <table class="table">
      <thead>
        <th>Name</th>
        <th>Course</th>
        <th>Rating</th>
      </thead>   
      <tbody>
        <tr v-for="student in students" :key="student.id">
          <td>{{ student.name }}</td>
          <td>{{ student.course }}</td>
          <td>{{ student.rating }}</td>
        </tr>
      </tbody>
    </table>
  </div>
</template>

<script>
// import {ref} from 'vue'

// const msg = ref('Welcome!');
// const students = ref([]);

// var response = await fetch('http://127.0.0.1:8000/api/students/');

// students.value = await response.json();

// console.log(students.value)

export default {
  name: 'App',
  data(){
    return {
      students: []
    }
  },

  async created(){
    var response = await fetch('http://127.0.0.1:8000/api/students/');
    this.students = await response.json();
  }
}

</script>

```

Check the App.vue for more details.



