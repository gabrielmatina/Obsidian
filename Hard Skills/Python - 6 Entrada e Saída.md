[[Python]]
[[Python - 4 Funções]]

Funções podem receber argumentos das pessoas que usam o programa, processá-los e retornar algum valor. Mas como estes argumentos chegam a elas? E o resultado do nosso processamento, como exibi-lo para a pessoa utilizando nossa aplicação?

De modo geral, podemos dizer que um programa seria menos útil se não pudéssemos coletar valores de pessoas usuárias, e muito menos agradável de se utilizar caso o resultado apresentado fosse pouco legível.

Existem algumas maneiras de nos comunicarmos com o exterior do programa em **Python** para receber dados, assim como existem maneiras de melhorar a exibição dos nossos resultados.

## Entrada

Uma das maneiras que existem de receber valores em nossos programas é utilizando o módulo `sys`. Quando executamos um _script_ e adicionamos parâmetros, os mesmos estarão disponíveis através de uma variável chamada `sys.argv`, que é preenchida sem que precisemos fazer nada. Na prática, podemos escrever o conteúdo abaixo em um arquivo e passar alguns parâmetros ao executá-lo.

```python
import sys


if __name__ == "__main__":
    for argument in sys.argv:
        print("Received -> ", argument)
```

Podemos executar o código usando os parâmetros através do comando abaixo:

```bash
python3 arquivo.py 2 4 "teste"
```

A saída será:

```bash
Received ->  arquivo.py
Received ->  2
Received ->  4
Received ->  teste
```

Também é possível coletar dados do terminal através da função `input`, que vem embutida na própria linguagem. Esta função está vinculada à entrada padrão do sistema operacional e tem como parâmetro opcional o `prompt` que, caso seja fornecido, exibirá a mensagem passada para ele em tela. O valor recebido através da função será do tipo texto (`str`):

```python
user_input = input("Digite uma mensagem: ")
print('O valor recebido foi:', user_input)
```

## Saída

Como já visto, a função `print` — que já vem embutida na linguagem — é a principal função para imprimirmos um valor em “tela”. Normalmente esta função escreve na saída padrão do sistema operacional, mas veremos que é possível modificar este e outros comportamentos.

Esta função recebe parâmetros de forma variável, ou seja, pode receber nenhum, um, dois ou _n_ parâmetros durante sua invocação.

```python
print("O resultado é", 42)  # saída: O resultado é 42
print("Os resultados são", 6, 23, 42)  # saída: Os resultados são 6 23 42
```

O separador padrão dos argumentos é um espaço em branco, que pode ser alterado.

```python
print("Maria", "João", "Miguel", "Ana")  # saída: Maria João Miguel Ana
print("Maria", "João", "Miguel", "Ana", sep=", ")  # saída: Maria, João, Miguel, Ana
```

Além do separador, podemos também alterar o caractere de fim de linha que, por regra, é uma quebra de linha:

```python
print("Em duas ")
print("linhas.")
```

_Saída:_

```text
Em duas
linhas.
```

Alterando o padrão:

```python
print("Na mesma", end=" ")
print("linha.")
```

_Saída:_

```text
Na mesma linha.
```

Já sabemos que erros podem acontecer e o sistema operacional normalmente espera que um programa escreva seus erros na saída de erros.

Existe um parâmetro que nos permite modificar a saída padrão para a saída de erros:

```python
import sys


err = "Arquivo não encontrado"
print(f"Erro aconteceu: {err}", file=sys.stderr)
```

> 💡 Em **Python**, podemos fazer interpolação de variáveis e expressões utilizando [f-string](https://docs.python.org/pt-br/3/tutorial/inputoutput.html#tut-f-strings). Adicionamos um `f` antes das aspas e o valor de saída entre chaves. Essa dica é importante, pois é a maneira mais eficiente de formatar strings.

## Para fixar

Antes de prosseguirmos, vamos resolver alguns exercícios.

## Exercício 1:

Faça um programa que solicite o nome de uma pessoa usuária e imprima-o na vertical. Exemplo:

```bash
F
U
L
A
N
O
```

```python
NAME = input("Insira seu nome: ")

for letter in NAME:
    print(letter)
```


## Exercício 2:

Escreva um programa que receba vários números **naturais** e calcule a soma desses valores. Caso algum valor da entrada seja inválido (por exemplo uma letra), uma mensagem deve ser exibida na saída de erros no seguinte formato: “Erro ao somar valores, {valor} é um valor inválido”. Ao final, você deve imprimir a soma dos valores válidos.

🦜 Receba os valores em um mesmo `input`, solicitando à pessoa usuária que separe-os com um espaço. Receba os valores no formato `str` e utilize o método `split` para pegar cada valor separado. O método `isdigit`, embutido no tipo `str`, pode ser utilizado para verificar se a _string_ corresponde a um número natural.

```python
nums = input("Insira valores aqui, separados por espaço: ")

nums_arr = nums.split(" ")

sum = 0
for num in nums_arr:
    if not num.isdigit():
        print(f"Erro ao somar valores, {num} não é um dígito.")
    else:
        sum += int(num)

print(f"A soma dos valores válidos é: {sum}")
```


## Desempacotamento de Valores


O **desempacotamento de valores** é um recurso muito útil de Python quando queremos separar os valores recebidos em variáveis diferentes. Quando há uma atribuição múltipla e o valor da direita pode ser percorrido, o Python entende que deve atribuir cada um dos valores a uma variável da esquerda, seguindo a ordem. Vejamos no exemplo abaixo:

> 💡 Execute o código abaixo para que você veja os valores printados e entenda melhor o funcionamento.

```python
a, b = "cd"
print(a)  # saída: c
print(b)  # saída: d

head, *tail = (1, 2, 3) # Quando um * está presente no desempacotamento, os valores são desempacotados em formato de lista.
print(head)  # saída: 1
print(tail)  # saída: [2, 3]
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/61e88b4a-b97a-4f96-b5a0-abaa50651e37/lesson/af7e97a4-654f-47c1-a3e8-f726ee91960f/solution)
