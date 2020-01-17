# Instalar python
  - `sudo apt install python3`
  
# Virutal env
  - `sudo apt install python-virtualenv`
  - `virtualenv [myenv]`
  - `source myenv/bin/activate` (ou no lugar de source use ".")
  
# Instalar Django
  - Criar arquivo `requirements.txt`:
    - Escrever nele: `Django~=2.2.4`
    - Instalar dentro da env: `pip install -r requirements.txt`
    
# Criar projeto
  - `django-admin startproject [mysite] .`
  
    
