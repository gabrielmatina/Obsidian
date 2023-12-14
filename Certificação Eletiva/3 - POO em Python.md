[[Python]]
# Introdução à tipagem com Python

Tudo na Programação Orientada a Objetos gira em torno da passagem e recebimento de valores por diferentes entidades do programa, sendo essencial que tais interações sejam fáceis de se fazer funcionar de forma correta. Tudo isso começa com o conhecimento acerca dos tipos de valores que diferentes variáveis num programa podem armazenar e como eles interagem.

Durante a maior parte da sua jornada em JavaScript, e até agora em Python, tudo funciona bem sem que se formalize esse conhecimento, certo? Mas _dar nome aos bois_ e entender quais valores fazem o quê, dará **robustez ao seu conhecimento** e te permitirá fazer mais errando menos. Você já sabe as vantagens que um código explicitamente tipado traz para quem programa. Python não é explicitamente tipado, mas é importante conhecer os tipos que ele atribui às variáveis por trás dos panos!

Quando falamos de tipagem, estamos querendo saber como identificamos o tipo de uma variável. Se é um número, uma string, uma função ou algo assim. Em Python, existem formas de se _checar_ o tipo de uma variável, já que esses tipos não são explicitamente definidos e que algumas operações só podem ser realizadas em variáveis de tipos específicos. Para isso usamos as checagens de tipos que podem ser divididas em dois grupos: estática ou dinâmica; estrutural ou nominal.

## Tipagem estática, dinâmica, estrutural e nominal

A **Tipagem estática** existe quando o tipo de uma variável é definido de forma que, antes mesmo do código ser executado, durante sua compilação, erros de tipo são detectados e disparados. Isso acontece, por exemplo, no `TypeScript`.

A **Tipagem dinâmica** é a que ocorre durante a execução do código, quando a cada linha executada os tipos das variáveis vão sendo “descobertos” e erros de tipo são disparados somente durante a execução do programa. O `Javascript`. funciona assim.

A **Tipagem nominal** verifica se dois valores são do mesmo tipo de acordo com **o nome** do seu tipo. Por exemplo: se duas classes diferentes tem conteúdos absolutamente idênticos, só mudando o nome da mesma, essa tipagem crava que os valores de cada classe são de tipos diferentes.

A **Tipagem estrutural** infere o tipo de um valor de acordo com os métodos aos quais o tipo dá suporte. No exemplo anterior, os valores das duas classes diferentes seriam considerados do mesmo tipo se as classes forem idênticas, ainda que elas tenham nomes diferentes.

A tipagem em Python, por padrão, é dinâmica, mas suporte para tipagem estática foi introduzido recentemente através dos _type hints_. Por padrão, também, Python tem tipagem **nominal**, mas a ferramenta `Protocol` e a biblioteca `MyPy` trazem a tipagem estrutural para Python também. Conclusão: se você não for intencional com sua tipagem em Python, ela será dinâmica e nominal, mas se você preferir sua tipagem pode ser estática e estrutural. Python dá suporte a todas as formas de tipagem, ficando a critério de quem usa escolher qual usar!

> Atenção: não confundir os conceitos acima com tipagem forte e fraca. Tipagem forte significa que a linguagem não faz coerção de tipos. Python possui tipagem forte: `'1' + 1` levanta um `TypeError` em Python, algo que retornaria `11` em JavaScript.

## Anotações de tipo

_Type Annotations_, também conhecidas como _Type Hints_ (Dicas de tipo) são utilizadas para checagem estática e, como o nome sugere, são apenas dicas: o comportamento em tempo de execução do Python continua inalterado. Ou seja: elas ajudam quem programa a entender o código, mas se você desrespeitar a dica o código rodará normalmente. Há, porém, analisadores de tipagem disponíveis para Python que usam essas dicas para detectar e acusar erros de tipagem.

Elas são úteis porque podemos rodar um analisador de tipagem antes mesmo de rodar o código, descobrindo bugs por uso de tipos errados com antecedência.

Para usar as dicas de tipo, basta colocar o tipo após o sinal de dois pontos (`:`) depois de uma declaração de variável ou parâmetro de função, por exemplo `var: int = 1` ou `def my_func(parameter: str)`. Para indicar o tipo do retorno de uma função, utilizamos a notação de seta após o fechamento dos parênteses. Exemplo `def my_func(parameter: str) -> int: ...`.

### Tipos simples

