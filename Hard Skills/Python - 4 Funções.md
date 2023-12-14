[[Python]]

Por meio do vídeo, notamos que funções são definidas através da palavra reservada `def`, seguida por um nome e os parâmetros entre parênteses. Como todo bloco de código em **Python**, o caractere `:` define o início do bloco, e a indentação define seu fim.

Os parâmetros podem ser passados de forma:

- posicional: são aqueles definidos por meio da posição em que cada um é passado;
- nomeada: são definidos por meio de seus nomes.



```python
def soma(x, y):
    return x + y

soma(2, 2)  # os parâmetros aqui são posicionais

soma(x=2, y=2)  # aqui estamos nomeando os parâmetros
```

Os parâmetros também podem ser variádicos, ou seja, variam em sua quantidade.

Anota aí ✏️: Parâmetros posicionais variádicos são acessados como uma tupla no interior de uma função, e parâmetros nomeados variádicos como um dicionário.

Copiar

```python
def concat(*strings):
    # Equivalente a um ", ".join(strings), que concatena os elementos de um iterável em uma string utilizando um separador
    # Nesse caso a string resultante estaria separada por vírgula
    final_string = ""
    for string in strings:
        final_string += string
        if not string == strings[-1]:
            final_string += ', '
    return final_string

# pode ser chamado com 2 parâmetros
concat("Carlos", "Cristina")  # saída: "Carlos, Cristina"

# pode ser chamado com um número n de parâmetros
concat("Carlos", "Cristina", "Maria")  # saída: "Carlos, Cristina, Maria"

# dict é uma função que já vem embutida no python
dict(nome="Felipe", sobrenome="Silva", idade=25)  # cria um dicionário utilizando as chaves passadas

dict(nome="Ana", sobrenome="Souza", idade=21, turma=1)  # o número de parâmetros passados para a função pode variar
```

As variáveis definidas dentro das funções tem escopo local. Porém, quando uma função não encontra um nome no escopo local, ela irá procurar no espaço de nomes global.

Em alguns casos, podemos querer limitar um parâmetro em nomeado ou posicional para evitar ambiguidades e/ou aumentar legibilidade.

Copiar

```python
len([1, 2, 3, 4])  # função len não aceita argumentos nomeados

len(obj=[1, 2, 3, 4])  # este código irá falhar

print("Coin", "Rodrigo", ", ")  # imprime Coin Rodrigo ,

print("Coin", "Rodrigo", sep=", ")  # nomeando o terceiro parâmetro, agora temos a saída: Coin, Rodrigo
```

Antes de adentrarmos a próxima seção, vamos ver um vídeo que mostra maiores detalhes sobre o conceito de escopo.


# Escrevendo os primeiros arquivos

Até aqui, programamos utilizando o terminal interativo e tínhamos uma resposta logo em seguida. Mas, se fecharmos o terminal vamos perder tudo que fizemos até agora, além disso programas do dia a dia não funcionam assim.

Por isso, vamos começar a escrever nossos primeiros arquivos de código? Mas antes de seguirmos, que tal darmos uma olhadinha no [Guia de configuração de ambiente Python](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/f04cdb21-382e-4588-8950-3b1a29afd2dd/section/aa76abc8-b842-40d9-b5cc-baa960952129/lesson/dd80466d-31d4-4b35-bacf-d789e261fa7d)?

Antes de escrever nosso primeiro arquivo, precisamos saber que todo arquivo com extensão `.py` é considerado um módulo.

Módulos são declarados utilizando _snake case_, ou seja, com nomes minúsculos e quando possuírem mais de uma palavra, devem ser separadas por _underscore_ (`_`).

1. Vamos agora criar o arquivo `area.py`, declarando funções que calculam a área de algumas figuras geométricas.
    
    > area.py
    

Copiar

```python
 PI = 3.14  # PI é uma "constante" em nosso módulo


 def square(side):
     '''Calculate area of square.'''
     return side * side


 def rectangle(length, width):
     '''Calculate area of rectangle.'''
     return length * width


 def circle(radius):
     '''Calculate area of circle.'''
     return PI * radius * radius
```

Observe que esse código segue algumas boas práticas para legibilidade, tais como:

- Entre cada função temos um espaço de 2 linhas;
- As funções são declaradas com nomes em letras minúsculas;
- A constante `PI` é definida em letras maiúsculas.

