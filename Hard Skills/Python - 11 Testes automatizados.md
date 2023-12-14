[[Python]]

Quem nunca arrumou um problema em um código e acabou atrapalhando o funcionamento dele em outro cenário? Ou ficou horas testando as mais diversas condições para um algoritmo e no meio do caminho teve de mexer no código e recomeçar tudo novamente. 😁

Através de testes automatizados, a pessoa desenvolvedora é capaz de identificar mais facilmente um _bug_ ou verificar que alterações do código não afetaram o comportamento esperado do sistema.

Em nosso curso utilizaremos a biblioteca [`pytest`](https://docs.pytest.org/en/latest/), um _framework_ que facilita a escrita de testes simples, mas capazes de testar funcionalidades complexas em aplicações e bibliotecas.

⚠️ Lembre-se de instalar a biblioteca somente no ambiente virtual do seu projeto.

A instalação é feita através do `pip` utilizando o comando:

```bash
python3 -m pip install pytest
```

E podemos verificar utilizando o comando:

```bash
python3 -m pytest --version
```

A saída esperada é similar à apresentada abaixo.

```bash
This is pytest version 5.3.0, imported from /home/cassiobotaro/projects/gerenciador-tarefas/.venv/lib/python3.8/site-packages/pytest.py
```

📝 **Que tal vermos um exemplo?**

> codigo.py

```python
def is_odd(number):
    'Retorna True se um número é ímpar, senão False.'
    return number % 2 != 0
```

> test_codigo.py

```python
from codigo import is_odd


def test_is_odd_when_number_is_odd_returns_true():
    'Para um número ímpar a função deve retornar o valor True'
    assert is_odd(3) is True


def test_is_odd_when_number_is_even_returns_false():
    'Para um número par a função deve retornar o valor False'
    assert is_odd(2) is False
```

Notem que o nome do arquivo de testes possui o prefixo `test_`, assim como a definição das funções de teste. Isto é necessário para que seus testes sejam descobertos pela ferramenta.

Uma função de teste é similar a qualquer outra, porém tem o propósito de verificar se o resultado obtido foi o mesmo do esperado. No código, isto pode ser visto através da utilização da palavra reservada `assert`.

O comando `assert` funciona da seguinte maneira: caso a expressão recebida seja verdadeira (avaliada como `True`), nada acontece. Porém, caso seja falsa (avaliada como `False`), uma exceção do tipo `AssertionError` é lançada. A `pytest` captura este erro e tenta apresentar uma comparação entre o esperado e o recebido da melhor maneira possível.

Vamos rodar nossos testes e ver o resultado? Vamos utilizar o comando:

```bash
python3 -m pytest
```

![Testes rodando de forma bem sucedida](https://content-assets.betrybe.com/prod/777f9861-8d94-485f-94bf-7a4a74473a9c-Testes%20rodando%20de%20forma%20bem%20sucedida.png)Testes rodando de forma bem sucedida.

> 💡 Experimente modificar estes testes para uma falha e veja o resultado.


# Testando falhas

Erros acontecem e nem sempre são inesperados. O **Python** utiliza exceções para sinalizar que ocorreram erros durante a execução de um código e que nem sempre são fatais.

Podemos escrever testes que verificam que um erro **deve** ocorrer a partir de uma determinada entrada.

📝 Um exemplo pode ser visto abaixo:

> codigo.py

```python
# ...

def divide(a_number, other_number):
    "Retorna a divisão de a_number por other_number"
    return a_number / other_number
```

> test_codigo.py

```python
import pytest
from codigo import is_odd, divide

# ...

def test_divide_when_other_number_is_zero_raises_an_exception():
    with pytest.raises(ZeroDivisionError, match="division by zero"):
        divide(2, 0)
```

Utilizamos a função `raises` da `pytest` para verificar se a exceção ocorreu. Caso contrário, ela lança um `AssertionError`, indicando que o teste não passou. Podemos verificar também se o texto retornado na exceção é o esperado através do parâmetro `match`, que pode receber uma expressão regular. No exemplo, temos uma divisão por zero, que lança a exceção esperada e o teste passa com sucesso.

# Evoluindo nossos testes

O ecossistema de testes do Python é extremamente poderoso e ainda nos fornece diversas outras ferramentas interessantes:

- `pytest.fixture`s: são funções que podemos criar e serão executadas antes e/ou depois dos nossos testes. Podemos usá-las para inicializar ou limpar um banco de dados, criar valores de mock reutilizáveis, capturar logs do terminal e muito mais! Se quiser aprofundar, [veja a documentação oficial](https://docs.pytest.org/en/6.2.x/fixture.html).
- `unittest.mock`: é uma biblioteca nativa que oferece diversas formas de simular comportamentos que queremos testar. São úteis quando estamos fazendo testes unitários e não queremos depender do funcionamento de outras partes da aplicação (como testar um _Service_ sem depender de sua _Model_). Para aprofundar, [veja a documentação oficial](https://docs.python.org/pt-br/3/library/unittest.mock.html)!
- `hypothesis` e `faker`: são 2 exemplos de bibliotecas externas (precisam ser instaladas com o `pip`) que nos ajudam a criar diversos valores de teste. A biblioteca `hypothesis` ([documentação aqui](https://hypothesis.readthedocs.io/en/latest/)) nos ajuda executando um teste para diversos valores a partir de uma regra como “_números pares_“ ou “_dicionários com a chave ‘name’ sendo uma string_“. Já a `faker` ([documentação aqui](https://faker.readthedocs.io/en/master/)) possui diversos geradores mais complexos (_ex: email, telefone, endereço, CPF, cartão de crédito, etc._), e tudo isso com poucas linhas de código!


## Exercício 1

Utilizando o arquivo [pokemons.json](https://lms-assets.betrybe.com/lms/pokemons.json), vamos escrever um programa que sorteie um pokemon aleatoriamente. O programa deve perguntar à pessoa usuária “Quem é esse pokemon?” até que ela o acerte. A cada erro, apresente um número de letras do nome daquele pokemon igual ao número de erros.

Exemplo: O pokemon sorteado pelo programa é `butterfree`; a pessoa usuária chuta `charizard`; o programa deve exibir `b`. Em seguida, a pessoa chuta `blastoise`; o programa deve exibir `bu`; e assim por diante até a pessoa acertar.

🦜 Você pode utilizar o método `choice` do modulo `random` para sortear aleatoriamente um pokemon. Ex: `random.choice(pokemon)` -> `{"name": "Pikachu" }`.

```python
import json
import random


def shot(pokemon_name):
    wrong_shot = True
    num_of_shots = 0
    while (wrong_shot):
        num_of_shots += 1
        shot = input("Quem é esse pokemon? ")
        if (shot == pokemon_name):
            print("Você acertou! Parabéns!")
            break
        elif num_of_shots == len(pokemon_name):
            print("Você errou!")
            break
        else:
            print("Dica: ", end="")
            for i in range(0, num_of_shots):
                print(pokemon_name[i], end="")
            print("")


if __name__ == "__main__":
    with open("pokemons.json") as file:
        pokemons = json.load(file)["results"]
        pokemon = random.choice(pokemons)
        pokemon_name = pokemon["name"]

    shot(pokemon_name)
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/61e88b4a-b97a-4f96-b5a0-abaa50651e37/lesson/a0596a77-23ac-4fd4-82c0-257fa72b1b16)
