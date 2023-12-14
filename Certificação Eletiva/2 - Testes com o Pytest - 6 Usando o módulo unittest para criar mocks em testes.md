[[2 - Testes com o Pytest]]

O módulo `unittest` é uma biblioteca de testes unitários nativa do Python. Ele é inspirado no JUnit, um _framework_ de testes para Java. O `unittest` é uma ferramenta muito poderosa, mas também é um pouco mais complexa que o _Pytest_. Por isso, vamos aprender a utilizá-la aos poucos.

A grande vantagem de utilizar o `unittest` é que ele já vem instalado com o Python, então não é necessário instalar nenhuma biblioteca adicional. Além disso, ele é uma ferramenta muito completa, que nos permite criar _mocks_ de forma poderosa e organizada.

E melhor: o _Pytest_ se integra muito bem com o `unittest`, então é possível utilizar os dois juntos! 🤩

Para executar os códigos de exemplo, utilize o _Pytest_. Para isso, crie o ambiente virtual (`python -m venv .venv`), garanta que ele esteja ativo (`source .venv/bin/activate`) e instale o _Pytest_ com o comando `pip install pytest==7.3.1`.

## Criando um _mock_ com o `unittest`

Para criar um _mock_ com o `unittest`, é preciso utilizar a classe `unittest.mock.Mock`. Essa classe é muito parecida com a fixture `monkeypatch` do Pytest, mas um pouco mais complexa.

Vamos a um exemplo com leitura de arquivos JSON! Primeiro, vamos criar um arquivo `person_data.json` com o seguinte conteúdo:

```json
{
    "nome": "João",
    "idade": 30
}
```

Para esse exemplo de manipulação de arquivos JSON, criaremos três arquivos:

1. `analyzer.py`, que vai conter as funções que vamos testar;
2. `tests/test_analyzer.py`, que vai conter os testes;
3. `tests/__init__.py`, que ficará vazio mesmo. Seu propósito é indicar ao Python que a pasta `tests` é um pacote Python.

```python
# arquivo: analyzer.py
import json


def read_json_file(file_path):
    with open(file_path, "r") as file:
        return json.load(file)


def analyze_json_file(file_path) -> str:
    if not file_path.endswith(".json"):
        raise ValueError("O arquivo precisa ser um arquivo JSON.")

    data = read_json_file(file_path)
    return (
        f"A pessoa de nome {data['nome']} "
        f"tem {data['idade']} anos de idade."
    )


```

```python
# arquivo: tests/test_analyzer.py
from analyzer import analyze_json_file


def test_analyze_json_file():
    result = analyze_json_file("person_data.json")
    assert result == "A pessoa de nome João tem 30 anos de idade."
```

> **De olho na dica 👀:** Quando um pacote é importado em um módulo Python, o interpretador procura pelo arquivo `__init__.py` dentro do diretório correspondente ao pacote. A presença desse arquivo indica que o diretório é um pacote válido e pode ser importado.

O teste que implementamos funciona, mas ele não é muito bom. Ele depende de um arquivo externo, e isso pode causar problemas. Por exemplo, se o arquivo `person_data.json` for apagado ou mesmo alterado, o teste vai falhar.

Para resolver esse problema, criaremos um _mock_ para a função `read_json_file`, ou seja, um objeto que se comporta como a função `read_json_file`. Para isso, serão utilizadas a classe `unittest.mock.Mock` e a função `patch`:

Copiar

```python
# arquivo: testes/teste_analyzer.py

# Passo 1
from unittest.mock import Mock, patch

from analyzer import analyze_json_file


def test_analyze_json_file():
    # Passo 2
    mock_read_json_file = Mock(return_value={"nome": "Maria", "idade": 31})
    fake_file_path = "invalid.json"

    # Passo 3
    with patch("analyzer.read_json_file", mock_read_json_file):
        result = analyze_json_file(fake_file_path)

    assert result == "A pessoa de nome Maria tem 31 anos de idade."

```

Vamos entender o que foi feito aqui:

1. Primeiro, foram importadas a classe `Mock` e a função `patch` do módulo `unittest.mock`.
2. Depois, foi criado um _mock_ para a função `read_json_file` com a classe `Mock`.
    - Com o parâmetro `return_value=`, foi definido que esse _mock_ sempre retornará um dicionário com os dados da pessoa.
3. Por fim, a função `patch` foi utilizada para substituir a função `read_json_file` dentro do módulo `analyzer` pelo _mock_ criado.
    - Assim, quando a função `analyze_json_file` for executada, ela utilizará o _mock_ no lugar da função `read_json_file`.

O resultado é que o teste vai funcionar independentemente da existência do arquivo, pois o _mock_ criado sempre vai retornar o mesmo dicionário. 🤩

> **Anota aí 📝:** Podemos usar o patch como um decorador (`@patch(...)`) acima da assinatura da função, ou como um _context manager_ (`with patch(...):`) dentro da função. Nesse exemplo utilizamos o _context manager_, e temos a vantagem de que o _patch_ só está aplicado ‘dentro’ da identação do `with`.

## Evoluindo nosso exemplo

### Averiguando se um _mock_ foi chamado com os argumentos corretos

Até o momento, não há muita coisa diferente do que já havia sido feito com a fixture `monkeypatch`, do Pytest. Mas o `unittest` tem uma vantagem: ele nos permite averiguar se um _mock_ foi chamado, o que é muito útil para testar funções que chamam outras funções, por exemplo.

Para averiguar se um _mock_ foi chamado, utilizaremos o método `assert_called_with` do _mock_. Veja um exemplo:

Copiar

```diff
# arquivo: testes/teste_analyzer.py
from unittest.mock import Mock, patch

from analyzer import analyze_json_file


def test_analyze_json_file():
    mock_read_json_file = Mock(return_value={"nome": "Maria", "idade": 31})
    fake_file_path = "invalid.json"

    with patch("analyzer.read_json_file", mock_read_json_file):
        result = analyze_json_file(fake_file_path)
    
    assert result == "A pessoa de nome Maria tem 31 anos de idade."
+   mock_read_json_file.assert_called_with(fake_file_path)
```

Agora, se a função `read_json_file` não for chamada no decorrer da execução, o teste vai falhar!

> **De olho na dica 👀:** objetos Mock têm diversos métodos para averiguar se foram chamados. Você pode conferir a lista completa [na documentação do módulo unittest](https://docs.python.org/3/library/unittest.mock.html#unittest.mock.Mock).

### Explorando mais poderes do `unittest.mock`

O `Mock` e o `patch` do `unittest.mock` são ferramentas muito poderosas! Além do que já foi mostrado, alguns outros exemplos do que elas possibilitam são:

- Criar um _mock_ com atributos e métodos (que podem ser _mocks_ também).
- Criar um _mock_ que retorna um valor diferente a cada chamada.
- Criar um _mock_ que lança uma exceção.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/7a9abd0d-1642-4b80-80c8-4c0dfa8e0470/lesson/b55fbb58-01c9-445c-8da2-5ebcef024aaf)
