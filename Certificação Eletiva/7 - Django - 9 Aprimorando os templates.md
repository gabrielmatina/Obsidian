[[7 - Django]]

Pra finalizar a nossa aplicação, que tal acrescentarmos **estilo**, com CSS, às nossas páginas? Com isso feito, nossa aplicação já estará pronta pra ser usada!

Primeiro, vamos fazer uma alteração no nosso template `home.html` para facilitar a estilização da página. Vamos incluir um pouco mais de estrutura HTML para termos com o que trabalhar no CSS - além de incluir uma lógica para exibição de imagens dos eventos!

```html
<!-- events/templates/home.html -->
 {% extends 'base.html' %}
 {% load static %}

 {% block title %}
   Primeiro Template
 {% endblock %}

 {% block content %}
     <h1> Eventos {{ company }} </h1>
    {% for event in events %}
        <a href="{% url 'details-page' event.id %}"> 
            <div>
              {% if event.image %}
                <img src="{% static event.image.url %}" alt="Imagem sobre o evento" height="50">
              {% endif %}
                <h3> {{ event.title }} </h3>
                <p> {{ event.date }} </p>
                <p> {{ event.location }} </p>
            </div>
        </a>
    {% empty %}
        <p> Não existem eventos cadastrados </p>
    {% endfor %}
 {% endblock %}
```

> **De olho na dica 👀:** Se você tiver algum registro no banco de eventos que não possua imagem, a tag `img` não será renderizada em razão da condição imposta.

Use o painel admin para criar alguns eventos de maneira que você consiga fazer o upload de uma imagem que represente o evento. Para criar uma conta admin você pode executar `python3 manage.py createsuperuser` no mesmo diretório em que se encontra o arquivo `manage.py`. Além disso, também será necessário fazer o registro do modelo `Event` dentro do site, usando o arquivo `admin.py`:

```python
from django.contrib import admin
from events.models import Event


admin.site.site_header = 'Event Manager Admin Panel'
admin.site.register(Event)
```

![Print da página admin ao adicionar um evento com todos os campos](https://content-assets.betrybe.com/prod/7a8e2922-211b-4e1d-95a6-9264f9651ef0-Print%20da%20p%C3%A1gina%20admin%20ao%20adicionar%20um%20evento%20com%20todos%20os%20campos.png)Print da página admin ao adicionar um evento com todos os campos

Mesmo adicionando um evento com imagem você ainda não será capaz de visualizar as imagens. Isso acontece porque ainda não fizemos a configuração de como vamos servir os arquivos estáticos do projeto.

![Print da página inicial sem a imagem aparecendo](https://content-assets.betrybe.com/prod/7a8e2922-211b-4e1d-95a6-9264f9651ef0-Print%20da%20p%C3%A1gina%20inicial%20sem%20a%20imagem%20aparecendo.png)Print da página inicial sem a imagem aparecendo

## Arquivos estáticos

O primeiro passo para fazer a configuração é instalar dois pacotes que ajudarão com essa tarefa:

```bash
pip install whitenoise # Serve os arquivos estáticos a partir de um diretório
pip install django-static-autocollect # Coleta os arquivos estáticos e os coloca em um diretório
```

Faça as modificações necessárias no arquivo `settings.py`:

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
    'events',
+   'static_autocollect'
 ]

 MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
+   'whitenoise.middleware.WhiteNoiseMiddleware',
    'django.contrib.sessions.middleware.SessionMiddleware',
    'django.middleware.common.CommonMiddleware',
    'django.middleware.csrf.CsrfViewMiddleware',
    'django.contrib.auth.middleware.AuthenticationMiddleware',
    'django.contrib.messages.middleware.MessageMiddleware',
    'django.middleware.clickjacking.XFrameOptionsMiddleware',
 ]

 ...

+ MEDIA_URL = ''
+ MEDIA_ROOT = BASE_DIR / 'media'

 STATIC_URL = 'static/'
+ STATIC_ROOT = BASE_DIR / 'staticfiles'

+ STATICFILES_DIRS = [
+     str(BASE_DIR / 'media/'),
+ ]

+ STORAGE = {
+    "default": {
+        "BACKEND": "django.core.files.storage.FileSystemStorage",
+    },
+    "staticfiles": {
+        "BACKEND": "whitenoise.storage.CompressedStaticFilesStorage",
+    }
+ }

+ WHITE_NOISE_AUTOREFRESH = True
```

Com essas modificações estamos:

- instalando o pacote `static_autocollect` no projeto;
- adicionando o pacote `whitenoise` na lista de middlewares;
- definindo o caminho relativo onde se encontra o diretório `media` em `MEDIA_URL`;
- definindo o caminho absoluto em `MEDIA_ROOT` e que será usado como caminho base para o upload de imagens vindas das pessoas usuárias;
- definindo o caminho absoluto em `STATIC_ROOT` e que será usado pelo `whitenoise` para servir os arquivos estáticos;
- definindo uma lista de caminhos em `STATICFILES_DIRS` que serão usados pelo `static_autocollect` para coletar os arquivos estáticos e direcionar para `STATIC_ROOT`;
- definindo o comportamento de armazenamento do `whitenoise`;
- definindo que o `whitenoise` deve atualizar os arquivos estáticos automaticamente.

Use o comando `python3 manage.py watch_static & python3 manage.py runserver` para executar o servidor e o `static_autocollect` em paralelo. Agora, a próxima adição de registro que for feita já será refletida na página inicial.

> **De olho na dica 👀:** A _tag de template_ `static` serve para indicar o caminho relativo do arquivo estático e junto com os `whitenoise` e `static_autocollect`, possibilita servir os arquivos estáticos. **Anota aí 📝:** A metodologia mais comum para servir arquivos estáticos é separar e servi-los externamente, [leia mais sobre isso](https://whitenoise.readthedocs.io/en/latest/django.html#use-a-content-delivery-network).

![Print da página inicial com a imagem aparecendo](https://content-assets.betrybe.com/prod/7a8e2922-211b-4e1d-95a6-9264f9651ef0-Print%20da%20p%C3%A1gina%20inicial%20com%20a%20imagem%20aparecendo.png)Print da página inicial com a imagem aparecendo

Com um pouco de estilização, você pode deixar sua aplicação mais apresentável. Você pode usar CSS puro ou qualquer framework de CSS que desejar, fica à sua escolha e como se sentir mais confortável. A seguir temos um exemplo de estilização para a página inicial, ele foi feito usando o Tailwind CSS e contém exatamente as mesmas `tags` que foram apresentadas até então.

![Print da página inicial com estilização](https://content-assets.betrybe.com/prod/7a8e2922-211b-4e1d-95a6-9264f9651ef0-Print%20da%20p%C3%A1gina%20inicial%20com%20estiliza%C3%A7%C3%A3o.png)Print da página inicial com estilização

Você pode fazer o download dos templates estilizados: [`base.html`](https://lms-assets.betrybe.com/lms/base.html) e [`home.html`](https://lms-assets.betrybe.com/lms/home.html). Nesse exemplo foi usado o CDN do Tailwind CSS, mas você poderia registrar o seu próprio arquivo CSS no template `base.html`.


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/a3e9b6ef-54ea-486d-a979-a6444e788bc0/lesson/e6826307-9406-432a-873c-448375ef2b3a)
