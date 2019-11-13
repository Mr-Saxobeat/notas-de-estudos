# Notas seguindo o tutorial da [Django Girls](https://djangogirls.org/)

# Para começar um projeto:

- Criar uma pasta (nome em minúsculo)
- Criar um enviroment (nome em minúsculo): `python -m venv [myenv]`
- Para trabalhar com este env mais tarde: `venv\Scripts\activate`
- Instalar Django:
   - Criar um arquivo requirements.txt
      - Conteúdo: `Django~=2.2.4`
   - `pip install -r requirements.txt`
- Criar novo projeto Django:
   - `django-admin.exe startproject [mysite] .` (o ponto é o diretório atual)
   
# Configurando o site:

- Editar os campos no `settings.py`:
  - `LANGUAGE_CODE` de acordo com o código da [Lista de códigos de linguagem](https://docs.djangoproject.com/en/2.2/ref/settings/#language-code])
  - `TIME_ZONE` de acordo com a zona de tempo da [Lista de Zonas de Tempo da Wikipédia](https://en.wikipedia.org/wiki/List_of_tz_database_time_zones)   
  - Adicionar linha ao final do arquivo: `STATIC_ROOT = os.path.join(BASE_DIR, 'static')`
  - Alterar: `ALLOWED_HOSTS = ['127.0.0.1', '.pythonanywhere.com']`
  
# Criando um banco de dados:
  - Executar no prompt: `python manage.py migrate`

# Iniciando o servidor:
  - `python manage.py runserver`
  - Verificar o endereço no browser: `http://127.0.0.1:8000/`
  
  ------
  
  # Django Models
  ## Criando uma aplicação:
  
  - `python manage.py startapp blog`
  - Depois, devemos dizer ao Django para usar o app blog:
    - No arquivo `settings.py`, adicionar ao `INSTALLED_APPS` `'blog.apps.BlogConfig',`

#Criando um modelo de blog post

- No arquivo `models.py`:
  ```
  from django.conf import settings
  from django.db import models
  from django.utils import timezone


  class Post(models.Model):
      author = models.ForeignKey(settings.AUTH_USER_MODEL, on_delete=models.CASCADE)
      title = models.CharField(max_length=200)
      text = models.TextField()
      created_date = models.DateTimeField(default=timezone.now)
      published_date = models.DateTimeField(blank=True, null=True)

      def publish(self):
          self.published_date = timezone.now()
          self.save()

      def __str__(self):
          return self.title
    ```
    
# Criando tabelas para os modelos no banco de dados

 - Prepara um arquivo de migração: `python manage.py makemigrations blog`
 - Aplica o modelo ao banco de dados: `python manage.py migrate blog`

# Django admin
No arquivo `blog/admin.py`:
   ```
   from django.contrib import admin
   from .models import Post
   
   admin.site.register(Post)
   ```
   
   - Criar superuser: `python manage.py createsuperuser`
   - Mais sobre admin: https://docs.djangoproject.com/en/2.2/ref/contrib/admin/
   
# Deploy
   - Adicionar os arquivos no .gitignore:
      ```
      *.pyc
      *~
      __pycache__
      myenv
      db.sqlite3
      /static
      .DS_Store
      ```
## Adicionando ao GitHub
   - Criar repositório
   - `git remote add origin https://github.com/<your-github-username>/my-first-blog.git`
   - `git push -u origin master`
   - Criar API token no **pythonanywhere**
   - No bash do **pythonanywhere**:
      - `pip3.6 install --user pythonanywhere`
      - `pa_autoconfigure_django.py --python=3.6 https://github.com/<your-github-username>/my-first-blog.git [--nuke]` 
      - `python manage.py createsuperuser`
   - [Django deployment checklist](https://docs.djangoproject.com/en/2.2/howto/deployment/checklist/)
   - [Debugging tips on PythonAnywhere website](http://help.pythonanywhere.com/pages/DebuggingImportError)
   
# URLs
   - Para Django apontar a raíz para o blog. Em `mysite/urls.py`:
      ```
      from django.contrib import admin
      from django.urls import path, include

      urlpatterns = [
      path('admin/', admin.site.urls),
      path('', include('blog.urls')),
      ]
      ```
   - Criar `blog/urls.py`:
      ```
      from django.urls import path
      from . import views
      
      """ Atribui a URL raíz para a view chamada post_list
      urlpatterns = [
      path('', views.post_list, name='post_list'),
      ]
      ```
      
# Django views
   - Em `blog/views.py`:
      ```
      from django.shortcuts import render

      # Create your views here.
      def post_list(request):
         return render(request, 'blog/post_list.html', {})
      ```
   - Cria o arquivo `blog/templates/blog/post_list.html`
   - Adicione qualquer html:
      ```
      <html>
         <head>
            <title>Ola's blog</title>
         </head>
          <body>
              <p>Hi there!</p>
              <p>It works!</p>
          </body>
      </html>
      ```
   - Atualiza o git:
      - `git status`
      - `git add --all .`
      - `git status`
      - `git commit -m "Altera o HTML para o site"`
      - `git push`
   - No bash do PythonAnywhere:
      - `cd ~/<your-pythonanywhere-domain>.pythonanywhere.com`
      - `git pull`
      - Na página "Web", clique em **Reoload**
      - Site publicado! :D
      
# Django ORM e QuerySets
   
   - Django console: `python manage.py.shell`
   - Mostrar todos os posts:
      ````
      from blog.models import Post
      
      Post.objects.all()
      ````
   - Criar objeto:
      ````
      # Importa modelo de usuário
      from django.contrib.auth import User
      
      # Armazena usuário na variável
      me = User.objects.get(username='username')
      
      # Cria post object
      Post.objects.create(author=me, title='Sample title', text='Test')
   - Filtrar objetos:
      - Retorna todos posts que satisfazem a condição: `Post.objects.filter(author=me)`
      - Retorna posts com a condição: `Post.objects.filter(title__contains='title')`
      - `Post.objects.filter(published_date__lte=timezone.now())`
   - Publicar post: `Post.objects.get(title='Sample title').publish()`
   - Ordenar: `Post.objects.order_by('created_date')`
   - Ordem contrária: `Post.objects.order_by('-created_date')`
   - `Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')`
   
# Dados dinâmicos no template

   - Importar os posts model: `from .model import Post`
   - No arquivo `blog/view.py`:
      ````
      from django.shortcuts import render
      from django.utils import timezone
      from .models import Post
      
      def post_list(request):
         posts = Post.objects.filter(published_date__lte=timezone.now()).order_by('published_date')
         return render(request, 'blog/post_list.html', {'posts': post}
      ````
# Django templates
   
   - Template tags, em `blog/templates/blog/post_list.html`:
      ````
      <div>
         <h1><a href="/">Django Girls Blog</a></h1>
      </div>
      
      {% for post in posts %}
         <div>
            <p>published: {{ post.published_date }}</p>
            <h2><a href="">{{ post.title }}</a></h2>
            <p>{{ post.text|linebreaksbr }}</p>
         </div>
      {% endfor %}
      ````
      - Atualizar o banco de dados no **pythonanywhere** e ver como ficou o site.
      
# CSS
   - Instalar Bootstrap:
      ````
      <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap.min.css">
      <link rel="stylesheet" href="//maxcdn.bootstrapcdn.com/bootstrap/3.2.0/css/bootstrap-theme.min.css">
      ````
## Arquivos estáticos:
   - Pasta `django/blog/static`
   - Css: `django/blog/static/css/blog.css`
   - Carregar arquivos estáticos no html: 
      ````
      {% load static %}
      <html>
         <head>
            <link rel="stylesheet" href="{% static 'css/blog.css' %}>
         </head>
      </html>
      ````
# Template extending
   
   - Criar um arquivo template base: `blog/templates/blog/base.html`
   - Usar `template tag` em `base.html`:
      ````
      <body>
          <div class="page-header">
              <h1><a href="/">Django Girls Blog</a></h1>
          </div>
          <div class="content container">
              <div class="row">
                  <div class="col-md-8">
                  {% block content %}
                  {% endblock %}
                  </div>
              </div>
          </div>
      </body>
      ````
   - No arquivo `post_list.html`:
      ````
      {% extends 'blog/base.html' %}
      
      {% block content %}
         {% for post in posts %}
             <div class="post">
                 <div class="date">
                     {{ post.published_date }}
                 </div>
                 <h2><a href="">{{ post.title }}</a></h2>
                 <p>{{ post.text|linebreaksbr }}</p>
             </div>
         {% endfor %}
      {% endblock %}
      ````
      
# Extend your application
### Criar um template de link para detalhes do post:
   - Em `blog/templates/blog/post_list.html` no link do título do post:
      `<h2><a href="{% url 'post_detail' pk=post.pk %}">{{ post.title }}</a></h2>`
### Criar uma URL para o detalhes do post:
   -  Em `blog/urls.py`:
      `path('post/<int:pk>', views.post_detail, name='post_detail')` 
### Adicionar uma view para detalhes do post:
   - Em `blog/views.py`:
      ````
      def post_detail(request, pk):
         post = get_object_or_404(Post, pk=pk)
         return render(request, 'blog/post_detail.html', {'post': post})
      ````

      
      
      
   
      
