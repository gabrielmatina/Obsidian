[[7 - Django]]

Você percebeu que o modelo `Event` herda de `models.Model`? Todas as classes que fazem essa mesma herança são usadas para representar tabelas do banco de dados. Pode não parecer importante, mas isso mostra o vínculo entre essa classe e a sua própria tabela no banco.

Além de representarem tabelas do banco, todas as classes que herdam de `models.Model` possuem um atributo chamado `objects`. Esse atributo permite a interação direta com o banco de dados usando a própria sintaxe do Python. Através desse atributo você pode criar novas entradas no banco, fazer consultas e até mesmo aplicar filtros em uma consulta. Já tivemos um gostinho disso no começo da seção.

Vamos ver na prática? 🤓

Execute o comando `python3 manage.py shell` no terminal, no mesmo diretório do arquivo `manage.py`. Esse comando abre o shell do Django já carregando suas configurações e permitindo usar o ORM do framework. Execute os comandos abaixo, linha a linha, para entender como podemos trabalhar com o banco de dados usando a sintaxe do Python:

```python
from events.models import Event # importa o modelo Event

Event.objects.all() # retorna todos os eventos do banco. Se você não criou nenhum, o retorno será um QuerySet vazio

Event.objects.create(title='Conferência de Django', description='Evento massa sobre Django', date='2023-09-29 12:00:00-03:00', location='São Paulo', event_type='C', is_remote=False) # cria um novo evento no banco

Event.objects.all() # retorna todos os eventos do banco. Agora o retorno será um QuerySet com um evento a mais

Event.objects.create(title='Django Workshop', description='Workshop que acontece semestralmente sobre Django', date='2024-10-02 15:30:00-03:00', location='Web', event_type='W', is_remote=True) # cria outro evento no banco

Event.objects.filter(is_remote=True) # retorna apenas os eventos do banco que são remotos

Event.objects.filter(event_type='W') # retorna apenas os eventos do banco que são workshops

Event.objects.filter(event_type='C', is_remote=False) # retorna apenas os eventos do banco que são conferências e presenciais, simultaneamente

Event.objects.filter(date__year=2024) # retorna apenas os eventos do banco que acontecem em 2024

Event.objects.filter(date__range=['2023-01-01', '2024-12-31']) # retorna apenas os eventos do banco que acontecem entre 2023 e 2024
```

São muitas as possibilidades! 🤯

Uma segunda maneira de fazer a inserção de elementos no banco de dados é através da instanciação e depois uso do método `save()`. Além disso, quando um objeto do modelo é instanciado podemos também acessar o método `delete()` para removê-lo do banco. Veja só:

```python
from events.models import Event # importa o modelo Event

Event.objects.all() # <QuerySet [<Event: Conferência de Django - 2023-09-29 15:00:00+00:00 - São Paulo>, <Event: Django Workshop - 2024-10-02 18:30:00+00:00 - Web>]>

evento_1 = Event(title='Django Devs', description='Pessoas fantásticas que usam Django se reunindo em um só lugar', date='2025-07-02 13:30:00-03:00', location='Web', event_type='W', is_remote=True) # instancia um novo evento

evento_1.save() # salva o evento no banco

evento_2 = Event(title='DjangoFest', description='Um festival um pouco menos legal que desenvolver com Django', date='2023-11-22 18:00:00-03:00', location='São Paulo', event_type='C', is_remote=False) # instancia outro evento

evento_2.save() # salva o evento no banco

Event.objects.all() # <QuerySet [<Event: Conferência de Django - 2023-09-29 15:00:00+00:00 - São Paulo>, <Event: Django Workshop - 2024-10-02 18:30:00+00:00 - Web>, <Event: Django Devs - 2025-07-02 16:30:00+00:00 - Web>, <Event: DjangoFest - 2023-11-22 21:00:00+00:00 - São Paulo>]>

evento_3 = Event(title='DJ ANGO', description='Conheça a mais nova sensação musical.', date='2027-06-19 20:00:00-03:00', location='São Paulo', event_type='C', is_remote=False) # instancia um evento idêntico ao anterior

evento_3.save() # salva o evento no banco

Event.objects.all() # <QuerySet [<Event: Conferência de Django - 2023-09-29 15:00:00+00:00 - São Paulo>, <Event: Django Workshop - 2024-10-02 18:30:00+00:00 - Web>, <Event: Django Devs - 2025-07-02 16:30:00+00:00 - Web>, <Event: DjangoFest - 2023-11-22 21:00:00+00:00 - São Paulo>, <Event: DJ ANGO - 2027-06-19 23:00:00+00:00 - São Paulo>]>

evento_3.delete() # remove o evento do banco

Event.objects.all() # <QuerySet [<Event: Conferência de Django - 2023-09-29 15:00:00+00:00 - São Paulo>, <Event: Django Workshop - 2024-10-02 18:30:00+00:00 - Web>, <Event: Django Devs - 2025-07-02 16:30:00+00:00 - Web>, <Event: DjangoFest - 2023-11-22 21:00:00+00:00 - São Paulo>]>
```

