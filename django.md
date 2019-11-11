## Notas seguindo o tutorial da [Django Girls](https://djangogirls.org/)

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
  # Criando uma aplicação:
  
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
      - `pip3.6 install -user pythonanywhere`
      - `pa_autoconfigure_django.py --python=3.6 https://github.com/<your-github-username>/my-first-blog.git`
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
      
      
