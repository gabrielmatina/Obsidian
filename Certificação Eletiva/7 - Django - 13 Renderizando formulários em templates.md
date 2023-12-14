[[7 - Django]]

Neste momento, você já sabe como fazer para criar registros no banco de dados e também já sabe checar se um formulário é válido ou não. Chegou a hora de unir esses dois conhecimentos!

> **Relembrando 🧠:** para criar um novo registro no banco, você pode usar o método `.create()` do atributo `objects`, do modelo em questão.

## Novo registro a partir de um formulário

Uma vez que você já possui um formulário que tem dados válidos, é preciso repassar esses dados para o modelo e, assim, criar o novo registro no banco. Para isso, depois de usar o método `is_valid()` para checar a integridade dos dados passados, você pode usar o atributo `cleaned_data` para que um dicionário com todos os dados sejam retornados para você. Esses dados, agora já validados, podem ser usados para criar um novo registro no banco.

O passo a passo abaixo demonstra como é possível fazer isso e pode ser executado no terminal interativo do Django:

```python
from playlists.forms import CreateMusicForm
from playlists.models import Music

form = CreateMusicForm({"name":"Be brave, Dev", "recorded_at":"2023-06-05", "length_in_seconds":180})

if form.is_valid():
    data = form.cleaned_data # data será igual à {"name":"Be brave, Dev", "recorded_at":"2023-06-05", "length_in_seconds":180}
    Music.objects.create(**data) # criando um novo registro no banco com os dados do formulário
    # Music.objects.create(**data) é o mesmo que Music.objects.create(name="Be brave, Dev", recorded_at="2023-06-05", length_in_seconds=180)
```

Você pode apertar a tecla `enter` duas vezes para sair do escopo da condição (`if`) que acabamos de criar. 😉

> **Anota aí 📝:** A sintaxe `**data` é do Python e é uma desestruturação para passar cada um dos pares chave e valor, individualmente, como parâmetros.

Prontinho! Conseguimos conectar os conhecimentos sobre criação de registros no banco de dados e formulários. 🤩 O próximo passo agora é receber os dados direto da requisição e, a partir deles, criar o novo registro no banco. Vamos lá?

## Formulários e templates

Você já sabe que podemos renderizar variáveis passadas como contexto para um template. Vamos explorar esse recurso?

Crie o diretório `templates` dentro da aplicação `playlists` e nele crie os dois primeiros templates `base.html` e `music.html`. Implemente a estrutura para herança de templates e, no arquivo `music.html`, renderize a variável `form` dentro do bloco `content`.


```html
<!-- playlists/templates/base.html -->

<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>{% block title %} {% endblock %}</title>
</head>
<body>
    {% block content %} {% endblock %}    
</body>
</html>
```

```html
<!-- playlists/templates/music.html -->

{% extends 'base.html' %}

{% block title %}
    Formulário para Nova Música
{% endblock %}

{% block content %}
    {{form}}
{% endblock %}
```

Implemente a primeira função no arquivo `views.py` com nome de `music` que irá renderizar `music.html`. Passe no contexto uma instância do formulário `CreateMusicForm` como valor da chave `form`.

```python
# playlists/views.py

from django.shortcuts import render
from playlists.forms import CreateMusicForm


def music(request):
    form = CreateMusicForm()
    context = {"form": form}
    return render(request, "music.html", context)
```

Crie o arquivo `urls.py`, dentro da aplicação `playlists`. Nele, configure a rota para a função `create_music` que você acabou de criar.

```python
# playlists/urls.py

from django.urls import path
from playlists.views import music


urlpatterns = [
    path("musics/", music, name="musics-page"),
]
```

Por fim, inclua a rota da aplicação no arquivo `urls.py` **do projeto**.

```diff
# playlist_manager/urls.py

from django.contrib import admin
+ from django.urls import path, include


urlpatterns = [
    path('admin/', admin.site.urls),
+     path("", include("playlists.urls"))
]
```

Execute a aplicação (`python3 manage.py runserver`) e veja como o formulário é renderizado na [tela](http://localhost:8000/musics/). 😱

![Print da página home com formulário renderizado](https://content-assets.betrybe.com/prod/96e99cca-8ea5-49f9-9722-6bd9cfb47988-Print%20da%20p%C3%A1gina%20home%20com%20formul%C3%A1rio%20renderizado.png)
Print da página home com formulário renderizado

A instância do formulário é convertida para um conjunto de tags HTML que renderizam o formulário criado por você. Você pode alterar a forma como esse formulário é renderizado por meio de alguns atributos com _layouts_ diferentes. Usaremos aqui o `as_p`:

```diff
<!-- playlists/templates/music.html -->

{% extends 'base.html' %}

{% block title %}
    Formulário para Nova Música
{% endblock %}

{% block content %}
+     {{form.as_p}}
{% endblock %}
```

Experimente trocar o `as_p` por `as_div` e `as_ul`, inspecione o conteúdo HTML ao usar cada um e veja a diferença entre eles!

Você deve ter notado, também, que embora o formulário esteja lá, não temos nenhum botão para enviar os dados. Veremos, após o exercício, como incluí-lo 😉

### Para fixar

### Exercício 3

Repita o procedimento para renderização do formulário no template, mas usando o formulário implementado por você: `CreateSingerForm`. Use o template `singer.html` para essa renderização e vincule a função `singer` a rota `/singers`.

#### Solução

**Primeiro Passo**: Crie o template `singer.html` e adicione o seguinte conteúdo à ele:

```html
<!-- playlists/templates/singer.html -->

{% extends 'base.html' %}

{% block title %}
    Formulário para Nova Pessoa Cantora
{% endblock %}

{% block content %}
    {{form.as_p}}
{% endblock %}
```

**Segundo Passo**: Crie a função `singer` no arquivo `views.py`:

```python
# playlists/views.py
from playlists.forms import CreateSingerForm
from django.shortcuts import render


def singer(request):
    form = CreateSingerForm()
    context = {"form": form}
    return render(request, "singer.html", context)
```

**Terceiro Passo**: Crie a rota para a função `singer` no arquivo `urls.py` da aplicação `playlists`:

```diff
# playlists/urls.py
from django.urls import path
+ from playlists.views import music, singer


urlpatterns = [
    path("musics/", music, name="musics-page"),
+     path("singers/", singer, name="singers-page"),
]
```

**Quarto Passo**: Execute o servidor `python3 manage.py runserver` e acesse a rota `localhost:8000/singers` para ver o formulário renderizado.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/18f78a76-c572-41f0-b465-dcd9000c288d)
