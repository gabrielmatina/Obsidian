[[Python]]

> Vamos imaginar a seguinte situação: em uma análise de dados sobre pessoas desenvolvedoras, temos uma base de dados que contém o salário delas, mas não mostra a informação sobre sua senioridade.

Para fazer um agrupamento por essa classificação de nível de experiência, precisamos criar uma nova coluna que será baseada no salário:

- Menor que R$2.000,00, pessoa desenvolvedora estagiária;
- Entre R$2.000,00 e R$5.800,00, pessoa desenvolvedora júnior;
- Entre R$5.800,00 e R$7.500,00, pessoa desenvolvedora pleno;
- Entre R$7.500,00 e R$10.500,00, pessoa desenvolvedora sênior;
- Qualquer valor acima do que já foi mencionado a pessoa desenvolvedora é considerada liderança.

```python
position = ""
if salary <= 2000:
    position = "estagiário"
elif 2000 < salary <= 5800:
    position = "júnior"
elif 5800 < salary <= 7500:
    position = "pleno"
elif 7500 < salary <= 10500:
    position = "senior"
else:
    position = "líder"
```

> Anota aí ✏️: A indentação do código deve ser feita com 4 espaços em vez de tabs.

Você pode estar se perguntando: “Não está faltando alguma coisa nessa estrutura?”🤔

> Resposta: Note que `if` e `elif` são seguidos de uma expressão que se avaliada como verdadeira, o trecho de código será executado. Um outro detalhe é a ausência de chaves para definir o bloco. Utilizamos o caractere `:` para indicar abertura de um bloco e somente indentação para indicar o término.

⚠️ Aviso: O bloco `else` será executado se nenhuma das condições anteriores for satisfeita.

Agora que escrevemos mais linhas, notamos também a ausência do caractere `;`. Pois bem, a filosofia da linguagem Python nos diz: _“Legibilidade conta…”_, então, com isso, não precisamos mais desse caractere.

Com a senioridade em mãos, podemos criar uma nova coluna em nossa base dados com essa informação e realizar o agrupamento por cargo, desenhando lindos gráficos. 📊

> Você pode estar se perguntando: “Posso modificar para uma estrutura `switch`?” Resposta: A estrutura condicional `if` e seu aninhamento com `elif` e `else` é tão simples e legível que não precisamos da estrutura `switch`.

De olho na dica👀:”Simples é melhor do que complexo” - Zen do python

Em alguns casos, em que não seja prejudicada a legibilidade, podemos criar estruturas de mapeamento (_dicts_) para simplificar o aninhamento de condicionais. Como o exemplo a seguir:

```python
key = "id"
from_to = {
    "id": "identifier",
    "mail": "email",
    "lastName": "last_name",
}
from_to[key]
```

# Estruturas de repetição

## for

Imagine um sistema que faça a listagem de restaurantes. Estes restaurantes possuem uma nota proveniente da avaliação dos seus clientes.

```python
restaurants = [
    {"name": "Restaurante A", "nota": 4.5},
    {"name": "Restaurante B", "nota": 3.0},
    {"name": "Restaurante C", "nota": 4.2},
    {"name": "Restaurante D", "nota": 2.3},
]
```

Quando um cliente pede a listagem de restaurantes, ele pode escolher filtrar o resultado de acordo com a nota. Essa filtragem pode ocorrer percorrendo a lista de restaurantes ou criando uma nova lista com somente aqueles que atendem ao filtro, assim como mostra o exemplo abaixo:

```python
filtered_restaurants = []
min_rating = 3.0
for restaurant in restaurants:
    if restaurant["nota"] > min_rating:
        filtered_restaurants.append(restaurant)
print(filtered_restaurants)  # imprime a lista de restaurantes, sem o B e D
```

Dado que a maior parte do tempo estamos percorrendo estruturas, os criadores do Python decidiram que o `for each` seria o laço de repetição principal na linguagem.

Para cada repetição do nosso laço, um novo elemento da estrutura iterável é atribuído a variável de iteração. No exemplo acima, vemos que a cada iteração um novo restaurante é colocado na variável `restaurant`.

> Anota ai ✏️: Em alguns casos, podemos ainda querer percorrer uma sequência numérica, e para isto iteramos sobre a estrutura de dados `range`.

```python
for index in range(5):
    print(index)
```

Além de listas, várias outras estruturas são iteráveis, como strings (`str`), tuplas (`tuple`), conjuntos (`set`), dicionários (`dict`) e até mesmo arquivos.

## Compreensão de lista (_list comprehension_)

A compreensão de listas em Python possui uma sintaxe fácil e compacta para criação de listas, seja a partir de uma string ou de outra lista. É uma maneira concisa de criação que executa uma operação em cada item da lista já existente.

