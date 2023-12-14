**Algoritmos desta categoria buscam um item com uma determinada propriedade dentro de uma coleção**, podendo esta coleção ser gerada elemento a elemento, a partir de uma série de operações (fórmula matemática, procedimento), não necessitando uma coleção de fato. Esses algoritmos não devem ser associados somente com arrays. São considerados algoritmos desta categoria aqueles que fazem travessias em estruturas de dados com o propósito de encontrar um valor.

![Onde está o wally](https://content-assets.betrybe.com/prod/2f5a0c29-5add-4223-aec7-cd0893753cda-Onde%20est%C3%A1%20o%20wally.jpg)
|Onde está o wally?

## Busca Linear

Uma forma simples de fazer a busca de algum valor em um array ou lista é usando a **busca linear**, que consiste em percorrer toda a estrutura elemento a elemento, tentando encontrar o valor. Também é conhecida como busca sequencial, por conta da maneira com que percorremos a estrutura em busca do valor.

A busca linear pode ser simples, mas não necessariamente será a solução mais rápida, já que ela faz uma verificação de todos os elementos para encontrar qual é o correspondente.

Veja na imagem abaixo:

![Busca Linear](https://content-assets.betrybe.com/prod/2f5a0c29-5add-4223-aec7-cd0893753cda-Busca%20Linear.gif)
Busca Linear

Um exemplo de implementação da busca linear é:

```python
def linear_search(numbers, target):
    n = len(numbers) # N será a quantidade de elementos da lista
    for index in range(0, n): # vamos iterar a lista completa
        if numbers[index] == target: # se encontrar o elemento alvo, retorne a posição
            return index

    return -1 # Não encontrou? Retorne -1


print(linear_search([1, 2, 3], 2))  # saída: 1
print(linear_search([1, 2, 3], 4))  # saída: -1
```

## Busca Binária

A Busca binária (_binary search_) é mais um exemplo onde empregamos a técnica da divisão e conquista. É importante destacar que ela supõe que nossa coleção está **ordenada** e seu funcionamento se dá através de múltiplas divisões do espaço de busca, reduzindo-o, buscando o elemento no meio do espaço.

**Vamos entender melhor?** 💪

1. Vamos imaginar que você está estudando inglês e precisa encontrar uma palavra _mind_ no dicionário. Como sabemos que ele já está ordenado, eu suponho que você o abriria em uma página mais próxima da metade, certo? Pois você estará mais perto da letra M!
2. Supondo que você abriu uma página com as iniciais em J, agora você percebe que deve avançar, pois o M vem após o J na ordem alfabética. Visto isso, você abre mais um bloco para frente.
3. Agora você abriu o dicionário na letra N. Puxa, foi quase! Você observa que o M está logo antes do N e volta poucas páginas.
4. Pois bem! Agora você está na letra M e pode encontrar a palavra desejada.

Com esse exemplo você já pode ter uma ideia do que é uma busca binária. **Agora vamos para um exemplo computacional:**

1. Vamos supor a seguinte lista: `[1, 10, 35, 42, 51, 60, 75]`.
2. Nesse caso, o número buscado é `60`.
3. Dividimos a lista em duas partes e verificamos se o elemento do meio (`42`) é o elemento procurado.
4. Como sabemos que a lista está ordenada e que o valor buscado é maior que o encontrado, não precisamos comparar com todos os outros à esquerda. Vamos procurar somente os valores posteriores a ele `[51, 60, 75]`.
5. Realizamos o mesmo processo de divisão e nosso elemento do meio passa a ser `60`.
6. Como encontramos o valor, vamos retornar o seu índice, `5`.

> 👀 **De olho na dica:** a busca binária é mais rápida que a busca linear, visto que o número de comparações necessárias, mesmo em um caso onde não encontre um elemento, é menor.

Veja na imagem abaixo:

![Busca Binária Binary Search](https://content-assets.betrybe.com/prod/2f5a0c29-5add-4223-aec7-cd0893753cda-Busca%20Bin%C3%A1ria%20Binary%20Search.gif)
Busca Binária (Binary Search)

**Agora vamos ver na prática:**

```python
def binary_search(numbers, target):
    # definir os índices
    start = 0
    end = len(numbers) - 1

    while start <= end: # os índices podem ser no máximo iguais, o início não pode ultrapassar o fim
        mid = (start + end) // 2 # encontro o meio

        if numbers[mid] == target: # se o elemento do meio for o alvo, devolve a posição do meio
            return mid
        
        if target < numbers[mid]: # se o elemento for menor, atualiza o índice do fim
            end = mid - 1
        else: # caso contrário, atualiza o índice do inicio
            start = mid + 1
    
    return -1 # Não encontrou? Retorna -1

numbers = [2, 3, 4, 10, 40]
target = 40

result = binary_search(numbers, target)
print(f"Elemento encontrado na posição: {result}")
```

## Complexidade

O algoritmo `linear_search`, no pior caso _(se o elemento estiver na última posição ou não existir)_, precisará percorrer toda a estrutura para encontrar o elemento. Diante disso, sua complexidade é `O(n)`. No entanto, o algoritmo de `linear_search` não necessita que a coleção esteja ordenada.

No caso do algoritmo `binary_search`, no pior caso ele precisará de `O(log n)` operações para encontrar o elemento _(também ocorre caso o elemento não exista)_.

Vamos ver uma comparação entre as buscas? Suponha que cada operação computacional execute em um tempo de 1ms.

![Uma breve comparação entre os dois. Retirado do livro “Entendendo Algoritmos”](https://content-assets.betrybe.com/prod/2f5a0c29-5add-4223-aec7-cd0893753cda-Uma%20breve%20compara%C3%A7%C3%A3o%20entre%20os%20dois.%20Retirado%20do%20livro%20%E2%80%9CEntendendo%20Algoritmos%E2%80%9D.png)
Uma breve comparação entre os dois. Retirado do livro “Entendendo Algoritmos”


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/bebc59d2-de53-4b14-98f8-ccdbc58e8b9f/lesson/978512d4-3ab7-4141-a399-6219c1659883)
