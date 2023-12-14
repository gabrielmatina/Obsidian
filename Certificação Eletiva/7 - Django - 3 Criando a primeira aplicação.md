[[7 - Django]]

No Django, temos o conceito de **projeto** e de **aplicação (ou app)**. Um projeto pode ser descrito como a estrutura geral que abrange todas as configurações e aplicações relacionadas a ele. Já a aplicação é um componente reutilizável que tem uma função específica dentro do projeto.

De forma resumida, todas as aplicações (componentes reutilizáveis tipo os de React), que estão registradas na variável `INSTALLED_APPS`, do arquivo `settings.py` fazem parte do projeto.

Por exemplo, podemos ter um projeto de uma loja virtual que tenha uma aplicação de autenticação, uma aplicação de cadastro de produtos, uma aplicação de cadastro de clientes, etc.

Já criamos nosso projeto, agora chegou a hora de criar nossa primeira aplicação!

Vamos começar voltando no arquivo `settings.py` e adicionando o app que iremos criar à lista preexistente:

```diff
# ecommerce/ecommerce/settings.py

INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
+    "products",
]
```

Com isso feito, é hora de efetivamente criar o _app_. O comando é similar ao utilizado para criar o projeto, mas agora vamos utilizar `startapp` em vez de `startproject`:

```bash
django-admin startapp products
```

Observe que um diretório com o nome da nossa aplicação (`products`) foi criado e a estrutura de diretórios agora passa a ser:

```bash
ecommerce
│   ├── .venv
│       └── ...
│   ├── database
│       └── ...
│   ├── ecommerce
│       └── ...
│   ├── products
│       └── ...
├──  Dockerfile
├──  manage.py
```

No diretório da aplicação **products** já existem alguns arquivos. O primeiro deles que iremos ajustar é o `models.py`, que é onde definimos nossos modelos de dados.

Nele, vamos criar uma classe chamada `Product`, que será o modelo de dados que representa um produto em nosso sistema. Para isso, vamos importar o módulo `models` do Django e criar uma classe que herda de `models.Model` e, em seguida definiremos os campos que nosso modelo terá criando os atributos da classe `Product`:

```python
# ecommerce/products/models.py

from django.db import models


class Product(models.Model):
    name = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=10, decimal_places=2)
    amount = models.IntegerField(default=0)
    description = models.TextField()
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    image = models.ImageField(
      upload_to="media/products", null=True, blank=True
    )

```

Perceba que utilizamos os tipos de dados do Django para definir os campos do nosso modelo. Aqui utilizamos:

- `CharField` para campos de texto curtos, passando a opção `max_length` para definir o tamanho máximo do campo;
- `DecimalField` para campos de números decimais, passando as opções `max_digits` e `decimal_places` para definir o número máximo de dígitos e o número de casas decimais, respectivamente;
- `IntegerField` para campos de números inteiros, passando a opção `default` para definir um valor padrão para o campo;
- `TextField` para campos de texto longos;
- `DateTimeField` para campos de data e hora, passando as opções `auto_now_add` e `auto_now` para definir que o campo deve ser preenchido automaticamente com a data e hora atual quando o objeto for **criado** e **atualizado**, respectivamente;
- `ImageField` para campos de imagens, passando as opções `upload_to` para definir o diretório em que as imagens serão salvas, `null=True` para permitir que o campo seja nulo e `blank=True` para permitir que o campo seja vazio.

