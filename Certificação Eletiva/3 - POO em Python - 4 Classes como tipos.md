[[3 - POO em Python]]

Uma classe pode ser utilizada como tipo quando uma instância dela é esperada como valor de alguma variável. Por exemplo, vamos considerar que queremos que uma função receba como parâmetro um liquidificador qualquer.

Veja como é o exemplo com duas das quatro formas de checagem de tipagem em Python.

> A quarta forma, a tipagem estrutural estática por meio de protocolos, será discutida mais à frente na seção 😎.

Para isso, considere as seguintes declarações:

Copiar

```python
class Liquidificador:
    def __init__(self, cor: str) -> None:
        self.cor = cor


class Geladeira:
    def __init__(self, cor: str) -> None:
        self.cor = cor


class Fogão:
    def __init__(self, quantidade_de_bocas: int) -> None:
        self.quantidade_de_bocas = quantidade_de_bocas


liq = Liquidificador("preto")
gel = Geladeira("branca")
fog = Fogão(4)
```

## Com tipagem estrutural dinâmica

Vamos começar observando a tipagem estrutural dinâmica (_duck typing_ ou _tipagem pato_, a “padrão” no mundo Python).

```python
def print_cor(liquidificador): # Não colocamos type hints
    print(liquidificador.cor) # simplesmente tentamos acessar o atributo

print_cor(liq) # roda normalmente e tem saída "preto"
print_cor(gel) # roda normalmente e tem saída "branca"
print_cor(fog) # levanta um AttributeError
```

Aqui está o poder do _duck typing_: A primeira chamada a `print_cor` funciona normalmente passando um liquidificador (`liq`). A segunda chamada também! Mesmo que `gel` não seja um liquidificador, e sim uma geladeira, pois como ela tem um atributo `cor` a função funciona normalmente. Por isso, tipagem estrutural: se faz _quack_ como um pato, deve ser um pato (ou nesse caso, se tem cor como um liquidificador, deve ser um liquidificador). Por fim, fogão não faz _quack_ (um objeto da classe fogão não tem o atributo `cor`), fazendo com que um `AttributeError` seja levantado ao tentar executar `print(liquidificador.cor)`, sendo `liquidificador`, neste contexto, `fog`.

## Com tipagem nominal estática

```python
def print_cor(liquidificador: Liquidificador) -> None:  # Colocamos Type Hint
    print(liquidificador.cor)
print_cor(liq)
print_cor(gel)
print_cor(fog)
```

Tal como no caso do _duck typing_, em tempo de execução as duas primeiras chamadas à `print_cor` funcionam, mas a terceira levanta um `AttributeError`. Em tempo de execução nada muda. Mas se rodarmos o _mypy_ antes de executarmos o código, ele nos dirá algo semelhante a isso:

```text
app.py:26: error: Argument 1 to "print_cor" has incompatible type "Geladeira"; expected "Liquidificador"  [arg-type]
app.py:27: error: Argument 1 to "print_cor" has incompatible type "Fogão"; expected "Liquidificador"  [arg-type]
Found 2 errors in 1 file (checked 1 source file)
```

São os mesmos erros que identificamos com a verificação nominal dinâmica (a primeira chamada à `print_cor` está okay, mas as duas últimas recebem tipos incorretos).

A diferença é que obtemos essas informações sem a necessidade de executar o código (sem afetar o tempo de execução), sem restringir sua flexibilidade (ainda podemos executar a segunda chamada, mesmo que o _mypy_ emita um aviso), e sem adicionar uma grande quantidade de código adicional (o código aumenta, mas não de forma significativa).

## Para fixar

## Exercício 9

Crie as classes `Motocicleta` e `Caminhao` e inicialize as duas com o atributo `velocidade_maxima`.

### Solução

```python
class Motocicleta:
    def __init__(self, velocidade_maxima: int) -> None:
        self.velocidade_maxima = velocidade_maxima


class Caminhao:
    def __init__(self, velocidade_maxima: int) -> None:
        self.velocidade_maxima = velocidade_maxima


cg_150 = Motocicleta("120 km/h")
yamaha_R1 = Motocicleta("300 km/h")
ford_C_2042 = Caminhao("140 km/h")
```

