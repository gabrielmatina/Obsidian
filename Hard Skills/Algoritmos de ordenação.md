Os Algoritmos de ordenação (_sorting algorithms_) fazem parte de uma categoria de algoritmos que buscam colocar elementos de uma sequência em uma determinada ordem definida. Esta ordem pode ser numérica, lexicográfica ou por qualquer outra característica. As razões para se ordenar uma sequência podem variar, desde facilitar a visualização até facilitar uma busca.

**Vamos para um exemplo!**

Imagine uma coleção de músicas. Pense que queremos exibi-las em ordem alfabética ou ordenadas pelo número de vezes em que foram tocadas. 🎵 Agora imagine uma lista telefônica na qual precisamos buscar um nome. ☎️

_Nos dois casos, não seria mais fácil se ordenássemos em ordem alfabética?_ Lidando com listas, arrays, matrizes e outras coleções, numéricas ou não, muitas vezes teremos de utilizar a ordenação.

Nesse caso, precisamos fazer a implementação e análise de alguns dos algoritmos existentes (os mais populares). Existem vários outros e basta uma busca por algoritmos de ordenação (_sorting algorithms_) para obter uma lista extensa de algoritmos deste estilo!

# Algoritmos que usam força bruta

Como já vimos anteriormente, a **força bruta** caracteriza-se por ser uma técnica que testa cada possibilidade existente, uma a uma, até resolver um problema.

**Por exemplo:** imagine que você tem um cadeado com 4 dígitos, cada um de 0-9. Você esqueceu sua combinação, mas não quer comprar outro cadeado. Como você não consegue se lembrar de nenhum dos dígitos, é necessário usar um método de força bruta para abrir a fechadura. Portanto, você define todos os números de volta para 0 e os tenta um por um: `0001`, `0002`, `0003` e assim por diante até que seja aberto. Na pior das hipóteses, seriam necessárias **10⁴** ou 10.000 tentativas para encontrar sua combinação.

## Selection Sort

Vamos entender qual é a ideia do Selection Sort?

A ideia do Selection Sort é bem intuitiva:

1️⃣ Encontre o menor elemento da lista (nesse caso, a ideia é ordenar em ordem alfabética); 2️⃣ Adicione o elemento encontrado em uma outra lista; 3️⃣ Repita o processo para todos os elementos restantes.

Achou intuitivo? 😉

Agora, vamos para um exemplo computacional simples! A nossa tarefa aqui será ordenar a lista abaixo:

- Primeiro, encontraremos o menor elemento da lista:

