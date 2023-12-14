[[Python]]

Erros podem acontecer: um arquivo pode não existir, permissões podem faltar e codificações podem falhar. Por isso temos de garantir que, ainda que um erro ocorra, faremos o fechamento do nosso arquivo.

Como conseguimos lidar com erros em Python? Vamos agora falar sobre exceções!

Há pelo menos dois tipos de erros que podem ser destacados: **erros de sintaxe e exceções**.

## Erros de Sintaxe

Erros de sintaxe ocorrem quando o código utiliza recursos inexistentes da linguagem que não consegue interpretá-los. É como executar `print{"Olá, mundo!"}` em vez de `print("Olá, mundo!")`.

## Exceções

Já as exceções ocorrem durante a execução e resultam em mensagem de erro. Veja exemplos de exceções:

```python
print(10 * (1 / 0))
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# ZeroDivisionError: division by zero
print(4 + spam * 3)
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# NameError: name 'spam' is not defined
print('2' + 2)
# Traceback (most recent call last):
#   File "<stdin>", line 1, in <module>
# TypeError: can only concatenate str (not "int") to str
```

Observe que, apenas no exemplo acima, podemos observar três exceções: `ZeroDivisionError`, `NameError` e `TypeError`. A lista completa de exceções já embutidas na linguagem pode ser vista [aqui](https://docs.python.org/pt-br/3/library/exceptions.html#bltin-exceptions).

## Tratamento de exceções

Para tratar exceções utilizamos o conjunto de instruções `try`, com as palavras reservadas `try` e `except`. O funcionamento dessa cláusula ocorre da seguinte forma:

- Se nenhuma exceção ocorrer, a cláusula `except` é ignorada e a execução da instrução `try` é finalizada.
- Se ocorrer uma exceção durante a execução da cláusula `try`, as instruções remanescentes na cláusula são ignoradas. Se o tipo da exceção ocorrida tiver sido previsto em algum `except`, então essa cláusula será executada.
- Se não existir nenhum tratador previsto para tal exceção, trata-se de uma exceção não tratada e a execução do programa termina com uma mensagem de erro.

Vamos agora ver um exemplo de tratamento de exceções:

```python
while True:
    try:
        x = int(input("Por favor digite um número inteiro: "))
        break
    except ValueError:
        # 'ValueError' é a exceção que ocorre quando a função int() recebe um
        # valor que não pode ser traduzido para número inteiro
        print("Oops! Esse não era um número inteiro. Tente novamente...")
```

Cole o código acima no terminal interativo e teste, na prática, como funciona o tratamento de exceções.

## Lidando com exceções enquanto manipulamos arquivos

Vamos ver agora como podemos nos prevenir de possíveis erros que ocorrem quando manipulamos arquivos. Sempre devemos fechar um arquivo e podemos, em um bloco `try`, fazer isso utilizando a instrução `finally` ou `else`. O `finally` é uma outra cláusula do conjunto `try`, cuja finalidade é permitir a implementação de ações de limpeza, que sempre devem ser executadas independentemente da ocorrência de ações. Já o `else` ocorre sempre que todo o `try` for bem sucedido.

Copiar

```python
try:
    with open("arquivo.txt", "r") as file:
        print(file.read())
except FileNotFoundError:
    # será executado caso haja a exceção 'FileNotFoundError'
    print("Arquivo inexistente")
else:
    # será executado se tudo ocorrer bem no try
    print("Arquivo manipulado e fechado com sucesso")
finally:
    # será sempre executado, independentemente de erro
    print("Fim da tentativa de ler o arquivo")
```

Como estamos abrindo o arquivo em modo de leitura, uma exceção será levantada caso ele não exista, executando as cláusulas `except` e `finally`. Entretanto, se alterarmos o modo para escrita, o arquivo será criado mesmo se inexistente, executando as cláusulas `else` e `finally`.

## Vamos praticar um pouco?

## Exercício 3:

Dado um arquivo contendo estudantes e suas respectivas notas, escreva um programa que:

1. lê todas essas informações;
2. aplique um filtro, mantendo somente as pessoas que estão reprovadas;
3. escreva seus nomes em outro arquivo.

Considere que a nota miníma para aprovação é 6.

_Arquivo:_

```bash
Marcos 10
Felipe 4
José 6
Ana 10
Maria 9
Miguel 5
```

_Exemplo saída:_

```bash
Felipe
Miguel
```

🦜 A função `split` pode ser utilizada para dividir o nome em uma linha. Ex: `line.split()` -> `["Marcos", "10"]`.

```python
recovery_students = []
with open("file.txt") as grades_file:
    for line in grades_file:
        student_grade = line
        student_grade = student_grade.split(" ")
        if int(student_grade[1]) < 6:
            recu_students.append(student_grade[0] + "\n")


with open("recuStudents.txt", mode="w") as recu_students_file:
    print(recuStudents)
    recu_students_file.writelines(recuStudents)
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/61e88b4a-b97a-4f96-b5a0-abaa50651e37/lesson/8ba41a24-789a-4453-8af4-1c53b5571281)
