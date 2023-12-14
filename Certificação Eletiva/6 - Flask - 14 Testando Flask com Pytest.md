[[6 - Flask]]

Como já vimos antes, o `Pytest`pode ser utilizado para criar vários tipos de teste, e ele se encaixa também quando o assunto é testar o `Flask`.

Os testes que iremos construir no `pytest` são bem parecidos com os construídos no `unittest`, com algumas diferenças de sintaxes e estrutura, mas os testes farão as mesmas verificações. Vamos ver como será feito isso:

Primeiro vamos instalar o `pytest`:

```bash
pip install pytest==7.3.1
```

Vamos criar um arquivo chamado `conftest.py` e adicionar o seguinte código a ele:

```bash
touch src/tests/conftest.py
```

> src/tests/conftest.py

```python
import pytest
from app import app


@pytest.fixture
def client():
    return app.test_client()


@pytest.fixture
def response(client):
    return client.get("/")
```

No código acima definimos 2 `fixtures` (funções que rodam antes e/ou depois dos testes) para o nosso teste `client` que nos permite simular requisições à aplicação e `response` que faz a requisição para a aplicação e nos fornece o retorno dessa requisição.

Vamos criar nosso teste e verificar o status da resposta da nossa requisição no arquivo `test_home_pytest.py`:

```bash
touch src/tests/test_home_pytest.py
```

> src/tests/test_home_pytest.py

```python
def test_status_response(response):
    assert response.status_code == 200
```

Execute os testes:

```bash
python3 -m pytest -v
```

Saída esperada:

![Test status response success](https://content-assets.betrybe.com/prod/73aeda5f-d0be-48e6-9e1f-f07c3aad53f5-Test%20status%20response%20success.png)

A nossa função irá receber como parâmetro o a `fixture` e isso nos permitirá acessar as informações que ela retorna. O novo teste assim como os anteriores continuam passando e eles não interferem uns nos outros. Se quiser mude o status esperado para `400` e execute novamente os testes.

Saída esperada:

![Test status response failure](https://content-assets.betrybe.com/prod/73aeda5f-d0be-48e6-9e1f-f07c3aad53f5-Test%20status%20response%20failure.png)

Altere de volta para `200` o status esperado e vamos criar nosso próximo teste. Vamos testar se foram criados os containers para cada um dos nossos 3 projetos:

> src/tests/test_home_pytest.py

```python
# ...
from src.tests.mocks.home import project_cards
# ...
# def test_status_response(response):
    # assert response.status_code == 200

def test_quantity_of_projects(response):
    assert response.text.count(project_cards) == 3
```

Execute os testes:

```bash
python3 -m pytest -v
```

Saída esperada:

![Test quantity of projects](https://content-assets.betrybe.com/prod/73aeda5f-d0be-48e6-9e1f-f07c3aad53f5-Test%20quantity%20of%20projects.png)

Vamos agora testar se os títulos dos 3 projetos estão sendo renderizados, criaremos um dicionário para guardar as informações dos títulos dos 3 projetos que queremos testar:

> src/tests/test_home_pytest.py

```python
# ...
from src.tests.mocks.home import project_cards, project_titles
# ...
# def test_quantity_of_projects(response):
#     assert response.text.count(project_cards) == 3

def test_projects_titles(response):
    assert project_titles[1] in response.text
    assert project_titles[2] in response.text
    assert project_titles[3] in response.text
```

Execute os testes novamente e confira a saída:

![Test projects titles](https://content-assets.betrybe.com/prod/73aeda5f-d0be-48e6-9e1f-f07c3aad53f5-Test%20projects%20titles.png)

Vamos testar uma rota desconhecida e qual será o seu retorno:

> src/tests/test_home_pytest.py

```python
# ...
from mocks.home import project_cards, project_titles, unknown_page
# ...
# def test_projects_titles(response):
     assert project_titles[1] in response.text
     assert project_titles[2] in response.text
     assert project_titles[3] in response.text

def test_unknown_route(client):
    response = client.get("/unknown")
    assert response.status_code == 404
    assert unknown_page['title'] in response.text
    assert unknown_page['text'] in response.text
```

Executando os testes, a saída esperada será a da imagem a seguir:

![Test projects titles](https://content-assets.betrybe.com/prod/73aeda5f-d0be-48e6-9e1f-f07c3aad53f5-Test%20projects%20titles.png)


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/b9d963cc-d516-4f86-854f-262dbe185ed7/lesson/a98278cf-14b8-4579-8707-dfbe7cf11fe5)
