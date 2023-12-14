[[2 - Testes com o Pytest]]

O _Pytest_ é uma ferramenta poderosa para a criação de testes, desde os simples até os mais complexos. Para isso, além de oferecer várias funcionalidades, sua utilização é bem simples.

Chegou a hora de aprendermos a configurar o _Pytest_ e criar alguns testes com ele. Além disso, veremos também como executar _doctests_ com o _Pytest_ e como rodar os testes diretamente no VS Code.

## Instalando o Pytest

O passo a passo para usar o _pytest_ é bem simples:

0. Garanta que está com seu ambiente virtual devidamente criado e ativado.
    1. Crie o ambiente virtual com o comando `python -m venv .venv`.
    2. Ative o ambiente virtual com o comando `source .venv/bin/activate`.
1. Instale o _Pytest_ com o comando `pip install pytest==7.3.1`.
2. Confirme que a instalação foi bem sucedida rodando o comando `pytest --version`.

## Criando um teste

Para criarmos testes, precisamos primeiramente criar arquivos de teste.

O _Pytest_ procura automaticamente arquivos Python (com extensão `.py`) que comecem com `test_` ou que terminem com `_test.py`. O primeiro formato ajuda a identificar arquivos de teste, mas o segundo facilita na busca por arquivos em ordem alfabética e facilita a visualização. Você pode escolher o formato que achar melhor.

Os testes em si, devem ser funções que comecem com o prefixo `test_`. Vamos a um exemplo!

Crie um arquivo chamado `a_test.py` com o seguinte conteúdo:

```python
def test_a_simple_test():
    assert True

```

Rode o teste com o comando `pytest` e você verá que um teste passou com sucesso. Além disso, é possível ver um ponto ao lado do nome do arquivo, indicando que o único teste que tem lá passou.

> **De olho na dica 👀:** Você pode ver uma saída mais verbosa utilizando o comando `pytest -vv`.

Modifique a linha 2 de `assert True` para `assert False`, rode o _pytest_ novamente e veja como ele indica que o teste falhou.

### Como funcionam os testes

De maneira geral, os testes com o _pytest_ são bem simples e utilizam a palavra reservada `assert`, do Python. Se sua função de teste não levantar nenhum erro, seu teste passa. Já se levantar um `AssertionError`, significa que seu teste não passou.

Se levantar algum erro diferente de `AssertionError`, é provável que você tenha feito alguma configuração errada da suíte de testes ou corrompido seu código fonte (por exemplo, tentando importar uma função de um arquivo que foi movido ou deletado).

Um exemplo de teste para verificar uma soma de números pode ser:

```python
def test_sum():
    assert sum([1, 2, 3]) == 6

```

Um exemplo de teste para verificar a multiplicação de elementos de uma lista por uma constante pode ser:

```python
def test_list_item_multiply():
    my_list = [1, 2, 3]
    assert [item * 3 for item in my_list] == [3, 6, 9]

```

Os arquivos de teste podem, opcionalmente, ficar dentro de subdiretórios. O _Pytest_ faz uma busca em todos os diretórios dentro do atual, portanto, é bem comum termos um diretório chamado `tests` separado do código fonte da aplicação.

## Rodando doctests com o Pytest

Depois de criar _doctests_, não queremos simplesmente “perdê-los” ao criar testes com o _Pytest_. O _Pytest_ pode rodar, além dos testes que criamos nos arquivos, _doctests_. Para isso, basta incluir a flag `doctest-modules` no comando de execução dos testes no terminal.

Crie um arquivo chamado `main.py` no mesmo diretório que o arquivo `a_test.py` e cole o seguinte conteúdo:

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

Ao rodar o comando `pytest --doctest-modules -vv`, é possível observar uma saída semelhante a esta:

```text
=================== test session starts ====================
collected 3 items                                          

a_test.py::test_a_simple_test PASSED                 [ 33%]
a_test.py::test_list_multiply PASSED                 [ 66%]
main.py::main.mean PASSED                            [100%]

==================== 3 passed in 0.01s =====================
```

É interessante observar que o _doctest_ inteiro é tratado como um teste só.

## Rodando os testes no VS Code

O VS Code tem um menu exclusivo para visualizar e executar os testes, que funciona com várias linguagens. Por lá, você pode ver cada um dos testes, executar um teste individual ou um conjunto de testes em específico, ou até mesmo abrir o debugger direto em um teste.

Para integrar corretamente o _pytest_ com o VS Code, você precisa da [extensão do Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) instalada. Para termos uma melhor experiência com essa feature do VS Code, vamos também fazer alguns ajustes no arquivo de configurações do próprio editor. Para isso, você pode abrir seu arquivo de configurações por meio do atalho `F1` e digitar no campo que aparece: “_Preferences: Open User Settings (JSON)_“ (ou “_Preferências: Abrir as Configurações do Usuário (JSON)_, se seu VS Code estiver em português). Basta pressionar a tecla enter e adicionar os seguintes pares de chave/valor no arquivo que irá aparecer:

