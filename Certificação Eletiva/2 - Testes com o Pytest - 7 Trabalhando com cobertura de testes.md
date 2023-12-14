[[2 - Testes com o Pytest]]

Você está brilhando! 🌟 Mas ainda podemos avançar na construção dos nossos testes, né?

No exemplo anterior, conseguimos testar muitas partes do nosso código, mas ainda não testamos todas as possibilidades. Vamos ver como podemos fazer isso?!

A **cobertura de testes** é uma métrica crucial que nos auxilia a compreender quais partes do código estão sendo testadas e é fundamental para assegurar que todas essas áreas sejam testadas.

É importante lembrar que a cobertura de testes não garante a correção do código, mas apenas indica que ele está sendo testado. Mesmo com uma cobertura de 100%, se os testes não forem adequados, o código pode não cumprir todas as suas funcionalidades corretamente e até mesmo conter bugs. Portanto, é crucial que os testes sejam bem escritos e cubram todos os **casos de uso** do código.

## Instalando o pytest-cov

Para calcular a cobertura de testes do nosso código, será utilizado o pacote `pytest-cov`. Ele é um dos diversos plugins disponíveis para o `Pytest` que nos ajudam a escrever testes melhores.

> **De olho na dica 👀:** Existem plugins incríveis para o Pytest! Você pode conferir a lista completa de plugins disponíveis [aqui](https://docs.pytest.org/en/7.3.x/reference/plugin_list.html) e aprender como escrever seu próprio plugin [aqui](https://docs.pytest.org/en/7.3.x/how-to/writing_plugins.html#writing-plugins).

Para instalar o `pytest-cov`, basta executar o comando abaixo:

```bash
pip install pytest-cov==4.0.0
```

## Calculando a cobertura de testes

Para entender o comportamento do `pytest-cov`, considere os seguintes arquivos de código:

```python
# arquivo: analyzer.py
import json


def read_json_file(file_path):
    with open(file_path, "r") as file:
        return json.load(file)


def analyze_json_file(file_path):
    if not file_path.endswith(".json"):
        raise ValueError("O arquivo precisa ser um arquivo JSON.")
    
    data = read_json_file(file_path)
    return (
        f"A pessoa de nome {data['nome']} "
        f"possui {data['idade']} anos de idade."
    )

```

Copiar

```python
# arquivo: tests/test_analyzer.py
from unittest.mock import Mock, patch

import pytest

from analyzer import analyze_json_file, read_json_file


def test_analyze_json_file():
    mock_read_json_file = Mock(
        side_effect=[
            {"nome": "Maria", "idade": 31},
            {"nome": "Agenor", "idade": 86},
        ]
    )
    fake_file_path = "invalid.json"

    with patch("analyzer.read_json_file", mock_read_json_file):
        assert (
            analyze_json_file(fake_file_path)
            == "A pessoa de nome Maria possui 31 anos de idade."
        )
        assert (
            analyze_json_file(fake_file_path)
            == "A pessoa de nome Agenor possui 86 anos de idade."
        )

    mock_read_json_file.assert_called_with(fake_file_path)


def test_analyze_json_file_propagates_exception():
    mock_read_json_file = Mock(side_effect=FileNotFoundError)
    fake_file_path = "invalid.json"

    with patch("analyzer.read_json_file", mock_read_json_file):
        with pytest.raises(FileNotFoundError):
            analyze_json_file(fake_file_path)


def test_read_json_file(tmp_path):
    fake_file_path = tmp_path / "fake.json"
    fake_file_path.touch()

    mock_json = Mock()
    mock_json.load = Mock(return_value={"nome": "Maria", "idade": 31})

    with patch("analyzer.json", mock_json):
        result = read_json_file(fake_file_path)

    assert result == {"nome": "Maria", "idade": 31}

```

> **Relembrando 🧠:** A fixture `tmp_path` do Pytest é usada para criar um diretório temporário que será excluído após a execução dos testes. Ela fornece um objeto especial do tipo `pathlib.Path`, e por isso o método `.touch()` pode ser usado para criar o arquivo “fake.json”.

Para calcular a cobertura de testes, vamos executar `pytest` com o argumento `--cov`:

```bash
pytest --cov
```

ou:

```bash
python3 -m pytest --cov
```

O resultado será algo parecido com isso:

![Saída do comando `python3 -m pytest --cov`](https://content-assets.betrybe.com/prod/0d956884-61c3-4d1b-bd14-50030e178d6e-Sa%C3%ADda%20do%20comando%20%60python3%20-m%20pytest%20--cov%60.png)
Saída do comando `python3 -m pytest --cov`

O `pytest-cov` nos mostra a cobertura de execução de cada arquivo do nosso projeto. Nesse caso, há 2 arquivos: o `analyzer.py` e o `test_analyzer.py`. O `test_analyzer.py` tem 100% de cobertura, pois todas as suas linhas foram executadas durante os testes. Porém, o `analyzer.py` tem apenas 89% de cobertura, pois uma das linhas não foi executada. É possível saber isso porque a coluna `Miss` está com o valor `1`.

## Detalhando a cobertura de testes

Como nosso interesse é apenas no arquivo `analyzer.py`, é possível executar o `pytest` novamente ajustando o argumento `--cov` para `--cov analyzer`:

Para entender qual linha não foi executada no arquivo `analyzer.py`, é possível usar o argumento `--cov-report=term-missing`. Adicionando essas duas alterações, o comando fica assim:

```bash
pytest --cov analyzer --cov-report=term-missing
```

> **Anota aí 📝:** O argumento `--cov` recebe um ou mais **módulos ou pacotes**, por isso não se coloca o `.py` ao final de `analyzer`. Se for passado apenas o nome do pacote (pasta), será calculada a cobertura de todos os arquivos do pacote. Já se for passado apenas o nome de um módulo (arquivo), o cálculo será apenas daquele módulo.

O resultado será algo parecido com isso:

![Saída do comando `pytest --cov analyzer --cov-report=term-missing`](https://content-assets.betrybe.com/prod/0d956884-61c3-4d1b-bd14-50030e178d6e-Sa%C3%ADda%20do%20comando%20%60pytest%20--cov%20analyzer%20--cov-report=term-missing%60.png)
Saída do comando `python3 -m pytest --cov analyzer --cov-report=term-missing` (ou `pytest --cov analyzer --cov-report=term-missing`)

Antes de avançar, é importante destacar alguns pontos:

1. Apesar de o argumento `--cov analyzer` ter sido indicado na linha de comando, não há interferência em _**quais testes são executados**_: todos os testes são executados normalmente.
2. Agora sabemos a linha que não foi executada, por meio da coluna `Missing` da tabela! 🎉 Nesse caso, a linha 11 não foi executada.

## Aumentando a cobertura de testes

Agora que você sabe qual linha não foi executada, pode escrever um teste que passe por ela! Vamos lá?! 🚀

## Para fixar

## Exercício 1

Escreva uma nova função de teste no arquivo `test_analyzer.py` que passe pela linha 11 do arquivo `analyzer.py` do exemplo, garantindo 100% de cobertura de testes.

Uma possível solução para o exercício é:

```python
def test_analyze_json_file_raises_exception_when_not_json():
    fake_file_path = "invalid.txt"

    with pytest.raises(
        ValueError, match="O arquivo precisa ser um arquivo JSON."
    ):
        analyze_json_file(fake_file_path)
```

Aqui, você está testando o caso em que o arquivo não é um arquivo JSON. Para isso, foi passado um arquivo com extensão `.txt` para a função `analyze_json_file`. Como esperado, a função lançou um `ValueError` com a mensagem “O arquivo precisa ser um arquivo JSON.”.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/7a9abd0d-1642-4b80-80c8-4c0dfa8e0470/lesson/901b5b23-0ff3-4ca5-ac03-d88154c0a62d)
