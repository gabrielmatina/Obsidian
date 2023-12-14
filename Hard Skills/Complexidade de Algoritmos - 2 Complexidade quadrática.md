[[Python]]
[[Complexidade de Algoritmos]]


Nós já compreendemos o que é e qual a notação que representa a Complexidade Algorítmica. A partir de agora, vamos ver que, dependendo da forma como um algoritmo é escrito, seu tempo de execução vai ser alterado de acordo com diferentes taxas de crescimento.

Nesta e nas próximas seções veremos como o _“tempo de execução dos algoritmos cresce a taxas diferentes”_ (BHARGAVA, ADITYA Y.).

Observe o algoritmo abaixo:

```python
# Os arrays têm sempre o mesmo tamanho
def multiply_arrays(array1, array2):
    result = []
    for number1 in array1:
        for number2 in array2:
            result.append(number1 + number2)

    return result
```

No algoritmo acima, são recebidos dois `arrays` de tamanhos iguais e é retornado um novo `array`, cujos elementos são resultado da soma de cada um dos elementos do `array1` com todos os elementos do `array2`.

> Qual seria a taxa de crescimento do tempo de execução desse algoritmo?🤔

Para cada número do `array1` ser somado com todos os números contidos no `array2`, é necessário que o segundo seja percorrido por inteiro.

Isso significa que para `array1` e `array2` com duas posições, serão necessárias 4 iterações (ou operações), para o algoritmo concluir sua execução. Se cada uma das entradas tiver 3 elementos, serão necessárias 9 operações para a conclusão da execução e assim sucessivamente.

Rode o exemplo abaixo para conferir:

```python
def multiply_arrays(array1, array2):
    result = []
    number_of_iterations = 0

    for number1 in array1:
        print(f'Array 1: {number1}')
        for number2 in array2:
            print(f'Array 2: {number2}')
            result.append(number1 * number2)
            number_of_iterations += 1

    print(f'{number_of_iterations} iterações!')
    return result


meu_array = [1, 2, 3, 4, 5]

multiply_arrays(meu_array, meu_array)
```

> Para o exemplo acima, no qual as duas entradas continham 5 elementos, foram necessárias **25 operações** para obtermos o resultado final!

Anota aí 🖊: conforme aumentamos o tamanho dos `arrays` de entrada, o número de operações para a execução do algoritmo cresce ao quadrado. Isso significa que, para entradas de tamanho `n`, a quantidade de operações para executar o algoritmo é de `n²`. Sendo assim, a complexidade desse algoritmo é dada por `O(n²)` e a chamamos de Complexidade Quadrática.

Com esses exemplos, percebemos que algoritmos diferentes crescem a taxas diferentes. Vamos, agora, fazer alguns exercícios para fixar melhor esses conceitos!

**Exercício 2:** Meça o tempo de execução do algoritmo acima e, mudando o tamanho das entradas, veja como, se você aumenta a entrada em `n` vezes, o tempo de execução aumenta em `n²` vezes!

```python
def multiply_arrays(array1, array2):
    result = []
    number_of_iterations = 0

    for number1 in array1:
        print(f'Array 1: {number1}')
        for number2 in array2:
            print(f'Array 2: {number2}')
            result.append(number1 * number2)
            number_of_iterations += 1

    print(f'{number_of_iterations} iterações!')
    return result


meu_array = [1,2,3,4,5]

multiply_arrays(meu_array, meu_array)
```

Para desembaraçar a sopa de letrinhas que a seção anterior criou, meça o tempo de execução do algoritmo acima e, mudando o tamanho das entradas, veja como, se você aumenta a entrada em `n` vezes, o tempo de execução aumenta em `n²` vezes!


**Exercício 3:** Faça um algoritmo qualquer com três loops aninhados um dentro do outro. Entenda como ele terá uma complexidade de `O(n³)`!

```python
def multiply_arrays(array1, array2, array3):
    result = []
    number_of_iterations = 0

    for number1 in array1:
        for number2 in array2:
            for number3 in array3:
                result.append(number1 * number2 * number3)
                number_of_iterations += 1

    print(f'{number_of_iterations} iterações!')
    return result


# Usar arrays de tamanho 1000 aqui pode ser muito lento!
meu_array = list(range(1, 100))
multiply_arrays(meu_array, meu_array, meu_array)
```

A complexidade será O(n³). Em termos do número de iterações necessárias, para um array de 10 elementos iremos realizar 1000 iterações! 10_10_10 == 1000. O tempo de execução pode variar entre uma máquina e outra, mas, se estiver no ambiente Linux ou MacOS, tente utilizar o comando time para aferir o tempo de execução para entradas variáveis em seu computador! Observe o padrão de crescimento.

**Dica:** Tenha sempre o ctrl+c engatilhado em seu terminal para encerrar o processo caso este tome muito tempo para concluir.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/cb6a6831-db64-41b7-9197-6656dc970b75/lesson/3ab473f7-2c25-4c01-a7f6-3dca46cddb6b)
