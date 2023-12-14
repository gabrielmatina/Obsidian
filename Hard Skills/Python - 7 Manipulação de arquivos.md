[[Python]]

Seja para armazenar alguma informação processada, para manipular imagens, áudios, vídeos ou recuperar dados de uma planilha, precisamos manipular arquivos.

Podemos fazer uma operação de leitura, de escrita ou até de ambas, a depender da nossa necessidade. Porém, independentemente da operação executada, é preciso sempre fechar o arquivo após operá-lo.

A função `open` é a responsável por abrir um arquivo, tornando possível sua manipulação. Seu único parâmetro obrigatório é o nome do arquivo. Por padrão, arquivos são abertos **somente para leitura**, mas podemos modificar isto passando o modo com que vamos abrir o arquivo. No exemplo abaixo, estamos utilizando `mode="w"`, ou seja, estamos abrindo o arquivo para escrita:

```python
file = open("arquivo.txt", mode="w")  # ao abrir um arquivo para escrita, um novo arquivo é criado mesmo que ele já exista, sobrescrevendo o antigo.
```

O objeto especial `file` gerado no código anterior será usado para fazermos leituras e escritas no arquivo, como veremos a seguir. Depois de realizarmos todas as operações desejadas, é imprescindível que o comando `file.close()` seja executado pois existe uma quantidade limite de arquivos que podemos abrir de uma só vez. Caso o limite seja excedido, o Sistema Operacional levantará um `OSError`.

Para ter um melhor controle da abertura de fechamento de arquivos, devemos utilizar os **gerenciadores de contexto** do Python. Essa funcionalidade é comum também em outros recursos que devemos utilizar e liberar ao final (conexões de bancos de dados, por exemplo).

O `with` é a palavra reservada para gerenciamento de contexto no Python. Este gerenciamento (`with`) é utilizado para encapsular a utilização de um recurso, garantindo que certas ações sejam tomadas independentemente se ocorreu ou não um erro naquele contexto.

A função `open` retorna um objeto que se comporta como um gerenciador de contexto de arquivo que será responsável por abrir e fechar o mesmo. Isto significa que o arquivo possui mecanismos especiais que, quando invocados utilizando `with`, alocam um determinado recurso — um arquivo — e o liberam quando o bloco for finalizado.

```python
# Criamos um contexto, limitando o escopo onde o arquivo está aberto.
# O uso do "as" aqui é somente para atribuir o valor utilizado no contexto à variável file
with open("arquivo.txt", "w") as file:
    # como estamos DENTRO do contexto, verificamos que o arquivo está ABERTO
    # através do atributo booleano 'closed' (file.closed = False)
    print(file.closed)
# como estamos FORA do contexto, o arquivo está FECHADO (file.closed = True)
print(file.closed)
```

⚠️ A partir de agora, faremos todas as leituras e escritas de arquivo utilizando o gerenciador de contexto do Python.

Para escrevermos um conteúdo em um arquivo utilizamos a função `write`:

```python
# with open("arquivo.txt", "w") as file:
    # file.write("nome idade\n")

    file.write("Maria 45\n")
    file.write("Miguel 33\n")
```

> 💡 Só é possível escrever em um arquivo após abri-lo em um modo que permita escrita.

Assim como podemos redirecionar a saída do nosso programa para a saída de erros, podemos redirecionar o conteúdo digitado dentro de `print` para um arquivo. Ou seja, também podemos escrever em um arquivo através do `print`.

```python
# with open("arquivo.txt", "w") as file:
#     file.write("Miguel 33\n")


    # Não precisa da quebra de linha, pois esse é um comportamento padrão do print
    print("Túlio 22", file=file)
```

Para escrever múltiplas linhas podemos utilizar a função `writelines`. Repare que a função espera que cada linha tenha seu próprio caractere de separação (`\n`):

```python
# with open("arquivo.txt", "w") as file:
#   ...

    LINES = ["Alberto 35\n", "Betina 22\n", "João 42\n", "Victor 19\n"]
    file.writelines(LINES)
```

A leitura do conteúdo de um arquivo pode ser feita utilizando a função `read`. Para experimentar, vamos escrever em um arquivo e lê-lo logo em seguida!

```python
# escrita
with open("arquivo.txt", "w") as file:
    file.write("Trybe S2")

# leitura
with open("arquivo.txt", "r") as file:
    content = file.read()
    print(content)
```

Um arquivo é também um iterável, ou seja, pode ser percorrido em um laço de repetição. A cada iteração, uma nova linha é retornada. Vamos fazer igual ao exemplo anterior, porém dessa vez vamos escrever mais de uma linha!

```python
# escrita
with open("arquivo.txt", "w") as file:
    LINES = ["Olá\n", "mundo\n", "belo\n", "do\n", "Python\n"]
    file.writelines(LINES)

# leitura
with open("arquivo.txt", "r") as file:
    for line in file:
        print(line)  # não esqueça que a quebra de linha também é um caractere da linha
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/61e88b4a-b97a-4f96-b5a0-abaa50651e37/lesson/256f962c-3303-46ae-a4a7-dc5551499389)
