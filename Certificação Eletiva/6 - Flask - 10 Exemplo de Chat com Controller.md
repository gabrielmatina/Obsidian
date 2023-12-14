[[6 - Flask]]

## Criando a _controller_ do Chat

Para a _controller_, vamos atuar com duas rotas: a `GET`, para representar um acesso de uma pessoa visitante, não autenticada no formulário anterior; e o `POST`, que publica uma nova mensagem e recebe uma resposta do Chatbot.

Para esse propósito, vamos reconhecer o usuário e a mensagem recebida do formulário que acessou esta rota.

Posteriormente, salvaremos a mensagem recebida e a resposta no banco de dados e retornaremos todas as mensagens para a camada `View`.

Iremos declarar alguns métodos que não são rotas, apenas para deixar as rotas das _controllers_ mais limpas. Estes métodos poderiam até ser uma camada `SERVICE`, mas vamos manter este formato por hoje.

> src/controllers/chat_controller.py

```python
from flask import Blueprint, request, render_template
from datetime import datetime
import random
from models.joke_model import JokeModel
from models.message_model import MessageModel

chat_controller = Blueprint("chat_controller", __name__)


# GET considerará usuário visitante, POST o usuário recebido do formulário
@chat_controller.route("/", methods=["GET", "POST"])
def continue_chat():
    username = request.form.get("username") or "Visitante"
    message_content = request.form.get("message-content")

    _save_message(message_content, _from=username, to="Trybot")
    # Prepara a resposta
    answer = _prepare_answer(username, message_content)
    _save_message(answer, _from="Trybot", to=username)

    # Retorna todas as mensagens que este usuário possui com o bot
    session_messages = _get_session_messages(username)
    return render_template(
        "chat.html", username=username, session_messages=session_messages
    )


# Métodos de serviços auxiliares
def _save_message(message_content, _from, to):
    if not message_content:
        return

    chat_message = MessageModel(
        {
            "content": message_content,
            "from": _from,
            "to": to,
            "time": datetime.now().strftime("%H:%M"),
        }
    )
    chat_message.save()


def _prepare_answer(name, message_content):
    if not message_content:
        return _answer_first(name)
    if "1" in message_content:
        return _answer_joke()
    return _answer_default()


def _answer_first(name):
    return (
        f"Olá { name }, bem vindo a central de ajuda! Por hora posso "
        "te ajudar em algumas coisas 😄<br>1 - Contar uma piada"
    )


def _answer_default():
    return random.choice(
        [
            "Interessante, me conte mais sobre isso.",
            "Compreendo como você se sente.",
            "Isso é algo com o qual muitas pessoas lidam.",
            "Como você está lidando com isso?",
            "Eu estou aqui para você, se precisar conversar.",
        ]
    )


def _answer_joke():
    joke = JokeModel.get_random()
    return joke.to_dict()["joke"] if joke else "Ainda não conheço piadas"


def _get_session_messages(name):
    messages = MessageModel.find({"$or": [{"to": name}, {"from": name}]})
    return [message.to_dict() for message in messages]

```

## Registrando a nova _controller_

Não podemos nos esquecer de adicionar a _controller_ no arquivo `src/app.py`:

```python
# from flask import Flask
from controllers.chat_controller import chat_controller
# ...
# app.register_blueprint(home_controller, url_prefix="/")
# app.register_blueprint(jokes_controller, url_prefix="/jokes")
app.register_blueprint(chat_controller, url_prefix="/chat")
```

## Alterando o _index.html_

Precisamos realizar uma alteração no arquivo `index.html`. Vamos modificar o valor do atributo `action` para que ele aponte para a rota `/chat`.

> index.html


```diff
...
    <img src="{{ url_for('static', filename='images/logo.png') }}" width="40%">

-    <form action="/" method="post">
+    <form action="/chat" method="post">
        <p>Olá {{username|capitalize|trim}}! {{greeting}}, como você se chama?</p>
        <div class="send-box">
            <input type="text" name="username">
...
```

