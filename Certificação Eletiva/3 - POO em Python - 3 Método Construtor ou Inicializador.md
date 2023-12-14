[[3 - POO em Python]]

Você pode se perguntar: “se eu tiver 300 objetos, cada um com uns 400 atributos, vou ter que ficar digitando `objeto.atributo = "valor"` para cada um deles, tal como foi feito na linha `maria.nome = "Maria"`? Para resolver este e outros problemas existe o método construtor. O construtor é um método especial que roda automaticamente quando a gente cria (instancia) o objeto. Na maioria das linguagens, o construtor cria e devolve a instância do objeto e já inicializa os seus atributos.

Em **python**, esta operação é dividida em dois métodos:

- `__new__` (Construtor)
- `__init__` (Inicializador)

O Python já implementa esses métodos por padrão para cada nova classe criada (ainda que você só escreva um `pass` dentro dela 😉), mas você pode implementá-los novamente, ou seja, sobrescrevê-los. É desse modo que customizamos nosso construtor/inicializador.

Com isso em mente, vamos criar um método construtor para uma classe `Liquidificador`: basta recriar o método `__init__` dentro de nossa classe, conforme o exemplo a seguir:

```python
class Liquidificador:
    def __init__(
        self,
        cor: str,
        potência: int,
        tensão: int,
        preço: float | int
    ) -> None:
        # Adiciona o valor do parâmetro `cor` a um atributo de mesmo nome (homônimo)
        # do objeto que está sendo criado (`self.cor`).
        self.cor = cor
        self.preço = preço
        self.potência = potência
        self.tensão = tensão

        # Valores _hard coded_
        self.ligado = False
        self.velocidade = 0
        self.velocidade_máxima = 3
        self.corrente_atual_no_motor = 0

# Agora, podemos criar nossos primeiros liquidificadores.
# Os argumentos nos parênteses são passados para o `__init__`
meu_liquidificador = Liquidificador("Azul", 200, 127, 200)
seu_liquidificador = Liquidificador(
    cor="Vermelho", potência=250, tensão=220, preço=100
)
```

No exemplo `self.cor = cor`, o valor do parâmetro `cor` do método `__init__` é atribuído ao atributo homônimo `cor` do objeto (`self.cor`). Entretanto, o parâmetro `cor` poderia valorar um atributo qualquer. Um exemplo disso seria se armazenássemos a corrente máxima que o motor do liquidificador pode ter: `self.corrente_máxima_no_motor = potência / tensão`. Observe como `potência` e `tensão` seriam dois parâmetros utilizados para valorar um único atributo.

Perceba que é possível ter atributos que não precisam ser passados por meio de parâmetros na chamada do construtor. Por exemplo, para o booleano `ligado` e o inteiro `velocidade`, o construtor vai iniciá-los sempre com os valores fixos (_hard coded_) `False` e `0`, respectivamente.

![Criação de objetos em Python](https://content-assets.betrybe.com/prod/02379e81-4d80-48bf-b10f-396b2fb0b70b-Cria%C3%A7%C3%A3o%20de%20objetos%20em%20Python.png)


## Para fixar

## Exercício 7

Crie um método construtor para a classe `Database` criada no exercício 3. O método deve receber os seguintes parâmetros:

- `dialect`
- `user`
- `host`
- `password`
- `database`
- `port` (opcional)

O parâmetro `port` pode ser do tipo `str` ou `int`, devendo ser um valor numérico, levantando `ValueError` caso contrário. Os demais parâmetros devem ser do tipo `str`.

> De olho na dica 👀: utilize a seguinte docstring, salve seu código em um arquivo `app.py` e valide sua implementação por meio do comando `python -m doctest app.py`, que não vai gerar nenhuma saída em caso de sucesso. Se você incluir algum `print` verá o valor impresso na tela.

```python
"""Creates a Database instance

Parameters
----------
dialect : str
user : str
host : str
password : str
database : str
port : str | int, optional, by default ""

>>> database = Database(
...     dialect = "",
...     user = "",
...     host = "",
...     password = "",
...     database = "",
...     port = "a",
... )
Traceback (most recent call last):
...
ValueError: `port` must have a numeric value
"""
```

### Solução

```python
class Database:
    def __init__(
        self,
        dialect: str,
        user: str,
        host: str,
        password: str,
        database: str,
        port: str | int = "",
    ) -> None:
        """Creates a Database instance

        Parameters
        ----------
        dialect : str
        user : str
        host : str
        password : str
        database : str
        port : str | int, optional, by default ""

        >>> database = Database(
        ...     dialect = "",
        ...     user = "",
        ...     host = "",
        ...     password = "",
        ...     database = "",
        ...     port = "a",
        ... )
        Traceback (most recent call last):
        ...
        ValueError: `port` must have a numeric value
        """
        if type(port) == str and not port.isnumeric():
            raise ValueError("`port` must have a numeric value")
```


## Exercício 8

O método construtor de `Database` deve criar um atributo chamado `connection_url`, no formato `dialect://username:password@host:port/database`.

Adote as portas padrão dos serviços do _MySQL_ e _PostgreSQL_. Se o dialeto não for nenhum desses dois e a porta não for especificada, levante um `ValueError`.

Dica: Adicione à docstring do exercício anterior mais doctests:

```python
"""
>>> Database(
...     dialect = "",
...     user = "",
...     host = "",
...     password = "",
...     database = "",
... )
Traceback (most recent call last):
...
ValueError: invalid default `port` for unrecognized `dialect`

>>> print(Database(
...     dialect = "postgres",
...     user = "tryber",
...     host = "trybe",
...     password = "i_love_python",
...     database = "computer_science",
... ).connection_url)
postgres://tryber:i_love_python@trybe:5432/computer_science
"""
```

### Solução

```python
class Database:
    def __init__(
        self,
        dialect: str,
        user: str,
        host: str,
        password: str,
        database: str,
        port="",
    ) -> None:
        """Creates a Database instance

        Parameters
        ----------
        dialect : str
        user : str
        host : str
        password : str
        database : str
        port : str | int, optional, by default ""

        >>> Database(
        ...     dialect = "",
        ...     user = "",
        ...     host = "",
        ...     password = "",
        ...     database = "",
        ...     port = "a",
        ... )
        Traceback (most recent call last):
        ...
        ValueError: `port` must have a numeric value

        >>> Database(
        ...     dialect = "",
        ...     user = "",
        ...     host = "",
        ...     password = "",
        ...     database = "",
        ... )
        Traceback (most recent call last):
        ...
        ValueError: invalid default `port` for unrecognized `dialect`

        >>> print(Database(
        ...     dialect = "postgres",
        ...     user = "tryber",
        ...     host = "trybe",
        ...     password = "i_love_python",
        ...     database = "computer_science",
        ... ).connection_url)
        postgres://tryber:i_love_python@trybe:5432/computer_science
        """
        if not port:
            if dialect == "mysql":
                port = 3306
            elif dialect == "postgres":
                port = 5432
            else:
                raise ValueError(
                    "invalid default `port` for unrecognized `dialect`"
                )
        if type(port) == str and not port.isnumeric():
            raise ValueError("`port` must have a numeric value")

        self.connection_url = (
            f"{dialect}://{user}:{password}@{host}:{port}/{database}"
        )
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/d530112b-87a8-4577-acd2-1a9651a89888/lesson/caf1d837-3f67-432c-9545-7efaae1b2e60)
