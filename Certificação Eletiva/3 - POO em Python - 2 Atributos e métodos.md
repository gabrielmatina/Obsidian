[[3 - POO em Python]]

## Atributos / Propriedades

Atributos são propriedades das coisas. Se um liquidificador tem cinco velocidades, isso seria um atributo dele. No contexto de uma classe ou um objeto, atributos são variáveis. Uma classe define quais atributos tem, e um objeto quais os valores dos atributos. Existe uma exceção: atributos de classe, mas não falaremos deles agora.

Pense que um liquidificador tem uma cor e uma potência. A classe `Liquidificador` vai declarar esses atributos, mas não vai definir valores para eles. Isso quer dizer que todo liquidificador vai ter essas duas propriedades: cor e potência.

O meu liquidificador é preto, e ele tem 1200W (Watts) de potência. Vamos supor que o seu é vermelho e tem 1500W de potência. É perceptível que todo liquidificador tem valores diferentes (ou que _podem_ ser diferentes), logo, a valoração dos atributos é feita no objeto criado a partir da classe, e não na classe.

Reforçando: **a classe define atributos e objetos os valoram.**

Os atributos podem ser acessados pelo objeto por meio de um ponto (`.`). Exemplo:

```python
class Pessoa:
    pass

maria = Pessoa()

# Atribuo o valor "Maria" a um atributo chamado `nome` dentro do objeto `maria`
maria.nome = "Maria"
print(maria.nome) # saída: Maria
```

![Atributos e Métodos](https://content-assets.betrybe.com/prod/741ad7e6-838f-4d60-9dfb-58047a4f9629-Atributos%20e%20M%C3%A9todos.png)

### Métodos / Comportamentos

Além de definir os atributos do objeto, a classe também define seus métodos (ou comportamentos). Métodos são ações que um objeto ou uma classe podem realizar. No contexto de uma classe ou um objeto, métodos são funções. Uma classe geralmente define os métodos, e os objetos os invoca, embora em alguns casos os métodos também possam ser chamados diretamente a partir da classe. Por exemplo, observe possíveis métodos para algumas classes:

- Classe Carro
    - acelerar
    - frear
- Classe Pessoa
    - comer
    - respirar
- Classe Elevador
    - subir
    - descer
    - abrir
    - fechar
- Classe BancoDeDados
    - inserir
    - consultar

> De olho na dica 👀: Os **métodos** podem manipular os atributos.

```python
class Pessoa:
    def diz_nome_e_idade(self, idade: int) -> None:
        print(f"{self.nome} tem {idade} anos.")

maria = Pessoa()
maria.nome = "Maria"
maria.diz_nome_e_idade(20) # saída: Maria tem 20 anos.

jorge = Pessoa()
jorge.nome = "Jorge"
jorge.diz_nome_e_idade(28) # saída: Jorge tem 28 anos.
```

Podemos observar algumas coisas interessantes:

- Há um parâmetro chamado `self` no método `diz_nome_e_idade`.
- O atributo `nome` que é utilizado no método `diz_nome_e_idade` não existe na declaração da classe (sem as linhas 6 e 10 o código levantaria um erro).
- Nas linhas 7 e 11 só passamos o argumento do parâmetro `idade`, ignorando o `self`.

Como podemos ter vários objetos da classe `Pessoa`, precisamos referenciar, dentro da classe, o objeto atual que está acessando o método. Para isso, existe o `self` (também chamado de `this` em outras linguagens). Ele é o parâmetro explícito de todos os métodos, e recebe automaticamente como valor o objeto que está chamando o método. Quando chamamos `jorge.diz_nome_e_idade(20)`, o Python entende isso como `Pessoa.diz_nome_e_idade(jorge, 20)`. É por isso que na linha 3, ao chamar `self.nome` no print o método sabe que o nome é “Jorge”.

Existem dois pontos bastante positivos do `self` ser um parâmetro explícito (está na lista de parâmetros da função):

- Ele pode se chamar qualquer coisa. `self` é apenas uma convenção (que todo mundo segue).
- Os métodos de uma classe não precisam ser declarados dentro dela. O exemplo abaixo é equivalente ao anterior:

```python
def método(self, idade: int) -> None:
    print(f"{self.nome} tem {idade} anos.")

class Pessoa:
    diz_nome_e_idade = método

maria = Pessoa()
maria.nome = "Maria"
maria.diz_nome_e_idade(20) # saída: Maria tem 20 anos.
```

## Para fixar

## Exercício 5

Cria uma classe `Carro` e, depois, atribua valores aos atributos `marca`, `modelo`, `ano` e `cor`.

### Solução

```python
class Carro:
    pass


carro = Carro()

carro.marca = "Fiat"
carro.modelo = "Uno"
carro.ano = 2010
carro.cor = "vermelha"

```

## Exercício 6

Com base na classe `Carro` do exercício anterior, crie um método `identificar` que imprima “Meu carro é um `modelo`, da `marca`. Ele é do `ano` e tem a cor `cor`“.

### Solução

```python
class Carro:
    def identificar(self, modelo: str, marca: str, ano: int, cor: str) -> None:
        print(f"Meu carro é um {modelo}, da {marca}. Ele é do ano {ano} e tem a cor {cor}.")


carro = Carro()

carro.identificar("Uno", "Fiat", 2010, "vermelha")

```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/d530112b-87a8-4577-acd2-1a9651a89888/lesson/caf1d837-3f67-432c-9545-7efaae1b2e60)
