[[3 - POO em Python]]

Polimorfismo é mais um **pilar da orientação a objetos**, que ocorre quando diferentes métodos são chamados pelo mesmo nome.

> Polimorfismo vem do grego _poli_ = muitas, _morphos_ = formas.

Além da definição, há um trecho que dá mais nitidez ao significado prático disso. Veja a seguir.

> O polimorfismo é caracterizado quando duas ou mais classes distintas têm métodos de mesmo nome, de forma que uma função possa utilizar um objeto de qualquer uma das classes polimórficas sem necessidade de tratar de forma diferenciada conforme a classe do objeto.

Existem vários tipos de polimorfismo, mas dois são mais comuns: sobrecarga de métodos (_method overloading_) e sobrescrita de métodos (_method overriding_). Vamos estudar cada um deles.

![Polimorfismo com sobrescrita de métodos](https://content-assets.betrybe.com/prod/46663aca-efa3-4389-b813-6bc6eef7c9fc-Polimorfismo%20com%20sobrescrita%20de%20m%C3%A9todos.png)

## Sobrecarga de métodos

A sobrecarga de métodos é algo que, embora não exista em Python, é comum em outras linguagens, portanto é interessante entender. Ela acontece quando mais de um método pode ser definido com o mesmo nome, mas aceitando parâmetros em quantidades ou tipos diferentes.

### Parâmetros opcionais

Apesar de não existir a sobrecarga, é comum a existência de parâmetros opcionais nas funções e nos métodos e, a depender do uso, pode levar a um funcionamento similar ao da sobrecarga.

Pensando no exemplo da classe Pessoa, para declarar uma nova pessoa informamos a idade, o nome e o saldo_na_conta. Será que é necessário dar todos os dados sempre? Podemos deixar alguns opcionais? Observe o exemplo a seguir:

Copiar

```python
class Pessoa:
    def __init__(self, nome:str, idade=None, saldo_na_conta=None) -> None:
        self.idade = idade
        self.nome = nome
        self.saldo_na_conta = saldo_na_conta
        self.brinquedos = []

pessoa_1 = Pessoa("Marcelo", 22, 700)
pessoa_2 = Pessoa("Matheus")
pessoa_3 = Pessoa("Maria", 33)
pessoa_4 = Pessoa("Márcia", saldo_na_conta=100)
```

Com isso, a depender de como chamamos o método, coisas diferentes vão acontecer. Se passarmos uma idade, ela será valorada como um atributo da pessoa. Se não, o valor será `None`. Nesse caso, estamos apenas definindo o valor da variável, mas poderíamos ter uma série de `if`s que iriam realizar ações diferentes com base nos parâmetros de entrada, o que não é uma sobrecarga – visto que só um método é declarado – mas se assemelha a ela no uso prático, visto que temos várias formas de se chamar e usar um mesmo método

### Despacho

Na verdade, Python até suporta sobrecarga de funções e métodos, mas não de maneira transparente e simplificada, e sim por meio dos decoradores `singledispatch` e `singledispatchmethod`, respectivamente.

**Ambos estão presentes no módulo `functools`.**

A sobrecarga pode ser feita decorando uma função ou um método com o decorador equivalente e, em seguida, registrando funções/métodos adicionais com o decorator `@nome_da_função.register`. Essas funções/métodos adicionais não devem ter um nome, sendo declaradas com um `_`. Para isso, o `register` deve receber o tipo do parâmetro ou esse tipo deve ser declarado como dica de tipo (_type hint_) nas funções decoradas.

```python
import functools


@functools.singledispatch  # decorando a função para criar um dispatcher
def func(parâmetro):
    print(parâmetro)


@func.register(int)  # passando um tipo para o registrador do dispatcher
def _(parâmetro):  # observe que o nome da função é irrelevante
    print(parâmetro * 2)


@func.register  # NÃO passando o tipo para o registrador
def _(parâmetro: float):  # mas passando o tipo como type hint
    print(parâmetro * 5)


func(4)  # saída: 8
func(4.0)  # saída: 20.0
func("4")  # saída: 4

```

No exemplo acima, chamar `func` com qualquer parâmetro que não seja do tipo _int_ ou _float_ vai chamar a função original.

Apesar de ser bem legal, temos um problema: como o nome sugere (_singledispatch_ significa despacho único), eles servem para realizar a sobrecarga de somente 1 parâmetro. Para fazer isso com vários parâmetros, precisamos de um _multipledispatch_, que não existe na biblioteca padrão, apenas as bibliotecas externas fazem isso.

## Sobrescrita de métodos

Já a sobrescrita de métodos é mais comum em qualquer linguagem que tenha orientação a objetos. A sobrescrita ocorre quando um método definido em uma superclasse é novamente definido (reescrito/sobrescrito) na subclasse.

Vamos continuar de onde paramos na lição anterior, o qual criamos a classe `Liquidificador` que herda da classe `Eletrodoméstico`. Podemos reescrever alguns métodos de forma a deixá-los mais convenientes para o nosso uso. Para realizar a sobrescrita, basta declarar novamente o método na subclasse. Vamos a um exemplo:

```python
class Liquidificador(Eletrodoméstico):
    def esta_ligado(self):
        return False
```

Esse método não é nada útil, visto que sempre retorna `False`, mas nos ajuda a entender que podemos simplesmente escrever uma nova lógica para um método.

### Super

Talvez você já conheça o `super`, mas vamos ver como ele pode ser útil no contexto da sobrescrita de métodos. Imagine que você quer somente melhorar o método da superclasse, por exemplo, mudando o valor que ele retorna. Não faz sentido, em diversas ocasiões, que você reescreva tudo e modifique só algumas coisas. Às vezes, você quer reaproveitar o que já foi feito e somente dar uma incrementada. É aí que entra o `super`.

Lembre-se de que o `super` é uma referência à superclasse, ou seja, a classe da qual você está herdando. Por meio dessa referência, você pode acessar métodos da superclasse pela subclasse. Para isso, utilizamos a notação `super().método()`.

```python
class Liquidificador(Eletrodoméstico):
    def esta_ligado(self):
        return "Sim" if super().esta_ligado() else "Não"
```

Nesse exemplo em específico, nós modificamos o tipo de retorno do método `esta_ligado`, retornando _strings_ ao invés de um booleano. Trata-se de uma má prática, pois fere o princípio de substituição de Liskov, mas vamos ignorar esse detalhe para o exemplo continuar simples.

Observe que há uma chamada ao mesmo método na superclasse, de forma a não precisar recriar a lógica dele na subclasse. Nesse caso aqui é algo simples, pois é só o retorno de um booleano, mas `Eletrodomestico.esta_ligado` poderia ser um método complexo e custoso, fazendo com que reaproveitá-lo seja a melhor escolha.

Vamos ver um exemplo de como informar que o `Ventilador` e o `Liquidificador` herdam da classe `Eletrodomestico`:

```python
class Liquidificador(Eletrodoméstico):
    pass

class Ventilador(Eletrodoméstico):
    def __init__(self,
        cor: str,
        potencia: str,
        tensao: str,
        preco: str,
        quantidade_de_portas=1
    ) -> None:
        # Chamada ao construtor da superclasse
        super().__init__(cor, potencia, tensao, preco)

        # Faz outras coisas específicas dessa subclasse
        self.quantidade_de_portas = quantidade_de_portas


class Pessoa:
    def __init__(self, nome, saldo_na_conta):
        self.nome = nome
        self.saldo_na_conta = saldo_na_conta
        self.eletrodomesticos = []

    # Permite a aquisição de qualquer eletrodoméstico
    def comprar_eletrodomestico(self, eletrodomestico):
        if eletrodomestico.preco <= self.saldo_na_conta:
            self.saldo_na_conta -= eletrodomestico.preco
            self.eletrodomestico.append(eletrodomestico)
```

Ao sobrescrever o construtor, devemos chamar o construtor da superclasse também, de forma a garantir que ele seja executado e o que ele faça, seja aproveitado. Caso não façamos isso, como em qualquer método normal, teremos de reimplementar a lógica do construtor da superclasse manualmente na subclasse.

## Para fixar

## Exercício 9

Crie a seguinte estrutura:

1. Uma classe `Produto` com os atributos `nome` e `preco` e os métodos `descrever` e `preco`;
2. Uma classe `Livro` que herda de `Produto` e tem os mesmos atributos da superclasse mais o atributo `autor` e os métodos `descrever` e `preco`;
3. Uma classe `DVD` que herda de `Produto` e tem os mesmos atributos da superclasse mais o atributo `diretor` e os métodos `descrever` e `preco`.

### Solução

```python
class Produto:
    def __init__(self, nome: str, preco: float) -> None:
        self.nome = nome
        self.preco = preco

    def descrever(self):
        pass

    def preço(self):
        pass


class Livro(Produto):
    def __init__(self, nome: str, autor: str, preco: float) -> None:
        super().__init__(nome, preco)
        self.autor = autor

    def descrever(self):
        return f"{self.nome} por {self.autor}"

    def preço(self):
        return self.preco


class DVD(Produto):
    def __init__(self, nome: str, diretor: str, preco: float) -> None:
        super().__init__(nome, preco)
        self.diretor = diretor

    def descrever(self):
        return f"{self.nome} dirigido por {self.diretor}"

    def preço(self):
        return self.preco

```

## Exercício 10

Agora, crie uma função `imprimir_detalhes` para mostrar na tela informações sobre os seguintes produtos:

```sh
Livro("O Senhor dos Anéis", "J.R.R. Tolkien", 29.99)
DVD("O Poderoso Chefão", "Francis Ford Coppola", 19.99)
```

### Solução

```python
def imprimir_detalhes(produto: Produto):
    print(f"Descrição: {produto.descrever}")
    print(f"Preço: {produto.preco}")

produtos = [
    Livro("O Senhor dos Anéis", "J.R.R. Tolkien", 29.99),
    DVD("O Poderoso Chefão", "Francis Ford Coppola", 19.99)
]

for produto in produtos:
    imprimir_detalhes(produto)

# Saída:
# Descrição: O Senhor dos Anéis por J.R.R. Tolkien
# Preço: 29.99
# Descrição: O Poderoso Chefão dirigido por Francis Ford Coppola
# Preço: 19.99
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/cae79849-85dd-4853-b0c3-065f657bfee4/lesson/2b91d20e-69c0-4cbc-8164-d5a0734cb984)
