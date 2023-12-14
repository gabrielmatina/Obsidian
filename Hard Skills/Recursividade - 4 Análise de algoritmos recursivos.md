[[Recursividade]]

Para que consigamos realizar a análise de algoritmos recursivos, iremos fazer o uso da **Árvore de Recorrência**!

> Você pode estar se perguntando: “Mas o que é isso?!”🤔

![algoritmos_meme](https://content-assets.betrybe.com/prod/algoritmos_meme.jpeg)

## Árvore de Recursão

O método da árvore de recursão pode ser utilizado para **estimar** o custo de um algoritmo. É um modo de analisarmos seu custo, o que nos ajuda a decidir se tal solução recursiva vale a pena ou não.

Podemos visualizar nível a nível da estrutura de um algoritmo recursivo por meio de uma árvore recursiva. No final, tem-se a estimativa de tempo do problema.

Vamos ver na prática como isso acontece:

```python
def fibonacci(num):  # nome da função e parâmetro
    if (num <= 1):  # condição de parada
        return num
    else:
        return fibonacci(num - 2) + fibonacci(num - 1)  # chamada de si mesma com um novo valor
```

No exemplo acima, estamos fazendo um código recursivo para cálculo de Fibonacci.

Na imagem abaixo, visualizamos a representação do algoritmo Fibonacci recursivo, que acabamos de montar, convertido em uma estrutura que chamamos de árvore:

![Fluxograma Fibonacci Recursivo](https://content-assets.betrybe.com/prod/Fluxograma%20Fibonacci%20Recursivo.png)
Fluxograma Fibonacci Recursivo

Cada nó da árvore acima representa o custo da solução de um subproblema. Quando olhamos para a árvore como um todo, ou seja, quando expandimos ela, podemos somar todos os custos de cada nível dessa árvore e então teríamos o resultado total do problema.

O problema começa com apenas um nó e vai decompondo até alcançar os casos base, que são as “folhas” da árvore. Folhas são, basicamente, nós da estrutura que não possuem nenhum nó abaixo deles.

Vamos ver agora, de forma interativa, como essa árvore chegou a esse resultado passo a passo:

|![gif Arvore recursao fibonacci](https://content-assets.betrybe.com/prod/gif%20Arvore%20recursao%20fibonacci.gif)|
|---|
|Exemplo retirado do site: www.visualgo.net/en/recursion|

⚠️ Aviso: Caso você se confunda com as estratégias de análise de recursão, tenha tranquilidade! Isso é um assunto mais desafiador e com o tempo e experiência esse conhecimento será consolidado.

Se algum exemplo estiver te confundindo,fale com a gente no Slack. 😁

De olho na dica👀: desenhe todas as recursões do problema até chegar aos casos base e some as complexidades! Fique de olho nas proporções! Se cada subproblema é `O(n)` e você criou um para cada elemento da sua entrada de tamanho `n`, você tem aí uma complexidade `O(n * n)`, ou seja, uma complexidade quadrática. Se, por outro lado, **a cada subproblema você dividiu o tamanho do problema original por dois, você gerará `log n` subproblemas.** Se cada um desses custa `O(n)`, você teria uma complexidade `O(n* log n)`

> A forma de traduzir a lógica da árvore de recursão para uma notação puramente matemática se chama **Teorema Mestre**! Não é o nosso escopo por agora, ok? Mas fique à vontade para pesquisar a respeito e nos trazer todas as perguntas, se quiser!

# Principais cuidados ao usar recursão

Como visto, chamadas de funções ocupam memória já que, toda vez que uma chamada é feita, o SO reserva memória para as variáveis e parâmetros.

Quando um loop recursivo é muito grande, ele fará muitas chamadas, e quando ele faz muitas chamadas podemos ter um `stack overflow` (que não é apenas o fórum de ajuda para devs 😂). O stack overflow, ou estouro de pilha em português, significa que ficaríamos sem memória para continuar com a execução do programa.

Para evitar um estouro de pilha, é importante que as chamadas recursivas parem. Para que consigamos fazer as chamadas recursivas pararem é importante lembrarmos sempre de **implementar a condição de parada na função**.

![cuidado_meme](https://content-assets.betrybe.com/prod/cuidado_meme.jpeg)

Apesar de funções recursivas serem mais harmoniosas e fáceis de implementar, elas costumam ser menos eficientes que do que as iterativas, por causa do _overhead_ de empilhar e desempilhar chamadas de funções.

De olho na dica👀: Não é tão simples decidir quando usar uma solução recursiva para um problema, mas você vai perceber que alguns problemas são muito mais fáceis e intuitivos de serem resolvidos recursivamente. É nesses casos que a recursão vale a pena.

# Estratégias para solução de problemas

Nos deparamos com diversos problemas diferentes no nosso dia a dia e na nossa vida. Na nossa carreira como pessoa desenvolvedora não será diferente!

![problema_meme](https://content-assets.betrybe.com/prod/96ed1f5d-187b-41b4-8e09-36ca5378807e-problema_meme.png)

> Como diria Brad Miller e David Ranum no livro `Resolução de Problemas com Algoritmos e Estruturas de Dados usando Python`, escrito por ambos, e traduzido pela USP: “a ciência da computação é muitas vezes difícil de definir. Isto é provavelmente devido ao infeliz uso da palavra ‘computador’ no nome. (…) **A ciência da computação é o estudo de problemas, resolução de problemas e soluções que surgem do processo de resolução de problemas.**“

Bora ver os tipos de estratégias:

## Iterativa

A solução iterativa é caracterizada pela repetição de uma determinada operação, procurando resolver algum problema encontrando sucessivas aproximações, a partir de uma suposição inicial.

A ideia nesse tipo de processo é repetir um determinado cálculo várias vezes, obtendo-se a cada repetição, ou iteração, um resultado mais preciso que aquele obtido na iteração anterior.

A cada iteração, utiliza-se o resultado da anterior como parâmetro de entrada para o cálculo seguinte. O resultado é uma sequência de valores aproximados, não exatos, mas que estão dentro de uma faixa de erro aceitável.

## Força bruta

A força bruta, também conhecida como _tentativa e erro_ ou _busca exaustiva_, é a estratégia mais trivial e intuitiva para solução de problemas. Ela consiste basicamente em enumerar todas as combinações possíveis para uma solução e avaliar se satisfazem o problema. Dessa forma, é possível escolher a melhor das soluções,mas apesar de trivial, em alguns casos, a força bruta possui desempenho geralmente ruim.

Vamos exemplificar essa estratégia, solucionando um problema chamado de `problema da mochila`:

Dada uma mochila com capacidade _C_, e _n_ objetos com peso (i = 1…n), deve ser possível preencher a mochila com o maior peso total, respeitando a capacidade _C_.

Suponha uma mochila com capacidade de 15kg e objetos de peso 12kg, 2kg, 4kg e 8kg.

Este problema possui mais que uma solução ótima, ou seja, possui soluções ótimas equivalentes:

```bash
Uma solução ótima: 12kg + 2kg = 14kg;

Outra solução ótima: 8kg + 2kg + 4kg = 14kg.
```

Soluções viáveis seriam, entre outras:

```bash
- 12kg;

- 2kg;

- 4kg;

- 8kg;

- 2kg + 4kg.

# ...
```

No caso acima, as soluções são viáveis, porém não são ótimas. Elas não são ótimas, pois a mochila está sendo preenchida, mas não está chegando mais próximo possível ao peso máximo. Por exemplo, uma das soluções que temos acima é preencher a mochila com um objeto de 2kg apenas, sendo que a mochila suporta 15kg.

Observe mais uma possível solução:


```bash
- 12 kg + 4 kg = 16kg.
```

O caso acima é inviável, pois o peso máximo que a mochila comporta é 15kg. Com isso, podemos concluir que o exemplo acima é inviável, pois ultrapassa os 15kg.

Um método baseado em tentativa e erro testaria todas as combinações entre elementos checando:

- Se a solução é viável;  
- Se a solução possui valor melhor que outra encontrada anteriormente.

Para conseguir definir qual seria a melhor solução, todas devem ser enumeradas e registradas, e, ao final, os caminhos que não chegaram a um solução final, devem ser retirados.

## Dividir e conquistar

A estratégia **dividir e conquistar**, também chamada de divisão e conquista, consiste em dividir o problema em partes menores, encontrar soluções para as partes, e só então combinar as soluções obtidas em uma solução global.

Usar essa estratégia para resolver problemas, nos quais os subproblemas são versões menores do problema original, geralmente leva à soluções eficientes e harmoniosas, especialmente quando é utilizado a recursividade.

A estratégia emprega modularização de programas e frequentemente conduz a um algoritmo simples e eficiente. Esta técnica é bastante utilizada em desenvolvimento de algoritmos paralelos, onde os subproblemas são tipicamente independentes um dos outros, podendo assim serem resolvidos separadamente.

Anota aí 🖊: A modularização de um programa consiste em dividi-lo em partes funcionais que conversam entre si, tornando o software mais eficiente.

A técnica de _Divisão e Conquista_ consistem em três passos:

- **Divisão**: dividir a instância do problema original em duas ou mais instâncias menores, considerando-as como subproblemas;
- **Conquista**: resolver cada subproblema recursivamente;
- **Combinação**: combinar as soluções encontradas em cada subproblema, compondo uma solução para o problema original.

Um exemplo para ilustrar o uso dessa técnica é o algoritmo de ordenação de um vetor por intercalação, ou, como é chamado, _MergeSort_. Sua representação pode ser feita por meio de uma árvore binária, conforme a imagem abaixo:

![Imagem dividir e conquistar](https://content-assets.betrybe.com/prod/96ed1f5d-187b-41b4-8e09-36ca5378807e-Imagem%20dividir%20e%20conquistar.png)
Imagem retirada do site: www.pucrs.br/ciencias/viali/graduacao/po_2/literatura/pdinamica/artigos/Vol8_Artigo1.pdf

Observe na imagem acima que o primeiro (`a`) faz a divisão dos elementos, e o segundo (`b`) de baixo para cima, faz a conquista, ou seja, resolve cada parte do problema e depois combina todas as soluções encontradas.

“Agora que já aprendemos sobre Recursividade e Estratégias para solução de problemas, borá para prática! 🚀

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/bfc198bb-2ceb-4271-b137-2388dddbeee2/lesson/4ae9d74d-4c23-41fb-b5f6-1f0863a63280)

