[[7 - Django]]

Para deixar as aplicações ainda mais poderosas, é possível usar o DTL para criar _templates_ que possuam lógica de programação e que também sejam capazes de exibir informações de uma forma dinâmica. Para isso, falaremos sobre sintaxe de _templates_, herança, variáveis, filtros e tags.

## Herança de _templates_

O Django permite que não se crie toda a estrutura de HTML para cada um dos _templates_. A DTL (_Django Template Language_) permite que se crie um template base que contém a estrutura essencial do HTML e lacunas intencionais - com cada template filho preenchendo as lacunas com o próprio conteúdo. Esse mecanismo é chamado de _Herança de templates_. Como exemplo, relembre o template `home.html` que criamos:

```html
<!-- events/templates/home.html -->
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

Para ver a herança acontecendo na prática, copie todo o conteúdo desse arquivo e cole dentro de um novo arquivo HTML chamado `base.html` dentro do diretório `events/templates`.

Substitua, em seguida, o conteúdo da tag `title` (_Primeiro Template_) por `{% block title %} {% endblock %}`, além disso, também substitua a linha da tag `h1` por `{% block content %} {% endblock %}`. Ao final dessas alterações o arquivo `base.html` fica assim:

```html
<!-- events/templates/base.html -->
<!DOCTYPE html>
<html lang="pt-BR">
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

A sintaxe `{% %}` indica que está sendo usada uma **Tag de template** do DTL. Ela é a **lacuna** que mencionamos mais cedo - um template filho irá preenchê-la. Nesse caso, usamos a tag `block`. Existem muitas **Tags de template** já implementadas no DTL. Você pode conferir todas as tags nativas do DTL na [documentação oficial](https://docs.djangoproject.com/pt-br/4.2/ref/templates/builtins/).

Ao fazer essas alterações, foram criados blocos vazios que poderão ser preenchidos por aqueles _templates_ que herdarem o arquivo `base.html`. Acima, criamos dois blocos - um chamado _title_ e outro chamado _content_ - para escrever o título da página que será exibida e para colocar todo o conteúdo HTML que se quer exibir, respectivamente.

Para usar a herança de _template_, faça o seguinte:

1. Vá no template filho e inclua no seu cabeçalho a seguinte sintaxe: `{% extends 'base.html' %}`, onde se usa a palavra reservada `extends` seguida de qual _template_ se quer herdar.
2. Modifique o template filho, por exemplo o `home.html`, criando os blocos com os mesmos nomes daqueles criados no _template_ herdado de acordo com a sintaxe abaixo.

> **Anota aí 📝:** para que a herança aconteça é obrigatório que o `{% extends 'nome-do-template.html' %}` seja a primeira tag de template que aparece no arquivo.

```html
<!-- events/templates/home.html -->
{% extends 'base.html' %}

{% block title %}
  Primeiro Template
{% endblock %}

{% block content %}
  <h1> Meu primeiro template usando Django! </h1>
{% endblock %}
```

Note que, ao invés de toda a estrutura base do HTML, você inclui as tags do template base e as preenche com o HTML que quiser. Ao rodar sua aplicação, verá que tudo continua funcionando, ou seja, a herança foi feita com sucesso! 👏

## Criando o _model_ `Event`

Antes de exibir a lista de eventos no _template_, é importante definir o modelo que será usado para representá-los. Eis ele abaixo:

```python
# events/models.py
from django.db import models


class Event(models.Model):
    TYPE_CHOICES = (
        ('C', 'Conference'),
        ('S', 'Seminar'),
        ('W', 'Workshop'),
        ('O', 'Other'),
    )

    title = models.CharField(max_length=200)
    description = models.TextField()
    date = models.DateTimeField()
    location = models.CharField(max_length=200)
    event_type = models.CharField(max_length=50, choices=TYPE_CHOICES)
    is_remote = models.BooleanField(default=False)
    image = models.ImageField(upload_to='events/img', blank=True)

    def __str__(self): # O método __str__ é sobrescrito para indicar como será a visualização do objeto
        return f'{self.title} - {self.date} - {self.location}' # Título do evento - Data - Local
```

A tabela `event` ao ser criada no banco terá 8 colunas, sendo elas:

- `id`: inteiro e chave primária única pro evento (que não precisa ser explicitamente declarado no modelo);
- `title`: texto com no máximo 200 caracteres;
- `description`: texto sem limitação de caracteres;
- `date`: data e hora do evento;
- `location`: texto com no máximo 200 caracteres;
- `event_type`: texto com no máximo 50 caracteres e que só pode assumir os valores `C`, `S`, `W` ou `O` (ao usar o parâmetro choices, o Django faz a validação se o valor inserido é um dos valores permitidos);
- `is_remote`: booleano (True ou False) que indica se o evento é remoto ou não;
- `image`: imagem que será salva na pasta `{CAMINHO-DE-MÍDIA}/events/img` (o caminho de mídia pode ser definido no arquivo `settings.py`)

![Detalhes da tabela event pelo workbench](https://content-assets.betrybe.com/prod/64465619-fb06-4e3c-b7d2-08de3a9f7c33-Detalhes%20da%20tabela%20event%20pelo%20workbench.png)Detalhes da tabela event pelo workbench

> **Relembrando 🧠:** quando há um campo imagem é preciso fazer a instalação do módulo Pillow. Para isso, basta executar o comando `pip install Pillow` no terminal. **Relembrando 🧠:** depois de definir o modelo que será usado, crie as _migrations_ e logo depois migre-as para o banco. Para isso, execute `python3 manage.py makemigrations` e `python3 manage.py migrate` no terminal.

## Renderizando os eventos no _template_

Toda função que renderiza um _template_ usando o método _render_, do Django, é capaz também de fornecer um _contexto_ para esse _template_. O termo _contexto_ aqui se refere a um dicionário (`dict`), que pode ser construído dentro da função e passado para o _template_ como terceiro parâmetro do método _render_.

Todas as chaves do contexto podem ser acessadas diretamente pelo _template_ através da sintaxe `{{ chave }}`. Assim, o _template_ fará a renderização do valor que estava associado à chave. Modifique a função `index` do arquivo `events/views.py` para que ela fique assim:

```python
# events/views.py
from django.shortcuts import render


def index(request):
    context = {"company": "Trybe"}
    return render(request, 'home.html', context)
```

Modifique também seu _template_ `home.html` para renderizar o valor da chave `company` do contexto:

```html
<!-- events/templates/home.html -->
 {% extends 'base.html' %}

 {% block title %}
   Primeiro Template
 {% endblock %}

 {% block content %}
     <h1> Meu primeiro template usando Django! </h1>
     <h2> {{ company }} </h2>
 {% endblock %}
```

As modificações feitas acima farão com que o template renderize o valor da chave `company` do contexto, que aqui, é a palavra `Trybe`. Ao atualizar a aplicação você terá:

![Print da página home com a variável renderizada](https://content-assets.betrybe.com/prod/64465619-fb06-4e3c-b7d2-08de3a9f7c33-Print%20da%20p%C3%A1gina%20home%20com%20a%20vari%C3%A1vel%20renderizada.png)Print da página home com a variável renderizada

O contexto pode conter qualquer tipo de dado, inclusive objetos do tipo `QuerySet` que são retornados quando fazemos uma consulta ao banco de dados. O que é preciso fazer agora é obter os eventos do banco e passá-los para o template através do contexto. Vamos entender como fazer isso? 🧠

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/a3e9b6ef-54ea-486d-a979-a6444e788bc0/lesson/664a8baf-92e0-44b9-84cd-0f2b94883740)