```json
{ // Chave de abertura das configurações. Apague caso vá copiar para um arquivo existente
    "python.testing.pytestEnabled": true, // Habilita o pytest
    "python.testing.pytestArgs": [ // Argumentos do pytest
        "--doctest-modules", // Procura por doctests em arquivos .py
        "-vv", // Aumenta o nível de verbosidade
    ],
} // Chave de fechamento das configurações.
```

Você pode abrir a janela de testes por meio do menu `View -> Testing` (`Ver -> Testar`, no VS Code em português).

![VS Code testing: Aba de testes na direita listando todos os testes em uma hierarquia e arquivos que contém testes na esquerda, com botões triangulares verdes ao lado esquerdo de cada teste, onde normalmente apareceria o símbolo de breakpoint](https://content-assets.betrybe.com/prod/b0e3f517-9a3d-4d8b-b7ba-953296bec8eb-VS%20Code%20testing:%20Aba%20de%20testes%20na%20direita%20listando%20todos%20os%20testes%20em%20uma%20hierarquia%20e%20arquivos%20que%20cont%C3%A9m%20testes%20na%20esquerda,%20com%20bot%C3%B5es%20triangulares%20verdes%20ao%20lado%20esquerdo%20de%20cada%20teste,%20onde%20normalmente%20apareceria%20o%20s%C3%ADmbolo%20de%20breakpoint.png)VS Code testing: Aba de testes na direita listando todos os testes em uma hierarquia e arquivos que contêm testes na esquerda, com botões triangulares verdes ao lado esquerdo de cada teste, onde normalmente apareceria o símbolo de breakpoint

Para rodar somente um dos testes, basta clicar no botão triangular ao lado deste teste, já para rodar todos os testes no arquivo, você deve clicar no botão triangular em cima do nome do arquivo. Da mesma forma, se você desejar rodar todos os testes do projeto, deve clicar no botão em cima do nome do projeto. Isso é muito útil em projetos complexos com vários testes, inclusive alguns que demoram muito para rodar.

É possível observar outros dois botões, sendo o segundo um triângulo com um inseto (bug), que indica que você pode abrir o debugger naquele teste (ou naquele conjunto de testes) e, por último, um ícone de arquivo, que abre o arquivo do teste já na linha do teste.

Na visão do arquivo, as opções de rodar um teste, rodar o debugger no teste ou adicionar breakpoints podem ser vistas em testes individuais ao clicar com o botão direito em cima do triângulo ao lado de cada teste no arquivo.

![VS Code testing: Todos os testes passando ao clicar no botão de executar todos os testes](https://content-assets.betrybe.com/prod/b0e3f517-9a3d-4d8b-b7ba-953296bec8eb-VS%20Code%20testing:%20Todos%20os%20testes%20passando%20ao%20clicar%20no%20bot%C3%A3o%20de%20executar%20todos%20os%20testes.png)VS Code testing: Todos os testes passando ao clicar no botão de executar todos os testes

![VS Code testing: O primeiro dos 4 testes falhando ao ser trocado de `assert True` para `assert False`, com a saída do _Pytest_ sendo exibida por um pop-up de ajuda no meio do arquivo que contém o teste que falhou](https://content-assets.betrybe.com/prod/b0e3f517-9a3d-4d8b-b7ba-953296bec8eb-VS%20Code%20testing:%20O%20primeiro%20dos%204%20testes%20falhando%20ao%20ser%20trocado%20de%20%60assert%20True%60%20para%20%60assert%20False%60,%20com%20a%20sa%C3%ADda%20do%20_Pytest_%20sendo%20exibida%20por%20um%20pop-up%20de%20ajuda%20no%20meio%20do%20arquivo%20que%20cont%C3%A9m%20o%20teste%20que%20falhou.png)
VS Code testing: O primeiro dos quatro testes falhando ao ser trocado de `assert True` para `assert False`, com a saída do _Pytest_ sendo exibida por um pop-up de ajuda no meio do arquivo que contém o teste que falhou

## Configurando um projeto com o Pytest (opcional)

Se você tem um projeto mais complexo, pode ser que encontre alguns problemas ao tentar utilizar o _pytest_ (e até mesmo algumas outras ferramentas como o _black_, o _flake8_, o _isort_ e o _mypy_).

A maioria dos problemas ocorrem na forma de erros de importação, onde o seu projeto não está registrado no _path_ do Python para uma importação relativa, e o _pytest_ é executado de um caminho diferente do seu projeto e isso quebra _imports_ relativos.

Se você está tendo este problema, ou pretende criar um projeto do zero, convém tomar como referência o vídeo [Packaging Your Python Code With pyproject.toml | Complete Code Conversation](https://www.youtube.com/watch?v=v6tALyc4C10), do canal _Real Python_ no YouTube. Apesar de estar em um contexto diferente do _pytest_, o vídeo retrata muito bem o problema e indica o que deve ter no `pyproject.toml` para evitá-lo.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/eeb316f5-4729-427e-960b-1397a647e5d7/lesson/099e438a-5b63-474d-9eb8-010786235195)

