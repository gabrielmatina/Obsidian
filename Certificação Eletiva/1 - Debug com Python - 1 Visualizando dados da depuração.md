[[1 - Debug com Python]]

Enquanto estamos usando o _debugger_, o VS Code nos apresenta algumas janelas extras na lateral esquerda que serão muito úteis na investigação de nossos bugs:

- `VARIABLES` (variáveis)
- `CALL STACK` (pilha de chamadas)
- `WATCH` (inspeção)
- `BREAKPOINTS` (pontos de parada)

Além disso, também será possível utilizar o `DEBUG CONSOLE`, como veremos em breve! 😉

## Janela `VARIABLES` (variáveis)

Nessa janela poderemos ver todas as variáveis e os seus valores no contexto atual da aplicação.

No nosso exemplo, não vemos nenhum valor inicialmente, pois o _breakpoint_ está na função `main()` e, no momento do breakpoint, nenhuma variável foi criada ainda.

Assim que decidimos executar a linha atual (com `F10` ou `F11`), a variável `input_list` aparecerá na janela `VARIABLES` com seu valor: `[1, 2, 3, 4, 5]`. Ela fica dentro de `Locals` pois é uma variável do escopo da função atual.

Dentro de `Globals` não há nada aparente porque não criamos nenhuma variável no escopo global, mas se expandirmos `function variables` poderemos ver as funções que declaramos (e importamos) no nosso arquivo e seus respectivos metadados como `__name__`, `__doc__` e `__annotations__`.

Para ver mais detalhes sobre a variável, clique no símbolo de `>` à esquerda do nome. Como estamos olhando para uma lista do Python, é mostrado o valor armazenado em cada índice e também seu tamanho (representado em `len()`). Incrível, né?! 🤩

Dentro de `special variables` ficam os métodos e atributos “mágicos” (_magic methods_ ou _dunder methods_) daquele objeto, como `__str__`, `__class__` e `__contains__`.

Dentro de `function variables`, veremos os métodos padrões daquele objeto, como `append`, `remove` e `count`.

![VSCode debug: janela de variables](https://content-assets.betrybe.com/prod/8c38df4b-0b1a-47e6-9228-de27b9011a1c-VSCode%20debug:%20janela%20de%20variables.png)
VSCode debug: janela variables

## Janela `CALL STACK` (pilha de chamadas)

Aqui podemos ver, no ponto da execução do debug, qual é a **pilha de chamadas** da nossa aplicação. Em outras palavras, essa janela mostra quais funções/métodos/módulos foram chamados para que a execução chegasse até a linha atual.

No momento atual do nosso exemplo, temos a seguinte pilha:

- `main . . . . . . . . example.py [15:1]`
- `<module> . . . . . . example.py [19:1]`

A forma de ler isso é:

> A execução começou no arquivo `example.py` no escopo global `<module>` e seguiu até a linha `19` desse arquivo. A função `main` no arquivo `example.py` foi chamada e seguiu até a linha `15` desse arquivo.

Agora, vamos observar o que acontece quando avançamos com execução avançando para a próxima chamada de função. Para isso, utilize o “_step into_“ (ou intervir - atalho `F11`) para que o _debugger_ **entre** na função `map_factorial`.

Opa! 👀

A função `map_factorial` foi **empilhada** na nossa pilha de chamadas, e agora temos:

- `map_factorial . . . example.py [5:1]`
- `main . . . . . . . . example.py [15:1]`
- `<module> . . . . . . example.py [19:1]`

Juntando tudo, podemos interpretar que:

> A execução começou no arquivo `example.py` no escopo global `<module>` e seguiu até a linha `19` desse arquivo. A função `main` no arquivo `example.py` foi chamada e seguiu até a linha `15` desse arquivo. **A função `map_factorial` no arquivo `example.py` foi chamada e seguiu até a linha `5` desse arquivo.**

Ah, repare que a janela `VARIABLES` também mudou! Agora já não vemos mais a variável `input_list`, e temos o valor de `numbers` (parâmetro recebido pela função `map_factorial`).

## Janela `WATCH` (inspeção)

Essa janela mostra resultados de **qualquer expressão em Python** que desejarmos “vigiar”!

Por exemplo, podemos inspecionar o resultado de `sum(result)`. Assim, para cada avanço do _debugger_ a janela `WATCH` nos mostrará a soma de todos os elementos dentro da variável `result`. Para isso, basta clicar no sinal de ➕ no cabeçalho da janela e definir as expressões que desejar (como na imagem a seguir)

![VSCode debug: janela de watch](https://content-assets.betrybe.com/prod/8c38df4b-0b1a-47e6-9228-de27b9011a1c-VSCode%20debug:%20janela%20de%20watch.png)
VSCode debug: janela watch

Repare que, para o nosso exemplo, a variável `result` ainda não foi definida então o resultado da expressão `sum(result)` é um `NameError`.

Se avançarmos na execução com “_step into_“ (atalho `F11`), veremos que o valor da expressão será atualizado a medida que o conteúdo de `result` é alterado quando passamos pela linha `result.append(factorial(num))`. Os valores que veremos são: `0`, `1`, `3`, `9`, `33` e por fim `153`.

Viu que a janela `VARIABLES` foi alterando seus valores? A medida que avançamos, `result` teve seu valor preenchido e a variável `num` (_auxiliar do `for`_) foi sendo alterada a cada iteração!

Quando a função `map_factorial` retornar, `sum(result)` terá novamente um `NameError` pois a variável `result` deixa de existir no escopo da execução.


## Para fixar

## Exercício 1

Responda as seguintes perguntas:

1. Qual a importância de debugar o código?
2. Quais são as formas mais comuns de debugar código em Python?
3. Quais as vantagens de usar `debugger` do VS Code?

### Solução

1. Debugar o código é importante para encontrar erros e bugs e poder corrigi-los. Além disso, esse processo possibilita entender melhor o fluxo do código e como ele funciona.
2. As formas mais comuns de debugar código em Python são usar o `print` e o `debugger`, em especial o embutido do VS Code.
3. As vantagens de usar o `debugger` do VS Code são:
    - É possível parar a execução do código em um ponto específico.
    - É possível executar o código passo a passo.
    - É possível visualizar o valor das variáveis em cada passo da execução do código.
    - É possível executar o código em modo de debug sem precisar alterar o código.


## Exercício 2

Considere o exemplo de código a seguir:

```python
from math import factorial


def map_factorial(numbers):
    result = []

    for num in numbers:
        result.append(factorial(num))

    return result


def main():
    input_list = [1, 2, 3, 4, 5]
    return map_factorial(input_list)


if __name__ == "__main__":
    main()

```

**Sem alterar o código**, descubra qual exceção é levantada se:

1. Um dos elementos da `input_list` for um **inteiro negativo**.
2. Um dos elementos da `input_list` for uma **string**.

