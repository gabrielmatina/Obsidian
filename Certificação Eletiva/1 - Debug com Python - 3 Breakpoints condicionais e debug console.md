[[1 - Debug com Python]]

Até agora você aprendeu a usar o debugger do VS Code para executar o código linha a linha, inspecionar variáveis e expressões, e até mesmo alterar o valor de variáveis durante a execução do programa. 🤩

Mas e se você quiser que o debugger pare a execução do programa somente em determinadas condições? Neste caso, os breakpoints condicionais do VS Code podem facilitar ainda mais o processo!

Para ilustrar isso, vamos utilizar como exemplo uma implementação prática do algoritmo de ordenação conhecido como _Insertion Sort_. Esse algoritmo percorre o array da esquerda para a direita e, a cada iteração, insere o elemento atual na posição correta do array. Mas não se preocupe, você não precisa dominar o funcionamento do algoritmo para entender o exemplo. 😉

```python
def insertion_sort(array):
    for i in range(1, len(array)):
        current_value = array[i]
        position = i

        while position > 0 and array[position - 1] > current_value:
            array[position] = array[position - 1]
            position -= 1

        array[position] = current_value

    return array


def main():

    array = [23, 423, 1, 54, 8, 980, 45, 768, 34, 55, 88, 99, 100, 234, 567]

    sorted_array = insertion_sort(array)

    print(f"Array ordenado: {sorted_array}")


if __name__ == "__main__":
    main()

```

## Breakpoints condicionais

Como o próprio nome sugere, os breakpoints condicionais são breakpoints que só param a execução do programa quando uma condição específica é satisfeita. Vamos analisar o código do exemplo para entender como isso funciona.

Imagine que você está debugando o código do _Insertion Sort_ e quer saber o valor da variável `array` ao final do _loop_ `while` quando o valor da variável `i` for maior ou igual a `5`.

Para fazer isso, você pode colocar um breakpoint na linha de código após o _loop_ `while` e configurá-lo para parar a execução somente quando o valor da variável `i` for igual a `5`.

Para fazer isso, basta clicar com o botão direito do mouse na bolinha vermelha 🔴 do breakpoint e selecionar a opção “Edit Breakpoint…”:

![VSCode debug: editando breakpoint](https://content-assets.betrybe.com/prod/3f8bda42-e1ef-4703-8e67-b5c7b5f66133-VSCode%20debug:%20editando%20breakpoint.png)
VSCode debug: editando breakpoint

Na janela que se abre, você pode digitar a condição que precisará ser satisfeita para o breakpoint interromper a execução do programa, nesse caso `i >= 5`, e apertar `Enter` para definir esse breakpoint condicional:

![VSCode debug: breakpoint condicional](https://content-assets.betrybe.com/prod/3f8bda42-e1ef-4703-8e67-b5c7b5f66133-VSCode%20debug:%20breakpoint%20condicional.png)VSCode debug: breakpoint condicional

> **Relembrando 🧠:** Neste caso, o breakpoint só vai parar a execução do programa quando o valor da variável `i` for maior ou igual a `5`.

Avance a execução do programa até que o breakpoint seja atingido!

## VS Code Debug Console

Nesse caso, infelizmente a janela VARIABLES não nos mostra o valor **completo** da variável `array`. O valor após 234 fica oculto 😢

```python
1  [1, 8, 23, 54, 423, 980, 45, 768, 34, 55, 88, 99, 100, 234, ...]
```

Mas não se preocupe, o VS Code tem uma ferramenta que nos permite inspecionar o valor de variáveis e expressões durante a execução do programa: o **VS Code Debug Console** (console de Debug do VS Code).

Para abrir o VS Code Debug Console, clique no ícone do VS Code Debug Console na barra de ferramentas do VS Code.

Na janela que se abre, você pode realizar qualquer operação (como se fosse o próprio terminal do Python) e inspecionar o valor de variáveis e expressões durante a execução do programa. Para o nosso exemplo, vamos imprimir o valor da variável `array`:

```python
print(array)
```

Agora sim, podemos ver o valor completo da variável `array`:

```python
1 [1, 8, 23, 54, 423, 980, 45, 768, 34, 55, 88, 99, 100, 234, 567]
```

![VSCode debug: abrindo o debug console](https://content-assets.betrybe.com/prod/3f8bda42-e1ef-4703-8e67-b5c7b5f66133-VSCode%20debug:%20abrindo%20o%20debug%20console.png)VSCode debug: abrindo o debug console

Além disso, podemos extrapolar e realizar operações como somar todos os elementos do array e remover o último elemento do array:

```python
sum(array)
# 3479

array.pop()
# 567
```

> **De olho na dica 👀:** Se continuarmos a execução do programa com o debugger, o valor da variável `array` continua alterado.

## Para Fixar

## Exercício 3

Considere o exemplo de código a seguir:


```python
def insertion_sort(array):
    for i in range(1, len(array)):
        current_value = array[i]
        position = i

        while position > 0 and array[position - 1] > current_value:
            array[position] = array[position - 1]
            position -= 1

        array[position] = current_value

    return array


def main():

    array = [23, 423, 1, 54, 8, 980, 45, 768, 34, 55, 88, 99, 100, 234, 567]

    sorted_array = insertion_sort(array)

    print(f"Array ordenado: {sorted_array}")


if __name__ == "__main__":
    main()

```

Descubra o valor de `current_value` na linha do `while` quando a soma de `i` e `position` for igual a `6` pela primeira vez.

### Solução

O valor é 54.

A forma mais fácil de descobrir o valor é usando o `debugger` do VS Code. Para isso, basta colocar um breakpoint condicional na linha do `while` com a condição `i + position == 6` e executar o código em modo de debug. Quando o código parar no breakpoint, basta conferir o valor da variável `current_value` na janela `VARIABLES`.