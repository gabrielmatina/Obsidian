[[7 - Django]]

Para conseguir criar o _template_ de detalhes do evento, será necessário criar uma nova função no arquivo `views.py`. Essa função renderizará o novo _template_ `details.html` que será criado dentro da pasta `_templates_`. Além disso, na função a ser implementada, é necessário passar à _view_ o contexto com o evento específico que será renderizado no _template_.

Mas como o _template_ saberá qual evento será renderizado? 😱 Resposta: Será recebido um parâmetro na função que permitirá o resgate do evento e sua renderização. No modelo `Event`, esse parâmetro é o `id`, chave primária do evento. Observe a implementação:

Copiar

```python
# events/views.py
from events.models import Event
from django.shortcuts import render
from django.shortcuts import get_object_or_404


def event_details(request, event_id):
    event = get_object_or_404(Event, id=event_id)
    return render(request, 'details.html', {'event': event})
```

```html
<!-- events/templates/details.html -->
{% extends 'base.html' %}

{% block title %}
    {{ event.title }}
{% endblock %}


{% block content %}

    <h1>{{ event.title }}</h1>

    <p>{{ event.description }}</p>

    <p>{{ event.date|date }} - {{ event.location }}</p>

    {% if event.is_remote %}
        <p> Evento remoto </p>
    {% else %}
        <p> Evento presencial </p>
    {% endif %}

{% endblock %}
```

Na função `event_details`, o parâmetro `event_id` será recebido e utilizado para resgatar o evento específico que se quer renderizar. Esse resgate é feito com o uso da função `get_object_or_404()`, essa função recebe dois parâmetros: o primeiro é o modelo a ser resgatado e o segundo indica a busca a ser feita. No exemplo acima, é buscado por um `Event` cujo `id` é igual ao `event_id` recebido como parâmetro. Caso o evento não seja encontrado, será levantada uma exceção do tipo `Http404`.

Ao passar a chave `event` no contexto, é possível acessá-la dentro do _template_ e usá-la para recuperar o evento alvo com todos os seus atributos. Esses atributos podem ser acessados dentro do _template_ através da sintaxe `{{ event.title }}`, por exemplo. Assim, é possível montar um _template_ genérico para a renderização de qualquer evento, desde que ele seja passado no contexto. 🤯

Perceba também que foi utilizada a sintaxe condicional com a _Tag de Template_ `{% if %}` `{% else %}` e, assim como no `{% for %}`, é necessário indicar o fim da condição com `{% endif %}`.

Você deve ter notado o `{{ event.date|date }}` no _template_, né? A sintaxe para o uso de filtros de _template_ é composta da variável à qual quer se aplicar o filtro seguida por um `|` e logo depois o nome do filtro. O filtro, nesse caso, é como uma máscara formatadora: ela pega a informação e ajusta a forma como ela será exibida. Nesse exemplo foi usado o filtro de data, para que a formatação da data seja no padrão `DD de MMMMM de AAAA`.

