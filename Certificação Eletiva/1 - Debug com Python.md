[[2 - Testes com o Pytest]]
[[Python]]

## Onde estamos?

Nessa seção você vai aprender a utilizar a ferramenta de depuração do Visual Studio Code para solucionar problemas no seu código e tornar o processo de desenvolvimento mais eficiente.

Os exemplos de código serão em Python, mas as técnicas que você vai aprender são aplicáveis em diversas outras linguagens. Você será capaz de:

- Utilizar a ferramenta de debug do VS Code, bem como navegar por meio dos atalhos desse depurador.
- Configurar adequadamente o VS Code para debug com python.
- Compreender como transitar entre a visualização padrão do código e a visualização de debug do VS Code.
- Compreender os pontos de parada (_breakpoints_) de linha, execução linha a linha e execução por bloco de código (de _breakpoint_ para _breakpoint_).
- Executar estratégias de depuração como:
    1. Identificar o valor de uma variável em determinado momento da execução do código.
    2. Identificar a árvore de chamadas de funções.
    3. Executar parcialmente um trecho de código durante o debug por meio do _Display_ do VS Code.

---

## Por que isso é importante?

Você já perdeu horas tentando identificar um erro no seu código? Ou pior, já entregou um projeto com bugs que foram identificados pelas pessoas que usaram seu software?

Se a resposta for sim, esta seção é para você! Afinal, quem nunca passou por isso, não é mesmo?

