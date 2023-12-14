[[7 - Django]]

Na seção passada você aprendeu sobre _templates_ funcionando com o Flask e também com o Jinja2. Agora, vamos abordar um pouco mais sobre esse mesmo conceito, mas, dessa vez, funcionando com o Django.

## Jinja2 X Django _Template_ Language (DTL)

Embora sejam muito parecidos e tenham o mesmo propósito, criar _templates_ em aplicações web, o Jinja2 e o Django _Template_ Language (DTL) são dois mecanismos de _templates_ diferentes. Cada um deles possui vantagens e desvantagens, cabe a pessoa desenvolvedora escolher qual deles utilizará em seu projeto.

Por exemplo: o Jinja2 pode ser usado em qualquer projeto Python, enquanto o DTL é um mecanismo específico do Django. Contudo, por ser um mecanismos específico do Django, o DTL possui algumas funcionalidades que não estão presentes no Jinja2.

Quando o tema é mercado de trabalho e comunidade, o DTL é o mecanismo de _template_ mais utilizado em aplicações Django. Por essa razão, durante todo o aprendizado sobre Django usaremos o DTL para criar nossos _templates_.

![Pesquisa sobre utilização dos mecanismos de templates](https://content-assets.betrybe.com/prod/56d6ff4e-7797-4972-bf83-7c8cd29593e5-Pesquisa%20sobre%20utiliza%C3%A7%C3%A3o%20dos%20mecanismos%20de%20templates.png)
Pesquisa realizada pela JetBrains com pessoas desenvolvedoras que usam Django. [Fonte](https://lp.jetbrains.com/pt-br/django-developer-survey-2022/)

## Configuração de _Templates_ no Django

Toda vez que um projeto Django é iniciado, um arquivo `settings.py` é criado dentro da pasta do projeto, e é dentro deste arquivo que é feita a configuração para indicar o mecanismo de _template_ que será utilizado: Jinja2 ou o DTL.

Como o DTL é o mecanismo de _template_ padrão do Django, não é necessário fazer nenhuma modificação para conseguir usá-lo. Contudo, caso no futuro você queira estudar o uso do Jinja2 como mecanismo de _template_, basta fazer a seguinte modificação no `settings.py`:

```diff
    ...
TEMPLATES = [
    {
-       'BACKEND': 'django.template.backends.django.DjangoTemplates',
+       'BACKEND': 'django.template.backends.jinja2.Jinja2',
        'DIRS': [],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
    ...
```

> **Atenção ⚠️:** lembre-se que não precisa fazer a alteração mencionada acima, seguiremos os estudos de Django utilizando o DTL como mecanismo de _templates_.

## Próximos passos

O que acha de começar o aprendizado sobre _templates_ no Django com um pouco de prática? Hoje, será construído um projeto Django para listar eventos, como seminários, conferências, workshops, etc. Além disso, também será possível visualizar os detalhes de cada um destes eventos.

![Gif com pessoas animadas dançando](https://content-assets.betrybe.com/prod/56d6ff4e-7797-4972-bf83-7c8cd29593e5-Gif%20com%20pessoas%20animadas%20dan%C3%A7ando.gif)
Bora lá? 🤩


# Colocando o primeiro _template_ para funcionar

Finalmente, chegou a hora de colocar a mão ~~na massa~~ no código! 🎉

## Setup inicial

Para começar, crie o ambiente virtual que será utilizado e faça a instalação dos pacotes que serão utilizados:

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install django
pip install Pillow # biblioteca para trabalhar com imagens
pip install mysqlclient # biblioteca para se comunicar com o MySQL
```

Em seguida, crie o projeto Django e a aplicação:

```bash
django-admin startproject event_manager .
django-admin startapp events
```

Faça a instalação da aplicação dentro do projeto no arquivo `settings.py`:

```diff
# event_manager/settings.py
...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
+   'events',
]

...
```

Faça também a mudança para usar o MySQL como banco de dados:

```diff
# event_manager/settings.py
...

DATABASES = {
    'default': {
-       'ENGINE': 'django.db.backends.sqlite3',
+       'ENGINE': 'django.db.backends.mysql',
-       'NAME': BASE_DIR / 'db.sqlite3',
+       'NAME': 'event_manager_database',
+       'USER': 'root',
+       'PASSWORD': 'password',
+       'HOST': '127.0.0.1',
+       'PORT': '3306',
    }
}

...
```

Crie o arquivo para o script SQL dentro do diretório `./database`:

```bash
mkdir database && cd database
touch 01_create_database.sql
```

Adicione o conteúdo do script para criação do banco de dados `event_manager_database`:

```sql
CREATE DATABASE IF NOT EXISTS event_manager_database;

USE event_manager_database;
```

Crie o Dockerfile na raiz do projeto:

```yaml
FROM mysql:8.0.32

ENV MYSQL_ROOT_PASSWORD password
COPY ./database/01_create_database.sql /docker-entrypoint-initdb.d/data.sql01
```

Faça o _build_ da imagem, basta rodar o comando dentro da pasta do projeto que contém o arquivo Dockerfile.

```bash
docker build -t event-manager-db .
```

Execute o container e o script de criação do banco copiado no Dockerfile:

```bash
docker run -d -p 3306:3306 --name=event-manager-mysql-container -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=event_manager_database event-manager-db
```

Acesse o banco de dados pelo _Workbench_ e verifique se ele foi criado corretamente.

Execute o comando `migrate` do Django:

```bash
python3 manage.py migrate
```

## Renderizando seu primeiro _template_

Antes de começarmos, saiba que a configuração padrão do Django permite que você crie seus _templates_ dentro de cada uma das aplicações do seu projeto, e assim faremos.

É possível alterar essa configuração para indicar diretórios específicos onde o Django deve procurar por _templates_. Por exemplo: na configuração abaixo, o Django irá buscar por _templates_ dentro do diretório `_templates_`, que está na raiz do projeto e não mais dentro de cada uma das aplicações do projeto. Lembre-se que você não precisa fazer a alteração abaixo.

Copiar

```diff
# event_manager/settings.py
+ import os

TEMPLATES = [
    {
        'BACKEND': 'django.template.backends.django.DjangoTemplates',
-       'DIRS': [],
+       'DIRS': [os.path.join(BASE_DIR,'templates')],
        'APP_DIRS': True,
        'OPTIONS': {
            'context_processors': [
                'django.template.context_processors.debug',
                'django.template.context_processors.request',
                'django.contrib.auth.context_processors.auth',
                'django.contrib.messages.context_processors.messages',
            ],
        },
    },
]
```

Agora sim, crie um novo diretório com nome `templates` dentro da aplicação `events` e, em seguida, crie o arquivo `home.html` dentro do novo diretório e inicie um arquivo HTML:

Copiar

```html
<!--events/templates/home.html-->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Primeiro Template</title>
</head>
<body>
    <h1> Meu primeiro template usando Django! </h1>
</body>
</html>
```

O próximo passo é implementar a view que irá fazer a renderização do _template_ criado. Acesse o arquivo `views.py` dentro do app `events` e escreva a função que fará essa tarefa:

Copiar

```python
# events/views.py
from django.shortcuts import render


def index(request):
    return render(request, 'home.html')
```

Prontinho! A função acima usa o método `render` do Django para renderizar o _template_ passado como segundo parâmetro `home.html`. O primeiro parâmetro, _request_, representa a requisição feita pela pessoa que usa a aplicação.

Mas agora você pode estar se perguntando: _Como faço para invocar a função foi implementada?_ 🤔

A resposta é: através das rotas da nossa aplicação. A função criada será vinculada a uma das rotas da aplicação e, em seguida, serão incluídas nas rotas da aplicação no projeto.

Crie o arquivo `urls.py` dentro da aplicação `events` e nele escreva o código abaixo:

```python
# events/urls.py
from django.urls import path
from events.views import index


urlpatterns = [
    path("", index, name="home-page")
#   path("/rota-comentada", função-que-será-executada, name="nome-que-identifica-a-rota")
]
```

No código acima, uma lista de rotas (`urlpatterns`) foi definida e cada uma das rotas é definida através da função `path`, que recebe três parâmetros: o primeiro é o caminho para a rota em si (`""` indica a raiz da aplicação `https://localhost:8000/`), o segundo é a função que será executada quando a rota for acessada e o terceiro é o nome que identifica essa rota.

Agora, será necessário incluir as rotas da aplicação no projeto principal. Para isso, acesse o arquivo `urls.py` do projeto e faça a seguinte alteração:

```python
# event_manager/urls.py
  from django.contrib import admin
  from django.urls import path, include


  urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('events.urls'))
  ]
```

Com essas alterações você acabou de incluir as rotas da aplicação `events` no projeto `event_manager`, e fez isso usando o método `include` nativo do Django.

Acabou! 🎉🎉🎉 Execute o servidor e acesse a rota `http://localhost:8000/` para ver o template criado sendo renderizado.

> **Relembrando 🧠:** Para executar o servidor faça: `python3 manage.py runserver` no mesmo diretório em que se encontra o arquivo `manage.py`.

![Primeiro template funcionando!](https://content-assets.betrybe.com/prod/38495e8e-0cec-4af5-9166-8fce81c83e35-Primeiro%20template%20funcionando!.png)Primeiro template funcionando! 🤩|

Agora você aprendeu a renderizar templates usando o Django e é capaz de criar uma aplicação com diversos templates vinculando cada um à uma rota diferente. Não é incrível? 🤩

Mas calma, ainda temos muito o que aprender! 🤓

## Para fixar

## Exercício 1

Siga o mesmo passo a passo para criar a visualização de um novo template `about.html`:

1. Crie o arquivo `about.html` dentro do diretório `templates` da aplicação `events` e adicione o conteúdo HTML que você quer visualizar na tela.
2. Crie a função `about` no arquivo `views.py` da aplicação `events` e faça a renderização do template criado no passo anterior.
3. Crie uma nova rotas no arquivo `urls.py` da aplicação `events` e vincule a função `about` criada no passo anterior a essa nova rota.
4. Acesse a rota via browser!

> **De olho na dica 👀:** ao definir o nome de uma rota, para acesso via url, você não precisa incluir a barra no começo da rota! Use `about`, e não `/about`.

### Solução

1. Implementação do `about.html`:

Copiar

```html
<!--events/templates/about.html-->
<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Página Sobre</title>
</head>
<body>
    <h1> Aqui você conhecerá um pouco mais sobre a empresa! </h1>
</body>
</html>
```

2. Implementação do `views.py`:

```python
# events/views.py
from django.shortcuts import render


def index(request):
    return render(request, 'home.html')


def about(request):
    return render(request, 'about.html')    
```

3. Implementação do `urls.py`:

```python
# events/urls.py
from django.urls import path
from playlists.views import index, about


urlpatterns = [
    path("", index, name="home-page"),
    path("about", about, name="about-page")
#   path("/rota-comentada", função-que-será-executada, name="nome-que-identifica-a-rota")
]
```


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/a3e9b6ef-54ea-486d-a979-a6444e788bc0/lesson/b63cbefb-cd96-42c4-a7a9-c20d8bd55a82)