## Para fixar

## Exercício 2

Adicione mais 1 entrada no banco de dados, dentro da tabela `events` utilizando cada um dos métodos mostrados.

## Renderizando os eventos no _template_

Agora sim! Finalmente será possível renderizar os eventos no _template_. Para isso, precisamos passar todos os eventos que estão no banco como contexto para o _template_. Modifique o contexto da função `index` no arquivo `views.py` para que exista uma chave `events` cujo valor será uma consulta com todos os eventos que estão cadastrados no banco de dados:

```python
# events/views.py
from events.models import Event
from django.shortcuts import render


def index(request):
    context = {"company": "Trybe", "events": Event.objects.all()}
    return render(request, 'home.html', context)
```

Agora, adicione uma segunda tag `h2` no _template_ renderizando a chave `events`:

```html
<!-- events/templates/home.html -->
{% extends 'base.html' %}

{% block title %}
  Primeiro Template
{% endblock %}

{% block content %}
    <h1> Meu primeiro template usando Django! </h1>
    <h2> {{ company }} </h2>
    <h2> {{ events }} </h2>
{% endblock %}
```

![Print da página home com eventos renderizados](https://content-assets.betrybe.com/prod/5f572d81-7bf9-495d-9f3b-924c877724a5-Print%20da%20p%C3%A1gina%20home%20com%20eventos%20renderizados.png)Print da página home com eventos renderizados

A visualização dos eventos ainda não está muito amigável, não é mesmo? 🙁 Isso acontece porque o retorno de `Event.objects.all()` é uma consulta (`QuerySet`), que pode ter 0, 1, 2, … n elementos. Para tornar essa visualização mais amigável é necessário iterar pelos elementos que existem na consulta e renderizar cada um deles individualmente.

A iteração pode ser feita usando a tag de _template_ `{% for %}`, cuja sintaxe é muito semelhante à sintaxe do Python, com a diferença que você precisará indicar no _template_ onde o `for` se encerra com a `tag de _template_` `{% endfor %}`:

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
         <p> {{ event }} </p>
     {% endfor %}
{% endblock %}
```

A sintaxe acima permite que, dentro do _template_, seja feita uma iteração sobre cada um dos eventos presentes no contexto. Para cada elemento da iteração, é criada uma nova tag `p` renderizando aquele evento em específico.

![Print da página home com eventos depois da iteração](https://content-assets.betrybe.com/prod/5f572d81-7bf9-495d-9f3b-924c877724a5-Print%20da%20p%C3%A1gina%20home%20com%20eventos%20depois%20da%20itera%C3%A7%C3%A3o.png)Print da página home com eventos depois da iteração

Já imaginou o que aconteceria se a consulta não tivesse nenhum elemento? 🤔 A resposta é: nada! Em uma consulta vazia não haverá nenhum evento para renderizar e você deve concordar que isso também não é muito amigável! 😅

Para resolver isso vamos usar a `tag de _template_` `{% empty %}` dentro do `for`, ela indicará o que queremos mostrar na tela caso não exista nenhum elemento na consulta que estamos fazendo:

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
       <p> {{ event }} </p>
    {% empty %}
       <p> Não existem eventos cadastrados </p>
    {% endfor %}
{% endblock %}
```

Agora sim! 🎉🎉🎉 Ainda da para melhorar um pouquinho a visualização dos eventos, mas espere um pouco para fazer isso. Antes, vamos à implementação da visualização dos detalhes de um evento específico. 🤓

### Solução

Acesse o shell do Django `python3 manage.py shell`;

Usando o método `create` do atributo `objects`:

```python
from events.models import Event
Event.objects.create(title='Campus Party Brasil', description='Um dos maiores eventos de tecnologia, inovação e empreendedorismo do Brasil.', date='2023-07-25 08:00:00-03:00', location='Brasília', event_type='O', is_remote=False)
```

Usando o método de instanciação de um objeto:

```python
from events.models import Event
evento = Event(title='Agile Brazil', description='Uma conferência dedicada à metodologia ágil de desenvolvimento de software.', date='2023-10-09 10:00:00-03:00', location='São Paulo', event_type='C', is_remote=False)
evento.save()
```


### Para fixar

### Exercício 3

Remova todas as entradas que estão no banco para conseguir visualizar a mensagem dizendo que não existem eventos cadastrados no banco.

#### Solução

Você pode fazer a remoção dos elementos ou pelo terminal ou pelo painel administrador do Django, aqui, mostraremos como fazer pelo terminal;

Acesse o shell do Django `python3 manage.py shell`;

Faça uma iteração por todos os elementos do banco e para cada um deles use o método `delete()`.

```python
from events.models import Event


for event in Event.objects.all():
    event.delete()
```


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/a3e9b6ef-54ea-486d-a979-a6444e788bc0/lesson/1e9bccb2-c03d-441a-ae81-5c14f72860b2)

