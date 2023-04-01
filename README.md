```python
INSTALLED_APPS = [
    'app',
    'rest_framework'
]
```

```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    author = models.CharField(max_length=100)

    def __str__(self):
        return self.title
```


```python
from django.contrib import admin
from .models import Book

admin.site.register(Book)
```

## app/serializers.py
```python
from .models import Book
from rest_framework import serializers


class BookSerializer(serializers.ModelSerializer):
    class Meta:
        model=Book
        fields='__all__'
```


```bash
python manage.py makemigrations
python manage.py migrate
```


```python

from django.contrib import admin
from django.urls import path
from app.views import BookListCreateAPIView, BookRetrieveUpdateDestroyAPIView

urlpatterns = [
    path('admin/', admin.site.urls),
    path('books/', BookListCreateAPIView.as_view(), name='book-list'),
    path('books/<int:id>/', BookRetrieveUpdateDestroyAPIView.as_view(), name='book-detail'),

]

```



```python
from django.shortcuts import render

# Create your views here.
from rest_framework.mixins import ListModelMixin,RetrieveModelMixin,CreateModelMixin,DestroyModelMixin,UpdateModelMixin
from rest_framework.generics import GenericAPIView
from .models import Book
from .serializers import BookSerializer

class BookListCreateAPIView(ListModelMixin,CreateModelMixin,GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer

    def get(self, request, *args, **kwargs):
        return self.list(request, *args, **kwargs)

    def post(self, request, *args, **kwargs):
        return self.create(request, *args, **kwargs)

class BookRetrieveUpdateDestroyAPIView(RetrieveModelMixin,UpdateModelMixin,DestroyModelMixin,GenericAPIView):
    queryset = Book.objects.all()
    serializer_class = BookSerializer
    lookup_field = 'id'

    def get(self, request, *args, **kwargs):
        return self.retrieve(request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request, *args, **kwargs)

    def delete(self, request, *args, **kwargs):
        return self.destroy(request, *args, **kwargs)

```