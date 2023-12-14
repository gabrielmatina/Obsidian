[[Recursividade]]

Antes de falarmos sobre a `pilha de chamadas`, precisamos entender um pouco a estrutura de dados chamada **Pilha.** Para isso, vamos a uma breve introdução sobre seu conceito.

⚠️ Aviso: Não se preocupe em entender, nesse momento, 100% dos conceitos de pilha. Você irá vê-lo mais para frente. O importante aqui é que você entenda a parte conceitual do funcionamento da estrutura para que possamos progredir no assunto de recursividade!

Imagine um conjunto de pratos que estão um sobre o outro, ou seja, empilhados. Quando queremos pegar um prato, vamos pegar o do **topo** da pilha, certo? Em outro momento, se você quiser adicionar outro prato àquela pilha, você também irá adicioná-lo ao **topo** da pilha.

Logo, podemos observar que uma pilha tem duas ações triviais:

- Inserir (Push)
- Retirar (Pop)

E todas essas ações são efetuadas no **topo** da pilha.

![Push e pop na Pilha](https://content-assets.betrybe.com/prod/f3ed319f-e20d-4f6a-8b5e-c0ff3788ac0a-Push%20e%20pop%20na%20Pilha.png)Push e pop na Pilha.

Pois bem, a nossa pilha de chamadas funciona da mesma forma! Vamos conhecê-la?

A pilha de chamadas, também conhecida como **call stack**, organiza as sub-rotinas que estão executando no computador. Trazendo para o nosso contexto de recursividade, a pilha de chamadas registra a execução das funções, ou seja, qual está sendo executada, em que ponto ela deve retornar, qual é a proxima a ser chamada, etc.

Resumindo, é como se a pilha de chamadas operasse com contextos. Exemplo:

Uma função `A` está sendo executada e durante a sua execução ela precisa do resultado do processamento de outra função `B` a qual ela chama. Diante disso, a função `A` ficará aguardando na pilha de chamadas enquanto a função `B` executa. Quando `B` terminar, seu resultado voltará para `A`que vai continuar sua execução.

Anota aí 🖊:

- Toda vez que chamamos uma função em um programa, o sistema operacional reserva memória para as variáveis e parâmetros da função;
- Sempre quando uma função é executada, ela é guardada na pilha;
- Quando a função termina de ser executada, ela sai da pilha.

Vamos utilizar esse código com recursão e visualizar o funcionamento da pilha de chamadas:


```python
def saudacao():
    print("Oi")

def despedida():
    print("Tchau")

def init():
    print("Inicio")
    saudacao()
    print("Fim")
    despedida()

init()
```

|![Gif com o passo a passo da execução](https://content-assets.betrybe.com/prod/f3ed319f-e20d-4f6a-8b5e-c0ff3788ac0a-Gif%20com%20o%20passo%20a%20passo%20da%20execu%C3%A7%C3%A3o.gif)
Gif com o passo a passo da execução

Bacana, né? 😄

Vamos ver como é a execução da nossa função `countdown`?

|![Visualização do código no VSCODE com a extensão Python Preview](https://content-assets.betrybe.com/prod/f3ed319f-e20d-4f6a-8b5e-c0ff3788ac0a-Visualiza%C3%A7%C3%A3o%20do%20c%C3%B3digo%20no%20VSCODE%20com%20a%20extens%C3%A3o%20Python%20Preview.gif)
Visualização do código no VSCODE com a extensão Python Preview.

Note que a cada vez que a função countdown é chamada, um novo dado é adicionado à uma pilha (canto direito do gif). É adicionado à pilha todos os valores executados, do 5 ao 1, até chegarmos no caso base 0. Quando a execução acaba, os dados são retirados da pilha, um a um, de forma reversa (do 0 ao 5), até que a pilha esvazie e o processamento finalize.

Vamos ver outro exemplo? Que tal o nosso `fatorial`mencionado anteriormente ?

Observe o gif que mostra de modo visual o funcionamento da pilha _(de maneira simplificada)_, apresentando a execução do código do **fatorial** passo a passo:

![Gif stack python recursive](https://content-assets.betrybe.com/prod/f3ed319f-e20d-4f6a-8b5e-c0ff3788ac0a-Gif%20stack%20python%20recursive.gif)
Gif retirado do site: www.realpython.com/python-thinking-recursively

De forma análoga, quando `factorial` é chamada, um novo dado é adicionado à uma pilha. Será adicionado à pilha todos os valores executados, do 5 ao 1, quando chegamos no caso base. Quando a execução acaba, os dados são retirados da pilha um a um de forma reversa (do 1 ao 5), **multiplicando** e **retornando** cada dado, até que a pilha esvazie e o processamento finalize.

Vamos praticar os conteúdo visto até aqui por meio do exercício!?

Exercício de fixação:

**Exercício:** Faça um algoritmo recursivo de soma. Esse algoritmo deve receber um número de parâmetro e deve somar todos os números antecessores a ele.

Exemplo:

```bash
Número passado por parâmetro à função: 4

Execução: 4 + 3 + 2 + 1

Resultado: 10
```

Solução

```python
def sum_recursive(n):
    if n == 0:
        return 0
    else:
        print(n)
        return n + sum_recursive(n - 1)


sum_recursive(4)
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/bfc198bb-2ceb-4271-b137-2388dddbeee2/lesson/f83d0728-eff7-4a03-aede-f2a0db31b8dd/solution)