Quando uma nova lista é criada como resultado de uma iteração, podemos simplificar utilizando [**compreensão de listas**](https://pt.wikipedia.org/wiki/Compreens%C3%A3o_de_lista).

```python
min_rating = 3.0
filtered_restaurants = [
    restaurant
    for restaurant in restaurants
    if restaurant["nota"] > min_rating
]
print(filtered_restaurants)  # imprime a lista de restaurantes, sem o B e D
```

A **compreensão de listas** é declarada da mesma maneira que uma lista comum, porém no lugar dos elementos nós colocamos a iteração que vai gerar os elementos da nova lista.

De olho na dica👀: É possível filtrar esses elementos utilizando o `if`.

Poderíamos listar também somente o nome dos restaurantes, veja o exemplo abaixo:

```python
# min_rating = 3.0
filtered_restaurants = [restaurant["name"]]  # aqui pedimos somente o nome do restaurante
#                        for restaurant in restaurants
#                        if restaurant["nota"] > min_rating]
# print(filtered_restaurants)  # imprime a lista de restaurantes, sem o B e D
```

![Conversão de loop for em compreensão de listas](https://content-assets.betrybe.com/prod/7e49b5dc-3177-4ba7-a935-ed0be6668ad4-Convers%C3%A3o%20de%20loop%20for%20em%20compreens%C3%A3o%20de%20listas.gif)
Conversão de loop for em compreensão de listas

A compreensão de listas também funciona com listas de strings. A seguinte cria uma nova lista de strings com os nomes que contém a letra ‘a’.

```python
names_list = ['Duda', 'Rafa', 'Cris', 'Yuri']
new_names_list = [name for name in names_list if 'a' in name]

# Aqui o for percorre cada nome em "names_list", verifica se existe a letra "a" nele,
# o adiciona à variável "name", e então gera nossa nova lista "new_names_list"
print(new_names_list)

# Saída
['Duda', 'Rafa']
```

O exemplo a seguir usa uma compreensão de listas para criar uma lista com o quadrado dos números entre 1 e 10.

Copiar

```python
quadrados = [x*x for x in range(11)]
print(quadrados)

# Saída
[0, 1, 4, 9, 16, 25, 36, 49, 64, 81, 100]
```

Isto é equivalente às operações de `map` e `filter` em JavaScript.

## while

🔢 Com o `while` nós podemos executar um conjunto de declarações enquanto a condição for verdadeira.

No código abaixo mostramos uma implementação da **Sequência de Fibonacci**, presente em diversas formas na natureza. Ela é uma sequência numérica começando por 0 e 1, e cada termo subsequente corresponde à soma dos dois anteriores.

Podemos escrever esta sequência da seguinte maneira:

```python
n = 10
last, next = 0, 1
while last < n:
    print(last)
    last, next = next, last + next
```

O laço de repetição `while` acontecerá enquanto a condição for satisfeita.

⚠️ Aviso: Tenha cuidado ao manipular a variável presente na condicional ou entrará em uma repetição infinita.

No exemplo anterior, estamos imprimindo os elementos da sequência até que atinja o valor 10. Neste caso, foi utilizado um truque chamado atribuição múltipla. Isto é, atribuição de vários valores a múltiplas variáveis ao mesmo tempo.

De olho na dica👀: Este truque pode ser utilizado também para fazer a troca de valores entre variáveis: `a, b = b, a`.

## enumerate

Em Python, um loop `for` geralmente é escrito como um loop sobre um objeto iterável. Isso significa que você não precisa de uma variável de contagem para acessar itens no iterável.

Porém, às vezes, pode acontecer de você querer uma variável que muda em cada iteração do loop. Em vez de criar e incrementar uma variável você mesmo, você pode usar `enumerate()` do Python para obter um contador e o valor do iterável ao mesmo tempo!

Copiar

```python
languages = ['Python', 'Java', 'JavaScript']

enumerate_prime = enumerate(languages)

# converte um objeto enumerate em uma lista
print(list(enumerate_prime))

# Saída: [(0, 'Python'), (1, 'Java'), (2, 'JavaScript')]
```

Você também pode desestruturar (_unpack_) os itens da lista ou tupla:

```python
languages = ['Python', 'Java', 'JavaScript']

for index, language in enumerate(['Python', 'Java']):
    print(f'{index} - {language}')
# Saída:
0 - Python
1 - Java
```

👀 **De olho na dica:** na linha 4 você deve ter reparado algo diferente neste código `print(f'{index} - {language}')`. A letra `f` usada dentro do `print` é chamada de [f-string](https://peps.python.org/pep-0498/). Ela fornece uma maneira de incorporar expressões dentro de strings literais, usando uma sintaxe mínima. Lembre-se de ler a documentação em caso de dúvidas.

Veja a [documentação do enumerate](https://docs.python.org/pt-br/3/library/functions.html#enumerate) para entender como essa ferramenta pode te ajudar em outros casos.

Para fixar as estruturas de repetição, vamos continuar resolvendo alguns exercícios!? 💪

## Exercício 12

O Fatorial de um número inteiro é representado pela multiplicação de todos os números predecessores maiores que 0. Por exemplo, o fatorial de 5 é 120 pois `5! = 1 * 2 * 3 * 4 * 5`. Escreva um código que calcule o fatorial de um número inteiro.

```python
number = 5
counter = 1
result = 1

while counter <= number:
    result = result * counter
    counter += 1
result
```

ou 

```python
number = 5
result = 1
# Usamos number + 1 pro range ir até o number
for factor in range(1, number+1):
    result *= factor
result
```

## Exercício 13

Um sistema de avaliações registra valores de 0 a 10 para cada avaliação. Após algumas mudanças estes valores precisam ser ajustados para avaliações de 0 a 100. Dado uma sequência de avaliações `ratings = [6, 8, 5, 9, 10]`. Escreva um código capaz de gerar as avaliações após a mudança. Neste caso o resultado deveria ser `[60, 80, 50, 90, 100]`.

```python
ratings = [6, 8, 5, 9, 10]
new_ratings = []

for rating in ratings:
    new_ratings.append(rating * 10)

new_ratings
```

## Exercício 14

Percorra a lista do exercício 13 e imprima “Múltiplo de 3” se o elemento for divisível por 3.

```python
ratings = [6, 8, 5, 9, 10]

for rating in ratings:
    # o sinal % representa a operação "resto".
    if (rating % 3) == 0: # se o resto é zero, é divisível
        print(f'{rating} é múltiplo de 3')
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/bee44ac6-0782-48cd-9ce8-1811980e558e/lesson/e26f6616-26e5-442c-b6f2-8cd7868035f1/solution)
