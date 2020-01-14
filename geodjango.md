# Tutorial do canal [Geocast Brasil](https://www.youtube.com/watch?v=ofTUgfZbEkU&t=7182s)

# Criar banco de dados
  - Comando do postgressql: `createdb [nome do db]`
  - Acessar banco de dados: `psql [nome do db]`
  - Listar tabelas do banco: `\d`
  - Instalar a extensão espacial do postgres:
    - Dentro do banco de dados: `create extension postgis;`
    - Se listar as tabelas com `\d`, verificará 4 views e 1 tabela `spatial_ref_sys`
  - Para sair do banco de dados: `\q`
  
# Criar máquina virtual
  - `py -m venv .venv`
  
# Ativar venv
  - `.venv\Scripts\activate`
  
# Instalar aplicações dentro da venv:
  - Django: `pip install django`
  
# Criar projeto Django
  - `django-admin startproject [nome do projeto] .` (este ponto indica que criará no diretório atual)
  - Para ver comandos disponíveis: `python manage.py`
  
# Pacotes a serem instalados:
  - Para ver os pacotes instalados na env: `pip freeze`
  - Para separar as configurações do projeto do código do projeto: `pip install python-decouple`
  - Adaptador do banco de dados: `pip install psycopg2`
  - Ferramenta para Django para conexões url com banco de dados: `pip install dj-database-url`
  
# Importar bibliotecas no `settings.py`:
  - Bibliotecas do python-decouple: `from decouple import config, Csv`
    - Em `settings.py`:
      - **SECRET-KEY**:`config('SECRET-KEY')`
      - **DEBUG**: `config('DEBUG', default=False, cast=bool)` significa que o `python-decouple` irá procurar a variável DEBUG no arquivo de configuração. Se não a encontrar, usará o valor padrão que está setado como FALSE. Se encontrar, pega o valor do arquivo de conf e o converte (cast) para booleano.
      - **ALLOWED_HOSTS**: `config('ALLOWED_HOSTS', default=[], cast=Csv())`
    - Criar um arquivo na raíz do projeto, chamado `.env`
      - Colocar as variávies de instância:
      (devo apagar as aspas e o espaço?)
      ````
        SECRET-KEY = [a chave aqui]
        DEBUG = true
        ALLOWED_HOSTS = 127.0.0.1, .localhost
      ````



