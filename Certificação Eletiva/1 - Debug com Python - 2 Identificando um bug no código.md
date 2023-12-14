[[1 - Debug com Python]]

Até o momento você viu as principais funcionalidades do debugger do VS Code, mas ainda não teve a experiência de usá-lo para **de fato** achar um bug em um código Python.

Essa hora chegou! 🤩

## O código que vamos debugar

Imagine que você recebeu um convite para implementar parte do código de um TTS (_text-to-speech_): um sistema que transforma texto em som ‘falado’.

Apesar de sistemas TTS serem bastante complexos, sua missão é “somente” ajudar no pré-processamento do texto, **transformando números em palavras**. Isso significa que você deve construir uma função que recebe um número inteiro e retorna uma string em português, em que cada dígito é representado por uma palavra.

Ou seja:

- 1 = “um”
- 2 = “dois”
- 3 = “três”

E assim por diante.

Experimentando algumas alternativas para resolver o problema, você construiu a função `generate_int_description` no código a seguir:

```python
import random


DIGITS_MAP = {
    0: "zero",
    1: "um",
    2: "dois",
    3: "três",
    4: "quatro",
    5: "cinco",
    6: "seis",
    7: "sete",
    8: "oito",
    9: "nove",
}


def generate_int_description(integer):
    """Transforma dígitos de um número em texto PT-BR"""
    digits = list(str(integer))

    description = f"{DIGITS_MAP.get(digits[0])}"
    for digit in digits[1:]:
        description += f" {DIGITS_MAP.get(digit)}"

    return description


def main():
    integer = random.randint(10000, 99999)

    description = generate_int_description(integer)

    print(f"Descrição do número {integer}: '{description}'")


if __name__ == "__main__":
    main()

```

O código inicia na função `main()` que irá gerar um inteiro de 5 dígitos aleatórios para usar como argumento da função `generate_int_description(integer)`, e por fim apresentará o resultado no terminal.

Porém, quando executamos esse código um problema aparece: qualquer número resulta em `'None None None None None'`.

O código não levanta uma exceção, mas ele contém um bug, afinal, o funcionamento não é o esperado e não sabemos o motivo. Essa é uma ótima situação para usar o debugger!

## Iniciando o processo

Para utilizarmos o debugger, o primeiro passo é definirmos (pelo menos) 1 linha em que desejamos interromper a execução para fazer nossa investigação.

Como não sabemos ao certo onde está nosso problema, podemos adicionar o breakpoint no início da lógica da função `main()`.

![VSCode debug: breakpoint inicial](https://content-assets.betrybe.com/prod/208bc3c8-7b9f-4311-aad8-ab7c7667bd93-VSCode%20debug:%20breakpoint%20inicial.png)VSCode debug: breakpoint inicial

## Avançando passo-a-passo

Com o breakpoint definido, podemos iniciar o processo de depuração com o atalho `F5` e avançar pouco a pouco com _Step into_ (ou intervir - atalho `F11`). A cada avanço, podemos ver as seguintes alterações nas janelas de debug:

> Acompanhe fazendo o passo-a-passo na sua máquina! 😉

1º avanço:

- **VARIABLES**: na seção `Globals`, temos o retorno da chamada de `random.randint` e a confirmação de que esse mesmo valor foi armazenado na variável `integer`. A cada execução esse valor é diferente, mas vamos supor que temos o número `68280`.

2º avanço:

- **CALL STACK**: agora entramos na função `generate_int_description`, empilhando sua chamada na pilha;
- **VARIABLES**: temos o valor `68280` do parâmetro `integer` dentro de `generate_int_description`.

3º avanço:

- **VARIABLES**: além do valor de `integer` que se mantém, temos agora o valor calculado para `digits` - a lista `['6', '8', '2', '8', '0']`. Até aqui, nada muito estranho aparentemente.

4º avanço:

- **VARIABLES**: Opa! O valor de `description` é `'None'`, algo que não gostaríamos. 😱

![VSCode debug: estado do debug após 4º avanço](https://content-assets.betrybe.com/prod/208bc3c8-7b9f-4311-aad8-ab7c7667bd93-VSCode%20debug:%20estado%20do%20debug%20ap%C3%B3s%204%C2%BA%20avan%C3%A7o.png)VSCode debug: estado do debug após 4º avanço

> Não encerre ou avance com o debugger ainda, vamos voltar à execução em breve!

## Entendendo os detalhes

Vamos avaliar com calma o que pode ter acontecido na linha que acabou de ser executada:

```python
    # ...
    description = f"{DIGITS_MAP.get(digits[0])}"
```

Nesta linha, estamos criando uma string a partir do retorno da chamada `DIGITS_MAP.get(digits[0])`. Então, se `description` é a string `'None'`, tudo indica que essa chamada retornou `None`.

Se consultarmos o comportamento do método `get()` dos dicionários (como `DIGITS_MAP`), veremos que o retorno é:

- o valor atrelado à chave informada no parâmetro;
- ou, _caso a chave não exista_, **o valor padrão `None`**.

Aha! 🎉 Agora **temos uma hipótese** do motivo de aparecer `'None'` na nossa string `description`.

A questão agora é: o valor de `digits[0]` não deveria ser uma chave válida do dicionário `DIGITS_MAP`? Por que nossa lógica não funcionou?

Se observarmos com atenção o valor de `digits`, veremos que seu primeiro elemento é `'6'`: a **string** (do tipo `str`) com o caractere `6`. E, avaliando o dicionário `DIGITS_MAP`, podemos observar que as chaves são valores **inteiros** (do tipo `int`).

## Testando a hipótese

Para testar se o código funcionaria como esperado casos os tipos fossem os mesmos, podemos usar a própria ferramenta de debug!

Agora vem a “mágica”: nós podemos editar o valor de `digits`! Para isso, basta clicar 2 vezes no valor à direita do nome `digits` e você conseguirá fazer a edição.

Faça a “troca” dos valores de `str` para `int` removendo as aspas ao redor dos números, e por fim aperte `Enter`. Feito: agora nossa lista `digits` tem, de fato, números inteiros e podemos continuar a execução e observar como o código se comporta!

5º avanço:

- **VARIABLES**: agora que entramos no _loop_ `for`, temos o primeiro valor de `digit` (que no nosso caso é `8`).

6º avanço:

- **VARIABLES**: o valor de `description` foi alterado para `'None oito'`, o que valida nossa hipótese! 🎉

Se você continuar avançando, verá que a string final será algo equivalente a `'None oito dois oito zero'`, no nosso exemplo.

## Corrigindo o bug no código

Agora que sabe o que há de errado com o código, você pode decidir como corrigir.

Nesse caso, há 2 alternativas:

1. alterar as chaves do `DIGITS_MAP` para `str`;
2. converter os valores da lista `digits` para `int` antes das chamadas `DIGITS_MAP.get(...)`.

Escolha sua favorita, faça a correção e execute para ver o código funcionando como esperava! No exemplo a seguir, fizemos o ajuste no `DIGITS_MAP`

![VSCode debug: código com bug corrigido](https://content-assets.betrybe.com/prod/208bc3c8-7b9f-4311-aad8-ab7c7667bd93-VSCode%20debug:%20c%C3%B3digo%20com%20bug%20corrigido.png)
VSCode debug: código com bug corrigido


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/3afc9219-354a-42ac-826c-02b2375d3524/lesson/45e41c41-e872-4716-bbe0-7005fcbed72d)
