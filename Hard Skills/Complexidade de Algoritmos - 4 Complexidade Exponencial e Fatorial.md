[[Python]]
[[Complexidade de Algoritmos]]


Essas complexidades caracterizam algoritmos que, para aumentos pequenos no tamanho da entrada, aumentam enormemente o número de operações a serem realizadas para serem executados e, consequentemente, seu tempo de execução. A relação do tempo de execução/espaço ocupado em cada caso é a seguinte:

- **Exponencial:** `2ⁿ` (`O(2ⁿ)`);
- **Fatorial:** `n!` (`O(n!)`).

No caso de um algoritmo com Ordem de Complexidade Exponencial, para uma entrada de dados `n` que possui vinte elementos, o algoritmo realizará aproximadamente _um milhão_ (ou 2²⁰) de operações. Para o caso fatorial, os mesmos vinte elementos rendem 24 **quatrilhões** de operações! (O número exato é: 2432902008176640000 operações 😨).

> Você pode estar se perguntando: “Mas por que alguém iria escrever um algoritmo de ordem de complexidade fatorial?!”🤔

Resposta: porque não há outro algoritmo conhecido que resolve o problema. Pode parecer estranho, mas há problemas para os quais ainda não possuímos uma solução otimizada.

Um exemplo clássico é o problema do **Caixeiro-Viajante**! Veja logo abaixo:

> _“Dada uma lista de cidades e a distância entre cada par de cidades, qual é a rota mais curta possível que visita todas as cidades exatamente uma vez e volta para a cidade de origem?”_

A única solução exata conhecida para este problema é a `força bruta`: testamos todas as possibilidades e escolher a menor rota.

Agora, imagine que o caixeiro-viajante tenha que passar por três cidades: Belo Horizonte, São Paulo e Florianópolis. Ele tem as seguintes rotas possíveis:

Copiar

```bash
- Belo Horizonte > São Paulo > Florianópolis

- Belo Horizonte > Florianópolis > São Paulo

- Florianópolis > Belo Horizonte > São Paulo

- Florianópolis > São Paulo > Belo Horizonte

- São Paulo > Belo Horizonte > Florianópolis

- São Paulo > Florianópolis > Belo Horizonte
```

O número de rotas para 3 cidades é `3! == 3 * 2 * 1 = 6`. Atualmente, o Brasil tem `5570` municípios. Se nosso caixeiro-viajante tivesse que passar por todos os municípios, isso daria a ele `5570 * 5569 * 5568 * ... *` rotas.

> Quantos milhares de anos um computador precisaria para rodar esse algoritmo nesse caso?!😱

Algoritmos que não têm solução conhecida em tempo polinomial, ou seja, cuja Ordem de Complexidade é _fatorial_ ou _exponencial_ e são possivelmente resolvidos somente com força bruta, pertencem a uma categoria de problemas na computação chamada problemas **NP Completos**.

De olho na dica👀: Se quiser conhecer mais sobre essa categoria de problemas, explore nossos recursos adicionais!

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/cb6a6831-db64-41b7-9197-6656dc970b75/lesson/2bd61b63-c579-430d-ac02-e120814e32d1)
