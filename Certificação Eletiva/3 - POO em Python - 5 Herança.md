[[3 - POO em Python]]

**Herança** é especializar o comportamento de uma classe, ou seja, a classe herdeira é tudo que a classe ascendente é – e talvez um pouco mais!

![Herança](https://content-assets.betrybe.com/prod/480dbafb-8e78-4c38-a226-23e1e496e59f-Heran%C3%A7a.png)

Se pensarmos em eletrodomésticos, alguns deles apresentam características em comum, como tensão, potência, ligado ou desligado e cor. Com isso, podemos perceber que as classes `Ventilador`, `Televisão` e `Liquidificador` terão alguns atributos repetidos.

🤔 Você pode estar refletindo: “Repetir tanto código não é eficiente, correto?”

Sim! E a boa notícia é que podemos evitar essa repetição quando falamos de especialização de comportamentos e conceitos com o uso de **herança**, criando assim uma classe `Eletrodoméstico`. Dessa forma, as classes ventilador, batedeira, máquina de lavar e secador serão suas filhas, ou seja, herdarão dela.

> Chamamos a classe base de _superclasse_ ou _classe pai/mãe_ e a classe que herda dela de _subclasse_ ou _classe filha_.

Veja o exemplo a seguir:

```python
class Eletrodoméstico:
    def __init__(
        self,
        cor: str,
        potência: int,
        tensão: int,
        preço: float | int
    ) -> None:
        self.cor = cor
        self.preço = preço
        self.potência = potência
        self.tensão = tensão
        self.velocidade_máxima = 3

        # Inicia os valores de `corrente_atual_no_motor`, `velocidade` e
        # `ligado` chamando o método `desligar` direto no construtor
        self.desligar()

    def ligar(self, velocidade: int) -> None:
        if velocidade > self.velocidade_máxima or velocidade < 0:
            raise ValueError(
                f"Velocidade deve estar entre 0 e {self.velocidade_máxima}"
            )

        self.velocidade = velocidade
        self.ligado = True

        corrente_máxima = self.potência / self.tensão
        velocidade_percentual = velocidade / self.velocidade_máxima
        self.corrente_atual_no_motor = corrente_máxima * velocidade_percentual

    def desligar(self) -> None:
        self.ligado = False
        self.velocidade = 0
        self.corrente_atual_no_motor = 0
```

Em Python, para declarar que um objeto herda as características de outro, basta fazer a declaração da classe “passarmos como parâmetro” a classe que será herdada. Vamos ver um exemplo de como podemos fazer um `Liquidificador` herdar de `Eletrodoméstico`:

```python
class Liquidificador(Eletrodoméstico):
    pass
```

Só por herdar de `Eletrodoméstico`, `Liquidificador` já tem acesso a todos os métodos e atributos definidos em `Eletrodoméstico`. Podemos criar novos métodos e atributos que não existem na superclasse à vontade. Novos métodos só precisam ter sua definição, mas novos atributos de instância (declarados no `__init__`) exigirão o uso de _polimorfismo_.

## Super

Em Python, a palavra-chave `super` é usada para acessar métodos e atributos de uma classe pai ou uma superclasse a partir de uma classe filha ou subclasse. Veja um exemplo a seguir sem o uso do `super`:


```python
from typing import Any


class Pai:
    def faz_algo(self, valor: Any) -> None:
        print(valor)


class Filha(Pai):
    def faz_outra_coisa(self) -> None:
        print("Método da classe Pai")

        # Chama o método da superclasse `Pai` passando o `self`
        # explicitamente
        Pai.faz_algo(self, valor=1)


sub_objeto = Filha()
sub_objeto.faz_outra_coisa()
# Método da classe Pai
# 1
```

Observe que a chamada comentada cria um acoplamento, ou seja, é necessário chamar explicitamente `Pai` dentro de um método de `Filha`. Quando a hierarquia de classes é grande isso vira um baita problema! Uma forma de melhorar isso é acessando a superclasse de `Filha` (que neste caso é de fato `Pai`) sem chamá-la explicitamente, delegando a um método que vai buscar qual é a superclasse. Esse método é o `super()`.

O exemplo anterior pode ter a linha `Pai.faz_algo(self, valor=1)` alterada para `super().faz_algo(valor=1)`.

> É importante observar a remoção do `self` na passagem de parâmetros para a `faz_algo`.

Mudar de acesso direto para `super`, além de não gerar prejuízo, gera uma melhoria: se eu mudar a classe da qual `Filha` herda de `Pai` para qualquer outra que tenha o método `faz_algo`, tudo continua a funcionar.

## Herança versus Composição

**Composição** é atribuir o objeto de uma classe a um atributo da outra, gerando, assim, um relacionamento de pertencimento entre elas. Veja mais [aqui](https://pt.stackoverflow.com/a/86718). Por exemplo: agora que temos nosso liquidificador funcionando, vamos associá-lo a uma pessoa cozinheira, dizendo que ela pode ter um liquidificador.

⚠️ Aviso: Preste atenção ao fato de que uma pessoa não é da mesma classe que um liquidificador, ela **tem** um liquidificador. Nesse caso, precisamos utilizar o conceito de **Composição**. Observe o exemplo a seguir que aplica esse conceito:

```python
class Pessoa:
    def __init__(self, nome: str, saldo_na_conta: float) -> None:
        self.nome = nome
        self.saldo_na_conta = saldo_na_conta
        self.liquidificador: Liquidificador | None = None

    def comprar_liquidificador(self, liquidificador: Liquidificador) -> None:
        if liquidificador.preço <= self.saldo_na_conta:
            self.saldo_na_conta -= liquidificador.preço
            self.liquidificador = liquidificador
```

Agora, a classe `Pessoa` tem o método específico para comprar seu liquidificador:

Copiar

```python
pessoa_cozinheira = Pessoa("Jacquin", 1000)
pessoa_cozinheira.comprar_liquidificador(liquidificador_vermelho)
```

Pronto, codamos a nossa entidade! 😄

É importante chamar a atenção para, quando possível, prefira utilizar composição ao invés de herança. Usamos herança quando queremos criar um subtipo de algo e composição em qualquer outra situação (por exemplo, expandir alguma funcionalidade sem haver uma relação de especialização).

## Para fixar

## Exercício 1

Crie uma classe chamada `Vehicle`, que possui um atributo `name` do tipo string, `capacity` do tipo inteiro e um método `move`, que recebe um parâmetro `distance` do tipo inteiro e que retorna a mensagem `'{self.name} carried {self.capacity} people across {distance} kilometers.'`. Crie uma classe `Car` que herda de `Vehicle` e modifica o método `move` para indicar, no começo da frase já existente, que foi especificamente um carro que levou as pessoas. Crie uma classe `Motorcycle` que herda de `Vehicle` e que tem capacidade fixada em 2.

Faça um print das mensagens de um carro e de uma moto se movendo.

### Solução

```python
class Vehicle:
    def __init__(self, name: str, capacity: int) -> None:
        self.name = name
        self.capacity = capacity

    def move(self, distance: int) -> str:
        return (
            f"{self.name} carried {self.capacity} people across {distance} "
            "kilometers."
        )


class Car(Vehicle):
    def move(self, distance: int) -> str:
        return f"Car {super().move(distance)}"


class Motorcycle(Vehicle):
    def __init__(self, name: str) -> None:
        self.name = name
        self.capacity = 2


my_car = Car(name="Opala", capacity=4)
my_bike = Motorcycle(name="Factor 125")

print(my_car.move(10)) # Car Opala carried 4 people across 10 kilometers.
print(my_bike.move(10)) # Factor 125 carried 2 people across 10 kilometers.
```

## Exercício 2

Crie uma classe chamada `Contact` (contato), que deverá ter os atributos `name` e `email`. Crie uma classe chamada `ContactList` que deverá ter os atributos do tipo lista `contacts` e `favorites`. Esta classe deverá ter os métodos:

- `add_contact`: recebe um contato e o adiciona na lista
- `remove_contact`: recebe um nome e o remove da lista, retornando-o
- `add_to_favorites`: recebe um nome e o move da lista de contatos para a de favoritos
- `remove_from_favorites`: recebe um nome e o move da lista de favoritos para a de contatos

Os três últimos métodos devem levantar um `LookupError` no caso do contato não ser encontrado.

### Solução

```python
class Contact:
    def __init__(self, name: str, email:str) -> None:
        self.name = name
        self.email = email


class ContactList:
    def __init__(self):
        self.contacts = []
        self.favorites = []

    def add_contact(self, contact: Contact) -> None:
        self.contacts.append(contact)


    def _find_contact(self, name: str, from_favorites=False) -> Contact:
        contact_list = self.favorites if from_favorites else self.contacts

        try:
            contact = [c for c in contact_list if c.name == name][0]
        except IndexError:
            raise LookupError(f"Contact named {name} not found")

        return contact

    def remove_contact(self, name: str) -> Contact:
        contact = self._find_contact(name)
        self.contacts.remove(contact)
        return contact

    def add_to_favorites(self, name: str) -> None:
        contact = self._find_contact(name)
        self.contacts.remove(contact)
        self.favorites.append(contact)


    def remove_from_favorites(self, name: str) -> None:
        contact = self._find_contact(name, from_favorites=True)
        self.favorites.remove(contact)
        self.contacts.append(contact)
```


# Herança múltipla

## Herdar mais de uma vez

Apenas a título de curiosidade, é interessante saber que existem heranças multinível e múltipla em Python. Embora sejam conteúdos relevantes e que você saiba que existam, não se preocupe em se aprofundar neles, pois o uso não é tão corriqueiro. Dê uma olhadinha!

### Herança multinível

Uma classe pode herdar de outra que herda de outra, ou seja, `A` herda de `B`, `B` herda de `C`. Não há diferenças significativas no funcionamento quando comparamos com uma herança de somente um nível (`A` herda de `B` e só), mas é interessante saber que é possível e relativamente normal.

![Herança multinível](https://content-assets.betrybe.com/prod/15d59892-0333-4d1b-ba97-180e9374248c-Heran%C3%A7a%20multin%C3%ADvel.png)

Exemplo:

```python
class C: # C
    def x(self): # método de exemplo
        print(1)

class B(C): # B herda de C
    pass

class A(B): # A herda de B
    pass


a = A()
a.x()
# 1
```

Ao acessar `a.x()` o método `x`, é buscado no objeto `a`, depois na classe `A`, depois na classe `B`, depois na classe `C`, subindo por toda a hierarquia de classes até ser encontrado ou levantar um `AttributeError`.

### Herança múltipla

Em Python existe também a chamada herança múltipla, não tão comum a outras linguagens, que é a capacidade que uma classe tem de herdar de mais de uma classe ao mesmo tempo. Ou seja, uma classe `A` pode herdar de `B` e `C` simultaneamente, sem que haja herança multi-nível. Eis um código que você pode ver por aí:

```python
class A(B, C): #
    pass
```

> ⚠️ Importante: o uso de herança múltipla é bastante desencorajado, pois gera complexidade extra na hierarquia de classes. Isso exige maior controle da pessoa desenvolvedora sobre o funcionamento implícito da linguagem, podendo levar a comportamentos inesperados na aplicação. Se você achar que seu uso é uma ideia interessante, experimente usar composição.

### Mixins

Uma forma de amenizar o problema da herança múltipla é o uso de mixins. No caso, são classes que não têm estado, mas apenas métodos, que podem ser utilizados por outras classes.

```python
class Animal:
    def __init__(self, name: str) -> None:
        self.name = name


class MixinVoar:
    def voar(self):
        print("Pássaro voando...")


class Passaro(Animal, MixinVoar):
    def __init__(self, name: str) -> None:
        super().__init__(name)


passaro = Passaro("Pardal")
passaro.voar() # Pássaro voando...

```

### MRO

Ao utilizar herança em Python, o método `super` busca os métodos em uma ordem específica das superclasses. Chama-se isso de _Method Resolution Order_ (MRO), ou _Ordem de Resolução de Métodos_ em português, e Você pode ler mais sobre o MRO [aqui](https://www.python.org/download/releases/2.3/mro/) se quiser, mas é um conteúdo avançado e, provavelmente. você não vai precisar dele para realizar construções não tão complexas.

## Para fixar

## Exercício 3

Crie uma classe `Animal` com um atributo `nome` e um método `fazer_som` que imprime “Animal fazendo som”. Depois, crie uma classe `Mamifero` que herda de `Animal` e tenha um método `amamentar`. Por fim, crie uma classe `Cachorro` que herda de `Mamifero` e tenha um método `latir` e imprima “Au au!”.

### Solução

```python
class Animal:
    def __init__(self, nome: str) -> None:
        self.nome = nome

    def fazer_som(self):
        print(f"{self.nome} fazendo som")


class Mamifero(Animal):
    def amamentar(self):
        print(f"{self.nome} amamentando")


class Cachorro(Mamifero):
    def latir(self):
        print(f"{self.nome} faz au au!")


cachorro = Cachorro("Rex")
cachorro.fazer_som() # Rex fazendo som
cachorro.amamentar() # Rex amamentando
cachorro.latir() # Rex faz au au!

```

## Exercício 4

Utilize a sua solução do exercício anterior e faça um mixin que conterá um método para mostrar a velocidade que o cachorro está correndo.

### Solução

```python
class Animal:
    def __init__(self, nome: str) -> None:
        self.nome = nome

    def fazer_som(self):
        print(f"{self.nome} fazendo som")


class Mamifero(Animal):
    def amamentar(self):
        print(f"{self.nome} amamentando")


class Cachorro(Mamifero):
    def latir(self):
        print(f"{self.nome} faz au au!")


class MixinCorrer:
    def correr(self, velocidade):
        print(f"{self.nome} correndo a {velocidade} km/h")


class CachorroCorredor(Cachorro, MixinCorrer):
    pass


cachorro = CachorroCorredor("Rex")
cachorro.fazer_som() # Rex fazendo som
cachorro.amamentar() # Rex amamentando
cachorro.latir() # Rex faz au au!
cachorro.correr(10) # Rex correndo a 10 km/h

```