Estes são só alguns dos tipos e opções disponíveis, por isso, recomendamos que você dê uma espiada na [documentação oficial](https://docs.djangoproject.com/en/3.2/ref/models/fields/) para saber mais. 😉

Provavelmente você se deparou, no servidor em execução, com o erro `products.Product.image: (fields.E210) Cannot use ImageField because Pillow is not installed.` A solução para ele é indicada na própria mensagem do erro: basta instalar essa biblioteca no projeto com o comando `python -m pip install Pillow`.

> **Anota aí 📝:** Pillow é um pacote Python que adiciona suporte para imagens ao Django. Ele é necessário para que possamos utilizar o campo `ImageField`.

Depois da instalação, é só rodar novamente o servidor (`python3 manage.py runserver`) para ver que tudo voltou a funcionar corretamente. Com isso feito, prontinho, o modelo de dados foi criado! 🎉 Mas e agora? Como informar ao Django que queremos que ele crie uma tabela no banco de dados para armazenar esses dados do modelo? 🤔

A resposta é muito simples: precisamos criar uma _migration_ e executá-la:

```bash
python3 manage.py makemigrations
python3 manage.py migrate
```

> Lembre-se de executar os comandos acima dentro do diretório em que se encontra o arquivo `manage.py`. 😉

O primeiro comando (`makemigrations`) cria um arquivo de _migration_ - resumidamente, são as instruções para a criação da tabela no banco de dados. Ele já olha para o seu _model_ e cria a _migration_ pra você! Já o segundo comando (`migrate`) executa as migrações, ou seja, usa as instruções do arquivo de _migration_ e cria a tabela no banco de dados.

Repare que um novo arquivo foi criado no diretório `ecommerce/products/migrations`. Ao abrir o arquivo `0001_initial.py` você verá que ele contém as instruções para a criação da tabela no banco de dados.

Com isso, nosso modelo de dados foi criado no banco de dados! 🎉

Que tal abrir o Workbench e verificar se a tabela foi criada corretamente? 😉

## Inserindo dados no banco de dados via terminal

Podemos inserir dados no banco de dados de diversas formas, mas hoje faremos isso de uma maneira nova: por meio do terminal do Django. O comando para acessar o terminal é:

```bash
python3 manage.py shell
```

Uma vez dentro do terminal, podemos importar o modelo que criamos:

```python
from products.models import Product
```

A partir disso, podemos criar um novo objeto e salvá-lo no banco de dados:

```python
moka = Product(name="Moka - 6 xícaras", price=199.99, amount=10, description="Cafeteira italiana, serve 6 xícaras, não elétrica")
moka.save()
```

Prontinho! Agora já temos o primeiro produto no banco de dados! 🎉

## Para fixar

1. Crie uma tabela para clientes do _e-commerce_. Ela deve se chamar `Customers` e ter os seguintes campos:
    - `name`: texto, tamanho máximo de 50 caracteres;
    - `address`: texto, tamanho máximo de 200 caracteres;
    - `phone`: texto, tamanho máximo de 20 caracteres.

2. Insira na tabela `Customers` os seguintes dados:
    - `name`: “Saul Goodman”, `address`: “Rua Better Call Saul, 123”, `phone`: “(85) 99998-9999”;
    - `name`: “Mike Ehrmantraut”, `address`: “Quadra 54, casa 66”, `phone`: “(21) 99988-8888”;
    - `name`: “Kim Wexler”, `address`: “Rua Mesa Verde, 5”, `phone`: “(51) 99977-7979”.

### Solução

**Primeiro passo**: crie uma classe que represente um modelo para a tabela `Customers`, dentro do arquivo `ecommerce/products/models.py`:

```python
class Customer(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=200)
    phone = models.CharField(max_length=20)

```

**Segundo passo**: crie a migration:

```bash
python3 manage.py makemigrations
```

**Terceiro passo**: execute a migration:

```bash
python3 manage.py migrate
```

2. Insira na tabela `Customers` os seguintes dados:
    - `name`: “Saul Goodman”, `address`: “Rua Better Call Saul, 123”, `phone`: “(85) 99998-9999”;
    - `name`: “Mike Ehrmantraut”, `address`: “Quadra 54, casa 66”, `phone`: “(21) 99988-8888”;
    - `name`: “Kim Wexler”, `address`: “Rua Mesa Verde, 5”, `phone`: “(51) 99977-7979”.

#### Solução

Para adicionar os dados usando o terminal do Django:

**Primeiro passo**: entre no terminal com o comando:

```bash
python3 manage.py shell
```

**Segundo passo**: importe o modelo criado para a tabela `customers`:

```python
from products.models import Customer
```

**Terceiro passo**: crie o primeiro objeto solicitado e salve-o no banco de dados:

```python
saul = Customer(name="Saul Goodman", address="Rua das Flores, 123", phone="(85) 99998-9999")
saul.save()
```

**Quarto passo**: crie o segundo objeto solicitado e salve-o no banco de dados:

```python
mike = Customer(name="Mike Ehrmantraut", address="Quadra 54, casa 66", phone="(21) 99988-8888")
mike.save()
```

**Quinto passo**: crie o terceiro objeto solicitado e salve-o no banco de dados:

```python
kim = Customer(name="Kim Wexler", address="Rua Mesa Verde, 5", phone="(51) 99977-7979")
kim.save()
```

**Sexto passo**: verifique se os dados estão corretor pelo Workbench.


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/32af04d3-9bec-4278-85dd-bee03a8887fb/lesson/56ce5db5-a753-44b7-bd98-9d8c874abedc)
