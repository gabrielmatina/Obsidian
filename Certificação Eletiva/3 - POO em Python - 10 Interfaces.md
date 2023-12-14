[[3 - POO em Python]]

Luciano Ramalho, em seu livro _Python Fluente_, define as **interfaces** como o subconjunto dos métodos públicos de um objeto. Trata-se de uma espécie de contrato para forçar a implementação de determinados métodos e determinadas propriedades. Em um alto nível, uma interface atua como um modelo para projetar classes.

![Interfaces](https://content-assets.betrybe.com/prod/55afc0d2-2ef7-420c-b316-f3f340ebb7a6-Interfaces.png)

Uma das metas da **programação orientada a objetos** é facilitar a manutenção do programa, a fim de que a pessoa desenvolvedora possa mantê-lo funcionando quando outras partes do sistema forem alteradas. Além disso, que ela possa alterar o programa para satisfazer novas condições – e no mundo dinâmico que vivemos hoje, as mudanças são praticamente diárias.

Um princípio ou padrão de projeto que ajuda a atingir essa meta é manter as interfaces separadas das implementações. Para objetos, isso quer dizer que os métodos que uma classe oferece não devem depender de como os atributos são representados.

Assim como as classes, as interfaces definem métodos. No entanto, ao contrário das classes, esses métodos são **abstratos**.

Um **método abstrato** é aquele que a interface simplesmente define, ou seja, não implementa o corpo desses métodos. No caso, esse corpo do método é construído em classes que implementam a interface e dão significado concreto aos métodos abstratos da interface.

Veja um exemplo simples de interface a seguir. Vamos utilizar a exceção **NotImplementedError** em uma classe pai:

> exemplo_interface.py


```python
class Funcionario:

    def calcular_salario(self):
      raise NotImplementedError("Classes derivadas de Funcionario precisam implementar o cálculo de salário.")
```

Dessa forma, para que a exceção não ocorra, todas as classes filhas de _Funcionario_ vão precisar implementar o próprio método para calcular_salario().

Veja o que acontece caso o método calcular_salario() não seja implementado:

```python
>>> class Analista(Funcionario):
...     pass
... 
>>> a = Analista()
>>> a.calcular_salario()

Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "<stdin>", line 3, in calcular_salario
NotImplementedError: Classes derivadas de Funcionario precisam implementar o cálculo de salário.
```

Bingo! Houve uma violação de contrato aqui, visto que não foi atendida uma especificação da interface.

Perceba que **Funcionario** é uma classe como qualquer outra (sem nenhuma sintaxe extra), e portanto você pode fazer o que quiser com ela, inclusive inserir métodos não abstratos (diferentemente de outras linguagens como Java).

Pode acontecer que, depois de implementar uma nova classe, você descubra uma implementação melhor. Se outras partes do programa estiverem usando a sua classe, mudar a interface pode ser trabalhoso e induzir a erros. No entanto, se projetou a interface cuidadosamente, pode alterar a implementação sem mudar a interface, e não será preciso mudar outras partes do programa. 💡

Agora, você sabe como as interfaces funcionam em uma linguagem com **tipagem dinâmica** como o Python?

Mesmo sem uma palavra reservada **interface** na linguagem Python e independente das ABCs, toda classe tem uma interface: os atributos públicos definidos (métodos ou atributos de dados), implementados ou herdados pela classe. Isso inclui métodos especiais, como `__getitem__` ou `__add__`. Ou seja, a interface é antes de mais nada um conceito que pode ser aplicado sempre que necessário.

## Interfaces formais vs Interfaces informais

Em certas circunstâncias, você pode não precisar das regras estritas de uma interface Python formal. A natureza dinâmica do Python permite a você que implemente uma **interface informal**.

As **classes abstratas** são exemplos de **interfaces formais**. Já a interface informal será muito parecida com uma classe Python comum. No caso, ela poderá definir métodos que posteriormente serão sobrescritos, mas não há obrigatoriedade para tal. Já uma interface formal funcionará como uma espécie de contrato, obrigando suas subclasses a implementar seus métodos.

Uma interface Python informal é uma classe que define métodos que podem ser substituídos, mas não há imposição estrita ou obrigatoriedade – assim como fizemos no exemplo `Funcionario` mais acima.

Um exemplo famoso e útil de **interface informal** é um **protocolo**, que será o próximo conceito apresentado ao longo dessa seção.

O Python oferece grande flexibilidade ao criar interfaces. Por exemplo, a **informal** é útil para pequenos projetos em que é menos provável que você fique confuso sobre quais são os tipos de retorno dos métodos. À medida que um projeto cresce, a necessidade de uma interface Python **formal** torna-se mais importante, visto que fica mais difícil inferir os tipos de retorno. Isso garante que a classe concreta, que implementa a interface, substitua os métodos abstratos.

## Para fixar

## Exercício 2

Crie uma interface informal em Python chamada `Produto`, ela deve definir um método abstrato chamado `imprimir_preco`. Caso o método `imprimir_preco` não seja implementado, lance uma exceção `NotImplementedError`. Crie também uma subclasse `Livro`, que será o nosso exemplo de `Produto` nesse exercício e implemente o método `imprimir_preco` para mostrar o preço do livro na tela.

### Solução

```python
class Produto:
    def __init__(self, preco):
        self.preco = preco

    def imprimir_preco(self):
        raise NotImplementedError(
            "Classes derivadas de Produto precisam implementar o método imprimir_preco."
        )


class Livro(Produto):
    def __init__(self, preco):
        super().__init__(preco)

    def imprimir_preco(self):
        print(f"O preço do livro é: {self.preco}")


livro = Livro(10)
livro.imprimir_preco()

```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/0451bc54-860e-4eff-9b0c-ac27bc64a2aa/lesson/c66b8c72-01fb-4c10-9123-8b03ff2f9cb1)
