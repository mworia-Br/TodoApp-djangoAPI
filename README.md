# TodoApp-djangoAPI
##How to build your first API in Django
#django #webdev


We often hear about how APIs are all the time. They can be very confusing when trying to understand how APIs work. In this article, We will create our API using Python/Django. Every program or piece of software uses REST APIs to connect the backend and frontend components. Let's get started with it.
What are APIs

First, we need to understand what APIs are and what they do.
API stands for Application Programming Interface. Just as seen in the name, They are interfaces. An API is a software interface that enables two apps to communicate with one another. Wikipedia gives a broad definition:

“In computer programming, an application programming interface (API) is a set of subroutine definitions, protocols, and tools for building application software. In general terms, it is a set of clearly defined methods of communication between various software components” - Wikipedia

We would be making use of the Django REST Framework for this tutorial to create our API.
What is Django REST Framework

Django REST framework (DRF) is a powerful and flexible toolkit for building Web APIs. Django is a web framework that makes it easier to construct REST interfaces by reducing the amount of code required to perform a task.
Setting up Django REST framework

This article assumes that you already have Django virtual environment installed on your computer. With that, we can get started by installing the Django REST framework.
To install Django rest framework in our virtual environment run:

$ pip install Django rest framework

Your result should look like this:

   $ pip3 install djangorestframework
   Collecting djangorestframework
     Downloading djangorestframework-3.12.2-py3-none-any.whl (957 kB)
        |████████████████████████████████| 957 kB 595 kB/s
   Requirement already satisfied: django>=2.2 in ./venv/lib/python3.8/site-packages (from djangorestframework) (3.1.3)
   Requirement already satisfied: asgiref<4,>=3.2.10 in ./venv/lib/python3.8/site-packages (from django>=2.2->djangorestframework) (3.3.1)
   Requirement already satisfied: sqlparse>=0.2.2 in ./venv/lib/python3.8/site-packages (from django>=2.2->djangorestframework) (0.4.1)
   Requirement already satisfied: pytz in ./venv/lib/python3.8/site-packages (from django>=2.2->djangorestframework) (2020.4)
   Installing collected packages: djangorestframework
   Successfully installed djangorestframework-3.12.2

To create our django_todo project now by running the command django-admin startproject todo

After successfully creating your Django project, change your working directory to your project folder and then run the Django Development server using this code: python manage.py runserver

$ cd todo
$ python manage manage.py runserver
Watching for file changes with StatReloader
Performing system checks...
System check identified no issues (0 silenced).
You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
Run `python manage.py migrate` to apply them.
November 17, 2020 - 10:14:01
Django version 3.1.3, using settings 'todo.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.

For our endpoints, we need to create a todoapp
run the following code to create your app

$ python3 manage.py startapp todoapp

To add our todo app to the INSTALLED_APPS list. Open settings.py in the project directory django_todo and then We'll also include the rest framework app so it can be used in our project.

# ./django_todo/settings.py
...
INSTALLED_APPS = [
    ...
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'rest_framework',
    'todoapp',
]

Creating the Todo model

Models in Django are Python classes that represent a database table. When we run the command python manage.py migrate, Django creates a Todo table in the database, which we can see in the Todo Model.
We'll create our Todo model in the models.py file of the todo app using the following code:

from django.db import models
# Create your models here.
class Todo(models.Model):
    title = models.CharField(max_length = 100)
    body = models.CharField(max_length = 100)
    is_completed = models.BooleanField(default=False)
    date_created = models.DateField(auto_created=True)
    last_modified = models.DateField(auto_now=True)
    def __str___(self):
        return self.title

To create the todoapp table in the database will run the command python manage.py migrate.
To make migrations run:

python manage.py makemigrations

To apply the migrations run

python manage.py migrate

Serializing the Todo model

Let's subclass the Django rest framework's Serializer.ModelSerializer class to make it easier to transform Python objects retrieved from the database to the JSON format required by our APIs and back to Python objects that can be mapped to our database tables.
In the todo app directory, create a file serializers.py, where we will write our TodoSerializer code.

from rest_framework import serializers
from todo.models import Todo
class TodoSerializer(serializers.ModelSerializer):
    class Meta:
        model = Todo
        fields = "__all__"

Creating the TodoAPIView

We'll build the code for our app's CRUD capabilities in the views.py file in our todo app directory. The Django Rest Framework has built-in classes that make creating CRUD functionality a breeze.

We'll begin by importing the Django rest framework classes we'll require to build our CRUD API; then we'll create a class for each of our crud endpoints and add the queryset and serializer class for our Todo model.

from django.shortcuts import render
from rest_framework.generics import ListAPIView
from rest_framework.generics import CreateAPIView
from rest_framework.generics import DestroyAPIView
from rest_framework.generics import UpdateAPIView
from todo.serializers import TodoSerializer
from todo.models import Todo
# Create your views here.
class ListTodoAPIView(ListAPIView):
    """This endpoint list all of the available todos from the database"""
    queryset = Todo.objects.all()
    serializer_class = TodoSerializer
class CreateTodoAPIView(CreateAPIView):
    """This endpoint allows for creation of a todo"""
    queryset = Todo.objects.all()
    serializer_class = TodoSerializer
class UpdateTodoAPIView(UpdateAPIView):
    """This endpoint allows for updating a specific todo by passing in the id of the todo to update"""
    queryset = Todo.objects.all()
    serializer_class = TodoSerializer
class DeleteTodoAPIView(DestroyAPIView):
    """This endpoint allows for deletion of a specific Todo from the database"""
    queryset = Todo.objects.all()
    serializer_class = TodoSerializer

Creating URL paths for our Todo endpoints

We may connect with the various Todo crud views using the URLs. Create a file urls.py in the todo app directory where we will write URLs for various endpoints.

from django.urls import path
from todo import views
urlpatterns = [
    path("",views.ListTodoAPIView.as_view(),name="todo_list"),
    path("create/", views.CreateTodoAPIView.as_view(),name="todo_create"),
    path("update/<int:pk>/",views.UpdateTodoAPIView.as_view(),name="update_todo"),
    path("delete/<int:pk>/",views.DeleteTodoAPIView.as_view(),name="delete_todo")
]

Testing the endpoints

Let's make sure our development server is up and running before we test our API endpoints. Run the command python manage.py runserver in the root folder of our project, where the manage.py file exists, to start the development server.

$ python manage.py runserver
To create a new Todo we make a POST request to
http://localhost:8000/api/v1/todo/create/ with the new Todo object.
{
    "date_created": "2020-11-19",
    "title": "Take a bath,
    "body": "Don't smell and take a break",
    "is_completed": true
}

Conclusion

In Django, we now know how to define and document restful endpoint APIs.

We'll protect our endpoints and add social authentication to our app in the following article.

Thanks for reading.
