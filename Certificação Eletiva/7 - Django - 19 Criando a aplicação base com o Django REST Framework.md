[[7 - Django]]

## Caso de uso

Você está desenvolvendo um sistema web para uma empresa que organiza casamentos. Essa empresa quer oferecer a clientes uma plataforma web completa onde ela pode selecionar fornecedores de bens e serviços - decoração, bebidas, buffet, e por aí vai - e montar um orçamento personalizado dinamicamente.

Sua primeira tarefa é construir uma aplicação base em Django que possa fazer operações de C.R.U.D. num banco de dados via requisições. O banco de dados deve relacionar fornecedores (`vendors`) com orçamentos (`budgets`), e cada orçamento deve pertencer a um casamento (`marriage`) e nenhum casamento deve existir sem um orçamento associado. Para ter velocidade na construção desse MVP você usará o _Django REST Framework_. Com a aplicação pronta vamos fazer, como **prova de conceito**, várias autenticações na aplicação. O objetivo é somente `admins` poderem cadastrar fornecedores mas cada pessoa usuária poder criar - e operar sobre - seus casamentos e orçamentos.

Bora lá?

## Para fixar

### Exercício 1 - Faça o passo a passo para criar a aplicação base

Reveja os conteúdos anteriores para **criar uma aplicação base**, em cima da qual acrescentaremos _models_ e _serializers_.

> **De olho na dica 👀:** sim! A intenção é fazer o passo a passo novamente. Isso traz fluência, velocidade e conforto com a ferramenta. É comum ter que conciliar testes técnicos de processos seletivos com diversos afazeres, então ter prática pra fazer essa parte rápido faz toda diferença!

Você deve, nessa ordem:

1. Criar um diretório para trabalhar e habilitar, nele, o ambiente virtual.
2. Instalar, como dependências, o Django e o _Django REST Framework_, além das dependências das quais estas necessitem.
3. Iniciar o projeto Django, a ser chamado `marryme`
4. Criar um app no projeto chamado `budget`
5. Configurar a aplicação para uso de MySQL
6. Criar um `Dockerfile` e demais arquivos necessários para subir uma imagem Docker com o banco de dados para então subi-la.
7. Fazer as _migrations_
8. Criar um _superuser_
9. Rodar o servidor e a interface de admin para garantir que tudo está funcionando.

## Models, Serializers e Viewsets

A seguir, terminaremos de desenhar nossa aplicação. Primeiramente, eis os nossos modelos:

```python
# budget/models.py

from django.db import models


# Fornecedor
class Vendor(models.Model):
    name = models.CharField(max_length=50)
    price = models.IntegerField()

    def __str__(self):
        return self.name


# Casamento
class Marriage(models.Model):
    codename = models.CharField(max_length=50)
    date = models.DateField()

    def __str__(self):
        return self.codename

    def total_value(self):
        vendors = self.budget.vendors.all()
        prices = [vendor.price for vendor in vendors]
        return sum(prices)


# Orçamento
class Budget(models.Model):
    vendors = models.ManyToManyField("Vendor", related_name="budgets")
    marriage = models.OneToOneField(
        Marriage, on_delete=models.CASCADE, related_name="budget"
        )

    def add_vendor(self, vendor):
        self.vendors.add(vendor)
        self.save()

    def remove_vendor(self, vendor):
        self.vendors.remove(vendor)
        self.save()

    def __str__(self):
        return f"{self.marriage.codename}'s budget"

```

> **De olho na dica 👀:** tome nota, aqui, de quais são os relacionamentos 1:1, N:M e quais funções cada modelo dá suporte a. Observe também a lógica da função `total_value` de `Marriage`! Veja como é útil 😉

Gere as migrações desse modelo com o `makemigrations` e as execute! Após isso, habilite os modelos para que sejam operados pelo painel de administração do Django:

```python
# budget/admin.py

from django.contrib import admin
from .models import Vendor, Marriage, Budget

admin.site.register(Vendor)
admin.site.register(Marriage)
admin.site.register(Budget)
```

