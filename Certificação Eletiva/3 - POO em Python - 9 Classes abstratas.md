[[3 - POO em Python]]

## O que são classes abstratas?

**Classes abstratas** são aquelas que contêm um ou mais métodos abstratos (métodos declarados, porém sem implementação, ou seja, somente a assinatura desses métodos sem corpo).

Uma classe abstrata em Python pode ser considerada um projeto, um contrato ou um molde para outras classes. Por meio dela, pode-se criar um conjunto de métodos que devem ser feitos em qualquer classe filha construída com base na classe abstrata.

Em Python, além de **não poderem ser instanciadas**, elas precisam de subclasses que gerem implementação para os métodos, ou seja, as subclasses que herdam a classe abstrata devem sobrescrever obrigatoriamente seus métodos. São usadas apenas para serem herdadas, funcionando como uma superclasse e forçando hierarquia para todas as subclasses. Em outras palavras, as classes abstratas deixam as interfaces explícitas e verificam a conformidade das implementações nas classes derivadas. Ou seja, elas **impõem** a conformidade com a interface.

Você pode se perguntar: Por que vou querer uma classe que não pode ser instanciada? No caso, o uso de classes abstratas criam um contrato que deverá ser respeitado por todas as pessoas desenvolvedoras que utilizarem o código, ajudando, assim, a padronizar o código.

Veja um exemplo de erro ao tentar instanciar uma classe abstrata:

```python
import abc


class InterfaceFormal(abc.ABC):
    @abc.abstractmethod
    def exemplo(self):
        pass


teste = InterfaceFormal()
```

Resultado:

```text
>>> TypeError: Can´t instantiate abstract class InterfaceFormal with abstract methods exemplo
```

Outro ponto é o **DRY**: (DRY — Don’t Repeat Yourself)! Em códigos longos, é comum termos diversas classes parecidas, o que pode gerar confusão, tanto para quem fez quanto para quem lê o código. Ao usarmos a classe abstrata como uma interface formal, deixamos o código mais limpo e com menos chances de _bugs_.

## ABCs (Classes-base abstratas)

Por padrão, o Python não fornece classes abstratas. Ele vem com um módulo que fornece a base para definir as classes-base abstratas (ABC) e o nome desse módulo é **ABC**. Esse último funciona decorando métodos (_annotations_) da classe base como abstratos e, então, registrando classes concretas, como implementações da base abstrata. Um método torna-se abstrato quando decorado com a palavra-chave `@abstractmethod`.

`ABCs` garantem que as classes derivadas implementem métodos específicos da classe base. No entanto, herdar de uma ABC é mais que implementar os métodos necessários: é também uma declaração clara da intenção da pessoa desenvolvedora. Trata-se de uma forma de forçar a padronização do código.

Ao usar ABCs, herança múltipla não só é comum (diferentemente de outras linguagens como Java que não permitem herança múltipla) como também é praticamente inevitável, visto que cada uma das ABCs fundamentais de coleção como **Sequence**, **Mapping** e **Set** estendem várias ABCs.

- [Documentação da ABC](https://docs.python.org/pt-br/3/library/abc.html)

## Criando a sua classe abstrata

Usaremos ABCs da forma mais comum, como superclasses em casos em que você precisa implementar uma interface. A ABC vai verificar subclasses concretas para saber se estão de acordo com a interface que ela define.

> ⚠️ Importante: cuide para não criar ABCs desenfreadamente, sem analisar a real necessidade, impondo uma série de regras excessivas em uma linguagem que se tornou popular por ser prática e pragmática.

Para justificar a criação de uma ABC, precisamos ter um contexto para usá-la como um ponto de extensão da sua solução. Suponha, por exemplo, que você esteja criando uma API para gerenciamento de propagandas em _sites_. Nela, você precisa exibir as propagandas em um _site_ ou aplicativo móvel em ordem aleatória, mas sem repetir as propagandas antes que o conjunto completo tenha sido mostrado. Um dos requisitos da sua API é aceitar classes definidas pelo usuário que façam seleção aleatória, sem repetição. Para deixar claro para quem vai utilizar sua API, o que se espera de um componente que faça “seleção aleatória, sem repetição”, você pode definir uma ABC.

Vejamos um exemplo de uso da ABC:

```python
from abc import ABC, abstractmethod


class Pessoa(ABC):
    @abstractmethod
    def nome(self):
        pass


class Vendedor(Pessoa):
    def __init__(self, seu_nome):
        self.seu_nome = seu_nome

    def nome(self):
        print(f"Seu nome é: {self.seu_nome}")

```

As classes concretas contêm apenas métodos concretos (normais), enquanto as classes abstratas podem conter métodos concretos e abstratos. A classe concreta fornece implementação de métodos abstratos, a classe base abstrata também pode fornecer uma implementação utilizando os métodos via super(). Vejamos um exemplo para utilizar o método usando super():

> exemplo_super.py

```python
from abc import ABC, abstractmethod


class X(ABC):
    @abstractmethod
    def exemplo(self):
        print("Classe-base abstrata")


class Y(X):
    def exemplo(self):
        super().exemplo()
        print("Subclasse")


z = Y()
z.exemplo()

```

Resultado:

```text
>>> Classe-base abstrata
>>> Subclasse
```

## Propriedades abstratas

Classes abstratas incluem atributos além de métodos. Você pode exigir os atributos em classes concretas definindo-os com o decorador **@property**. Vejamos um exemplo:

```python
class Teste:
    def __init__(self, nome):
        self._nome = nome

    @property
    def nome(self):
        return self._nome
```

## Para fixar

## Exercício 1

Crie uma classe abstrata base chamada `Pessoa` que contenha um método abstrato chamado `imprimir_cargo`. Além disso, crie duas subclasses que terão implementações concretas da classe base abstrata. São elas: `Vendedor` e `Gerente`. O método `imprimir_cargo` deverá imprimir “Meu cargo é de vendedor” e “Meu cargo é de gerente” respectivamente.

### Solução

```python
from abc import ABC, abstractmethod


class Pessoa(ABC):
    @abstractmethod
    def imprimir_cargo(self):
        pass


class Vendedor(Pessoa):
    def imprimir_cargo(self):
        print("Meu cargo é de vendedor.")


class Gerente(Pessoa):
    def imprimir_cargo(self):
        print("Meu cargo é de gerente.")


vendedor = Vendedor()
vendedor.imprimir_cargo()

gerente = Gerente()
gerente.imprimir_cargo()

```


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/0451bc54-860e-4eff-9b0c-ac27bc64a2aa/lesson/b588e887-366a-4a7d-a94d-24fe6a37d5ad)

