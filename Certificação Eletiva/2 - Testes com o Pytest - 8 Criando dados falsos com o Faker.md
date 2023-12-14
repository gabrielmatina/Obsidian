[[2 - Testes com o Pytest]]

Olha que incrível: você já sabe como testar o seu código Python e como calcular a cobertura de testes! 🎉

Contudo, como você já deve ter percebido, escrever testes pode ser um pouco trabalhoso. Além de criar a lógica para o teste, você ainda precisa criar os dados que serão usados para testar o seu código. Por isso, é importante que você saiba como criar dados falsos para testar seu código.

## O que são dados falsos, e por que usá-los

Por exemplo, suponha que você precisa testar uma função que cadastra uma pessoa como usuária de uma aplicação. Para fazer esse cadastro, você precisa de dados da pessoa que será cadastrada, como nome e CPF, por exemplo.

Então, como fazer um teste dessa função sem comprometer dados de uma pessoa de verdade? A resposta para isso é simples: você precisa criar **dados falsos**, cujo intuito é unicamente a realização de testes.

A maior vantagem de usar dados falsos é que você tem controle sobre os dados que serão usados para testar o seu código.

Retomando o exemplo, se você precisa testar uma função que cadastra uma pessoa, você pode criar um cadastro falso com um e-mail válido, uma senha válida, um nome válido, etc.

## Como criar dados falsos