## Exercício 10

Utilize as classes criadas no exercício anterior para criar uma função `print_velocidade_maxima` que recebe um objeto e imprime a velocidade máxima dele. Retorne uma mensagem de erro com um `TypeError` caso o veículo passado como argumento não seja uma motocicleta.

### Solução

```python
def print_velocidade_maxima(veiculo: int) -> None:
    if isinstance(veiculo, Motocicleta):
        print(f"A velocidade máxima do veículo é de {veiculo.velocidade_maxima}")
    else:
        raise TypeError("O objeto precisa ser uma motocicleta")


print_velocidade_maxima(cg_150)  # 120 km/h
print_velocidade_maxima(yamaha_R1)  # 300 km/h
print_velocidade_maxima(ford_C_2042)  # TypeError
```


# Projeto de exemplo

Para ajudar a consolidar os aprendizados, modelamos um pequeno projeto de exemplo que faz uso dos conceitos estudados no dia de hoje. Leia o código a seguir e analise o uso de classes, principalmente prestando atenção às relações de herança e composição, como também analisando os atributos, a tipagem estática, o `self` e o uso do `super()`.

Salve o conteúdo em um arquivo chamado `app.py`.

Com base na leitura, responda às perguntas a seguir:

- Onde se utilizou herança?
- Como o `super()` ajudou a reaproveitar código?
- Qual o motivo do `self` existir? Seria possível alterar o objeto sem ele?
- Tem um parâmetro no código que não tem uma dica de tipo.
    - Rode `mypy app.py`. O _mypy_ não reclama. Por quê?
    - Rode `mypy --strict app.py`. O _mypy_ passa a reclamar. Por quê?
        - Coloque a dica de tipo correta e satisfaça o _mypy_.

Por fim, execute o código e crie algumas questões (por exemplo as questões acima), observando o funcionamento. Se tiver tempo, crie testes para o código, novas funcionalidades, busque _bugs_, enfim, se divirta 😄!