Agora abra um painel, tente criar um `Vendor`. Tudo certo, né? Agora crie um `Marriage`. Repare que dá certo, mas desrespeita nossa regra de negócio! Precisamos que um casamento seja criado **junto** com um orçamento - não faz sentido casamentos existirem em nosso sistema sem esse vínculo.

Ou seja: não dá pra criar `Marriage` sem ter um `Budget`. No entanto, não podemos criar um `Budget` com antecedência e vinculá-lo ao `Marriage` ao criá-lo, porque o `Budget` também precisa obrigatoriamente de um `Marriage`! O painel de admin não sabe lidar com isso - e nossas requisições de API também não saberão. Vamos corrigir o problema? Altere o arquivo `budget/admin.py`:

```python
# budget/admin.py

from django.contrib import admin
from .models import Vendor, Budget, Marriage


class BudgetInline(admin.StackedInline):
    model = Budget


class MarriageAdmin(admin.ModelAdmin):
    inlines = [BudgetInline]


admin.site.register(Vendor)
admin.site.register(Marriage, MarriageAdmin)
```

Colocar um modelo **inline** com o outro significa ser capaz de criar as duas entidades **na mesma tela**! Nesse caso, estamos configurando o painel de admin para que permita a criação de um `Budget` dentro da tela de criação de `Marriage`. Note que, para isso, definimos duas classes: uma herdou de `admin.StackedInline` e definiu um atributo `model` com o _model_ a ficar **inline** e a outra definiu um atributo `inlines`, recebendo numa lista a classe anterior. Efetue as alterações e veja como tudo já funciona.

Agora, vamos criar os _Serializers_ e _Viewsets_! Vamos criar uma versão simples dos _serializers_. É uma versão que nos permite sair do zero pro um, mas encontraremos um problema na criação de casamentos. Em breve veremos qual!

```python
# budget/serializers.py


from rest_framework import serializers
from .models import Vendor, Marriage, Budget


class VendorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Vendor
        fields = ["id", "name", "price"]


class BudgetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Budget
        fields = ["id", "vendors", "marriage"]


class MarriageSerializer(serializers.ModelSerializer):
    budget = BudgetSerializer()

    class Meta:
        model = Marriage
        fields = ["id", "codename", "date", "budget"]

```

Agora que temos os nossos modelos para nos conectarmos com o banco de dados e os _serializers_ para tratar os dados recebidos via requisições, enviar os corretos e levantar bons erros em caso de problemas, vamos criar os _viewsets_ e as rotas para conectar nossa API com o mundo exterior.

```python
# budget/views.py


from rest_framework import viewsets
from .models import Vendor, Marriage, Budget
from .serializers import VendorSerializer, MarriageSerializer, BudgetSerializer


class VendorViewSet(viewsets.ModelViewSet):
    queryset = Vendor.objects.all()
    serializer_class = VendorSerializer


class BudgetViewSet(viewsets.ModelViewSet):
    queryset = Budget.objects.all()
    serializer_class = BudgetSerializer


class MarriageViewSet(viewsets.ModelViewSet):
    queryset = Marriage.objects.all()
    serializer_class = MarriageSerializer

```

Agora basta ajeitar as rotas e nossa API já estará funcionando:

```python
# budget/urls.py

from rest_framework import routers
from django.urls import path, include
from budget.views import VendorViewSet, MarriageViewSet, BudgetViewSet

router = routers.DefaultRouter()
router.register(r'vendors', VendorViewSet)
router.register(r'marriages', MarriageViewSet)
router.register(r'budgets', BudgetViewSet)

urlpatterns = [
    path('', include(router.urls))
]

```

```python
# marryme/urls.py

from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('budget.urls')),
]
```

Faça um teste! Vá ao thunderclient e faça algumas requisições. Crie entidades. Você criará com sucesso `Vendors` e `Budget`, mas baterá em um problema com `Marriage`. O que está acontecendo? Para ver por conta própria, experimente criar alguns `Vendors` e depois criar um `Marriage` com o corpo abaixo numa requisição `POST /marriages/`