Para criar dados falsos, você pode usar a biblioteca [Faker](https://faker.readthedocs.io/en/master/). Ela é muito útil porque permite que a criação de dados estruturados (_nome, e-mail, senha, endereço, cartão de crédito, etc._) falsos, de forma simples e rápida.

Para instalar a biblioteca `Faker`, você pode usar o comando `pip install faker==18.9.0` no terminal. Depois da instalação, você deve importá-la no seu código usando o comando `from faker import Faker`.

Em seguida, você pode usar o método `name()` da biblioteca `Faker` para criar nomes falsos. Crie um arquivo chamado `faker_names.py` com o trecho de código a seguir para ver funcionando em sua máquina! 😉

```python
from faker import Faker


faker = Faker()

print(faker.name())
print(faker.name())
print(faker.name())
print(faker.name())

```

Use o comando `python3 faker_names.py` para executar o código. Observe como os nomes falsos são retornados como strings que representam nomes válidos, mas que, na verdade, não estão associados a nenhuma pessoa real.

Executando o código, é possível observar que em cada `print` é exibido um nome diferente, e, em cada execução do código, os nomes são diferentes.

![Resultado da execução de múltiplos `faker.name()`](https://content-assets.betrybe.com/prod/c400e8fd-1d50-4b02-b870-1279453e8bfa-Resultado%20da%20execu%C3%A7%C3%A3o%20de%20m%C3%BAltiplos%20%60faker.name()%60.png)
Resultado da execução de múltiplos `faker.name()`

### Garantindo previsibilidade com _seed_

Uma discussão comum em geradores de dados é a **previsibilidade**, ou seja, “como eu sei que o comportamento do meu código será `X` ou `Y`“?

Na criação de testes, ocorre o mesmo problema. Com dados falsos que são criados aleatoriamente, os testes podem falhar ou passar de forma aleatória. Escrever bons testes é uma tarefa difícil e ter testes que passam ou falham de forma aleatória é um pesadelo! 😱

Para resolver esse problema, a biblioteca `Faker` permite a definição de uma _seed_ para os dados falsos que você cria.

> **Anota aí 📝:** A **seed** (semente) é um valor que serve como base para a geração de números aleatórios. Se a mesma _seed_ for usada, teremos sempre a mesma sequência de resultados. Por padrão, o `Faker` usa o _timestamp_ atual do sistema e por isso cada execução resulta em valores diferentes.

```python
from faker import Faker


faker = Faker()

Faker.seed(0)  # repare que usamos a classe 'Faker', e não a instância 'faker'

# O restante do código permanece igual
```

> **De olho na dica 👀:** o valor `0` é arbitrário, ou seja, você pode usar qualquer valor que quiser! A biblioteca `Faker` aceita valores dos tipos `int`, `float`, `str`, `bytes` e `bytearray`.

## Escolhendo o tipo de informação que você quer criar

Além de criar nomes falsos, a biblioteca `Faker` também permite a criação de diversos outros tipos de informação! 🤩

Cada “gerador de informações” é chamado de _**provider**_. A lista completa de _providers_ oficiais da biblioteca `Faker` está disponível [aqui](https://faker.readthedocs.io/en/master/providers.html), mas vale destacar alguns exemplos, como:

- `faker.email()`: cria um e-mail falso;
- `faker.password()`: cria uma senha falsa;
- `faker.address()`: cria um endereço falso;
- `faker.credit_card_number()`: cria um número de cartão de crédito falso;
- `faker.phone_number()`: cria um número de telefone falso;
- `faker.company()`: cria um nome de empresa falso;
- `faker.date()`: cria uma data falsa;
- `faker.cpf()`: cria um CPF falso.

> **De olho na dica 👀:** você pode criar seu próprio _provider_! [Aqui](https://faker.readthedocs.io/en/master/communityproviders.html) você pode conferir _providers_ escritos por outras pessoas da comunidade (e não pelo time oficial do `Faker`).

Que tal ajustar o código anterior para criar, além de um nome falso, também um telefone e um CPF falsos?

```python
from faker import Faker


faker = Faker()

print(faker.name())
print(faker.phone_number())
print(faker.cpf())
```

Quando esse código é executado… Oops! `AttributeError` 😱

> O que aconteceu? Por que o código quebrou? Você mentiu para mim? 😭

Calma, calma! Não se preocupe! A classe `Faker` não tem todos os recursos habilitados por padrão.

Nesse caso, o código falhou ao tentar usar o _provider_ `cpf()`, que só existe no _locale_ `pt_BR`. Para resolver esse problema, basta passar o parâmetro `locale` para o construtor da classe `Faker`:

```diff
# ...

- faker = Faker()
+ faker = Faker(locale='pt_BR')

# ...
```

Ao executar o código novamente, você verá que ele funciona! 🎉

![Resultado da execução de múltiplos _providers_](https://content-assets.betrybe.com/prod/c400e8fd-1d50-4b02-b870-1279453e8bfa-Resultado%20da%20execu%C3%A7%C3%A3o%20de%20m%C3%BAltiplos%20_providers_.png)
Resultado da execução de múltiplos _providers_

Olha que legal: além do CPF, agora temos nomes e telefones brasileiros! 🇧🇷

## Para fixar

## Exercício 2

Utilize a biblioteca `Faker` para criar os seguintes dados falsos no `locale` espanhol da Argentina, e exibi-los na tela:

- Sobrenome.
- E-mail.
- Senha.
- URL.
- Placa de carro.

> **De olho na dica 👀:** O locale `es_AR` é o locale espanhol da Argentina.


### Solução

Uma possível solução para o exercício é:


```python
from faker import Faker


faker = Faker(locale='es_AR')

print(faker.last_name())
print(faker.email())
print(faker.password())
print(faker.url())
print(faker.license_plate())
```

Os _providers_ usados podem ser encontrados aqui:

- [Sobrenome](https://faker.readthedocs.io/en/master/providers/faker.providers.person.html#faker.providers.person.Provider.last_name)
- [E-mail](https://faker.readthedocs.io/en/master/providers/faker.providers.internet.html#faker.providers.internet.Provider.email)
- [Senha](https://faker.readthedocs.io/en/master/providers/faker.providers.misc.html#faker.providers.misc.Provider.password)
- [Url](https://faker.readthedocs.io/en/master/providers/faker.providers.internet.html#faker.providers.internet.Provider.url)
- [Placa de carro](https://faker.readthedocs.io/en/master/providers/faker.providers.automotive.html#faker.providers.automotive.Provider.license_plate)

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/7a9abd0d-1642-4b80-80c8-4c0dfa8e0470/lesson/8dd3100b-1ae4-4029-8e93-21591596c9ba)
