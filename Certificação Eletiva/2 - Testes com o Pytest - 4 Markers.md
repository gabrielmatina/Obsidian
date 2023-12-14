[[2 - Testes com o Pytest]]

Os _markers_ (marcadores) no _Pytest_ são uma forma de marcar testes com atributos específicos que podem ser usados para executar, filtrar ou pular testes. Os marcadores podem ser definidos usando a sintaxe `@pytest.mark.nome_do_marker` no código de teste.

Para criar seu próprio marcador, basta criar uma função que tenha como argumento um objeto de tipo pytest.mark. Por exemplo, para criar um marcador chamado “slow” dentro de um arquivo `markers_test.py`, você pode definir a seguinte função em um arquivo de teste:

```python
import time

import pytest


@pytest.mark.slow
def test_slow_marker():
    time.sleep(4)

```

Você pode executar o comando `pytest -m MARKEXPR`, no qual **MARKEXPR** é uma expressão que adiciona ou remove a seleção de um ou mais marcadores. Por exemplo, ao rodar `pytest -m 'not slow' -vv` são executados todos os testes, menos os marcados como _slow_.

O resultado será algo como isso aqui:

Copiar

```text
================================================= test session starts =================================================
platform darwin -- Python 3.11.0, pytest-7.3.1, pluggy-1.0.0
collected 8 items / 1 deselected / 7 selected                                                                         

a_test.py::test_a_simple_test PASSED                                                                            [ 14%]
a_test.py::test_sum PASSED                                                                                      [ 28%]
another_test.py::test_list_item_multiply PASSED                                                                 [ 42%]
fixtures_test.py::test_print_to_stdout PASSED                                                                   [ 57%]
fixtures_test.py::test_error_to_stderr PASSED                                                                   [ 71%]
fixtures_test.py::test_my_function PASSED                                                                       [ 85%]
fixtures_test.py::test_generate_output PASSED                                                                   [100%]

================================================== warnings summary ===================================================
markers_test.py:6
  ./pytest_app/markers_test.py:6: PytestUnknownMarkWarning: Unknown pytest.mark.slow - is this a typo?
    You can register custom marks to avoid this warning
    for details, see https://docs.pytest.org/en/stable/how-to/mark.html
    @pytest.mark.slow

-- Docs: https://docs.pytest.org/en/stable/how-to/capture-warnings.html
===================================== 7 passed, 1 deselected, 1 warning in 0.02s ======================================
```

É interessante observar que temos um warning, pois criamos um marcador e não informamos ao _Pytest_ que ele existe. Para corrigir isso, você pode adicionar o seguinte código ao arquivo `conftest.py`:

Copiar

```python
def pytest_configure(config):
    config.addinivalue_line(
        "markers", "slow: marks tests as slow"
    )
```

## Markers built-in

Assim como as fixtures, o _Pytest_ já traz alguns marcadores embutidos. A seguir veremos dois deles!

### Skip

O marcador `skip` serve para pular um teste específico. Ele pode ser útil, por exemplo, em uma situação em que, por alguns dias, um sistema do qual seu código depende está indisponível. A suíte de testes pode quebrar por isso, mas você não quer apagar os testes. Portanto, pular esses testes que estão momentaneamente quebrando pode ser a solução.

Mais útil que o `skip` é a sua variante mais específica `skipif`, que pula um teste a depender de uma condição. Por exemplo, se um teste deve rodar somente em Windows e outro somente em Linux, você pode usar o skipif para rodar somente o teste apropriado para determinada plataforma.

### Xfail

Este é bem interessante porque para o teste marcado com ele passar, na verdade o teste tem que falhar. Confuso, não? 🤔

Primeiramente, é importante salientar que isso é diferente de criar um teste que espera um erro, por exemplo.

Aqui a intenção é que o teste de fato falhe. Por isso o nome do marcador é _expect fail_ (esperamos que falhe).

A [documentação](https://docs.pytest.org/en/stable/how-to/skipping.html) cita um caso de uso, mas temos um exemplo bem interessante aqui na Trybe: testes de testes.

Em alguns projetos cobramos a escrita de testes. Como fazemos para verificar se os testes que você escreveu no projeto funcionam? Simples: fornecemos implementações incorretas da função a ser testada e rodamos seu teste em cima de cada implementação incorreta esperando que o teste falhe (usamos o `XFAIL`). Por fim, rodamos o teste em cima de uma implementação correta e esperamos que ele passe. Se o seu teste **_falhar_** para as implementações incorretas e **_passar_** para a implementação correta, você pontua no requisito!

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/eeb316f5-4729-427e-960b-1397a647e5d7/lesson/89ae3ff1-2318-44ad-9ecf-0883335d4ef5)

