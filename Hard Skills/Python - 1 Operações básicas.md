[[Python]]

Bora praticar algumas operações básicas como adições e multiplicações!? Digite as operações abaixo e veja que o valor será avaliado e impresso.

```python
2 * 3  # saída: 6
2 + 3  # saída: 5
3 / 2  # saída: 1.5
```

> De olho na dica👀: O símbolo `#` marca um comentário e deste simbolo em diante (na linha) o código não será executado.

Para atribuir esses valores a um nome, basta utilizar o operador de atribuição `=`, conforme o exemplo a seguir:

```python
square_root = 25 ** (1/2)  # raiz quadrada de 25. O operador `**` significa "elevado a"

print(square_root + 1)  # saída: 6.0
```

⚠️ Aviso: Não é necessário a utilização de `let`, `var` ou `const` nas atribuições. Veremos escopo e questões de mutabilidade mais adiante.

> Você pode estar se perguntando:”Existe algum operador que **Python** não tenha? E se eu tentar incrementar um valor?”

Observe o que vai acontecer no exemplo a seguir:

```python
counter = 0
counter++  # esse código vai falhar
```

Ainda que possamos simplificar operações e atribuições, o incremento ou decremento não é válido na sintaxe `++`, `--`.

Um exemplo de simplificação válida é:

```python
# original
counter = counter + 1

# simplificado
counter += 1
```

Um outro operador um pouco diferente é o `//`. Consegue imaginar para sua função dentro do código?🤔 Observe o exemplo abaixo:

Copiar

```python
3 // 2  # saída: 1

3 / 2  # saída: 1.5
```

Note que o operador `//` realiza a divisão e arredonda o resultado para baixo. Ou seja, realiza o **quociente**.

> Você pode estar pensando:” Hummm… Tirando o `let` e `var`, ainda parece bem similar com _Javascript_“.🤔

De fato, operadores são comuns a todas as linguagens de programação. Entretanto, nem tudo é semelhante. Por exemplo, se realizarmos a operação de comparação `'1' == 1`, o resultado será falso (`False`), pois como são valores de tipos diferentes, nenhuma conversão é realizada.

Dado as listas `a = [1, 2, 3]` e `b = [1, 2, 3]`, se compararmos as duas `a == b` teremos como retorno `True` porque as listas possuem os mesmos valores e na mesma ordem.

Viu como `"Python" != "Javascript"`, mesmo que ambas tenham operadores básicos similares?

> Mas e sobre os operadores `&&` e `||`, não são operações de `and` e `or`?
> 
> Resposta: Quando queremos fazer operações lógicas, como verificar se uma temperatura está entre dois valores, utilizamos o operador `and`. Ou seja, para verificar se uma temperatura é menor que 25 graus **e** maior que 18 graus, podemos fazer algo como `temperatura < 25 and temperatura > 18`. Embora uma maneira mais pythonica de se escrever esta operação seja `18 < temperatura < 25`. 🤓
> 
> Da mesma forma, podemos validar intervalos utilizando o operador `or`. Por exemplo, se em um parque pessoas com idade menor ou igual a 5 e maiores de 65 anos não pagam, poderíamos escrever uma validação da seguinte maneira `idade <= 5 or idade >= 65`.

Chegou a hora de colocar a mão no código, ou seja, praticar!💪

## Exercício 1:

No terminal, inicialize duas variáveis a e b, sendo `a = 10` e `b = 5`. Mostre o resultado das 7 operações básicas (soma, subtração, multiplicação, divisão, divisão inteira, potenciação e módulo) envolvendo essas variáveis.

```python
a = 10
b = 5
a + b
a - b
a * b
a / b
a // b
a ** b
a % b
```

## Exercício 2:

Declare e inicialize uma variável: `hours = 6`. Quantos minutos têm em 6 horas? E quantos segundos? Declare e inicialize variáveis `minutes` e `seconds` que recebem os respectivos resultados das contas. Depois, imprima cada uma delas.

```python
hours = 6
minutes = hours * 60
seconds = minutes * 60
minutes
seconds
```

## Exercício 3:

Teste e verifique o que acontece se você colocar um ponto e vírgula no final de uma instrução em Python.

## Exercício 4:

Suponha que o preço de capa de um livro seja R$ 24,20, mas as livrarias recebem um desconto de 40%. O transporte custa 3,00 para o primeiro exemplar e 75 centavos para cada exemplar adicional. Qual é o custo total de atacado para 60 cópias? Escreva uma expressão que receba o custo total e a imprima.

```python
books = 60
book_price = (1 - 0.4) * 24.20
logistic = 3 + (books - 1) * 0.75
cost = books * book_price + logistic
cost
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/bee44ac6-0782-48cd-9ce8-1811980e558e/lesson/71641e4a-4804-43d0-b91c-b6c0c8724013/solution)