![Lista que precisamos ordenar e o menor elemento da mesma](https://content-assets.betrybe.com/prod/Lista%20que%20precisamos%20ordenar%20e%20o%20menor%20elemento%20da%20mesma.png)Lista que precisamos ordenar e o menor elemento da mesma.

- Neste caso, não criaremos outra lista para adicionar o elemento a ela. Simplesmente faremos a troca do menor elemento para a posição em que ele deve estar:

![O menor elemento da lista inteira ficará na primeira posição](https://content-assets.betrybe.com/prod/O%20menor%20elemento%20da%20lista%20inteira%20ficar%C3%A1%20na%20primeira%20posi%C3%A7%C3%A3o.png)|
O menor elemento da lista inteira ficará na primeira posição.

- Agora que o primeiro elemento está ordenado, repetiremos o processo para ordenar os próximos elementos!

👀**De olho na dica:** se chamarmos de N a quantidade de elementos da lista, podemos observar que precisamos ordenar os primeiros N-1 elementos. Nesse caso, o último elemento também estará na ordem correta!

![Restante da iteração do algoritmo](https://content-assets.betrybe.com/prod/Restante%20da%20itera%C3%A7%C3%A3o%20do%20algoritmo.png)
Restante da iteração do algoritmo.

- O gif abaixo pode ajudar ainda mais no entendimento:

![Ordenação por Seleção Selection Sort](https://content-assets.betrybe.com/prod/Ordena%C3%A7%C3%A3o%20por%20Sele%C3%A7%C3%A3o%20Selection%20Sort.gif)Ordenação por Seleção (Selection Sort)

**Vamos ver um exemplo de implementação:**

```python
def selection_sort(numbers):
    n = len(numbers) # Quantidade de elementos da lista

    for index in range(n - 1): # Precisamos ordenar N-1 elementos
        min_element_index = index # Definimos a variável para buscar o menor elemento

        for search_index in range(index + 1, n): # Início da busca pelo menor elemento
            if numbers[search_index] < numbers[min_element_index]:
                min_element_index = search_index # Atualiza o índice atual do menor elemento

        # Troca os elementos de posição
        current_element = numbers[index]
        numbers[index] = numbers[min_element_index]
        numbers[min_element_index] = current_element

    return numbers

numbers = [7, 5, 9, 2, 6, 8]
print(f"Lista inicial: {numbers}")
ordered_numbers = selection_sort(numbers)
print(f"Lista final: {ordered_numbers}")
```

**Outra maneira de implementar o Selection Sort (com mais recursos Pythônicos):**

```python
def search(numbers, start, end):
    min_element = numbers[start]
    min_element_index = start

    for i in range(start + 1, end): # Busca pelo menor elemento
        if numbers[i] < min_element:
            min_element = numbers[i]
            min_element_index = i

    return min_element_index # Retorna a posição do menor elemento

def selection_sort(numbers):
    n = len(numbers)

    for index in range(n - 1): # Início da iteração para ordenar os N-1 elementos
        min_element_index = search(numbers, index, n)
        numbers[index], numbers[min_element_index] = numbers[min_element_index], numbers[index] # Trocando os elementos utilizando desempacotamento.

    return numbers
```

Analisando a complexidade deste algoritmo, vemos que independente de todos os elementos estarem ordenados (ou não), ou parcialmente ordenados, sempre teremos que percorrer o array completamente e também `n - 1` elementos a cada iteração. Isto nos leva a uma complexidade `O(n²)` para todos os casos (pior, médio, melhor).

Como criamos apenas algumas variáveis de controle e não criamos uma lista auxiliar, nosso algoritmo tem uma complexidade de espaço constante, ou seja, não muda, seja para 10, 1000 ou 10.000 elementos.

## Insertion Sort

**A ordenação por inserção (_insertion sort_) tem esse nome por inserir um elemento de cada vez em sua posição correta.** Fazendo uma analogia a um jogo de cartas, onde sua “mão” esteja ordenada, é como se a cada nova carta recebida fossemos movendo ela até achar a posição correta e a inserimos ali, e faremos isso sucessivamente até que não tenha novas cartas e por consequência, nossa mão esteja ordenada. **A ordenação por inserção é mais eficiente que a ordenação por seleção e também considerada mais simples.**

_Vamos entender como podemos traduzir essa ideia para um algoritmo?_

Basicamente, o algoritmo reorganiza os elementos da lista a cada iteração.

- Utilizaremos a mesma lista do problema anterior para aplicarmos o Insertion Sort:

![Lista para ordenação](https://content-assets.betrybe.com/prod/Lista%20para%20ordena%C3%A7%C3%A3o.png)

Lista para ordenação

- Como queremos começar a ordenação a partir da primeira posição, pegaremos o elemento imediatamente à frente e o chamaremos de **chave**:

![Temos a chave e a posição que queremos deixar ordenada](https://content-assets.betrybe.com/prod/Temos%20a%20chave%20e%20a%20posi%C3%A7%C3%A3o%20que%20queremos%20deixar%20ordenada.png)

Temos a chave e a posição que queremos deixar ordenada

- Agora, vamos comparar essa chave com os elementos que estão na posição anterior e, se a chave for menor, copiaremos o elemento que estamos olhando para a sua frente:

![Elemento 7 remanejado para a posição a frente](https://content-assets.betrybe.com/prod/Elemento%207%20remanejado%20para%20a%20posi%C3%A7%C3%A3o%20a%20frente.png)
|---|
|Elemento 7 remanejado para a posição à frente

- Como não há outros elementos para comparar, paramos a iteração e colocaremos a chave na posição em que a iteração parou:

|![Primeiros elementos ordenados entre si!](https://content-assets.betrybe.com/prod/Primeiros%20elementos%20ordenados%20entre%20si!.png)Primeiros elementos ordenados entre si!

- Repetimos esses passos até que não existam mais elementos para ordenar:

![Restante da execução do Insertion Sort](https://content-assets.betrybe.com/prod/Restante%20da%20execu%C3%A7%C3%A3o%20do%20Insertion%20Sort.png)Restante da execução do Insertion Sort

- O gif abaixo pode ajudar ainda mais no entendimento:

![Ordenação por Inserção Insertion Sort](https://content-assets.betrybe.com/prod/Ordena%C3%A7%C3%A3o%20por%20Inser%C3%A7%C3%A3o%20Insertion%20Sort.gif)|
Ordenação por Inserção (Insertion Sort)

**Vamos ver um exemplo de implementação:**

```python
def insertion_sort(numbers):
    n = len(numbers) # Quantidade de elementos na lista

    for index in range(1, n): # Começaremos a ordenar pelo segundo elemento
        key = numbers[index] # Pegamos o segundo elemento e o definimos como chave

        new_position = index - 1 # Tomamos a posição anterior para iniciar a comparação
        while new_position >= 0 and numbers[new_position] > key: # Enquanto a chave for menor, remaneja o elemento para frente
            numbers[new_position + 1] = numbers[new_position] # Remaneja o elemento
            new_position = new_position - 1

        numbers[new_position + 1] = key # Insere a chave na posição correta

    return numbers

numbers = [7, 5, 9, 2, 6, 8]
print(insertion_sort(numbers))
```

Como precisamos percorrer cada um dos elementos e depois percorrer comparando os elementos à esquerda do mesmo, em um pior caso, onde a lista esteja inversamente ordenada, teremos uma complexidade de `O(n²)`. Isto se repete também em média, para listas parcialmente ordenadas. Porém, se inicialmente a lista estiver ordenada, este algoritmo terá complexidade `O(n)`, pois só fará a iteração de todos os elementos, e não precisará ficar remanejando os elementos.

Assim como na ordenação por seleção, como criamos apenas algumas variáveis de controle e não criamos uma lista auxiliar, nosso algoritmo tem uma complexidade de espaço constante, ou seja, não muda, seja para 10, 1000 ou 10.000 elementos.

# Algoritmos que usam soluções iterativas

**Soluções iterativas consistem na realização de uma ou mais operações repetidas vezes, por meios de comandos de repetição.** As ordenações demonstradas acima (seleção, inserção), são consideradas iterativas, pois estamos realizando operações de comparação e troca de elementos repetidas vezes por meios de comandos de repetição (`for`).

> 👀**De olho na dica:** toda solução iterativa pode ser reescrita de forma recursiva.

## Bubble Sort

Também conhecido como **ordenação por bolha (_bubble sort_), têm esse nome, pois a movimentação dos elementos lembra o movimento de bolhas flutuando.** Nesse caso, são realizadas múltiplas iterações sobre a coleção, sempre comparando o valor ao item adjacente e realizando a troca daqueles que estão fora de ordem. A cada iteração o próximo maior valor é colocado em sua posição correta, ou seja, cada item se desloca como uma **bolha** para a posição a qual pertence.

![Bubble Sort: flutuacao dos elementos](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Bubble%20Sort:%20flutuacao%20dos%20elementos.png)|
Bubble Sort: Flutuação dos elementos.

_Vamos entender melhor usando a lista que já conhecemos?_

Trazendo a ideia do método **Bubble Sort** para algoritmo, podemos imaginar que **os maiores elementos da lista serão jogados para as últimas posições** como se eles `flutuassem`.

1️⃣ O primeiro passo é começar a iteração com os dois primeiros elementos, trocando-os caso o maior esteja em uma posição anterior:

![Inicio da iteracao do algoritmo](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Inicio%20da%20iteracao%20do%20algoritmo.png)Início da iteração do algoritmo.

2️⃣ Continuaremos essa iteração até o maior elemento flutuar para a última posição:

![A cada passo o maior elemento e direcionado para a ultima posição da lista](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-A%20cada%20passo%20o%20maior%20elemento%20e%20direcionado%20para%20a%20ultima%20posi%C3%A7%C3%A3o%20da%20lista.png)
A cada passo, o maior elemento é direcionado para a última posição da lista.

3️⃣ O último elemento foi levado até a posição correta e, portanto, está ordenado:

![Elemento nove ordenado](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Elemento%20nove%20ordenado.png)
Elemento 9 ordenado.

4️⃣ Como esse elemento está ordenado, é necessário ordenar somente os elementos que estão antes dele. Diante disso, agora o segundo maior elemento deve ser flutuado:

![Iteracao para flutuar o segundo maior elemento para sua posicao correta](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Iteracao%20para%20flutuar%20o%20segundo%20maior%20elemento%20para%20sua%20posicao%20correta.png)|
|---|
|Iteração para flutuar o segundo maior elemento para sua posição correta.|

5️⃣ Essa sequência de iterações deverá ocorrer `n - 1` vezes, onde `n` é a quantidade de elementos da lista. Ordenando `n - 1` elementos, por consequência, o último também estará ordenado.

**Vamos ver um exemplo de implementação?**


```python
def bubble_sort(numbers):
    n = len(numbers) # Quantidade de elementos na lista

    for ordered_elements in range(n - 1): # Precisamos ordenar n-1 elementos
        for item in range(0, n - 1 - ordered_elements): # Vamos percorrer até o elemento anterior ao ordenado
            if numbers[item] > numbers[item + 1]: # se um elemento for maior, flutuamos ele para cima
                current_element = numbers[item]
                numbers[item] = numbers[item + 1]
                numbers[item + 1] = current_element
                
                # Lembra da troca com desempacotamento?
                # numbers[item], numbers[item + 1] = numbers[item + 1], numbers[item]
    return numbers

numbers = [7, 5, 9, 2, 6, 8]
print(bubble_sort(numbers))
```

Assim como nos algoritmos de força bruta, no pior caso do Bubble Sort ele executará `O(n²)` operações, então, ele também é um algoritmo de ordem quadrática. Além disso, é possível observar nas imagens a quantidade de passos para ordenarmos 1 elemento.

# Algoritmos que usam soluções iterativas

**Soluções iterativas consistem na realização de uma ou mais operações repetidas vezes, por meios de comandos de repetição.** As ordenações demonstradas acima (seleção, inserção), são consideradas iterativas, pois estamos realizando operações de comparação e troca de elementos repetidas vezes por meios de comandos de repetição (`for`).

> 👀**De olho na dica:** toda solução iterativa pode ser reescrita de forma recursiva.

## Bubble Sort

Também conhecido como **ordenação por bolha (_bubble sort_), têm esse nome, pois a movimentação dos elementos lembra o movimento de bolhas flutuando.** Nesse caso, são realizadas múltiplas iterações sobre a coleção, sempre comparando o valor ao item adjacente e realizando a troca daqueles que estão fora de ordem. A cada iteração o próximo maior valor é colocado em sua posição correta, ou seja, cada item se desloca como uma **bolha** para a posição a qual pertence.

![Bubble Sort: flutuacao dos elementos](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Bubble%20Sort:%20flutuacao%20dos%20elementos.png)|
Bubble Sort: Flutuação dos elementos.

_Vamos entender melhor usando a lista que já conhecemos?_

Trazendo a ideia do método **Bubble Sort** para algoritmo, podemos imaginar que **os maiores elementos da lista serão jogados para as últimas posições** como se eles `flutuassem`.

1️⃣ O primeiro passo é começar a iteração com os dois primeiros elementos, trocando-os caso o maior esteja em uma posição anterior:

![Inicio da iteracao do algoritmo](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Inicio%20da%20iteracao%20do%20algoritmo.png)
Início da iteração do algoritmo.

2️⃣ Continuaremos essa iteração até o maior elemento flutuar para a última posição:

![A cada passo o maior elemento e direcionado para a ultima posição da lista](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-A%20cada%20passo%20o%20maior%20elemento%20e%20direcionado%20para%20a%20ultima%20posi%C3%A7%C3%A3o%20da%20lista.png)
A cada passo, o maior elemento é direcionado para a última posição da lista.|

3️⃣ O último elemento foi levado até a posição correta e, portanto, está ordenado:

![Elemento nove ordenado](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Elemento%20nove%20ordenado.png)
Elemento 9 ordenado.

4️⃣ Como esse elemento está ordenado, é necessário ordenar somente os elementos que estão antes dele. Diante disso, agora o segundo maior elemento deve ser flutuado:

![Iteracao para flutuar o segundo maior elemento para sua posicao correta](https://content-assets.betrybe.com/prod/244f7d47-a8d5-49f6-83e4-3f641db13bb5-Iteracao%20para%20flutuar%20o%20segundo%20maior%20elemento%20para%20sua%20posicao%20correta.png)Iteração para flutuar o segundo maior elemento para sua posição correta.

5️⃣ Essa sequência de iterações deverá ocorrer `n - 1` vezes, onde `n` é a quantidade de elementos da lista. Ordenando `n - 1` elementos, por consequência, o último também estará ordenado.

**Vamos ver um exemplo de implementação?**

```python
def bubble_sort(numbers):
    n = len(numbers) # Quantidade de elementos na lista

    for ordered_elements in range(n - 1): # Precisamos ordenar n-1 elementos
        for item in range(0, n - 1 - ordered_elements): # Vamos percorrer até o elemento anterior ao ordenado
            if numbers[item] > numbers[item + 1]: # se um elemento for maior, flutuamos ele para cima
                 current_element = numbers[item]
                numbers[item] = numbers[item + 1]
                numbers[item + 1] = current_element
                
                # Lembra da troca com desempacotamento?
                # numbers[item], numbers[item + 1] = numbers[item + 1], numbers[item]
    return numbers

numbers = [7, 5, 9, 2, 6, 8]
print(bubble_sort(numbers))
```

Assim como nos algoritmos de força bruta, no pior caso do Bubble Sort ele executará `O(n²)` operações, então, ele também é um algoritmo de ordem quadrática. Além disso, é possível observar nas imagens a quantidade de passos para ordenarmos 1 elemento.


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/bebc59d2-de53-4b14-98f8-ccdbc58e8b9f/lesson/2e6abe81-c0be-474e-8f22-45e3d8acc309)


