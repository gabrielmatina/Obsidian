[[2 - Testes com o Pytest]]

_Fixtures_ são funções que rodam antes e/ou depois dos testes, geralmente criando um recurso que o teste precisa para rodar ou fazendo uma limpeza após a execução do teste. É muito comum utilizar _fixtures_ para criar dados que serão utilizados por diversos testes.

Vamos ver um primeiro exemplo, deixando nosso arquivo `a_test.py` assim:

Copiar

```python
import pytest


@pytest.fixture # Criamos a fixture por meio do decorador pytest.fixture
def my_list(): # Por padrão, o nome da fixture será o nome da função
    return [1, 2, 3] # Retorna o valor que a fixture possuirá


def test_a_simple_test():
    assert True


def test_sum(my_list): # Recebemos a fixture como parâmetro da função de teste
    assert sum(my_list) == 6 # Usamos a lista retornada pela fixture


def test_list_item_multiply(my_list): # Recebemos a mesma fixture aqui também
    assert [item * 3 for item in my_list] == [3, 6, 9]

```

Como você pode ver, podemos declarar a _fixture_ em um lugar só e usá-la em várias funções de teste.

> **Atenção ⚠️:** Decoradores são uma poderosa ferramenta em Python, pois permitem estender e modificar o comportamento de funções e classes de forma flexível e modular, melhorando a legibilidade e a reutilização do código. No caso acima, utilizamos um para a criação da função pytest.fixture, o que é evidenciado pela sintaxe que utiliza o símbolo `@`. Não se preocupe com essa sintaxe por enquanto, mas fique a vontade para ler mais sobre decoradores [aqui](https://docs.python.org/pt-br/3/glossary.html#term-decorator).

## Fixtures em vários arquivos

No exemplo anterior, usamos as _fixtures_ em dois testes somente - e ambos estavam no mesmo arquivo, mas poderíamos usá-las até mesmo em testes em vários arquivos diferentes.

Um pensamento natural que você pode ter a seguir é achar que se definimos a _fixture_ em um arquivo, devemos importá-la para usar em outro. Mas **não**! 🤯

Pode ser estranho em um primeiro momento, mas o que ocorre é que isso poderia gerar problemas de importações circulares (_`A` importa algo de `B` > `B` importa algo de `A` > etc etc_), por isso o _pytest_ utiliza arquivos chamados `conftest.py`. Podemos ter um arquivo deste para cada sub-diretório, e as suas definições valerão para todos os arquivos de teste do respectivo diretório (e sub-diretórios também).

Poderíamos dividir nosso arquivo `a_test.py` em 3: o próprio `a_test.py`, `another_test.py` e `conftest.py`. Com isso, movemos a _fixture_ para o `conftest.py`, e um dos testes de `a_test.py` para `another_test.py`:

```python
# conftest.py
import pytest


@pytest.fixture
def my_list():
    return [1, 2, 3]

```

```python
# a_test.py
def test_a_simple_test():
    assert True


def test_sum(my_list):
    assert sum(my_list) == 6

```

```python
# another_test.py
def test_list_item_multiply(my_list):
    assert [item * 3 for item in my_list] == [3, 6, 9]

```

De forma “mágica”, a _fixture_ `my_list` definida no `conftest.py` pode ser acessada nos arquivos `a_test.py` e `another_test.py`.

Rode os testes e perceba que continuam passando!

## Escopo de fixtures

O escopo das _fixtures_ do _pytest_ determina em qual momento uma _fixture_ será inicializada e finalizada durante a execução dos testes. Existem cinco escopos disponíveis:

- _function_ (função): é inicializada a cada função de teste que a utiliza;
- _module_ (módulo): é inicializada uma vez para cada módulo de teste que a utiliza;
- _class_ (classe): é inicializada uma vez para cada classe de teste que a utiliza;
- _package_ (pacote): é inicializada apenas uma vez para cada diretório que contém arquivos de teste;
- _session_ (sessão): é inicializada apenas uma vez, no início da execução da suíte de testes.

O escopo das _fixtures_ pode ser definido por meio de um parâmetro na declaração da _fixture_ ou em um arquivo `conftest.py`.

O escopo padrão é o de função, o que significa que uma nova instância da _fixture_ será criada para cada função de teste que a solicitar. Isso garante que as mudanças feitas em uma função de teste não afetem outras funções de teste que dependam da mesma _fixture_.

Se uma _fixture_ cria, por exemplo, um objeto de banco de dados para ser usado em um teste, a criação desse objeto pode ser custosa, então é melhor não recriá-lo para cada teste. No entanto, se um teste modifica o objeto do banco de dados, isso pode afetar outros testes que dependem da mesma _fixture_. Portanto, uma nova instância da _fixture_ é criada para cada teste para evitar efeitos colaterais indesejados, já que idealmente um teste nunca deve depender de outro teste para passar.

Para mudar o escopo, basta passar o escopo desejado como argumento para o parâmetro `scope` do decorador `pytest.fixture`, por exemplo `@pytest.fixture(scope="module")`.


# Fixtures builtin

O _Pytest_ já vem com várias _fixtures_ úteis prontas para nosso uso. Veremos brevemente três delas!

> **De olho na dica 👀:** Para os exemplos em código, considere que eles devem ser colocados em um arquivo `fixtures_test.py`.

## Capsys

Você usa `print` ao invés de retornar uma string nas suas funções? Aposto que faz isso para criar sua interface com quem usa seu software ou para debugar algumas coisas no _server side_. Lembre-se de que podemos utilizar ferramentas mais apropriadas pra isso!

Contudo, existem outras ocasiões em que o uso de `print` é essencial, por exemplo, quando desenvolvemos uma aplicação com uma [_CLI_](https://blog.betrybe.com/tecnologia/tudo-sobre-cli/). E aí, como testar?

A _fixture_ `capsys` é usada para capturar as saídas padrão e de erro em um teste. Com isso, é possível verificar se a saída está correta ou fazer asserções nas mensagens de erro. Por exemplo, considere este arquivo de teste:

```python
# Sim, é só receber `capsys` como parâmetro em qualquer função de teste que o
# pytest faz o resto da magia acontecer
def test_print_to_stdout(capsys):
    print("Hello, world!")
    captured = capsys.readouterr()
    assert captured.out == "Hello, world!\n"  # print coloca \n automaticamente

```

A função `print` escreve no `stdout`. A _fixture_ `capsys` tem um método chamado `readouterr`, que lê as saídas padrão e de erro e retorna um objeto contendo `err` e `out`. Em seguida, é só verificar se a saída corresponde ao esperado.

Além disso, a _fixture_ `capsys` também pode ser usada para capturar a saída de erro padrão `stderr`, como no exemplo abaixo:

```python
def test_error_to_stderr(capsys):
    import sys
    sys.stderr.write("Error message\n")
    captured = capsys.readouterr()
    assert captured.err == "Error message\n"
```

Dá uma olhadinha [na documentação da `capsys`](https://docs.pytest.org/en/7.3.x/reference/reference.html?highlight=capsys#std-fixture-capsys) ou no [tutorial de captura do stdout/stderr](https://docs.pytest.org/en/7.1.x/how-to/capture-stdout-stderr.html#captures) para mais detalhes!

## Monkeypatch

Além de `print` para a saída, você usa `input` para coletar as entradas no seu programinha, né?

O uso de `input` pode não ser uma boa prática, pois deixa o código menos flexível, necessitando de uma interação manual durante a execução do programa. Em vários casos, melhor que utilizar `input` é receber as entradas como parâmetros do seu script (pesquise sobre `sys.argv`).

Partindo para um exemplo, vamos imaginar que você decidiu criar uma CLI que usa suas funções e que utiliza `input` para isso. A partir disso, vamos ver uma _fixture_ que pode ajudar com os testes da CLI, simulando entradas digitadas pela pessoa que usa a aplicação.

A _fixture_ `monkeypatch`, do _pytest_, é usada para alterar o comportamento de funções ou métodos, permitindo a simulação de condições específicas para testes. Ela permite o acesso ao objeto “patch” que pode ser utilizado para modificar objetos ou módulos importados, bem como variáveis de ambiente, argumentos de linha de comando e outros.

Por exemplo, se você quiser testar uma função que chama a função `input()` do Python, pode usar a `monkeypatch` para simular a entrada da pessoa usuária sem precisar realmente digitar. Para fazer isso, você pode usar o método `setattr` da _fixture_ `monkeypatch` para substituir o objeto `input` da biblioteca padrão por uma função que retorna uma string de entrada simulada. Em seguida, quando a função testada chamar a função `input()`, ela obterá a entrada simulada em vez de esperar que quem a utiliza digite algo.

Aqui está um exemplo de como usar a _fixture_ `monkeypatch` para modificar o comportamento da função `input()`:

```python
def my_function():
    return f"Você digitou {input('Digite algo: ')}!"


def test_my_function(monkeypatch):
    # Input recebe um parâmetro que mock_input não usa, por isso o _
    def mock_input(_):
        return "Python"

    # Trocamos a input do sistema pela nossa mock_input
    monkeypatch.setattr("builtins.input", mock_input)
    output = my_function()

    assert output == "Você digitou Python!"

```

Dá uma olhadinha [na documentação](https://docs.pytest.org/en/7.1.x/reference/reference.html#monkeypatch) ou no [tutorial de uso do monkeypatch](https://docs.pytest.org/en/7.3.x/how-to/monkeypatch.html) para mais detalhes!

## Diretório temporário

Quando você está lidando com arquivos em testes, às vezes precisa criar alguns arquivos temporários, mas não quer poluir seu projeto ou sistema. O que fazer?

A _fixture_ `tmp_path` do _pytest_ é usada para criar um diretório temporário em que um teste pode criar e manipular arquivos. O _pytest_ se encarrega de criar e limpar o diretório temporário automaticamente antes e depois dos testes que utilizam a fixture.  
`tmp_path` retorna um objeto [`pathlib.Path`](https://docs.python.org/3/library/pathlib.html#pathlib.Path), que pode ser utilizado como uma string de caminho para um diretório, bem como pode fazer mais coisas.

> A `pathlib` é uma biblioteca bem bacana do Python e muito utilizada em aplicações no mercado de trabalho. Se não conhece, convém dar uma olhadinha [na documentação](https://docs.python.org/3/library/pathlib.html).

Um bom caso de uso da _fixture_ `tmp_path` pode ser para testar uma função que cria arquivos, por exemplo uma função que gera um arquivo de saída. O teste pode criar um diretório temporário com a _fixture_ e chamar a função a ser testada, passando o diretório temporário como argumento. Então, o teste pode verificar se a função criou os arquivos corretamente dentro do diretório temporário.

```python
import json
import os


def generate_output(content, path):
    with open(path, "w", encoding="utf-8") as file:
        file.write(json.dumps(content))


def test_generate_output(tmp_path):
    content = {"a": 1}
    output_path = tmp_path / "out.json"
    # O operador '/' funciona na linha anterior pois temp_path não é uma
    # string comum, mas sim um objeto Path

    generate_output(content, output_path)

    assert os.path.isfile(output_path)
    with open(output_path) as file:
        assert file.read() == '{"a": 1}'

```

Sempre que precisar, consulte a [documentação da `temp_path`](https://docs.pytest.org/en/7.3.x/reference/reference.html?highlight=tmp_path#std-fixture-tmp_path) ou o [tutorial de uso de diretórios e arquivos temporários](https://docs.pytest.org/en/7.3.x/how-to/tmp_path.html?highlight=tmp_path).

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/eeb316f5-4729-427e-960b-1397a647e5d7/lesson/8ea02bf1-35fa-4e43-af7f-7441d4b36c1c)
