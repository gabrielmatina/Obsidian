[[6 - Flask]]

Agora que você já sabe os fundamentos de testes com o `unittest`, vamos aplicar esse conhecimento em uma aplicação Flask. Para isso, vamos utilizar uma aplicação de exemplo que está disponível no seguinte repositório: [tryber/python-conteudo-flask-project-manager](https://github.com/tryber/python-conteudo-flask-project-manager). Faça o clone desse repositório e siga as instruções no `README.md` para deixar a aplicação funcional!

A aplicação deste repositório mostra tarefas de alguns projetos, atualmente a aplicação só mostra as tarefas e projetos já cadastrados no nosso banco de dados. Sinta-se a vontade para criar novas features e usar os conhecimentos de hoje para testá-las! 😉

Nós vamos utilizar o `unittest` para testar o retorno da nossa requisição, ou seja, vamos testar o que está sendo renderizado no _front-end_.

Vamos começar pela tela inicial, a tela `home`, que pode ser acessada pelas rotas `/` e `/projects`, vamos começar fazendo a preparação para os testes. Vamos criar um arquivo chamado `test_home_unittest.py` e adicionar o seguinte código a ele:

```bash
mkdir src/tests && touch src/tests/test_home_unittest.py
```

> src/tests/test_home_unittest.py

```python
import unittest
from app import app


class TestHome(unittest.TestCase):
    def setUp(self):
        test_app = app.test_client()
        self.response = test_app.get('/')
```

No código acima definimos a classe `TestHome`, que herda da classe `unittest.TestCase`, como vimos anteriormente.

Dentro da classe `TestHome`, há um método chamado `setUp()`. Esse método é executado antes de cada caso de teste e é usado para configurar o ambiente de teste. Dentro do método `setUp()`, criamos uma instância de `app.test_client()`, essa instância nos permite simular requisições à aplicação. Em seguida, fazemos uma solicitação `GET` à rota principal (`/`) da aplicação através da instância de `app.test_client()`, armazenando a resposta retornada em `self.response`. Essa linha simula o acesso à página inicial da aplicação e captura a resposta retornada pelo servidor.

Vamos criar nosso primeiro teste e verificar o status da resposta da nossa requisição:

> src/tests/test_home_unittest.py

```python
# ...
    # def setUp(self):
    #     test_app = app.test_client()
    #     self.response = test_app.get('/')

    def test_status_response(self):
        self.assertEqual(self.response.status_code, 200)
```

Execute os testes:

```bash
docker compose exec flask-api python3 -m unittest -v tests/test_home_unittest.py -b
```

Saída esperada:

![Test status response](https://content-assets.betrybe.com/prod/997505fc-d4ce-46fc-a3c5-ed9a1cb59193-Test%20status%20response.png)

Vamos verificar se foram criados containers para cada um dos nossos 3 projetos, para isso vamos criar um diretório chamado `mocks` e um arquivo chamado `home.py`:

```bash
mkdir src/tests/mocks && touch src/tests/mocks/home.py
```

> src/tests/mocks/home.py

```python
project_cards = '<section class="project-home">'
```

> src/tests/test_home_unittest.py

```python
# import unittest
# from app import app
from tests.mocks.home import project_cards
# ...
    # def test_status_response(self):
    #     self.assertEqual(self.response.status_code, 200)

    def test_quantity_of_projects(self):
        self.assertEqual(self.response.text.count(project_cards), 3)
```

Execute os testes:

```bash
docker compose exec flask-api python3 -m unittest -v tests/test_home_unittest.py -b
```

Saída esperada:

![Test quantity of projects](https://content-assets.betrybe.com/prod/997505fc-d4ce-46fc-a3c5-ed9a1cb59193-Test%20quantity%20of%20projects.png)

Vamos agora testar se os títulos dos 3 projetos estão sendo renderizados, criaremos um dicionário para guardar as informações dos títulos dos 3 projetos que queremos testar:

> src/mocks/home.py

```python
# ...

project_titles = {
    1: "<h1>Aplicação Web para Gerenciamento de Tarefas</h1>",
    2: "<h1>Aplicativo móvel para rastreamento de exercícios físicos</h1>",
    3: "<h1>Sistema de gestão de vendas online</h1>"
}
```

> src/tests/test_home_unittest.py

Copiar

```python
# ...
from tests.mocks.home import project_cards, project_titles
# ...
    # def test_status_response(self):
    #     self.assertEqual(self.response.status_code, 200)

    def test_projects_titles(self):
        self.assertTrue(project_titles[1] in self.response.text)
        self.assertTrue(project_titles[2] in self.response.text)
        self.assertTrue(project_titles[3] in self.response.text)
```

Execute os testes:

```bash
docker compose exec flask-api python3 -m unittest -v tests/test_home_unittest.py -b
```

Saída esperada:

![Test projects titles](https://content-assets.betrybe.com/prod/997505fc-d4ce-46fc-a3c5-ed9a1cb59193-Test%20projects%20titles.png)

Vamos testar uma rota desconhecida e qual será o seu retorno:

> src/tests/mocks/home.py

```python
# ...

unknown_page = {
    'title': '<h1>Not Found</h1>',
    'text': '<p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>'
}
```

> src/tests/test_home_unittest.py

```python
# ...
from tests.mocks.home import project_cards, project_titles, unknown_page
# ...
    # def test_projects_titles(self):
    #     self.assertTrue(project_titles[1] in self.response.text)
    #     self.assertTrue(project_titles[2] in self.response.text)
    #     self.assertTrue(project_titles[3] in self.response.text)

    def test_unknown_route(self):
        response = app.test_client().get("/unknown")
        self.assertEqual(response.status_code, 404)
        self.assertTrue(unknown_page['title'] in response.text)
        self.assertTrue(unknown_page['text'] in response.text)
```

Execute os testes:

```bash
docker compose exec flask-api python3 -m unittest -v tests/test_home_unittest.py -b
```

Saída esperada:

![Test unknown route](https://content-assets.betrybe.com/prod/997505fc-d4ce-46fc-a3c5-ed9a1cb59193-Test%20unknown%20route.png)


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/b9d963cc-d516-4f86-854f-262dbe185ed7/lesson/7c9c8183-1dc5-4d9f-bb68-79b2807a25eb)