```python
from typing import Any, Optional


class Questao:
    def __init__(self, enunciado: str, resposta_correta: Any) -> None:
        self.enunciado = enunciado
        self.resposta_correta = resposta_correta

    def resolver(self, resposta: Any) -> bool:
        return bool(self.resposta_correta == resposta)

    def __str__(self) -> str:
        # ? Para entender este método, olhe o tópico opcional na lição Herança
        return self.enunciado


class QuestaoDeMultiplaEscolha(Questao):
    def __init__(self, enunciado: str, resposta_correta: int) -> None:
        super().__init__(enunciado, resposta_correta)
        self.alternativas: list[str] = []

    def adicionar_alternativa(self, alternativa: str) -> None:
        self.alternativas.append(alternativa)

    def resolver(self, resposta: int) -> bool:
        # Tipagem nominal dinâmica por meio da chamada à função `int`
        return super().resolver(int(resposta))

    def __str__(self) -> str:
        # Retorna o enunciado e todas as alternativas numeradas, separadas
        # por quebras de linha (\n)
        return (
            super().__str__()  # Retorna o enunciado
            + "\n"  # Quebra de linha entre o enunciado e alternativas
            + "\n".join(  # Junta as alternativas com quebras de linha
                f"{num} - {texto}"  # Junta o número da alternativa com o texto
                # Enumera as alternativas, contando a partir do 0
                for num, texto in enumerate(self.alternativas)
            )
        )


class QuestaoAberta(Questao):
    def __init__(self, enunciado: str, resposta_correta: str) -> None:
        super().__init__(enunciado, resposta_correta)

    def resolver(self, resposta: str) -> bool:
        return super().resolver(resposta)


class Questionario:
    def __init__(self, questões: Optional[list[Questao]] = None) -> None:
        # Cria uma lista vazia se não for passada uma lista
        self.questões = questões or []

    def adicionar_questao(self, questao) -> None:
        self.questões.append(questao)

    def resolver(self) -> float:
        acertos = 0
        for questao in self.questões:
            print(questao)
            resposta = input("Resposta: ")
            if questao.resolver(resposta):
                acertos += 1

        return acertos / len(self.questões)


def criar_questao() -> Questao:
    input_str = "Digite A para uma questão aberta e M para múltipla escolha: "
    while (tipo := input(input_str).lower()) not in ["a", "m"]:
        print("Tipo de questão inválido!")

    enunciado = input("Digite o enunciado: ")

    # ? Questão aberta
    if tipo == "a":
        resposta_correta = input("Digite a resposta: ")
        return QuestaoAberta(enunciado, resposta_correta)

    # ? Questão de múltipla escolha
    alternativas: list[str] = []
    input_str = "Digite a alternativa ou P para parar: "
    while (alternativa := input(input_str)).lower() != "p":
        alternativas.append(alternativa)

    input_str = "Digite o número da alternativa correta: "
    # Enquanto a pessoa digitar uma alternativa menor que 0 ou maior que a
    # última, continua perguntando
    while (certa := int(input(input_str))) < 0 or certa >= len(alternativas):
        print("Número de alternativa inválido!")

    questao = QuestaoDeMultiplaEscolha(enunciado, resposta_correta=certa)

    for alternativa in alternativas:
        questao.adicionar_alternativa(alternativa)

    return questao


def main() -> None:
    questionario = Questionario()

    while True:
        escolha = input(
            "Digite A para adicionar uma questão, "
            "R para resolver o questionário "
            "ou S para sair: "
        ).lower()

        if escolha == "a":
            questionario.adicionar_questao(criar_questao())
        elif escolha == "r":
            resultado = questionario.resolver() * 100
            print(f"Sua nota foi {resultado:.2f}%")
        elif escolha == "s":
            print("Até mais!")
            break
        else:
            print("Escolha inválida!")


if __name__ == "__main__":
    while True:
        try:
            print("Vamos começar!")
            main()
        except Exception:
            pass
        else:
            break

```


# Para não esquecer mais

Vamos a uma lista de termos e o resumo de seus significados. Esta página é ideal para consulta!

- **Anotação de tipo | Dica de tipo | Type annotation | Type hint**: forma explícita de tipar valores em Python. Usando uma ferramenta externa conseguimos usá-los para ter tipagem estática em Python
    
- **Atributo**: características que definem uma classe. A classe os define e os objetos dão seus valores.
- Checagem de tipos
    - **Estática**: define o tipo explicitamente, permitindo sua checagem em tempo de execução
    - **Dinâmica**: ‘descobre-se’ o tipo durante a execução do código somente, sem necessidade de se definir nada explicitamente
    - **Estrutural**: infere-se o tipo de um valor baseado nos comportamentos que ele tem
    - **Nominal**: infere-se o tipo de um valor com base no nome do mesmo, ainda que outros tipos tenham comportamentos 100% idênticos
- **Classe**: entidade, que pode se basear em algo real ou num conceito abstrato, que possui atributos e comportamentos no seu código
- **Inferência de tipo**
- **Instância**: se a classe é o molde, a instância é a coisa que o molde faz. Uma classe pode gerar _N_ instâncias, cada uma com seus valores em atributos.
- **Método**: comportamento de uma classe - funções dela, que podem acessar seus atributos
- **mypy**: ferramenta que permite tipagem estática em Python
- **Objeto**: instância de uma classe. Se `Carro` é a classe,`meuCarroEsporte` é o objeto.
- **Self**: entidade que permite, no Python, uma classe acessar os valores dos atributos de quem a invocou, como o `this` do JavaScript.
- **Método construtor**: invocado sempre que se cria uma instância, ele coloca valores em atributos.
- **Classes como tipos**: se a instância de uma classe é esperada como valor, ela pode ser usada para fazer tipagem estática.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/d530112b-87a8-4577-acd2-1a9651a89888/lesson/932037fd-cfc6-46ef-bc59-014c1eb2b3fc)