```json
{
    "codename": "Casamento do Século",
    "date": "2023-12-31",
    "budget": {
        "vendors": [
            1,
            2
        ]
    }
}
```

E o que houve?!

Você verá que estamos com o mesmo problema que vimos antes no painel de admin: até conseguimos, no corpo de uma mesma requisição, inserir dados de `Marriage` e `Budget` para criar ambos ao mesmo tempo, mas `Budget` insiste em dar erro se não receber o `id` de uma entidade de `Marriage`. O problema é que esse `id`, no momento em que fazemos a requisição, não existe, pois estamos criando as duas entidades ao mesmo tempo. Mas o _serializer_ de `Budget` é categórico: sem `id` o modelo disparará um erro.

O que fazer?

### Configurando _serializers_ para criar entidades com relação 1:1

Lembre-se de como o _Django REST Framework_ funciona. O _model_ é a nossa interface com o banco de dados, respeitando todas as suas restrições de integridade. Os _viewsets_, que faremos adiante, são os locais por onde as requisições vem para nossa API. Os serializers são os locais que **recebem** os dados, os entregam corretamente para os _models_ e, em caso de problema, retornam erros bem formatados, tudo feito pra gente por traz dos panos.

`Vendor` e `Budget` tem os _serializers_ que esperamos - uma contato simples e direto com o nosso modelo, sem maiores alterações, para criarmos entidades corretamente. `Marriage`, por outro lado, precisa ser capaz de receber uma requisição que cria **duas entidades ao mesmo tempo**. O problema de simplesmente fazer um _serializer_ simples para `Marriage` - unindo-o com o _serializer_ que já temos para `Budget`, é que os dois não conseguem funcionar ao mesmo tempo. O _serializer_ de `Budget` vai disparar um erro sem um `Marriage` já criado para vincular à sua entidade - e sem um `Budget` criado, o _serializer_ de `Marriage` também dispara um erro.

Não podemos simplesmente remover de `BudgetSerializer` a obrigação pela presença do campo `marriage` - caso contrário requisições de API diretamente aos endpoints de `Budget` dispararão erros de integridade do banco, pois precisam do `id` para serem criadas. A solução é criar um **segundo _serializer_** para vincular a `Marriage` - um sem essa restrição. Nós **não iremos conectar esse serializer a nenhum _Viewset_**, então o mundo exterior não conseguirá criar entidades inadequadas. Esse _serializer_ será exclusivo para o vínculo com `Marriage`. Daí, dentro de `Marriage`, iremos garantir que o `Budget` criado junto com ele é criado corretamente. Veja como abaixo:

```python
# budget/serializers.py


from rest_framework import serializers
from .models import Vendor, Marriage, Budget


class VendorSerializer(serializers.HyperlinkedModelSerializer):
    class Meta:
        model = Vendor
        fields = ["id", "name", "price"]


class BudgetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Budget
        fields = ["id", "vendors", "marriage"]


class NestedBudgetSerializer(serializers.ModelSerializer):
    class Meta:
        model = Budget
        fields = ["id", "vendors"]


class MarriageSerializer(serializers.ModelSerializer):
    budget = NestedBudgetSerializer()

    class Meta:
        model = Marriage
        fields = ["id", "codename", "date", "budget"]

    def create(self, validated_data):
        budget_data = validated_data.pop('budget')
        budget_data['marriage'] = Marriage.objects.create(**validated_data)
        BudgetSerializer().create(validated_data=budget_data)
        return budget_data['marriage']

```

Nós sobrescrevemos a função de `create` do _serializer_ por uma lógica nossa - nós removemos dos dados validados os dados relacionados a `Budget`, e usamos os dados restantes para criar uma entidade `Marriage` no banco e atribuir a instância que tal criação retorna ao atributo `marriage` de `budget_data`. Em seguida, chamamos diretamente o serializer original de `Budget` para que crie nossa entidade já vinculada com o `Marriage` que criamos na linha acima. Por fim, retornamos o `Marriage` criado, fechando o comportamento esperado pela função `create` do _serializer_.

