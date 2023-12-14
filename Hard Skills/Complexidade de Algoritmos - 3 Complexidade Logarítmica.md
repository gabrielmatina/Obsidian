[[Python]]
[[Complexidade de Algoritmos]]

Agora, vamos entender o que é a Complexidade Logarítmica. Mas, antes disso, é preciso deixar nítido que, apesar do termo potencialmente assustador, a Complexidade Logarítmica não exige cálculos matemáticos complicados para ser entendida. 🙂

Representado pela notação `O(log n)`, um algoritmo **logarítmico** tem uma alteração na taxa de execução que, _geralmente_, reduz pela metade o tempo de finalização das iterações ao reduzir pela metade o tamanho do input a cada iteração.

Vamos refletir sobre isso:

> Suponha que temos um algoritmo cuja entrada `n` é igual a 4, se tivermos um algoritmo O(log n) a ser executado com essa entrada, teremos que fazer apenas 2 operações para executá-lo, pois _log₂n_ (lê-se: “log de n na base 2”) é igual a 2. Se a nossa entrada fosse o dobro, ou seja, 8 teríamos que realizar apenas 3 operações para chegar ao fim da execução. Ao dobrar o valor da entrada novamente, com `n` igual a 16, teríamos que realizar apenas 4 operações (_log₂n_ é igual a 4) e assim sucessivamente.

Anota aí 🖊: O número de operações para executar o algoritmo logarítmico tem uma relação inversa ao tamanho da entrada: quanto maior ela é, menor o número de operações e, consequentemente, menor o tempo para a execução do algoritmo!

> Você pode estar se perguntando: “Mas como é possível criar um algoritmo com essa Ordem de Complexidade?”🤔

No exemplo a seguir, temos um algoritmo de **busca binária** que entenderemos em detalhes mais adiante. Por ora, basta compreender que esse algoritmo representa uma complexidade `O(log n)`.

Suponha que vamos criar um algoritmo de lista telefônica. Temos uma lista de nomes de tamanho `n`, ordenada em ordem alfabética, e um nome `x`; devemos encontrar o número de telefone da pessoa passada na entrada.

![Relíquia de um passado remoto](https://content-assets.betrybe.com/prod/Rel%C3%ADquia%20de%20um%20passado%20remoto.png)|
Relíquia de um passado remoto.

Suponha que vamos procurar pelo nome `Tintim`. Como faremos isso?

- Buscar na página (ou posição) da lista que tenha nomes começando com a letra `T`;
- Escolher uma página aleatória da lista para abrir;
- Percebemos que caímos na posição da letra `M`;
- Como `M < T`, na ordem alfabética, então, devemos avançar algumas posições para encontrar o `T`;
- Então, escolhemos uma página que está mais adiante;
- Percebemos que caímos na posição da letra `V`;
- Como `V > T`, na ordem alfabética, então devemos voltar alguns posições;
- Vamos repetimos esse processo até encontrarmos o nome desejado.

Haveria outra forma de fazer essa pesquisa na lista telefônica? Sim! Nós poderíamos passar por cada página, da letra `A` até a letra `T` para encontrar `Tintim`. Porém, o número de operações necessárias para realizar isso seria muito maior do que aquele que usamos no exemplo acima!

Perceba o seguinte: o nosso alfabeto tem _26 letras_ e a letra `T` está na _posição 20_ dele. Se seguíssemos o algoritmo de **busca sequencial**, passando sequencialmente pelas letras de `A` à `T`, teríamos que realizar 20 operações para encontrar o que estávamos procurando. Mas, se usássemos o algoritmo de **busca binária**, que criamos acima, poderíamos resolver facilmente o problema de encontrar a letra `T` utilizando menos da metade das operações que uma busca sequencial demanda. Ou seja, poderíamos facilmente encontrar a letra `T` na lista telefônica com 10 ou menos passos, obtendo, assim, um algoritmo de complexidade `O(log n)` para resolver o problema.

Para entender melhor a diferença entre um algoritmo de **busca binária**, logarítmico, com um de **busca sequencial**, que é linear, observe o gif abaixo.

![binary-and-linear-search-animations](https://content-assets.betrybe.com/prod/binary-and-linear-search-animations.gif)

Agora que já passamos pelo conceito de Complexidade Logarítmica, vejamos o algoritmo de busca binária abaixo.

De olho na dica👀: é altamente recomendado que você rode na sua máquina para entender melhor como funciona):

```python
# A estrutura deve estar ordenada para que a busca binária funcione
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

Observe como, a cada iteração, o algoritmo de busca binária corta o problema pela metade:

- primeiro ele “corta” a lista em dois e pega o elemento do meio.
- Depois ele “caminha” para o lado no elemento que procura esta e “corta” este lado novamente pela metade.

Anota aí 🖊: Quando cortamos a entrada pela metade, a cada iteração, temos um padrão que segue a função matemática de logaritmo na base dois! Assim, nosso algoritmo é `O(log n)`.

> Um logaritmo em base 2 representa o número de vezes que um valor deve ser dividido pela metade para obter 1.

Dessa forma, sem precisarmos nos aprofundar na matemática, conseguimos calcular a ordem de complexidade de um algoritmo deste tipo: Quando a entrada é cortada pela metade a cada iteração temos um comportamento logarítmico!

Veja abaixo um gráfico que compara o tempo de execução de um algoritmo linear e um logarítmico.

![Um algoritmo logarítmico tem uma performance muito superior em relação a um linear!](https://content-assets.betrybe.com/prod/Um%20algoritmo%20logar%C3%ADtmico%20tem%20uma%20performance%20muito%20superior%20em%20rela%C3%A7%C3%A3o%20a%20um%20linear!.png)Um algoritmo logarítmico tem uma performance muito superior em relação a um linear!|

Agora, para fixar esse conceito, vamos fazer um exercício!

**Exercício 4:** Imagine que você recebe dois arrays de tamanho igual, `array1` e `array2`. Para cada elemento do `array1`, realize uma busca binária no `array2`. Mostre que a ordem de complexidade do algoritmo resultante é `O(n * log n)`, ou `O(n log n)`.

**Solução**

A busca binária tem complexidade O(log n), uma vez que reduz pela metade o número de elementos que deverá percorrer a cada nova iteração. Todavia, ela **só deve ser utilizada quando lidamos com arrays que já se encontram ordenados**, este é o caso do nosso array2. Uma vez que a busca binária precisará ser executada **n-vezes** para cada elemento em array1, partimos da operação `O(n) * O(log n)`, que resulta em `O(n log n)`. Uma possível representação abstrata do problema é a seguinte:

```python
def do_something(array1, array2):
    for number in array1: # O (n)
        binary_search(array2, number) # O (log n)
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/cb6a6831-db64-41b7-9197-6656dc970b75/lesson/38fde804-d1bf-4890-b217-d8811eb44a8d)
