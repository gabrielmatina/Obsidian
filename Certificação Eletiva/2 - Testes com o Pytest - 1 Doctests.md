[[2 - Testes com o Pytest]]

Em Python existem várias formas de se escrever e executar testes unitários. Utilizar _doctests_ (testes na documentação) é uma bem interessante e possivelmente a mais simples delas.

**Doctests** se baseiam na capacidade de criar exemplos em **docstrings** (strings de documentação). O módulo `doctest`, que já vem com o Python, roda todos os exemplos encontrados em _docstrings_ como se fossem testes, garantindo que funcionam.

Além de ajudar a garantir que o código esteja funcionando corretamente ao longo do tempo como testes normais, uso de _doctests_ tem várias vantagens. Dentre elas, temos:

1. Fornecer uma forma fácil e rápida de, ao mesmo tempo, criar testes e documentar como uma função deve ser usada, já que a documentação e os testes estão no mesmo lugar.
2. Facilitar o desenvolvimento orientado a testes (TDD), visto que os exemplos ficam logo na função, dispensando a necessidade de outro arquivo ou a criação de uma suíte de testes complexa.

É importante observar que _doctests_ não são a melhor opção para todos os casos de teste. Eles são mais adequados para testar funções simples que tenham uma entrada e uma saída bem definidas, e não são tão bons para testes mais complexos ou para testar funções que dependem de dados externos ou aleatórios.

## Como escrever testes com a `doctest`

Os _doctests_ usam a sintaxe de exemplos baseada no _REPL_ do Python: Você pode indicar expressões como colocaria no _REPL_, precedidas de `>>>` e um espaço, e colocar a saída na linha posterior.

Vamos pegar nosso primeiro bug do dia? Observe o exemplo de uso de _doctests_ abaixo. Salve em um arquivo `main.py` (ou com o nome que preferir) e execute o comando `python -m doctest main.py`

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
    0

    """
    return sum(numbers) / len(numbers)

```

O resultado da execução será algo como:

```text
**********************************************************************
File "main.py", line 10, in main.mean
Failed example:
    mean([])
Exception raised:
    Traceback (most recent call last):
      File "~/.pyenv/versions/3.11.0/lib/python3.11/doctest.py", line 1350, in __run
        exec(compile(example.source, filename, "single",
      File "<doctest main.mean[3]>", line 1, in <module>
        mean([])
      File "main.py", line 14, in mean
        return sum(numbers) / len(numbers)
               ~~~~~~~~~~~~~^~~~~~~~~~~~~~
    ZeroDivisionError: division by zero
**********************************************************************
1 items had failures:
   1 of   4 in main.mean
***Test Failed*** 1 failures.
```

Com isso podemos perceber que, ao passar uma lista vazia (linha 10), temos um `ZeroDivisionError`, pois `len(numbers)` é `0`.  
Podemos corrigir isso de duas formas:

1. Corrigimos nossa função para verificar o tamanho da lista e retornar 0 caso seja uma lista vazia (mais recomendável nesse caso).
2. Ajustamos nosso exemplo na _docstring_ para esperar um erro (menos recomendável nesse caso, mas será mostrado como fica para quando você precisar verificar erros em outros cenários).

A solução 1 seria deixar o código mais ou menos assim:

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
    0

    """
    # Adicionamos as duas linhas abaixo. O resto continua igual.
    try:
        return sum(numbers) / len(numbers)
    except ZeroDivisionError:
        return 0

```

Já a solução 2 seria deixar o código mais ou menos assim:

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

A constante Python _Ellipsis_, representada pelos três pontos (`...`), serve para indicar que existem mais linhas na saída, mas que o conteúdo delas não importa para o exemplo (visto que vão variar a depender de em qual computador o teste está sendo executado).

## Para fixar

## Exercício 1

A função `sum_two_numbers` abaixo contém um bug. Crie um exemplo na _docstring_ que pega esse bug ao rodar o módulo `doctest` e, em seguida, corrija-o.

```python
def sum_two_numbers(a, b):
    """Retorna a soma de dois números recebidos por parâmetro.

    Exemplos
    --------
    >>> sum_two_numbers(0, 0)
    0
    """
    return a - b

```

```python
def sum_two_numbers(a, b):
    """Retorna a soma de dois números recebidos por parâmetro.

    Exemplos
    --------
    >>> sum_two_numbers(0, 0)
    0
    >>> sum_two_numbers(2, 2) # Novo caso de teste
    4
    """
    return a + b # Bug corrigido
```