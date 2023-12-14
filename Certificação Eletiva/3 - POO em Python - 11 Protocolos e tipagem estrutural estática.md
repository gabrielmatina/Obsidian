[[3 - POO em Python]]

`Protocolos` são classes que herdam de `typing.Protocol` – e foram introduzidos no Python `3.8`. Eles servem para possibilitar a tipagem estática na verificação estrutural - que também é chamada de _duck typing_. Pode-se dizer que protocolos também são interfaces. No caso, não são interfaces no sentido de algo concreto em Python (classes abstratas que não têm métodos implementados), visto que têm um nome explícito, _Protocolos_. São interfaces no sentido amplo da palavra _interface_ no contexto de _software_ em geral, pois representam um contrato a ser seguido.

![Protocolos](https://content-assets.betrybe.com/prod/4c8e1337-8069-47a3-a2bd-798207234727-Protocolos.png)

Veja a seguir as vantagens no uso de protocolos.

- Fazem verificação estrutural, e não nominal. Isto é, não forçam que as classes usem herança, basta ter os métodos esperados.
    - Isso significa que existe um baixo acoplamento.
- Rodam em tempo de verificação estática, pegando erros antes de o código ser executado.
- Caso utilize uma _interface_ baseada em classe abstrata (ou seja, uma classe abstrata sem métodos concretos), a diferença será bem pequena, evitando muita mudança de código.

Veja a seguir as desvantagens.

- Comparando com a tipagem nominal, o uso de protocolos exige a criação de pelo menos duas classes: o protocolo e a classe que o respeita. Por vezes, a tipagem nominal pode ser mais simples por utilizar somente uma classe.
- Comparando com o _duck typing_ puro, há a necessidade de criar uma classe, colocar nos parâmetros que a esperam etc. Caso o seu código já funcione sem tipagem, pode ser um trabalho desnecessário.
- Não têm nenhuma utilidade em tempo de execução e podem indicar que são inválidos, durante a verificação estática, alguns códigos que rodam perfeitamente bem em tempo de execução.

Então, deve-se utilizar os protocolos quando:

- Estamos colocando tipagem estática em um código que já usa tipagem estrutural - _duck typing_ - e queremos mantê-la.
- Já estamos usando tipagem estática e queremos remover a tipagem nominal, deixando o código menos acoplado. Por exemplo, quando queremos ter mais de uma classe concreta que respeitam o mesmo protocolo.

## Protocolos na prática

Vamos ver um exemplo de uso de um protocolo, para isso, será criado um diretório chamado `protocols_example` no qual colocaremos nossos arquivos, tal como listado abaixo:

```tree
protocols_example
├── __init__.py
├── app.py
├── databases
│   ├── __init__.py
│   ├── mysql.py
│   └── postgresql.py
└── interfaces.py
```

O primeiro arquivo para ser observado é o `protocols_example/interfaces.py`. Chamamos o arquivo de “interfaces”, querendo utilizar o sentido abrangente da palavra: contratos a serem seguidos. É nesse arquivo que vamos disponibilizar os protocolos.


```python
from typing import Protocol


# Para criar um protocolo, basta criar uma classe que herda de Protocol
class Connection(Protocol):
    """Protocolo de conexão com o banco de dados"""

    # Definimos os métodos normalmente, com a devida assinatura
    def execute(self, query: str) -> list[str]:
        """Executa uma query no banco e retorna os dados, caso existam"""
        # O corpo do método não deve ter nada, portanto é comum utilizar
        # ellipsis (os 3 pontos abaixo) do Python, ou a palavra reservada pass
        ...


class Database(Protocol):
    """Protocolo de um banco de dados"""

    def connect(self, connection_url: str) -> Connection:
        """Realiza uma conexão com o banco de dados"""
        ...

```

Os próximos arquivos são os bancos de dados e as conexões concretas. No caso, esses códigos são apenas exemplos, com uma implementação apenas figurativa. Os conteúdos em `protocols_example/databases/mysql.py` e `protocols_example/databases/postgres.py`, respectivamente, são estes:

```python
class MySQLConnection:
    def execute(self, query: str) -> list[str]:
        return [f"MySQL executou a query `{query}`"]


class MySQLDatabase:
    def connect(self, connection_url: str) -> MySQLConnection:
        if connection_url == "":
            raise ValueError("Invalid connection")
        return MySQLConnection()

```

```python
class PostgresConnection:
    def execute(self, query: str) -> list[str]:
        return [
            f"Postgres executou a query `{query}`",
            "e retornou mais uma string adicional",
        ]


class PostgresDatabase:
    def connect(self, connection_url: str = "123") -> PostgresConnection:
        if connection_url == "123":
            raise ValueError("Invalid connection")
        return PostgresConnection()

```

É necessário observar aqui a falta da existência de herança: `MySQLDatabase` e `PostgresDatabase` **não** herdam do protocolo `Database`. Esse é o ponto positivo “baixo acoplamento”.

Agora, vamos ver o `protocols_example/app.py`:

```python
from .databases.mysql import MySQLDatabase  # Observe os imports relativos
from .databases.postgresql import PostgresDatabase
from .interfaces import Connection, Database


# Função que recebe um Database qualquer, que faz uso do protocolo como tipo
# na dica de tipo
def connect_and_print_result(database: Database) -> None:
    # Podemos inclusive dar a dica de tipo da conexão, apesar de não ser
    # necessário fazer isso explicitamente
    connection: Connection = database.connect("url_de_exemplo")
    result = connection.execute("query de exemplo")
    for element in result:
        print(element)


def main() -> None:
    database_name = input("Database: ").lower()

    if database_name == "postgres":
        # Podemos chamar a função passando qualquer coisa que respeite o
        # protocolo Database, seja um PostgresDatabase
        connect_and_print_result(PostgresDatabase())
    elif database_name == "mysql":
        # ou seja um MySQLDatabase
        connect_and_print_result(MySQLDatabase())
    else:
        raise ValueError("Database inválido!")


if __name__ == "__main__":
    main()

```

Você pode rodar o código com o comando `python -m protocols_example.app`. **Atenção** tanto na sintaxe quanto no comando. `python protocols_example/app.py` não vai funcionar em virtude dos _imports_ relativos. Esses _imports_ relativos são necessários para descobrir corretamente por parte do `mypy`. Falando nisso, executar `mypy --strict protocols_example` deve fazer com que a saída seja _Success: no issues found in 6 source files_.

Mudar o tipo do parâmetro `query` do método `MySQLConnection.execute` de `str` para `int` faz com que o código continue funcionando da mesma forma em tempo de execução, já que só alteramos as dicas de tipo. Entretanto, ao executar novamente o `mypy --strict protocols_example`, a saída deve ser similar a esta:

Copiar

```text
protocols_example/app.py:26: error: Argument 1 to "connect_and_print_result" has incompatible type "MySQLDatabase"; expected "Database"  [arg-type]
protocols_example/app.py:26: note: Following member(s) of "MySQLDatabase" have conflicts:
protocols_example/app.py:26: note:     Expected:
protocols_example/app.py:26: note:         def connect(self, connection_url: str) -> Connection
protocols_example/app.py:26: note:     Got:
protocols_example/app.py:26: note:         def connect(self, connection_url: str) -> MySQLConnection
Found 1 error in 1 file (checked 6 source files)
```

Isso demonstra a utilidade do Protocolo em entender que `MySQLDatabase` deixou de respeitar a “interface” (ou o Protocolo) `Database`, pois o tipo de `MySQLConnection` não respeita a interface (protocolo) `Connection`.

## Para fixar

## Exercício 3

Crie um protocolo chamado `CalculaPerimetro` que contenha um método abstrato chamado `calcular_perimetro`. Crie uma classe chamada `Quadrado` que implemente o protocolo `CalculaPerimetro` e o método `calcular_perimetro` para calcular o perímetro do quadrado.

### Solução

```python
from typing import Protocol


class CalculaPerimetro(Protocol):
    def calcular_perimetro(self):
        pass


class Quadrado(CalculaPerimetro):
    def __init__(self, lado: int) -> None:
        self.lado = lado

    def calcular_perimetro(self):
        return f"O perímetro do quadrado é de {self.lado * 4} cm"


quadrado = Quadrado(5)
print(quadrado.calcular_perimetro())

```

## Exercício 4

Quando executamos o `mypy` no código a seguir, recebemos o erro `error: Cannot instantiate abstract class "Triangulo" with abstract attribute "calcular_perimetro" [abstract]`. Corrija-o para que o `mypy` retorne a mensagem de sucesso.

> A classe a seguir precisa seguir o protocolo criado no exercício anterior.

```python
class Triangulo(CalculaPerimetro):
    def __init__(self, lado1: int, lado2: int, lado3: int) -> None:
        self.lado1 = lado1
        self.lado2 = lado2
        self.lado3 = lado3

    def calcular_area(self):
        return f"O perímetro do triângulo é de {self.lado1 + self.lado2 + self.lado3} cm"


triangulo = Triangulo(5, 5, 5)
print(triangulo.calcular_area())

```

### Solução

```python
class Triangulo(CalculaPerimetro):
    def __init__(self, lado1: int, lado2: int, lado3: int) -> None:
        self.lado1 = lado1
        self.lado2 = lado2
        self.lado3 = lado3

    def calcular_perimetro(self):
        return f"O perímetro do triângulo é de {self.lado1 + self.lado2 + self.lado3} cm"


triangulo = Triangulo(5, 5, 5)
print(triangulo.calcular_perimetro())

```

# Para não esquecer mais

Veja uma lista de termos e o resumo de seus significados. Esta é a página ideal para consulta!

|Termo|Descrição|
|---|---|
|**ABC (Abstract Base Class)**|Classe abstrata que contém um ou mais métodos abstratos.|
|**Classes abstratas**|Definem um conjunto de métodos e propriedades que precisam ser implementados pelas subclasses e não podem ser instanciadas diretamente.|
|**Duck typing**|Técnica em que o tipo de um objeto é determinado pelos métodos e pelos atributos que tem.|
|**DRY (Don’t Repeat Yourself)**|Princípio de programação que sugere evitar a duplicação de código em um programa.|
|**Interfaces**|Métodos públicos de um objeto que exige a implementação de determinados métodos e propriedades, usadas na engenharia de _software_ para melhorar a manutenção do programa.|
|**Interface formal**|Funciona como um contrato, obrigando suas subclasses a implementar seus métodos.|
|**Interface informal**|Classe que define métodos que posteriormente serão sobrescritos, mas não há obrigatoriedade para tal.|
|**Método abstrato**|Método declarado em uma classe abstrata, mas sem implementação, ou seja, apenas com a assinatura.|
|**Protocolos**|Classes que herdam de typing.Protocol, introduzidas no Python 3.8, que possibilitam a tipagem estática na verificação estrutural.|
|**Tipagem estrutural**|Verificação de tipo que não força a herança de classes, mas apenas que as classes têm os métodos esperados.|
|**Tipagem nominal**|Verificação de tipo que força a herança de classes.|



###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/0451bc54-860e-4eff-9b0c-ac27bc64a2aa/lesson/f5bf2e89-b4e2-474e-94ae-c3356448b91a)

