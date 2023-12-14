[[7 - Django]]

Se você inspecionar o conteúdo HTML do formulário que está renderizado no _template_, verá que, apesar de chamarmos de formulário, não há _tag_ `form` alguma. Isso é um problema, pois queremos enviar os dados inseridos para algum local, então vamos dar um jeito nisso!

O primeiro passo é justamente envolver o formulário em uma _tag_ `form`, indicando o método HTTP e ação que será realizada quando o formulário for submetido.

Além disso, duas outras coisas são necessárias: adicionar uma tag `input` capaz de submeter o formulário (`type: submit`) e adicionar `{% csrf_token %}` logo após a _tag_ `form`.

A _tag_ de _template_ `{% csrf_token %}` é uma estratégia de segurança do _framework_ contra _Cross-site Request Forgery_. Se quiser ler mais sobre esse tipo de ataque, visite esse [site aqui](https://www.ibm.com/docs/pt-br/sva/10.0.0?topic=configuration-prevention-cross-site-request-forgery-csrf-attacks).

```diff
<!-- playlists/templates/music.html -->

{% extends 'base.html' %}

{% block title %}
    Formulário para Nova Música
{% endblock %}

{% block content %}
+    <form method="post" action="">
+        {% csrf_token %}
        {{form.as_p}}
+        <input type="submit" value="Submeter formulário">
+    </form>
{% endblock %}
```

Neste ponto, você já deve ser capaz de submeter o formulário, contudo, esses dados não estão indo para lugar algum. É preciso indicar qual função da camada `view` receberá os dados submetidos pela requisição (`request`).

O parâmetro `request` possui atributos e métodos. Todos os dados que são submetidos por meio de formulários podem ser visualizados no atributo `POST`, na forma de um dicionário. Entretanto, se os dados forem enviados no `body` da requisição, eles podem ser acessados no atributo `body` na forma de _bytes_. Além disso, também é possível identificar o método HTTP utilizado por meio do atributo `method`. Logo mais veremos isso na nossa aplicação!

Adicione a _tag_ de template `{% url %}` para invocar a rota `musics-page` no template `music.html`:

```diff
<!-- playlists/templates/music.html -->

{% extends 'base.html' %}

{% block title %}
    Formulário para Nova Música
{% endblock %}

{% block content %}
+    <form method="post" action="{% url 'musics-page' %}">
        {% csrf_token %}
        {{form.as_p}}
        <input type="submit" value="Submeter formulário">
    </form>
{% endblock %}
```

Agora, ao submeter o formulário, você está enviando os dados submetidos para a função `music` que, por sua vez, renderiza novamente o template `music.html`.

Para conseguir visualizar no terminal os dados que estão sendo submetidos e o _body_ da requisição, adicione os _prints_ abaixo à função `music` e refaça a submissão do formulário:

```diff
# playlists/views.py

from django.shortcuts import render
from playlists.forms import CreateMusicForm


def music(request):
+    print(request.POST)
+    print(request.body)
+    print(request.method)
    form = CreateMusicForm()
    context = {"form": form}
    return render(request, "music.html", context)
```

Parabéns, você conseguiu passar dados de um _template_ para uma função da camada `view`! 🎉 O próximo passo é usar esse formulário para validar os dados enviados e, em seguida, criar um novo registro no banco!

> **De olho na dica 👀:** sempre que você quiser inspecionar métodos e atributos de uma variável, você pode usar o método `dir`, nativo do Python. Acrescente `print(dir(request))` aos prints da função e veja o que é mostrado no terminal ao submeter o formulário.

## Criando o novo registro

Iremos implementar uma nova função chamada `index`, que recebe no contexto todos os objetos `Music`. Também iremos renderizar um novo template `home.html`, no qual serão colocados na tela todos os objetos criados e um link de redirecionamento para a função `music`.

A implementação de ambos pode ser observada abaixo:

```python
# playlists/views.py

# ...
from playlists.models import Music


def index(request):
    context = {"musics": Music.objects.all()}
    return render(request, "home.html", context)

# ...
```

```html
<!-- playlists/templates/home.html -->

{% extends 'base.html' %}

{% block title %}
    Home Page
{% endblock %}

{% block content %}
    {% for music in musics %}
        <p>{{music}}</p>
    {% endfor %}

    <a href="{% url 'musics-page' %}">Criar nova música</a>
{% endblock %}
```

Registre a função `index` no arquivo `urls.py`:

```diff
# playlists/urls.py

from django.urls import path
+ from playlists.views import music, singer, index


urlpatterns = [
+    path("", index, name="home-page"),
    path("musics/", music, name="musics-page"),
    path("singers/", singer, name="singers-page"),
]
```

Para finalizar o processo de criação, basta implementar a lógica da instanciação e validação de um formulário e, se os dados forem válidos, adicionar o novo registro no banco e redirecionar para o template inicial `home.html`. Usaremos o método `redirect` e passaremos como parâmetro a identificação da rota desejada: `home-page`.

É preciso lembrar que esse processo completo só deve acontecer caso o método HTTP da requisição seja POST. Vale lembrar também que o próprio formulário passado como contexto para o _template_ é capaz de ligar com erros, caso existam.

Observe a implementação da função `music`:

```python
# playlists/views.py

from django.shortcuts import render, redirect
from playlists.forms import CreateMusicForm, CreateSingerForm
from playlists.models import Music


def music(request):
    form = CreateMusicForm()

    if request.method == "POST":
        form = CreateMusicForm(request.POST)

        if form.is_valid():
            Music.objects.create(**form.cleaned_data)
            return redirect("home-page")

    context = {"form": form}

    return render(request, "music.html", context)
```

Agora sim! Você conseguiu criar um novo registro no banco de dados por meio de um formulário! 🎉

Execute o servidor e veja funcionando! `python3 manage.py runserver`

### Para fixar

### Exercício 4

Refaça o procedimento explicado para a criação de objetos do tipo `Music` para o tipo `Singer` com a função `singer`. Adicione ao template `home.html` e o link de redirecionamento para `singers-page`.

#### Solução

**Primeiro Passo**. Adicione a tag `form` no template `singer.html`, além disso, também insira a tag de template que direcionará para a função de criação, o token de segurança, o método http POST e o input de submissão do formulário:

```html
<!-- playlists/templates/singer.html -->

{% extends 'base.html' %}

{% block title %}
    Formulário para Nova Pessoa Cantora
{% endblock %}

{% block content %}
    <form method="post" action="{% url 'singers-page' %}">
        {% csrf_token %}
        {{form.as_p}}
        <input type="submit" value="Submeter formulário">
    </form>
{% endblock %}
```

**Segundo Passo**. Altere a função `singer` no arquivo `views.py`:

```python
# playlists/views.py

from django.shortcuts import render, redirect
from playlists.forms import CreateSingerForm
from playlists.models import Music, Singer


def singer(request):
    form = CreateSingerForm()

    if request.method == "POST":
        form = CreateSingerForm(request.POST)

        if form.is_valid():
            Singer.objects.create(**form.cleaned_data)
            return redirect("home-page")

    context = {"form": form}

    return render(request, "singer.html", context)
```

**Terceiro Passo**. Adicione o link de redirecionamento ao template `home.html`:

```html
<!-- playlists/templates/home.html -->
{% extends 'base.html' %}

{% block title %}
    Home Page
{% endblock %}

{% block content %}
    {% for music in musics %}
        <p>{{music}}</p>
    {% endfor %}

    <a href="{% url 'musics-page' %}">Criar nova música</a>
    <a href="{% url 'singers-page' %}">Criar nova pessoa cantora</a>
{% endblock %}
```

**Quarto Passo**. Execute o servidor e veja o resultado! `python3 manage.py runserver` e acesse `localhost:8000`

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/424504d5-70a3-46fc-aa9d-623f00ea399e)