O atributo `action` na _tag_ `form` do HTML5 especifica a URL para onde os dados do formulário serão enviados, quando o usuário submetê-lo. Esse atributo define o destino do envio dos dados para processamento, seja para uma página no mesmo servidor, seja para um servidor remoto.

O valor do atributo `action` é uma URL relativa ou absoluta que representa o local para onde os dados do formulário serão enviados. Ao submeter o formulário, o navegador enviará uma solicitação HTTP para o URL especificado no atributo `action`, incluindo os dados do formulário como parte da solicitação.

![Resultado](https://content-assets.betrybe.com/prod/41667476-16c5-4a2e-a762-93d68baa2fcb-Resultado.png)
Resultado esperado após subir a aplicação.

Se quiser criar um arquivo `Seeds`, para ter algumas piadas, crie o seguinte arquivo e execute:

```bash
touch src/run_seeds.py
```

```python
from models.joke_model import JokeModel

jokes = [
    "Porque a galinha atravessou a rua? Para chegar do outro lado",
    "Qual o contrário de volátil? Vem cá sobrinho",
    "O que é um pontinho amarelo no céu? Um Yellowcóptero",
    "O que é um pontinho verde no canto da sala? Uma ervilha fazendo exercício",
    "O que é um pontinho azul no canto da sala? Um smurfs fazendo exercício"
]

for joke in jokes:
    JokeModel({'joke': joke}).save()
```

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install --no-cache-dir -r src/dev-requirements.txt
python3 src/run_seeds.py
```

Uau! Você conseguiu criar o _chat_!


# Vamos praticar!

## Exercício 1

No seu time de desenvolvimento, você recebeu a missão de criar um sistema de registro de alunos utilizando Flask, Jinja2 e a arquitetura MVC (Model-View-Controller). O sistema permitirá adicionar, exibir, editar e excluir informações dos alunos.

Requisitos:

1 - Criar uma rota para exibir a lista de alunos cadastrados. A lista deve conter o nome e o número de matrícula de cada aluno. Utilize um arquivo HTML chamado `alunos.html` para exibir a lista de alunos.

2 - Criar uma rota para adicionar um novo aluno ao sistema. Utilize um arquivo HTML chamado `adicionar_aluno.html` com um formulário que solicite o nome e o número de matrícula do aluno. Ao submeter o formulário, o aluno deve ser adicionado à lista de alunos e redirecionado para a página de lista de alunos.

3 - Criar uma rota para editar as informações de um aluno existente. Utilize um arquivo HTML chamado `editar_aluno.html` com um formulário preenchido com as informações atuais do aluno. Ao submeter o formulário, as informações do aluno devem ser atualizadas e o usuário redirecionado para a página de lista de alunos.

4 - Criar uma rota para excluir um aluno do sistema. Ao excluir um aluno, ele deve ser removido da lista de alunos e o usuário redirecionado para a página de lista de alunos.

5 - Estilizar as páginas HTML utilizando CSS. Crie um arquivo CSS chamado `style.css` e utilize-o para aplicar estilos aos elementos das páginas HTML.

### Solução

> app.py


```python
from flask import Flask, render_template, request, redirect

app = Flask(__name__)

# Lista de alunos (exemplo)
alunos = [
    {'nome': 'João', 'matricula': '2021001'},
    {'nome': 'Maria', 'matricula': '2021002'},
    {'nome': 'Pedro', 'matricula': '2021003'}
]

# Rota para exibir a lista de alunos
@app.route('/')
@app.route("/alunos")
def listar_alunos():
    return render_template('alunos.html', alunos=alunos)

# Rota para adicionar um novo aluno
@app.route('/alunos/adicionar', methods=['GET', 'POST'])
def adicionar_aluno():
    if request.method == 'POST':
        nome = request.form['nome']
        matricula = request.form['matricula']
        novo_aluno = {'nome': nome, 'matricula': matricula}
        alunos.append(novo_aluno)
        return redirect('/')
    return render_template('adicionar_aluno.html')

# Rota para editar as informações de um aluno
@app.route('/alunos/editar/<int:index>', methods=['GET', 'POST'])
def editar_aluno(index):
    if index < 0 or index >= len(alunos):
        return redirect('/')

    aluno = alunos[index]
    if request.method == 'POST':
        nome = request.form['nome']
        matricula = request.form['matricula']
        aluno['nome'] = nome
        aluno['matricula'] = matricula
        return redirect('/')

    return render_template('editar_aluno.html', aluno=aluno, aluno_index=index)

# Rota para excluir um aluno
@app.route('/alunos/excluir/<int:index>', methods=["GET", "POST"])
def excluir_aluno(index):
    if request.method == 'GET':
        aluno = alunos[index]
        return render_template("excluir_aluno.html", aluno=aluno, index=index)

    if index < 0 or index >= len(alunos):
        return redirect('/')

    alunos.pop(index)
    return redirect('/')

if __name__ == '__main__':
    app.run(debug=True)
```

> templates/alunos.html


```html
<!-- alunos.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Lista de Alunos</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <h1>Lista de Alunos</h1>
    <table>
        <tr>
            <th>Nome</th>
            <th>Matrícula</th>
            <th>Ações</th>
        </tr>
        {% for aluno in alunos %}
        <tr>
            <td>{{ aluno.nome }}</td>
            <td>{{ aluno.matricula }}</td>
            <td>
                <a href="/alunos/editar/{{ loop.index0 }}">Editar</a>
                <a href="/alunos/excluir/{{ loop.index0 }}">Excluir</a>
            </td>
        </tr>
        {% endfor %}
    </table>
    <a href="/alunos/adicionar">Adicionar Aluno</a>
</body>
</html>
```

> templates/adicionar_aluno.html


```html
<!-- adicionar_aluno.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Adicionar Aluno</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <h1>Adicionar Aluno</h1>
    <form action="/alunos/adicionar" method="post">
        <label for="nome">Nome:</label>
        <input type="text" id="nome" name="nome" required>
        <label for="matricula">Matrícula:</label>
        <input type="text" id="matricula" name="matricula" required>
        <button type="submit">Adicionar</button>
    </form>
    <a href="/alunos">Voltar para a Lista</a>
</body>
</html>
```

> templates/editar_aluno.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Editar Aluno</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <h1>Editar Aluno</h1>
    <form action="/alunos/editar/{{ aluno_index }}" method="post">
        <label for="nome">Nome:</label>
        <input type="text" id="nome" name="nome" value="{{ aluno.nome }}" required>
        <label for="matricula">Matrícula:</label>
        <input type="text" id="matricula" name="matricula" value="{{ aluno.matricula }}" required>
        <button type="submit">Salvar</button>
    </form>
    <a href="/alunos">Voltar para a Lista</a>
</body>
</html>
```

> templates/excluir_aluno.html

```html
<!-- excluir_aluno.html -->
<!DOCTYPE html>
<html>
<head>
    <title>Excluir Aluno</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <h1>Excluir Aluno</h1>
    <p>Deseja realmente excluir o aluno "{{ aluno.nome }}"?</p>
    <form action="/alunos/excluir/{{ index }}" method="post">
        <button type="submit">Excluir</button>
    </form>
    <a href="/alunos">Cancelar</a>
</body>
</html>
```

> static/styles.css


```css
/* style.css */
body {
    font-family: Arial, sans-serif;
}

h1 {
    color: #333;
}

table {
    width: 100%;
    border-collapse: collapse;
}

table th, table td {
    padding: 8px;
    border-bottom: 1px solid #ccc;
}

table th {
    text-align: left;
    background-color: #f2f2f2;
}

a {
    color: #007bff;
    text-decoration: none;
    margin-right: 10px;
}

button {
    background-color: #007bff;
    color: #fff;
    border: none;
    padding: 8px 16px;
    cursor: pointer;
    margin-top: 10px;
}

button:hover {
    background-color: #0056b3;
}

a, button {
    display: inline-block;
}

form label {
    display: block;
    margin-bottom: 5px;
}
```

## Exercício 2

Dando sequência ao Exercício 01, refatore o código para utilizar o MongDB ao invés de um array.

Requisitos:

1 - Crie um diretório chamado `models` e um arquivo de conexão com o banco chamado `db.py`

2 - Crie um arquivo chamado `student_model.py`, crie uma classe `StudentModel` e implemente dentro dela a lógica do CRUD de um aluno

3 - Faça as alterações que forem necessárias no arquivo `app.py` e/ou templates

### Solução

> models/db.py

```python
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017')

db = client.db_school
```

> models/student_model.py

```python
from .db import db
from pymongo.collection import ReturnDocument


class StudentModel():
    _collection = db['students']

    def __init__(self, data):
        self.data = data

    def save(self):
        result = self._collection.insert_one(self.data)
        inserted_document = self._collection.find_one({
            "_id": result.inserted_id
        })
        self.data = inserted_document
        return self.data

    def update(self, data):
        result = self._collection.find_one_and_update(
            {"_id": self.data["_id"]},
            {"$set": data},
            return_document=ReturnDocument.AFTER,
        )

        self.data = result
        return self.data

    def delete(self):
        self._collection.delete_one({"_id": self.data["_id"]})

    @classmethod
    def find(cls, query={}):
        data = cls._collection.find(query)
        return [cls(d) for d in data]

    @classmethod
    def find_one(cls, query={}):
        data = cls._collection.find_one(query)
        return cls(data) if data else None

    def to_dict(self):
        return {
            "nome": self.data["nome"],
            "matricula": self.data["matricula"],
            "id": self.data["_id"]
        }
```

> app.py

```python

from flask import Flask, render_template, request, redirect
from models.student_model import StudentModel

app = Flask(__name__)


# Rota para exibir a lista de alunos
@app.route("/")
@app.route("/alunos")
def listar_alunos():
    alunos = StudentModel.find()
    dict_alunos = [aluno.to_dict() for aluno in alunos]
    return render_template("alunos.html", alunos=dict_alunos)


# Rota para adicionar um novo aluno
@app.route("/alunos/adicionar", methods=["GET", "POST"])
def adicionar_aluno():
    if request.method == "POST":
        nome = request.form["nome"]
        matricula = request.form["matricula"]
        novo_aluno = {"nome": nome, "matricula": matricula}
        # alunos.append(novo_aluno)
        StudentModel(novo_aluno).save()
        return redirect("/")
    return render_template("adicionar_aluno.html")


# Rota para editar as informações de um aluno
@app.route("/alunos/editar/<int:index>", methods=["GET", "POST"])
def editar_aluno(index):
    print(index)
    aluno = StudentModel.find_one({"matricula": str(index)})
    print(f'aluno: {aluno}')
    if not aluno:
        return redirect("/")
    if request.method == "POST":
        nome = request.form["nome"]
        matricula = request.form["matricula"]
        aluno_para_atualizar = {"nome": nome, "matricula": matricula}
        aluno.update(aluno_para_atualizar)
        return redirect("/")
    return render_template(
        "editar_aluno.html",
        aluno=aluno.to_dict(),
        aluno_index=index
    )


# Rota para excluir um aluno
@app.route("/alunos/excluir/<int:index>", methods=["GET", "POST"])
def excluir_aluno(index):
    aluno = StudentModel.find_one({"matricula": str(index)})
    if request.method == 'GET':
        return render_template("excluir_aluno.html", aluno=aluno, index=index)

    if not aluno:
        return redirect("/")

    aluno.delete()
    return redirect("/")


if __name__ == "__main__":
    app.run(debug=True)
```

> alunos.html

```html
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <title>Alunos</title>
    <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>
<body>
    <h1>Lista de Alunos</h1>
    <table>
        <tr>
            <th>Nome</th>
            <th>Matrícula</th>
            <th>Ações</th>
        </tr>
        {% for aluno in alunos %}
        <tr>
            <td>{{ aluno.nome }}</td>
            <td>{{ aluno.matricula }}</td>
            <td>
                <a href="/alunos/editar/{{ aluno.matricula }}">Editar</a>
                <a href="/alunos/excluir/{{ aluno.matricula }}">Excluir</a>
            </td>
        </tr>
        {% endfor %}
    </table>
    <a href="/alunos/adicionar">Adicionar Aluno</a>
</body>
</html>
```

> editar_aluno.html

```html
<!DOCTYPE html>
<html>

<head>
  <title>Editar Aluno</title>
  <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>

<body>
  <h1>Editar Aluno</h1>
  <form action="/alunos/editar/{{ aluno.matricula }}" method="post">
    <label for="nome">Nome:</label>
    <input type="text" id="nome" name="nome" value="{{ aluno.nome }}" required>
    <label for="matricula">Matrícula:</label>
    <input type="text" id="matricula" name="matricula" value="{{ aluno.matricula }}" required>
    <button type="submit">Salvar</button>
  </form>
  <a href="/alunos">Voltar para a Lista</a>
</body>

</html>
```

> excluir_aluno.html

```html
<!-- excluir_aluno.html -->
<!DOCTYPE html>
<html>

<head>
  <title>Excluir Aluno</title>
  <link rel="stylesheet" type="text/css" href="{{ url_for('static', filename='style.css') }}">
</head>

<body>
  <h1>Excluir Aluno</h1>
  <p>Deseja realmente excluir o aluno "{{ aluno.nome }}"?</p>
  <form action="/alunos/excluir/{{ aluno.matricula }}" method="post">
    <button type="submit">Excluir</button>
  </form>
  <a href="/alunos">Cancelar</a>
</body>

</html>
```

## Exercício 3

Você foi contratado para a construção de um catálogo de filmes utilizando Flask, Jinja2 e MongoDB.

Neste exercício, vamos construir uma página de catálogo de filmes utilizando Flask como framework web, Jinja2 como mecanismo de templates e o MongoDB para armazenar informações dos filmes. A página exibirá uma lista de filmes e permitirá que o usuário pesquise por filmes específicos.

Requisitos:

1 - Crie uma estrutura de diretórios para o projeto com os seguintes arquivos:


```markdown
- app/
  - controllers/
    - filme_controller.py
  - models/
    - db.py
    - filme.py
  - static/
    - filme.css
    - style.css
  - templates/
    - filme.html
    - index.html
  - app.py
```

`app.py`: arquivo principal do projeto que conterá as rotas da aplicação. `models/filme.py`: arquivo que conterá a definição da classe “Filme” para representar um filme. `controllers/filme_controller.py`: arquivo que conterá a lógica de negócio da aplicação (controller). templates/: pasta que conterá os templates HTML. static/: Arquivos com alguma estilização CSS que preferir.

2 - No arquivo `models/filme.py`, defina a classe `Filme` com os seguintes atributos e métodos:

- Atributos
    - _collection: coleção do MongoDB a ser usada
- Métodos
    - `__init__`: inicializa o atributo data
    - `buscar_filmes_por_titulo`: busca filmes pelo título no DB, caso não receba um título, deve retornar todos os filmes registrados no banco.
    - `buscar_filmes_por_id`: busca um filme específico
    - `to_dict`: converte um filme para um dicionário com as seguintes propriedades:
        - `titulo`: string (título do filme).
        - `ano`: string (ano de lançamento do filme).
        - `diretor`: string (diretor do filme).
        - `genero`: string (gênero do filme).
        - `poster`: string (URL do poster do filme).
        - `_id`: string (_id do filme)

3 - Crie o arquivo `models/db.py`, este arquivo será responsável por fazer a conexão com o MongoDB

4 - No arquivo `controllers/filme_controller.py`, crie a classe `FilmeController` com os seguintes métodos:

- buscar_filmes_por_titulo(titulo): busca por filmes no banco de dados com base no título fornecido.
- buscar_filme_por_id(id): busca por um filme no banco de dados com base no ID fornecido.

5 - No arquivo `app.py`, importe os módulos necessários do Flask, models e controllers. Defina as seguintes rotas:

- Rota principal para exibir a página inicial com a lista de filmes (método GET).
- Rota para realizar a busca por filmes (método POST).
- Rota para exibir os detalhes de um filme específico (método GET).

6 - Crie os templates HTML na pasta templates/ para a página inicial e os detalhes do filme.

Dica: crie um arquivo `.mongodb`, cole o código abaixo dentro dele e execute-o:

```js
use('db_filmes')
db.filmes.insertMany([
    {
        'titulo': 'Interestelar',
        'ano': 2014,
        'diretor': 'Christopher Nolan',
        'genero': 'Ficção científica',
        'poster': 'https://play-lh.googleusercontent.com/em2griqrHoxmxEss_WM5Fi2iqSEKrEfLNAltjX54lREOR0nz0du__KuSi2bA_YNjS4w'
    },
    {
        'titulo': 'Carta Selvagem',
        'ano': '2015',
        'diretor': 'Simon West',
        'genero': 'Ação',
        'poster': 'https://leiturafilmica.com.br/wp-content/uploads/2023/05/carta-selvagem-poster.jpg'
    },
    {
        'titulo': 'Orgulho e Preconceito',
        'ano': '2005',
        'diretor': 'Joe Wright',
        'genero': 'Drama',
        'poster': 'https://br.web.img3.acsta.net/medias/nmedia/18/87/84/14/20028635.jpg'
    },
    {
        'titulo': 'A Bruxa',
        'ano': '2015',
        'diretor': 'Robert Eggers',
        'genero': 'Terror',
        'poster': 'https://br.web.img2.acsta.net/c_310_420/pictures/16/02/02/11/51/346769.jpg'
    },
    {
        'titulo': 'Projeto X',
        'ano': '2012',
        'diretor': 'Nima Nourizadeh',
        'genero': 'Comédia',
        'poster': 'https://delfos.net.br/wp-content/uploads/2017/05/975700-02-05-17-12.jpg'
    },
])

```

### Solução

> models/filme.py

```python
from .db import db
from bson.objectid import ObjectId


class Filme:
    _collection = db['filmes']

    def __init__(self, data):
        self.data = data

    @classmethod
    def buscar_filmes_por_titulo(cls, query={}):
        data = cls._collection.find(query)
        return [cls(d) for d in data]

    @classmethod
    def buscar_filmes_por_id(cls, id):
        data = cls._collection.find_one({"_id": ObjectId(id)})
        return cls(data) if data else None

    def to_dict(self):
        filme = {
            "titulo": self.data["titulo"],
            "ano": self.data["ano"],
            "diretor": self.data["diretor"],
            "genero": self.data["genero"],
            "poster": self.data["poster"],
            "_id": self.data["_id"]
        }
        return filme

```

> models/db.py

```python
from pymongo import MongoClient

client = MongoClient('mongodb://localhost:27017')

db = client.db_filmes

```

> controllers/filme_controller.py

```python
from models.filme import Filme


class FilmeController:
    def buscar_filmes_por_titulo(self, titulo=None):
        query = {"titulo": titulo} if titulo else None
        filmes = Filme.buscar_filmes_por_titulo(query)
        return [filme.to_dict() for filme in filmes]

    def buscar_filme_por_id(self, id):
        filme = Filme.buscar_filmes_por_id(id)
        return filme.to_dict()

```

> app.py

```python
from flask import Flask, render_template, request
from controllers.filme_controller import FilmeController

app = Flask(__name__)
filme_controller = FilmeController()


@app.route("/", methods=["GET", "POST"])
def index():
    if request.method == "POST":
        titulo = request.form.get("titulo")
        filmes = filme_controller.buscar_filmes_por_titulo(titulo)
    else:
        filmes = filme_controller.buscar_filmes_por_titulo()
        # Exemplo de busca inicial
    return render_template("index.html", filmes=filmes)


@app.route("/filmes/<id>")
def filme(id):
    print(id)
    filme = filme_controller.buscar_filme_por_id(id)
    return render_template("filme.html", filme=filme)


if __name__ == "__main__":
    app.run(debug=True)

```

> templates/filme.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Detalhes do Filme</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='filme.css') }}">
</head>
<body>
    {% if filme %}
    <section class="filme">
        <h1>{{ filme.titulo }}</h1>
        <p>Ano: {{ filme.ano }}</p>
        <p>Diretor: {{ filme.diretor }}</p>
        <p>Gênero: {{ filme.genero }}</p>
        <img src="{{ filme.poster }}" alt="{{ filme.titulo }}">
    </section>
    {% else %}
    <p>Filme não encontrado.</p>
    {% endif %}
