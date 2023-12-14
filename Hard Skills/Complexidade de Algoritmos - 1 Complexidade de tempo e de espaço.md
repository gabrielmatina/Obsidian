[[Complexidade de Algoritmos]]
[[Python]]

Anteriormente, dissemos que a complexidade de um algoritmo representa o crescimento de seu tempo de execução em função de uma taxa, a quantidade de operações que ele realiza. Porém, quando falamos em complexidade, não analisamos apenas o tempo, analisamos também o espaço gasto. Vejamos como isso funciona.

Observe o algoritmo a seguir:

```python
def squared_array(numbers):
    array_of_squares = []
    for number in numbers:
        array_of_squares.append(number * number)

    return array_of_squares
```

Esse algoritmo recebe um `array` de números, percorre esse `array` e retorna um _novo_ com os números ao quadrado. Ou seja, ele passa por _todos_ os elementos desse `array`. Isso significa que se houver 10 números na entrada de dados,por exemplo, serão realizadas 10 operações; se houver 100 serão realizadas 100 operações. O que isso representa em termos de complexidade?

Em relação à Complexidade de Tempo, temos aqui uma _taxa de crescimento linear_, uma vez que o aumento no tamanho do `array` faz crescer proporcionalmente o tempo gasto na execução do algoritmo. Sendo assim, podemos afirmar que a Complexidade de Tempo aqui é `O(n)`, chamada geralmente tempo linear (Lembre-se que `O` faz referência aqui a ordem de complexidade, enquanto `(n)` representa a fórmula matemática que diz sobre a taxa de crescimento do número de operações).

> “E quanto à Complexidade de Espaço?” 🤔

Bem, como sabemos, esse algoritmo vai sempre nos retornar um `array` com o mesmo tamanho da entrada de dados, pois ele sempre devolve um _novo_ `array`com _todos_ os números de entrada ao quadrado: se entrar um `array` de 10 números, sairá um de 10; se entrar um de 100, sairá um de 100 e assim sucessivamente. Desse modo, conforme a entrada cresce, a saída também cresce e, consequentemente, o _espaço_ ocupado por ela, o que implica dizer que sua Complexidade de Espaço é dada por `O(n)`.

Bora para mais um exemplo!

Recorde-se do algoritmo mencionado na seção passada, da função `sum_array`. Naquele caso, a Complexidade de Tempo também era `O(n)`, já que o tempo de execução crescia linearmente.

> Mas e sua complexidade de espaço? 🤔

No caso de `sum_array`, mesmo que a entrada de dados fosse crescendo, sua saída nunca ocuparia mais espaço, pois o retorno era sempre um número só. Sendo assim, sua Complexidade de Espaço era **constante** e pode ser representada pela notação `O(1)`.

Para finalizar, um ponto importante que deve ser ressaltado é que quando calculamos a complexidade de espaço _não_ levamos em consideração o espaço ocupado pela _entrada_, uma vez que o tamanho da entrada não é algo que podemos, com nosso algoritmo, influenciar.

> Anota aí 🖊: Se falamos em **ordem de complexidade** sem especificar se é de tempo ou de memória, assuma que é de tempo!

Que tal fazermos um exercício para fixar esses conceitos? 😄

**Exercício 1:** Qual é a Ordem de Complexidade (complexidade de tempo) do algoritmo abaixo? E a complexidade de espaço?

```python
def multiply_array(numbers):
    result = 1
    for number in numbers:
        result *= number

    return result
```




```python
def multiply_array(numbers):
    result = 1
    for number in numbers:
        result *= number

    return result
```

**Solução**

Complexidade de Tempo: `O(n)`

Complexidade de Espaço: `O(1)`

Sobre a complexidade de tempo: Independentemente do tamanho da entrada _(numbers)_ o nosso laço for irá percorrer cada elemento do array de tamanho **n**. Por isso, sua complexidade de tempo é **O(n)**.

Sobre a complexidade espacial, iremos operar sobre um número constante de variáveis que, por sua vez, armazenam um único elemento em memória. A variável result será responsável por armazenar o resultado numérico de nossa operação de multiplicação. Por isso, nossa complexidade espacial é **O(1)**.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/cb6a6831-db64-41b7-9197-6656dc970b75/lesson/bfaa031d-3290-4fbe-99da-0bda22587926)
