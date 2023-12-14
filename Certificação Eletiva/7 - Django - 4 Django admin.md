
[[7 - Django]]

O **Django admin** é uma ferramenta que permite a criação de um painel de administração para o projeto. Com ele, é possível visualizar, criar, editar e excluir objetos do banco de dados (o famoso **CRUD**), sem a necessidade de escrever código.

Lembra da rota `'/admin'` que você viu no arquivo `urls.py`? Ela é mais um exemplo dos recursos prontos para uso que o Django oferece, pois é ela que permite o acesso ao painel de administração do projeto.

Se você acessar agora mesmo [localhost:8000/admin](http://localhost:8000/admin), verá que já existe um painel de administração criado. Ele exige, porém, um login, e não temos uma autenticação de admin configurada para o nosso projeto. Faremos essa configuração agora!

## Criando um superusuário

A primeira coisa que devemos fazer é criar um superusuário para o projeto. Esse perfil terá permissões administrativas,ou seja, poderá acessar o painel de administração e realizar qualquer operação.

Para criar um superusuário, na raiz do projeto, execute o comando:

Copiar

```bash
1python3 manage.py createsuperuser
```

Será precisará informar um nome de usuário, e-mail e senha. Preencha os dados e, em seguida, acesse [localhost:8000/admin](http://localhost:8000/admin) e faça login com os dados de superusuário que você criou. Você verá uma página como esta:

![Página inicial do painel de administração do Django](https://content-assets.betrybe.com/prod/e4cf1ac4-33ad-4ddb-a0ad-96fc462c61d4-P%C3%A1gina%20inicial%20do%20painel%20de%20administra%C3%A7%C3%A3o%20do%20Django.png)Página inicial do painel de administração do Django

## Registrando o modelo

Para que o Django admin funcione, é preciso registrar os modelos criados no arquivo `admin.py`, dentro da pasta do _app_. Fazer isso é bem simples: abra o arquivo `ecommerce/products/admin.py` e adicione o código:

```python
from django.contrib import admin
from products.models import Product
from products.models import Customer # Modelo criado no exercício de fixação


admin.site.register(Product)
admin.site.register(Customer)

```

Já que estamos alterando este arquivo, que tal mudarmos também o cabeçalho do painel? Para isso, basta adicionar a linha no arquivo `ecommerce/products/admin.py`:

```diff
from django.contrib import admin
from products.models import Product


+ admin.site.site_header = "Trybe Products E-commerce"
admin.site.register(Product)
admin.site.register(Customer)
```

Agora, ao recarregar a página ou fazer login com seus dados de superusuário, você verá uma página como esta:

![Painel de administração do Django com as tabelas](https://content-assets.betrybe.com/prod/e4cf1ac4-33ad-4ddb-a0ad-96fc462c61d4-Painel%20de%20administra%C3%A7%C3%A3o%20do%20Django%20com%20as%20tabelas.png)Painel de administração do Django com as tabelas

Nessa página você pode consultar os dados que já existem no banco de dados, criar novos objetos, editar e excluir objetos existentes. Vamos fazer um teste?

Clique em **Products**, depois no botão **ADD PRODUCT** (canto superior direito da página), preencha os campos com um novo produto e clique em **SALVAR**. Você verá uma página como esta:

![Página de produtos](https://content-assets.betrybe.com/prod/e4cf1ac4-33ad-4ddb-a0ad-96fc462c61d4-P%C3%A1gina%20de%20produtos.png)
Página de produtos

Muito legal, né?! Viu só o quanto você já conseguiu fazer utilizando o Django e com tão pouco código?! E este é apenas o início! 🚀

Repare que, quando você abre a tabela, o nome do objeto não aparece certinho - aparece um `Product object (N)`. Isso se dá porque o Django chama a função `__str__` de uma classe ao exibi-la lá. Se você sobrescrever sua implementação padrão com uma específica, pode controlar como aquela visualização fica.

Teste colocar a função abaixo no arquivo `ecommerce/products/models.py`:

```diff
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

+    def __str__(self):
+        return f'{self.name} - {self.price}'


class Customer(models.Model):
    name = models.CharField(max_length=50)
    address = models.CharField(max_length=200)
    phone = models.CharField(max_length=20)

```

Veja como fica!

---

## Para fixar

3. Utilizando o painel de administração do Django, altere o produto cadastrado **Moka** para que ele tenha o preço de **R$ 99,99**.

### Solução

**Primeiro passo:** acesse o painel de administração do Django em [localhost:8000/admin](http://localhost:8000/admin) e faça login com os dados de superusuário que você criou.

**Segundo passo:** No painel de controle, clique em `Products` e, em seguida, em `Product object (1)`, uma vez que o produto Moka foi o primeiro que cadastramos.

**Terceiro passo:** Na página de edição do produto, altere o campo `price` para **99.99** e clique em `SALVAR`.


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/32af04d3-9bec-4278-85dd-bee03a8887fb/lesson/150266e7-e435-4253-b738-187f20c3f373)

