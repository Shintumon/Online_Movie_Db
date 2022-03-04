# Movies Web App using Python3 Django:

## Project Setup in CMD

`cd C:\Users\shint\PycharmProjects\Django_Project`

`mkdir movies_product_folder`

`cd movies_product_folder`

`mkvirtualenv movies`

`pip install django`

`django-admin startproject movie_project`

`cd movie_project`

## Project Setup in PyCharm
- Open PyCharm and access the project folder
- Access the virtual env using the command: `workon movies` 
- Start the application: `python manage.py startapp movie_app`

- Inside the settings.py of the movie_project, add the app_name under Installed_Apps.

- Also, set the DIRS path under Templates section of settings.py
`'DIRS': [os.path.join(BASE_DIR, 'TEMPLATES')]`

- Connect the urls of movie_project with movie_app
  ==movie_project.urls:==
`path('', include("movie_app.urls"))`
  ==movie_app.urls:==
    ```
    from django.urls import path
    from . import views
    urlpatterns = [
      path('',views.index, name='index')
    ]
    ```


- Open views.py of the movie_app and create the required functions
    ```
    def index(request):
      return HttpResponse("Hello World")
    ```

### Create Models:
==Movie_app.models.py==
```
    class Movie(models.Model):   
      name=models.CharField(max_length=250)  
      desc=models.TextField()
      year=models.IntegerField()
      def __str__(self):
         return self.name
```

- Complete migration after any change in models 
`python manage.py makemigrations`
`python manage.py migrate`
 
### Admin Panel Setup:
 >create SuperUser:
  `python manage.py createsuperuser`
  (admin, admin@admin.com, admin)
 
> Open movie_app.admin.py and importh the models created
  `from . models import Movie`
  `admin.site.register(Movie)`
  

>Display the items added in Admin panel to the movie_app.views.py
by creating dictionary:
```
  from .models import Movie
  movie = Movie.objects.all()
  context={
   'movie_list':movie
  }
  return render(request,'index.html', context)
```

==Index.html:==
```
  {% for i in movie_list %}
  <a href="movie/{{i.id}}">{{i.id}}.
  <b>{{i.name}}</b> ({{i.year}}):</a> <br>
  <p> <i>{{i.desc}}</i> </p> <br>
  {% endfor %}
```  
  
##### Details View:
- create a new function for detail view inside views.py and add the new path inside urls.py

views.py:
```
def detail(request,movie_id):
  movie=Movie.objects.get(id=movie_id)
  return render(request, "detail.html",{'movie':movie})
```
urls.py:
`path('movie/<int:movie_id>/',views.detail, name='detail')`


##### URL Setup:
Update url references in all pages accordingly as set in urls.py
`<a href="{% url 'detail' i.id %}">`

##### Namespace in Django:
- create app_name inside each application and give proper calls accodingly using that app_name
eg:
In urls.py: `app_name='movie_app'`
In whiciever page the detail is called - index.html: `'movie_app:detail'`


##### Adding Static Files:
- After copying the static contents to the newly created 'static' folder inside the project and loading the required files inside the 'templates' folder, open the settings.py file inside the project folder.

In the section, `STATIC_URL = '/static/' `add the required changes.
```
STATICFILES_DIRS = [os.path.join(BASE_DIR), 'static']
STATIC_ROOT = os.path.join(BASE_DIR, 'assets')
MEDIA_URL = '/media/'
MEDIA_ROOT = os.path.join(BASE_DIR, 'media')
```

- Also, open the urls.py file inside the project folder and add the following code under the urlpatterns section and also add the required import commands to avoid errors:

```
from travelproject import settiings
from django.conf.urls.static import static

if settings.DEBUG:
  urlpatterns += static(settings.STATIC_URL, document_root=settings.STATIC_ROOT)
  urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

- Inside the new static pages added to 'Templates' folder add the following to redirect to the static folder.
Add the following to the starting of the page to load static:
`{% load static %}`
Then change the href links to load from the static folder
eg: `href = "{% static 'styles/bootstrap4/bootstrap.min.css' %}"`


##### Bootstrap Touchup:
- add Navbar

##### Base Template:
- create a new 'base.html' file inside Templates folder
- create the template using the base.html file and call it in each page accordingly
`{% extends 'base.html' %}`

##### Adding Data to Database from front-end:
- add a new path in urls.py from the application
`path('add/', views.add_movie, name='add_movie')`
- add new views function inside views.py
`def add_movie(request):  `
&emsp; ` return render(request,'add_movie.html')`

- create add_movie.html inside template and make the required changes
- create the required entries into function inside views.py to accept the input and add it to the admin_panel/database
    ```
    if request.method=="POST":
        name=request.POST.get('name',)
        desc=request.POST.get('desc',)
        year=request.POST.get('year',)
        img=request.FILES['img']
        movie=Movie(name=name,  desc=desc, year=year, img=img)
        movie.save()
    ```

##### Editing Data in Database from fron-end:
- create a new forms file inside the application. eg: forms.py
- Inside forms.py:
    ```
    from django import forms
    from .models import Movie

    class MovieForm(forms.ModelForm):
    class Meta:
        model=Movie
        fields=['name', 'desc', 'year', 'img']
    ```
    
- create the function in views.py to update the contents
    ```
    from . forms import MovieForm
    def update(request,id):
        movie=Movie.objects.get(id=id)
        form=MovieForm(request.POST or None, request.FILES, instance=movie)
        if form.is_valid():
        form.save()
        return redirect('/')
        return render(request, 'edit.html', {'form': form, 'movie':movie})
    ```

- create a new 'edit.html' file inside 'Templates' folder and add the required contents
- Also,add the required path inside the urls.py file


##### Deleting Data in Database from fron-end:
- Add the url in urls.py
- Add the delete function in views.py
    ```
    def delete(request, id):
    if request.method=='POST':
        movie=Movie.objects.get(id=id)
        movie.delete()
        return redirect('/')
    return render(request,'delete.html')
    ```
   
- create the delete page accorrdingly and add the required changes.
    ```
    <form method="POST">
        {% csrf_token %}
        Do you want to delete this movie?
        <input type="submit">
    </form>
    ```