Ter o `NestedBudgetSerializer` vinculado ao `MarriageSerializer` lá em cima é importante - caso contrário o `MarriageSerializer` dispara o erro pela falta do `id` em `Budget` antes mesmo de chamar a função `create` que fizemos, que remedia o problema.

Rode esse código! Entenda como ele funciona, simule os erros. Se acessar as URLs pelo browser e/ou pelo Thunder Client agora poderá validar que já funcionam! Crie algumas entidades pelo painel de admin, pelas rotas, e veja tudo funcionando! Com o processo básico revisado e concluído pro nosso MVP, bora criar nossas autenticações?


#### Solução

1. Passo a passo:
    - Criar um diretório novo para o projeto e ativar o ambiente virtual:

```bash
mkdir marryme && cd marryme
python3 -m venv .venv && source .venv/bin/activate
```

- Instalar as dependências do Django e Django REST Framework:

```bash
pip install django djangorestframework
```

- Instalar as outras dependências:

```bash
pip install markdown django-filter mysqlclient
```

- Criar o projeto Django:

```bash
django-admin startproject marryme .
```

- Criar um app chamado ‘budget’ no mesmo nível do `manage.py`:

```bash
django-admin startapp budget
```

- No arquivo `marryme/settings.py`, adicionar a aplicação `budget` e o `rest_framework` ao `INSTALLED_APPS`:

```python
INSTALLED_APPS = [
    "django.contrib.admin",
    "django.contrib.auth",
    "django.contrib.contenttypes",
    "django.contrib.sessions",
    "django.contrib.messages",
    "django.contrib.staticfiles",
    "budget",
    "rest_framework",
]
```

- Criar um arquivo SQL para o script de criação do banco de dados:

```bash
mkdir database && cd database
touch 01_create_database.sql
```

- Insira os comandos de criação do banco de dados nele:

```sql
 CREATE DATABASE IF NOT EXISTS marryme_database;

 USE marryme_database;
```

- No arquivo `marryme/settings.py`, configurar o banco de dados para utilizar o MySQL:

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'marryme_database',
        'USER': 'root',
        'PASSWORD': 'password',
        'HOST': '127.0.0.1',
        'PORT': '3306',
    }
}
```

- Criar um arquivo `Dockerfile` na raiz do projeto com o seguinte conteúdo:

```yaml
FROM mysql:8.0.32
ENV MYSQL_ROOT_PASSWORD password
COPY ./database/01_create_database.sql /docker-entrypoint-initdb.d/data.sql01
```

- Na raiz do projeto, construir a imagem do banco de dados MySQL usando Docker:

```bash
docker build -t marryme-db .
```

- Executar o container do banco de dados MySQL:

```bash
docker run -d -p 3306:3306 --name=marryme-mysql-container -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=marryme_database marryme-db
```

- Espere alguns segundos aqui para a máquina subir totalmente, mesmo após o comando reportar execução com sucesso. A seguir, executar as migrações iniciais do Django:

```bash
python3 manage.py migrate
```

- Criar um superusuário para acessar o admin do Django:

```bash
python3 manage.py createsuperuser
```

- No arquivo `marryme/urls.py`, adicionar as rotas de admin se já não estiverem lá:

```diff
 from django.contrib import admin
 from django.urls import path

 urlpatterns = [
     path('admin/', admin.site.urls),
 ]
```

- Rodar o servidor para testar o projeto:

```bash
python3 manage.py runserver
```

Esse resumo inclui os principais comandos e modificações necessárias para configurar o ambiente e um projeto Django com Django REST Framework.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/15ce2d72-f1f9-4f0a-901c-7eff99291fc5/day/449e98ff-03c4-4f92-85c8-d7f6a4458053/lesson/3084d310-ff05-4e79-8dfa-954801d427db)
