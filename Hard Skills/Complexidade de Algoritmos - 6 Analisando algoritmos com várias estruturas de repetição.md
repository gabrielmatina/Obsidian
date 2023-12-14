[[Complexidade de Algoritmos]]

Agora que já sabemos analisar a Ordem de Complexidade, vamos para alguns algoritmos.

Observe o algoritmo o abaixo:

```python
def calculations(n):
    number1 = 0
    for n1 in range(n):
        number1 += n1

    number2 = 0
    for n1 in range(n):
       for n2 in range(n):
            number2 += n1 + n2

    number3 = 0
    for n1 in range(n):
       for n2 in range(n):
           for n3 in range(n):
               number3 += n1 + n2 + n3

    return number1, number2, number3

n1, n2, n3 = calculations(100)
print(f'{n1}, {n2}, {n3}')
```

Esse algoritmo tem três estruturas de repetição evidentes: uma linear, uma quadrática e uma cúbica.

> Qual é a Ordem de Complexidade dele? 🤔

Resposta: A rigor, ela seria `O(n + n² + n³)`.

De olho na dica👀: Se os loops estão aninhados você os multiplica, e se estão paralelos você os soma.

Podemos pensar em alguns outros exemplos:

- Um algoritmo de busca binária que roda três vezes teria `O(3 * log n)` de complexidade;
- Um algoritmo que roda uma busca binária num array de tamanho `n` para cada elemento de um array de tamanho `m` teria `O(m * log n)` de complexidade.

No entanto, geralmente simplificam-se essas notações. Estamos vendo, ao longo dos nossos estudos, que ordens de complexidade diferentes, para entradas grandes, têm valores absurdamente diferentes.

Imagine escrever `O(n! + log(n))`. Ora, para uma entrada de tamanho `8` esse número seria `O(40320 + 3)`. Observe como o componente fatorial da equação, `n! = 40320`, domina completamente a ordem de complexidade. Nesse cenário, dizemos que a complexidade menor é **desprezível** e, então, a omitimos.

Anota aí 🖊: Para valores grandes, dizer a maior ordem de complexidade do conjunto já basta para uma boa análise. Sendo assim, ao analisar várias estruturas de repetição em paralelo, responda somente com o valor da estrutura que tiver maior ordem de complexidade na hora de fazer a sua análise.

![Quando a entrada vai ficando grande, veja como cresce a diferença de desempenho das diferentes ordens de complexidade](https://content-assets.betrybe.com/prod/Quando%20a%20entrada%20vai%20ficando%20grande,%20veja%20como%20cresce%20a%20diferen%C3%A7a%20de%20desempenho%20das%20diferentes%20ordens%20de%20complexidade.png)
Quando a entrada vai ficando grande, veja como cresce a diferença de desempenho das diferentes ordens de complexidade


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/cb6a6831-db64-41b7-9197-6656dc970b75/lesson/1781b257-e43a-43e4-8215-d64bf7198609)

