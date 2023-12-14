[[2 - Testes com o Pytest]]

Sabe quando você está escrevendo vários testes quase iguais, mudando só os valores passados para a função testada? Ou até mesmo quando está fazendo vários `assert`s em um único teste (o que, no geral, não é uma boa prática)? Sua vida já vai melhorar.

A parametrização de testes é uma funcionalidade do _pytest_ que permite executar o mesmo teste múltiplas vezes com diferentes parâmetros de entrada. É uma forma eficiente de testar várias entradas e saídas de uma função em um único teste.

Para usar a parametrização de testes, basta adicionar o _marker_ `@pytest.mark.parametrize` acima da definição da função. Esse marcador recebe dois argumentos:

- Uma string ou uma sequência (lista, tupla, etc) de strings, representando o nome dos parâmetros.
- Um iterável (geralmente uma lista) com os valores para o parâmetro.
    - Se for mais de um parâmetro, um iterável de um iterável (geralmente uma lista de tuplas).

O _pytest_ irá executar a função de teste para cada conjunto de argumentos especificado.

Considerando nosso arquivo `main.py`:

```python
def mean(numbers):
    """
    Calcula a média de uma lista de números.

    >>> my_list = [1, 2, 3, 4, 5]
    >>> mean(my_list)
    3.0
    >>> mean([2.5, 3.75, 1.25, 4])
    2.875
    >>> mean([])
    Traceback (most recent call last):
    ...
    ZeroDivisionError: division by zero

    """
    return sum(numbers) / len(numbers)

```

Para passar os _doctests_ para arquivos de testes separados, o arquivo `parametrized_test.py` ficaria assim:

Copiar

```python
import pytest

from main import mean


@pytest.mark.parametrize(
    "input_numbers, expected_result",  # 1
    [  # 2
        ([1, 2, 3, 4, 5], 3.0),  # 3
        ([2.5, 3.75, 1.25, 4], 2.875),
    ],
)
def test_mean(input_numbers, expected_result):  # 4
    assert mean(input_numbers) == expected_result


def test_mean_fail():  # 5
    with pytest.raises(ZeroDivisionError):
        mean([])

```

Vamos para as observações:

1. O primeiro argumento, que poderia ser uma lista: [“input_numbers”, “expected_result”].
2. O segundo argumento, uma lista de tuplas, na qual cada tupla da lista é uma rodada a mais do teste, e cada parâmetro declarado no primeiro argumento precisa ter um valor correspondente na tupla.
3. Primeira tupla com dois parâmetros: `input_numbers` (lista de números) e `expected_result` (a média esperada para o teste).
4. Os parâmetros declarados no primeiro argumento de `parametrize` devem ser recebidos pela função de teste.
5. O ideal é parametrizar somente os testes que verificam um mesmo comportamento, mudando apenas os valores: quando o comportamento esperado é diferente, o ideal é criar um novo teste.

Executando os testes relacionados ao termo média com o comando `pytest -k mean -vv`, temos uma saída similar a esta:


```text
=========================== test session starts ============================
collected 11 items / 8 deselected / 3 selected                             

parametrized_test.py::test_mean[input_numbers0-3.0] PASSED           [ 33%]
parametrized_test.py::test_mean[input_numbers1-2.875] PASSED         [ 66%]
parametrized_test.py::test_mean_fail PASSED                          [100%]

===================== 3 passed, 8 deselected in 0.02s =====================
```

Como pode ver, `test_mean` é repetido 2 vezes, uma para cada parâmetro do `parametrize`.

> **De olho na dica 👀:** Se você precisar marcar um teste parametrizado, será necessário passar um `pytest.param(valor, marks=pytest.mark.marcador)` ao invés de simplesmente `valor` para o parametrize. Mais detalhes [aqui](https://docs.pytest.org/en/7.1.x/example/parametrize.html#set-marks-or-test-id-for-individual-parametrized-test).

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/eeb316f5-4729-427e-960b-1397a647e5d7/lesson/25b71cdf-96f9-48a3-8438-1d0288aeb376)