Alguns dos tipos simples são:

- `int`
- `float`
- `str`
- `dict`
- `list`
- `tuple`

É interessante observar que tipos de coleções, como `dict`, podem ser subscritíveis. Por exemplo, para dizer que temos uma lista de inteiros, basta usar a seguinte sintaxe: `my_list: list[int] = [1, 2, 3]`. Um dicionário que mapeia strings para floats seria `my_dict: dict[str, float] = {"key1": 1.5, "key2": 2.3}`.

> Em versões do Python anteriores à 3.10 não é possível utilizar os tipos simples citados acima sem a seguinte linha no começo do arquivo: `from __future__ import annotations`. Isso acontece pois estas funções ainda não podiam ser utilizadas como tipos subscritíveis. Uma outra solução para garantir essa “retrocompatibilidade”, muito utilizada, é importar os tipos do módulo `typing`, porém com _PascalCase_ (exemplo: `from typing import Dict, List`).

### Tipos complexos

Dentro do módulo `typing` temos alguns blocos construtores de tipos, bem como modificadores de significado. Não vamos entrar em detalhes sobre todos eles (você pode olhar [a documentação](https://docs.python.org/3/library/typing.html)), mas alguns são interessantes de conhecer:

- `Any` é o tipo que usamos quando queremos deixar explícita a falta de tipagem: o valor pode ser de qualquer tipo.
- `Union` é utilizado quando algo pode ter um dentre dois tipos. No exemplo a seguir a função aceita um inteiro ou uma string: `def my_func(parameter: Union[int, str]):`.
    
    > A partir do Python 3.10, a barra vertical (_pipe_, `|`) pode ser utilizado como operador de união em alguns contextos, este incluído. A função ficaria `def my_func(parameter: int | str):`
    
- `Optional` significa que o valor pode ser um tipo ou `None`. No exemplo a seguir a função pode retornar um inteiro ou `None`: `def my_func() -> Optional[int]`.
    
    > Não é uma boa prática que uma função retorne mais de um tipo, portanto o exemplo acima é um exemplo do que **não** deve ser feito: a função deveria retornar um inteiro ou levantar um erro caso não seja possível.
    
- `Callable` é o tipo que usamos quando queremos indicar que o valor é uma função (ou um invocável qualquer), visto que em Python funções podem ser passadas como parâmetros para outras funções e podem ser retornadas por outras funções.
    
    > Por exemplo, `my_func` recebe como parâmetro `param` uma função que recebe um inteiro, uma string e retorna um float: `def my_func(param: Callable[[int, str], float])`.
    

## mypy

O [mypy](https://www.mypy-lang.org/) é uma ferramenta que permite realizar a verificação de conformidade de tipagem antes do código ser executado. Para instalar o _mypy_, basta rodar um `pip install mypy`. Vamos criar um arquivo chamado `app.py` com o seguinte conteúdo:

Copiar

```python
def add_two_numbers(num1: int, num2: int):
    return num1 + num2

print(add_two_numbers(1, 2.0))
```

Ao rodar o comando `mypy app.py` teremos uma saída similar a esta:

```text
app.py:4: error: Argument 2 to "add_two_numbers" has incompatible type "float"; expected "int"  [arg-type]
Found 1 error in 1 file (checked 1 source file)
```

Isso acontece pois passamos um `float` (2.0) onde era esperado um número inteiro. Note que não colocamos o tipo de retorno da função. Explicar para o verificador de tipos (_mypy_ ou algum outro) os tipos envolvidos em algum código pode ser algo trivial em alguns momentos, mas bem complexo em outros. Existem até alguns códigos que funcionam, mas que não podem ser corretamente tipados devido a limitações do analisador.

Por padrão o _mypy_ não vai verificar a tipagem caso você não coloque os tipos explicitamente no código, como é o caso da falta do tipo de retorno da função `add_two_numbers`. Caso você queira que o _mypy_ exiba as linhas que deveriam ter anotações de tipo mas não têm, você pode executá-lo no modo estrito: `mypy app.py --strict`. O resultado vai ser semelhante a este:

```text
app.py:1: error: Function is missing a return type annotation  [no-untyped-def]
app.py:4: error: Argument 2 to "add_two_numbers" has incompatible type "float"; expected "int"  [arg-type]
Found 2 errors in 1 file (checked 1 source file)
```

## Tipagem gradual

Imagine uma base de códigos sem nenhuma tipagem e você recebe a tarefa de adicionar os tipos aos poucos. Isso é perfeitamente possível, e chamamos este processo de tipagem gradual. Para conseguir atingir o objetivo, você pode rodar o _mypy_ duas vezes:

- A primeira no modo normal, para garantir que todos os tipos colocados estão de acordo, geralmente no contexto de um _Continuous Integration_
- A segunda no modo estrito, geralmente durante o desenvolvimento, para ver o que precisa de tipagem e não tem.

### Inferência de tipo

Nem tudo precisa de tipagem. Variáveis declaradas com seus valores não precisam de Type Hints _(inclusive colocá-las pode deixar seu código com informação demais)_. Ainda é preciso, no entanto, colocar a tipagem caso a variável possa ter mais de um tipo.

```python
# tipo int é inferido sem que eu precise deixar explícito
var1 = 1

# não faça isso, é verboso e desnecessário
var2: int = 1

# importante deixar explícito que começa como int, mas pode mudar para float
var3: int | float = 1
```

## Para fixar

## Exercício 1

Modifique o código abaixo para que ele seja tipado corretamente.

```python
def add_two_numbers(num1, num2):
    return num1 + num2
```

### Solução

```python
def add_two_numbers(num1: int, num2: int) -> int:
    return num1 + num2
```

## Exercício 2

Modifique o código abaixo para que ele seja tipado corretamente.

```python
class Pessoa:
    def __init__(self, nome, idade, altura):
        self.nome = nome
        self.idade = idade
        self.altura = altura
```

### Solução

```python
class Pessoa:
    def __init__(self, nome: str, idade: int, altura: float) -> None:
        self.nome = nome
        self.idade = idade
        self.altura = altura
```


## Referências

- [Python Classes and Objects](https://www.w3schools.com/python/python_classes.asp)
- Python Fluente: Programação Clara, Concisa e Eficaz, Luciano Ramalho, O’Reilly, 2015.
- [Python Type Checking, no Real Python](https://realpython.com/python-type-checking/), acessado em 19/06/2023
- [PEP 484 - Type Hints](https://peps.python.org/pep-0484/), acessado em 19/06/2023 = [MyPy, Protocols and Structural subtyping](https://mypy.readthedocs.io/en/latest/protocols.html), acessado em 19/06/2023
- - [Uncle Bob - The Principles of OOD](http://butunclebob.com/ArticleS.UncleBob.PrinciplesOfOod)
- [Python Fluente](https://novatec.com.br/livros/pythonfluente/)
- - [Documentação oficial do Python sobre classes abstratas e interfaces](https://docs.python.org/3/library/abc.html)
- [Python Cookbook, Third Edition](https://www.oreilly.com/library/view/python-cookbook-third/9781449357337/)
- [Fluent Python: Clear, Concise, and Effective Programming](https://www.oreilly.com/library/view/fluent-python/9781491946237/)

## Recursos adicionais

- [Classes e Objetos - Fundamentos](https://panda.ime.usp.br/pensepy/static/pensepy/13-Classes/classesintro.html)
- [Paradigmas de programação](https://blog.betrybe.com/tecnologia/paradigmas-de-programacao/)
- [Diferença entre Associação, Agregação e Composição](https://pt.stackoverflow.com/a/86718)
- [UML: o que é, para que serve e quando usar essa linguagem de notação?](https://blog.betrybe.com/tecnologia/uml/)
- [UML - Examples by Types of Diagrams](https://www.uml-diagrams.org/index-examples.html)
- [RailsConf 2015 - Nothing is Something](https://www.youtube.com/watch?v=OMPfEXIlTVE)
- [Live de Python - Composição e métodos mágicos](https://www.youtube.com/watch?v=MYaXUrmvrho)
- [Tutorialspoint - Python Abstract Classes](https://www.tutorialspoint.com/python/python_classes.htm)
- [Real Python - Python’s Interfaces Explained](https://realpython.com/python-interfaces/)
- [Medium - Understanding Abstract Base Classes in Python](https://medium.com/@pyslackers/understanding-abstract-base-classes-in-python-bc72a5f0a799)
- [GeeksforGeeks - Protocol Classes in Python](https://www.geeksforgeeks.org/protocol-classes-python/)

Marcar lição como concluída

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/d530112b-87a8-4577-acd2-1a9651a89888/lesson/d2e19adc-8b2d-4adf-bca3-5959e3897df8)
