[[6 - Flask]]

## O que é o `Jinja2` ?

O **Jinja2** é um mecanismo de _template_ utilizado para criar páginas HTML dinâmicas por meio da incorporação de variáveis e comandos Python (`if`, `else`, `for`). O Flask possui uma integração nativa com o Jinja2, permitindo que os _templates_ sejam facilmente renderizados dentro do contexto da aplicação.

O Jinja2 também é uma biblioteca externa (ou seja, precisa ser instalada via `pip`), mas é amplamente adotado pela comunidade Python devido a sua flexibilidade, simplicidade e seu poder de integração com outras ferramentas.

## “Hello World” profissional com `Jinja2`

Utilizaremos como base desse material o código do repositório [tryber/python-conteudo-flask-trybot](https://github.com/tryber/python-conteudo-flask-trybot). Qualquer semelhança com o código produzido anteriormente sobre a API de Piadas é mera coincidência. 😜

Faça o clone do repositório e acompanhe o passo a passo!

```bash
git clone git@github.com:tryber/python-conteudo-flask-trybot.git
```

Confira, na imagem a seguir, a pasta `views` entrando em cena e fornecendo uma pasta de conteúdos estáticos `CSS` e algumas imagens, que usaremos hoje.

![Estrutura de pastas do projeto base](https://content-assets.betrybe.com/prod/353153c6-b12f-48cb-a4fd-8db5b99092fc-Estrutura%20de%20pastas%20do%20projeto%20base.png)
Estrutura de pastas do projeto base


Os arquivos CSS fornecidos garantem o estilo da nossa aplicação, customizando botões, _inputs_, _forms_, fundos. Como eles foram criados de forma bem simplificada, hoje, não temos por objetivo explicá-los aqui.

Pronto! Com o ambiente preparado, já podemos utilizar o Jinja2, para implementar o tão prometido _Chat_.

## Programando o `app.py`

Alguns ajustes serão necessários para utilizar o Jinja2. Então, vamos a eles:

> Arquivo `app.py`

```python
# ...

# 1 - Importe uma nova controller para nossa pagina inicial
# Obs: não se preocupe, implementaremos ela logo a seguir
from controllers.home_controller import home_controller

# ...

# Lembrando: instanciamos o servidor Flask
# app = Flask(__name__)

# 2 - Indique à aplicação onde ficarão nossos arquivos estáticos e templates
app.static_folder = "views/static"
app.template_folder = "views/templates"

# 3 - Registre a nova controller
app.register_blueprint(home_controller, url_prefix="/")

# ...
```

Em resumo, carregamos uma nova _controller_, a `home_controller`, para responder a raiz do projeto pelo prefixo `"/"`, ou seja, `http://localhost:8000/`.

O padrão é que as pastas de arquivos estáticos e _templates_ fiquem direto na raiz do projeto, porém, para representarmos o `MVC` estas duas foram movidas para dentro da pasta `view`.

## Programando a `home_controller.py`

Agora vamos fazer algo diferente: vamos criar uma _controller_ que renderiza um _template_ HTML.

Para isso, crie um novo arquivo `src/controllers/home_controller.py` com a devida Blueprint e um _endpoint_ `GET /`, e utilize a função `render_template` para renderizar o _template_ `index.html` (que será criado em breve).

Copiar

```python
from flask import Blueprint, render_template

# 1 - Crie o objeto Blueprint da home_controller, para o registro em app.py
home_controller = Blueprint("home_controller", __name__)


# 2 - Crie o endpoint GET /, que renderiza o template index.html
@home_controller.route("/", methods=["GET"])
def index():
    # 3 - Renderize a template index.html com a função render_template
    return render_template("index.html")
```

## Definindo o primeiro _template_

Vamos à criação do template `index.html`, que será responsável por nos dar um `Olá` e criar um formulário! 🤩

**Atenção ⚠️:** Confira sempre a estrutura de diretórios ao criar uma nova pasta.

Crie o arquivo dentro de `src/views/templates/` (lembra que definimos essa pasta no `app.py`?) e adicione o seguinte conteúdo:


```HTML
<!DOCTYPE html>
<html>

<head>
    <meta charset="UTF-8">
    <title>Chat Trybot</title>
    <!-- {{url_for}} utilizado para importar um css estático -->
    <link rel="stylesheet" href="{{ url_for('static', filename='css/main.css') }}">
</head>

<body class="container">
    <!-- {{url_for}} utilizado para importar uma imagem estática -->
    <img src="{{ url_for('static', filename='images/logo.png') }}" width="40%">

    <form action="/" method="post">
        <p>Olá! Bom dia, como você se chama?</p>
        <div class="send-box">
            <input type="text" name="username">
            <button type="submit" id="chat">Entrar</button>
        </div>
    </form>
</body>
</html>
```

Observe que aparentemente é um HTML normal, porém possui algumas _strings_ com _placeholders_ (`{{ comando }}`) que permitem criar expressões que incluem variáveis, filtros, funções, operadores e referências a arquivos estáticos. Essas _strings_ são chamadas de _expressões_ e vamos explorá-las mais adiante. 🤓

## Subindo a aplicação

Vamos subir nosso _docker_ e, caso haja conflito com _containers_ já existentes, experimente removê-los.

```bash
docker compose up flask-api
```

Acessando o navegador, você deverá ter acesso a este resultado:

![Resultado esperado para o template inicial](https://content-assets.betrybe.com/prod/353153c6-b12f-48cb-a4fd-8db5b99092fc-Resultado%20esperado%20para%20o%20template%20inicial.png)
Resultado esperado para o template inicial


## Customizando a mensagem com o `Jinja2`

Nosso formulário não faz nada, não é? E está sempre dando “Bom dia”. Vamos dar mais dinamismo a esta mensagem?

Pelo Jinja2 MVC, a _controller_ é responsável por indicar para o _template_ o que será renderizado. Para isso, devemos identificar se o método HTTP foi um POST e reconhecer o nome passado pelo formulário com `request.form.get`.

> home_controller.py

```python
# Precisamos adicionar a importação do módulo datetime, e do objeto request
from datetime import datetime
from flask import Blueprint, render_template, request


home_controller = Blueprint("home_controller", __name__)


# É possível uma configuração de rota já atender os dois métodos
@home_controller.route("/", methods=["GET", "POST"])
def index():
    # Reconhecimento do username que chegou do formulário da requisição
    username = request.form.get("username") if request.method == "POST" else ""

    # Ao renderizar novamente a template, alguns parâmetros devem ser passados
    return render_template(
        "index.html",
        username=username,
        greeting=_get_greeting(),
    )


def _get_greeting():
    """Retorna a saudação correta para o horário atual"""
    curr_hour = datetime.now().hour
    if curr_hour < 6:
        return "Boa noite"
    if curr_hour < 12:
        return "Bom dia"
    if curr_hour < 18:
        return "Boa tarde"
    return "Boa noite"
```

> **Anota aí 📝:** Quando passamos parâmetros para um _template_ no Jinja2, damos o nome de **contexto** (_context_).

Os parâmetros `username` e `greeting` serão passados como contexto para o _template_ `index.html`, onde podemos utilizá-los para customizar a mensagem de saudação. Para fazer isso, basta atualizar a nossa _tag_ `<p>`.

> index.html

Copiar

```html
<!-- width="40%"> ... -->
    <!-- <form action="/chat" method="post"> -->
        <!-- <p>Olá! Bom dia, como você se chama?</p> -->
        <!-- <div class="send-box"> -->
            <!-- <input type="text" name="username"> -->
            {% if username and greeting %}
                <p>Olá {{ username|capitalize|trim }}! {{ greeting }}!</p>
            {% endif %}
<!-- <div clas... -->
```

Poderíamos realizar somente um `Olá {{username}}! [...]`, porém vamos aproveitar os filtros nativos do Jinja2 para formatar nossa _string_.

[Filtros no Jinja2](https://jinja.palletsprojects.com/en/3.1.x/templates/#filters) são funções que recebem um valor e retornam um novo valor. Eles são separados do valor por um caractere de _pipe_ (`|`). No nosso caso, o filtro `capitalize` recebe o valor de `username` e retorna um novo valor com a primeira letra maiúscula. O filtro `trim` recebe o retorno do filtro `capitalize` e remove os espaços em branco do início e do fim da _string_.

Sim, seria o equivalente à chamada `trim(capitalize(username))` direto no HTML! 😎

![Resultado esperado após uso de contexto no _template_](https://content-assets.betrybe.com/prod/353153c6-b12f-48cb-a4fd-8db5b99092fc-Resultado%20esperado%20ap%C3%B3s%20uso%20de%20contexto%20no%20_template_.png)
Resultado esperado após uso de contexto no _template_


Uhul! 🎉 Agora que fizemos nosso primeiro _template_ com uso de contexto, vamos deixar nossa aplicação de chat funcional.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/0c01be06-7742-4513-ab65-a29fde93f3c2/lesson/53669d9b-bc82-419c-8927-958044bf576b)