</body>
</html>
```

> templates/index.html

```html
<!DOCTYPE html>
<html>
<head>
    <title>Catálogo de Filmes</title>
    <link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
    </head>
<body>
    <h1>Catálogo de Filmes</h1>
    <form action="/" method="POST">
        <input type="text" name="titulo" placeholder="Digite o título do filme">
        <button type="submit">Buscar</button>
    </form>
    <ul class="filmes">
        {% for filme in filmes %}
        <li class="filme">
            <a href="/filmes/{{ filme._id }}">
                <img src="{{ filme.poster }}" alt="{{ filme.titulo }} Poster">
                <h2>{{ filme.titulo }}</h2>
                <p>Ano: {{ filme.ano }}</p>
                <p>Diretor: {{ filme.diretor }}</p>
                <p>Gênero: {{ filme.genero }}</p>
            </a>
        </li>
        {% endfor %}
    </ul>
</body>
</html>
```

> static/styles.css


```css
body {
    align-items: center;
    display: flex;
    flex-direction: column;
    font-family: Arial, sans-serif;
}

ul {
    margin: 0;
    padding: 0;
    width: 90vw;
}

h1, h2 {
    color: #333;
}

form {
    align-items: center;
    display: flex;
    justify-content: space-evenly;
    width: 40%;
}