![Ilustração de uma pessoa desenvolvedora irritada](https://content-assets.betrybe.com/prod/0890bdae-c4b1-4c0d-8241-dfafae8b94df-Ilustra%C3%A7%C3%A3o%20de%20uma%20pessoa%20desenvolvedora%20irritada.png)Ilustração de uma pessoa desenvolvedora irritada

Como você já desenvolve há algum tempo, é provável que já tenha se deparado com bugs difíceis de serem encontrados. Às vezes, o problema pode estar escondido em meio a centenas ou até mesmo milhares de linhas de código. Nesse cenário, é comum que a solução seja um processo lento e cansativo de tentativa e erro, o que pode levar horas ou até mesmo dias para ser concluído.

Uma forma de debugar um código é utilizando `print`s ou `log`s. No entanto, essa estratégia pode ser bastante ineficiente, pois você precisa adicionar e remover esses `print`s a todo momento, além de ter que executar o código diversas vezes para encontrar o erro.

É nesse momento que a ferramenta de depuração (_debug_) do VS Code se torna uma grande aliada. Ela permite que você interaja com o código **enquanto ele está sendo executado**, parando a execução em pontos específicos para que você possa analisar o estado das variáveis, encontrar problemas e corrigi-los de forma mais rápida e eficiente.

Mas como utilizar essa ferramenta de forma adequada? É isso que vamos aprender neste conteúdo.

# Debug no VS Code

Você já passou horas procurando um erro em seu código e não conseguiu encontrar?

É frustrante, não é mesmo? _Debugar_ (depurar) é uma habilidade essencial para qualquer pessoa que desenvolve _software_, e o Visual Studio Code (VS Code) oferece uma excelente experiência de debug para Python.

> **De olho na dica** 👀: Além de Python, podemos debugar código de diversas outras linguagens com o VS Code! Basta procurar a extensão adequada para a linguagem que desejar.

Neste conteúdo, veremos como configurar seu ambiente para _debug_ com Python no VS Code e compartilhar os principais atalhos para te ajudar a encontrar erros em seu código mais rapidamente.

## Como configurar o ambiente do VS Code para debug com Python

Antes de começarmos a _debugar_, precisamos configurar o ambiente do VS Code para isso. Primeiro, você precisa ter o Python instalado em seu computador, assim como a extensão oficial do VSCode para o Python.

> 👉 Caso necessário, veja nosso [guia de instalação do Python](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/f04cdb21-382e-4588-8950-3b1a29afd2dd/section/aa76abc8-b842-40d9-b5cc-baa960952129/lesson/3f79a20e-f527-4871-b69b-544310cfbda0).
> 
> 👉 Caso necessário, veja nosso [guia de configuração do VS Code para o Python](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/f04cdb21-382e-4588-8950-3b1a29afd2dd/section/aa76abc8-b842-40d9-b5cc-baa960952129/lesson/9685a920-42b3-4c27-a293-c588f1e70527).

Durante parte do conteúdo, usaremos um exemplo de código no qual ao rodarmos o arquivo, a função `main()` será chamada e usará a função `map_factorial` para retornar uma lista contendo o cálculo do fatorial de cada número na lista passada como parâmetro.

Apesar de ser mais vantajoso usar o _debugger_ em aplicações maiores e mais complexas, neste momento escolhemos um código intencionalmente pequeno e sem bugs para focarmos na **ferramenta de debug** (e menos no código Python em si). Aproveite e crie um arquivo no VSCode para acompanhar também! 😃

Copiar

```python
from math import factorial


def map_factorial(numbers):
    result = []

    for num in numbers:
        result.append(factorial(num))

    return result


def main():
    input_list = [1, 2, 3, 4, 5]
    return map_factorial(input_list)


if __name__ == "__main__":
    main()

```


Com o arquivo aberto no VSCode, você pode executar o _debugger_ de 2 formas diferentes:

1. Apertando a tecla `F5`;
2. Acessando a aba `Run and Debug`(Executar e Depurar) no menu lateral esquerdo e clicando no botão `Run and Debug` (como na imagem a seguir).

![VScode Run debug](https://content-assets.betrybe.com/prod/4291f8df-5927-4c23-af2a-4b0f255e7271-VScode%20Run%20debug.png)
Opções de “Run and Debug” do VS Code

Em seguida, você deve escolher qual configuração usar para o _debug_. Para os primeiros exemplos, vamos usar a opção `Python File` (Arquivo Python), como na imagem a seguir:

![VScode debug: Python File](https://content-assets.betrybe.com/prod/4291f8df-5927-4c23-af2a-4b0f255e7271-VScode%20debug:%20Python%20File.png)Opção “Python File” como configuração para debug no VS Code

Pronto! Seu código executou e… nada de especial aconteceu! 😅

Esse é o comportamento padrão quando nenhum **breakpoint** (ponto de parada) é adicionado. Os breakpoints são informações essenciais ao _debugger_, e vamos ver agora como utilizá-los ao nosso favor.

## Como debugar no VS Code?

Para começar a _debugar_ de verdade, coloque um _breakpoint_ em seu código.

> **Anota aí 📝:** Um _breakpoint_ é um ponto em seu código onde a execução irá pausar.

Para colocar um _breakpoint_, clique na linha de código onde você quer pausar a execução e pressione `F9`.

Outra forma de adicionar um breakpoint na linha é clicar na bolinha vermelha 🔴 à esquerda do número das linhas que desejar (como na imagem a seguir).

![VScode debug: adicionar breakpoint](https://content-assets.betrybe.com/prod/4291f8df-5927-4c23-af2a-4b0f255e7271-VScode%20debug:%20adicionar%20breakpoint.png)
Adição de breakpoint no debugger do VS Code

Em nosso exemplo, estamos definindo que a execução do código deve pausar assim que atingir a linha `14`. O VScode irá pausar a execução sem executar a linha do _breakpoint_.

Depois de colocar o breakpoint, execute seu código em modo debug como foi ensinado: apertando `F5` ou pela aba `Run and Debug`.

Agora sim! Você pode usar os comandos de debug para continuar a execução, passo a passo, ou examinar as variáveis em seu código. Os principais comandos ficam no topo da janela, com botões como mostramos na imagem a seguir:

![VScode debug: comandos](https://content-assets.betrybe.com/prod/4291f8df-5927-4c23-af2a-4b0f255e7271-VScode%20debug:%20comandos.png)
Opções de comandos do debugger do VS Code

Veja qual a função de cada um dos botões/comandos, e seus respectivos atalhos no teclado:

1. _Continue_ (continuar): avança com a execução em modo _debug_ até o próximo breakpoint (atalho `F5`)
2. _Step over_ (contornar): executa a linha atual do código sem entrar em funções (atalho `F10`)
3. _Step into_ (intervir): executa a linha atual do código e entra em funções (atalho `F11`)
4. _Step out_ (sair): sai da função atual e continua a execução (atalho `Shift + F11`)
5. _Restart_ (reiniciar): recomeça a execução do _debugger_ (atalho depende do seu Sistema Operacional)
6. _Stop_ (interromper): encerra a execução do _debugger_ (atalho depende do seu Sistema Operacional)

🧪 Explore o comportamento dos 4 primeiros comandos! Como eles se comportam? Qual a vantagem e desvantagem de cada um?

A partir de agora, vamos utilizá-los para navegar durante o _debug_.

## Referências

- [Documentação do VS Code sobre o debugger do VS Code](https://code.visualstudio.com/docs/editor/debugging)
- [Documentação do VS Code sobre o debugger do Python](https://code.visualstudio.com/docs/python/debugging)
- [Debug ou Depuração: o que é e para que serve? (Blog da Trybe)](https://blog.betrybe.com/tecnologia/debug/)


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/3afc9219-354a-42ac-826c-02b2375d3524/lesson/1e0fda25-6fdf-47dc-a8a3-1a00d7d412b4)