⚠️Aviso: Existe uma convenção de declarar valores considerados constantes com letras maiúsculas, e o respeito por outros programadores de não alterarem aquele valor.

- Abra um terminal e para executar o módulo em python, escreva `python3 area.py`. Se não houve nenhum erro de digitação, nada deve ter acontecido. Neste módulo só temos definições das funções e valores, mas não estamos executando nenhuma delas. Isto é o que chamamos de execução do módulo como **script**.
    
   ![Nosso primeiro módulo e sua execução](https://content-assets.betrybe.com/prod/bcc129f3-9df7-4e01-9aed-324145199e07-Nosso%20primeiro%20m%C3%B3dulo%20e%20sua%20execu%C3%A7%C3%A3o.png)
    Nosso primeiro módulo e sua execução.

- Hora de testá-lo! No fim do arquivo, vamos adicionar algumas linhas para imprimir a área de algumas figuras geométricas.

```python
print("Área do quadrado:", square(10))
print("Área do retângulo:", rectangle(2, 2))
print("Área do círculo:", circle(3))
```

Agora, quando executamos o arquivo `area.py`, estes valores de teste são exibidos, conforme a imagem abaixo:

![Nosso módulo quando executado exibe alguns valores de teste](https://content-assets.betrybe.com/prod/bcc129f3-9df7-4e01-9aed-324145199e07-Nosso%20m%C3%B3dulo%20quando%20executado%20exibe%20alguns%20valores%20de%20teste.png)
Nosso módulo quando executado exibe alguns valores de teste.

- Vamos utilizar o nosso módulo de calcular área de figuras planas. Escreva um novo arquivo com nome `people.py` e ele será um _script_ para calcular pessoas que estão presentes em um show, dado a área do mesmo.

Este _script_ será escrito da seguinte maneira:

> people.py


```python
import area


PEOPLE_PER_SQUARE_METER = 2  # numero de pessoas por metro quadrado em média
FIELD_LENGTH = 60  # em metros
FIELD_WIDTH = 45  # em metros
people_at_concert = (
    area.rectangle(FIELD_LENGTH, FIELD_WIDTH) * PEOPLE_PER_SQUARE_METER
)
print("Estão presentes no show aproximadamente", people_at_concert, "pessoas.")
```

Anota aí ✏️: O import é utilizado para termos todas as funções do módulo disponíveis em outro arquivo. Uma outra maneira de utilizarmos é escrevendo `from area import rectangle`, por exemplo, se quisermos importar apenas `rectangle` a partir de `area`. Porém, tome cuidado com conflitos de nomes caso use essa segunda maneira.

Ao executá-lo com o comando `python3 people.py`, vemos que a saída não foi bem como esperávamos.

![Execução exibe os testes feitos no módulo de área](https://content-assets.betrybe.com/prod/bcc129f3-9df7-4e01-9aed-324145199e07-Execu%C3%A7%C3%A3o%20exibe%20os%20testes%20feitos%20no%20m%C3%B3dulo%20de%20%C3%A1rea.png)|
Execução exibe os testes feitos no módulo de área.

> Os nossos valores de teste estão sendo exibidos quando importamos o módulo. Mas não queremos que isso aconteça.

Para corrigir, podemos acrescentar uma condicional ao módulo para somente exibir esses valores de teste quando o módulo for executado como **script**.

A variável `__name__` é utilizada pelo interpretador **Python** para identificar o arquivo que esta sendo executado e seu valor será `"__main__"` quando invocamos um módulo como _script_.

> area.py


```python
# ...


if __name__ == "__main__":
    print("Área do quadrado:", square(10))
    print("Área do retângulo:", rectangle(2, 2))
    print("Área do círculo:", circle(3))
```

Ao executarmos novamente nosso _script_, agora tudo está ok! 🎉

![Execução só exibe o resultado correto](https://content-assets.betrybe.com/prod/bcc129f3-9df7-4e01-9aed-324145199e07-Execu%C3%A7%C3%A3o%20s%C3%B3%20exibe%20o%20resultado%20correto.png)Execução só exibe o resultado correto.

Agora que já conhecemos sobre a linguagem de programação Python, chegou a hora de avaliarmos nosso aprendizado por meio do quiz do dia !


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/bee44ac6-0782-48cd-9ce8-1811980e558e/lesson/1a6798ad-ef22-4116-a504-986ee80cb77a)

