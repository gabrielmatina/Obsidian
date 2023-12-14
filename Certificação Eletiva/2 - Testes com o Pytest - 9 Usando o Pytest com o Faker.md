[[2 - Testes com o Pytest]]

Agora que você já sabe como usar o `Faker` para criar dados falsos, pode integrá-lo com o `Pytest` para criar testes mais robustos.

Para a sua alegria, ao instalar o `Faker`, você ganha automagicamente~ uma _fixture_ para usar no `Pytest`! 🎉

Para usá-la, basta passar o parâmetro `faker` para a função de teste (_por ser uma fixture, não precisa importar nada!_). Por exemplo:

```python
def test_faker_email(faker):
    fake_email = faker.email()
    assert isinstance(fake_email, str)
    assert '@' in fake_email
    assert '.' in fake_email

```

## Garantindo os mesmos resultados em cada execução

Diferentemente do uso “puro” do `Faker`, a _fixture_ `faker` já possui a _seed_ configurada por padrão com o valor `0`, garantindo que os dados gerados sejam sempre os mesmos.

Se, em seu cenário de testes, for importante usar uma _seed_ diferente, será necessário criar uma outra _fixture_ especial chamada `faker_seed` que retorna o valor desejado. Como se deseja que os todos os testes aproveitem a mesma _fixture_ `faker_seed`, é possível configurá-la no arquivo `conftest.py`:

```python
# Arquivo: tests/conftest.py
import pytest


@pytest.fixture(scope="session", autouse=True)
def faker_seed():
    return "Trybe"

```

> **Anota aí 📝:** passar para uma fixture `autouse=True` faz com que ela seja utilizada pelas funções de teste mesmo que elas não recebam a fixture explicitamente como parâmetro.

## Configurando o locale do Faker no Pytest

Assim como foi feita a configuração da _seed_, também é possível configurar o _locale_ criando uma _fixture_ especial chamada `faker_locale`:

```python
# Arquivo: tests/conftest.py
import pytest


@pytest.fixture(scope="session", autouse=True)
def faker_seed():
    return "Trybe"


@pytest.fixture(scope="session", autouse=True)
def faker_locale():
    return "pt_BR"

```

Alguns pontos importantes sobre o exemplo:

- A _fixture_ `faker_seed` foi criada no arquivo `conftest.py` para que possa ser usada em todos os testes.
- A _fixture_ `faker_seed` foi configurada com o escopo `session` para que a _seed_ seja aplicada em todos os testes.
- A _fixture_ `faker_seed` foi configurada com o parâmetro `autouse=True` para que a _seed_ seja aplicada automaticamente em todos os testes.

## Exercício 3

Altere a função de teste `test_create_user` para utilizar o `Faker` a fim de gerar os dados de teste, em vez de dados fixos:



```python
def create_user(name, email):

    return {
        "first_name": name.split()[0],
        "last_name": name.split()[-1],
        "email": email,
        "email_domain": email.split("@")[-1],
    }

def test_create_user():
    fake_email = "teste.trybe@example.com"
    fake_name = "Teste Trybe"

    res = create_user(fake_name, fake_email)

    assert fake_name.startswith(res["first_name"])
    assert fake_name.endswith(res["last_name"])
    assert res["email"] == fake_email
    assert fake_email.endswith(res["email_domain"])

```

### Solução

Para utilizar o `faker` com o `Pytest`, basta:

1. Instalar a _biblioteca_ `faker` (no seu ambiente virtual): `pip install faker`.
2. Adicionar o uso da _fixture_ `faker` na função de teste: `def test_create_user(faker):`.

No caso do exercício, basta alterar a definição das variáveis `fake_email` e `fake_name` para usar os métodos `email` e `name` da _fixture_ `faker`:

Uma possível solução para o exercício é:

```python
def create_user(name, email):

    return {
        "first_name": name.split()[0],
        "last_name": name.split()[-1],
        "email": email,
        "email_domain": email.split("@")[-1],
    }


def test_create_user(faker):
    fake_email = faker.email()
    fake_name = faker.name()

    res = create_user(fake_name, fake_email)

    assert fake_name.startswith(res["first_name"])
    assert fake_name.endswith(res["last_name"])
    assert res["email"] == fake_email
    assert fake_email.endswith(res["email_domain"])

```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/7a9abd0d-1642-4b80-80c8-4c0dfa8e0470/lesson/e4cc467f-3433-4f83-9840-7406c616b7a6/solution)