input {
    border: 1px solid #007bff;
    border-radius: 10px;
    outline: none;
    padding: 10px;
    width: 70%;
}

.filmes {
    align-items: center;
    display: flex;
    flex-wrap: wrap;
    justify-content: space-around;
}

.filme {
    align-items: center;
    background-color: rgba(164, 164, 164, 0.467);
    border: 1px solid #ccc;
    border-radius: 10px;
    display: flex;
    flex-wrap: wrap;
    height: 400px;
    justify-content: space-around;
    margin: 10px;
    padding: 10px;
    text-align: center;
    width:29%;
}

img {
    width: 150px;
    height: 20%;
    margin-bottom: 10px;
}

a, p, li {
    color: #007bff;
    font-size: 13pt;
    list-style: none;
    text-decoration: none;
}

button {
    background-color: #007bff;
    border: none;
    border-radius: 10px;
    color: #fff;
    cursor: pointer;
    height: 100%;
    padding: 12px 16px;
}

button:hover {
    background-color: #0056b3;
}
```

> static/filme.css

```css
body {
    align-items: center;
    display: flex;
    flex-direction: column;
    font-family: Arial, sans-serif;
    height: 100vh;
    justify-content: center;
}

.filme {
    align-items: center;
    background-color: rgba(164, 164, 164, 0.467);
    border: 1px solid #ccc;
    border-radius: 10px;
    display: flex;
    flex-flow: column wrap;
    height: 70vh;
    justify-content: space-around;
    margin: 10px;
    padding: 10px;
    text-align: center;
    width: 60%;
}

img {
    width: auto;
    height: 100%;
    margin-bottom: 10px;
}

p {
    color: #007bff;
    font-size: 16pt;
}
```



###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/0c01be06-7742-4513-ab65-a29fde93f3c2/lesson/9e511504-d2ef-4595-a672-9397bbc7e00c)