É possível, naturalmente, aplicar outras configurações para mostrar a data em outro formato. Além do filtro de data, existem outros filtros já implementados e que podem ser acessados em todos os templates como `first`, `last`, `lower`, `upper`, `length`, `random`, `slugify`, etc. Para saber mais sobre os filtros disponíveis, acesse a [documentação oficial.](https://docs.djangoproject.com/en/4.2/ref/templates/builtins/#built-in-filter-reference).

O código que foi apresentado ainda não funciona: falta vincular a função criada com uma rota específica, dentro do arquivo `urls.py`. Será nessa rota em que haverá a indicação de que o `event_id` será passado como parâmetro. Veja a implementação:

```python
# events/urls.py
from django.urls import path
from events.views import index, event_details, about


urlpatterns = [
    path("", index, name="home-page"),
    path("about", about, name="about-page"),
    path("events/<int:event_id>", event_details, name="details-page"),
#   path("/rota-comentada", função-que-será-executada, name="nome-que-identifica-a-rota")
]
```

A rota `events/<int:event_id>` indica que a rota `events/` será seguida de um número inteiro, que representa um `event_id` e que será passado como parâmetro para a função `event_details`. Vale lembrar que o nome da rota é importante para que seja possível acessá-la dentro do _template_.

## Para fixar

## Exercício 4

Adicione um novo evento e acesse a sua rota de detalhes para comprovar que tudo está funcionando.

## Conectando a página inicial com a página de detalhes

A página de detalhes de um evento específico já funciona, acesse a rota `events/<int:event_id>` e veja! Entretanto, ainda não é possível acessá-la de maneira rápida e eficiente através da página inicial. Para adaptar a `home.html` , será necessário que você crie um link de redirecionamento para a página de detalhes de cada evento. Tarefa fácil ao usarmos a tag de template `url` que permite criar um link absoluto, veja:

```html
<!-- events/templates/home.html -->
{% extends 'base.html' %}

 {% block title %}
   Primeiro Template
 {% endblock %}

 {% block content %}
     <h1> Meu primeiro template usando Django! </h1>
     <h2> {{ company }} </h2>
    {% for event in events %}
       <p> <a href="{% url 'details-page' event.id %}"> {{ event }} </a> </p>
    {% empty %}
        <p> Não existem eventos cadastrados </p>
    {% endfor %}
{% endblock %}
```

A _tag de template_ `{% url %}` pode ser usada quando é necessário fazer a chamada de uma rota específica que já foi implementada e tem uma identificação no arquivo `urls.py`. No exemplo acima, a _tag de template_ é usada para invocar a rota identificada como `details-page`, e, como essa rota necessita do `id` do evento como parâmetro, ele é passado logo em seguida com `event.id`. Assim, ao adicionar a tag `a` cujo atributo `href` aponta para a rota de detalhes já implementada, é feito o vínculo entre as rotas. Agora, ao executar a aplicação você deve ter algo como:

![Print da página home com rotas vinculadas](https://content-assets.betrybe.com/prod/aa4e5a53-fc21-4673-aa9a-9d25d6df689f-Print%20da%20p%C3%A1gina%20home%20com%20rotas%20vinculadas.png)Print da página home com rotas vinculadas

## Lidando com exceções

O que será que acontece se uma pessoa tenta acessar uma página de evento que não existe? Tipo a página `http://127.0.0.1:8000/events/99999` 😱 A resposta para essa pergunta é: como durante a implementação a função `get_object_or_404` foi usada, automaticamente, se não for possível resgatar o evento com `id` informado, será renderizada uma página padrão do Django indicando uma resposta 404, _Not Found_. Contudo, é possível personalizar, tratar essa exceção e exibir a página que desejar, veja só:

```python
# events/views.py
from django.http import Http404
from django.shortcuts import render, get_object_or_404
from events.models import Event


def event_details(request, event_id):
    try:
        event = get_object_or_404(Event, id=event_id)
        return render(request, 'details.html', {'event': event})
    except Http404:
        return render(request, '404.html')
```

Daí, basta implementar o template `404.html` que deverá ser criado junto aos demais templates:

```html
<!-- events/templates/404.html -->
{% extends 'base.html' %}

{% block title %}
    Página não encontrada
{% endblock %}

{% block content %}
    <h1> 404 - Página não encontrada </h1>
    <h2> Desculpe, mas o evento não foi encontrado </h2>
    <p><a href="{% url 'home-page' %}"> Volte a página inicial </a></p>
{% endblock %}
```

Agora, ao tentar acessar uma página de evento que não existe, a exceção `Http404` levantada pela função `get_object_or_404` será tratada pelo try/except e resulta na renderização da página `404.html`. Na implementação da página foi usada a mesma sintaxe de herança de _templates_, e ao final do bloco content foi adicionado um link para a página inicial, usando novamente a `tag de _template_` `{% url %}` vinculando assim uma rota previamente identificada no `urls.py` (_home-page_).

### Solução

Você pode fazer a adição do evento pelo terminal ou pelo painel administrador do Django, aqui, mostraremos como fazer pelo terminal:

Acesse o shell do Django `python3 manage.py shell`;

Use o método `create` do atributo `objects` da classe `Event` para fazer a adição do evento.

```python
from events.models import Event

Event.objects.create(title='Evento teste dos detalhes', description='Aprendendo sobre como criar uma rota de detalhamento de um evento', date='2026-01-01 20:30:00-03:00', location='Acre', event_type='W', is_remote=True)

evento = Event.objects.filter(location='Acre').first()

evento.id # Esse comando irá retornar o id do evento que foi criado, use-o para acessar a rota de detalhes do evento.
```

Faça o acesso à rota de detalhes do evento criado e veja se tudo está funcionando corretamente. `http://localhost:8000/events/<id-retornado-do-comando-anterior>`

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/a3e9b6ef-54ea-486d-a979-a6444e788bc0/lesson/a94cb0a9-f4bf-41aa-a024-b7567c3ad81c)
