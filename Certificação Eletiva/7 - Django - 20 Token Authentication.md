[[7 - Django]]

# Basic Authentication

## Restringindo acesso a parte das operações C.R.U.D. para admins

Com a nossa aplicação feita, vamos acrescentar nossa autenticação! Temos duas demandas aqui:

1. Permitir que só pessoas administradoras possam alterar os dados de fornecedores
2. Permitir que cada casamento esteja vinculado a uma pessoa usuária - e que cada uma só veja o próprio casamento

Vamos estudar os prós e contras de fazer a autenticação de várias formas ao longo do dia de hoje, mas vamos começar com a mais simples: a `BasicAuthentication`. Antes de mais nada, garanta que a sua dependência do _Django REST Framework_ esteja na versão `3.12` ou superior. Para checar a versão da sua dependência, execute no ambiente virtual o seguinte comando:

```bash
pip show djangorestframework
```

Se for preciso, atualize a dependência:

```bash
pip install djangorestframework --upgrade
```

O próximo passo é ir no arquivo `marryme/settings.py` e acrescentar a seguinte configuração:

```python
# marryme/settings.py

# ...

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework.authentication.BasicAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
}

# ...

```

Essa configuração define quais serão, respectivamente, a autenticação e permissão padrão usada pela aplicação. Feito isso, nossa API está fechada e todas as rotas exigem autenticação básica! É o primeiro passo para satisfazer a ambas as nossas demandas. Agora vamos acrescentar à lógica para somente uma parte administradora lidar com os dados de Fornecedores. Primeiro, vamos aos _serializers_. Não remova os _serializers_ que já existem, só acrescente esse:

```python
# budget/serializers.py

# ...

class AdminVendorSerializer(serializers.ModelSerializer):
    class Meta:
        model = Vendor
        fields = '__all__'

    def create(self, validated_data):
        request = self.context.get('request')
        if request and request.user.is_superuser:
            return super().create(validated_data)
        raise serializers.ValidationError("Você não tem permissão para criar fornecedores.")

# ...
```

Neste novo Serializer, estamos substituindo o método create padrão que o _Django REST Framework_ nos fornece para verificar se a parte usuária que está fazendo a requisição é administradora (`is_superuser`). Se sim, o fornecedor será criado normalmente; caso contrário, uma exceção `ValidationError` será lançada. Note que a lógica de ter um `user` vinculado a uma requisição, e um que possui esse atributo `is_superuser`, é uma lógica que nos é fornecida pelo _framework_. Por hora, ela basta.

A seguir, vamos alterar o `Viewset` de fornecedores:

```python
# budget/views.py


from rest_framework import viewsets
from .models import Vendor, Marriage, Budget
from .serializers import (VendorSerializer,
                          MarriageSerializer,
                          BudgetSerializer,
                          AdminVendorSerializer)


class VendorViewSet(viewsets.ModelViewSet):
    queryset = Vendor.objects.all()
    serializer_class = AdminVendorSerializer

    def get_serializer_class(self):
        if self.action in ("create", "destroy", "update"):
            return AdminVendorSerializer
        return VendorSerializer

# ...
```

Aqui estamos usando o novo `Serializer` `AdminVendorSerializer` apenas para as ações de criação, atualização e remoção de Vendors. Para a ação de leitura, continuamos usando o `Serializer` padrão `VendorSerializer`. Note que aqui, também, substituímos a implementação de um método padrão por uma nossa - é o polimorfismo em ação!

Seu servidor já deve estar funcionando com autenticação. Vamos testar? Vá ao Thunderclient testar uma das requisições restritas.

|![Pilares da Programação Orientada a Objetos](https://content-assets.betrybe.com/prod/33bf22c3-647b-4643-850b-dd10ead1f136-Pilares%20da%20Programa%C3%A7%C3%A3o%20Orientada%20a%20Objetos.png)|
|---|
|Imagem com uma requisição GET à API sendo bloqueada com um erro 401|

Veja como, por padrão, as requisições já não são acessíveis por qualquer pessoa. Agora acrescente as credenciais do `superuser` que você criou na aba `Auth`, opção `Basic` e veja a diferença!

**A maior parte dessa lógica de permissões é a mesma independente ao tipo de autenticação que usamos** - mas a inserção das credenciais direto na requisição é uma característica da `BasicAuthentication`! Você entenderá melhor o que é o que quando implementarmos as outras modalidades de autenticação!

## Restringindo acesso a uma entidade a quem a cria

Agora, o próximo passo: somente a pessoa que cria um casamento e orçamento poder acessá-lo! Fora, naturalmente _admins_ terem acesso a tudo.

Para implementar a lógica onde um pessoa cadastrada só pode acessar os Casamentos e Orçamentos que ela criou, você pode utilizar um mecanismo de autorização personalizado no Django REST Framework. Vamos criar uma novo _permission class_ que verificará as permissões para acessar esses objetos. Além disso, configuraremos o `Viewset` de `Marriage` para que apenas quem for `admin` tenha acesso a todos os registros. Vamos fazer isso passo a passo.

Primeiro, a tal _permission class_. Crie um novo arquivo chamado `permissions.py` dentro do diretório `budget` e adicione o seguinte conteúdo:

```python
# budget/permissions.py

from rest_framework.permissions import BasePermission


class IsOwnerOrAdmin(BasePermission):
    def has_object_permission(self, request, view, obj):
        # Permite acesso a admin sempre
        if request.user.is_superuser:
            return True
        # Permite acesso se o objeto pertence a quem faz a requisição
        return obj.user == request.user
```

Neste `IsOwnerOrAdmin`, definimos uma classe de permissão personalizada. Se quem usa tiver permissões de administrador (`is_superuser`), sempre se terá acesso. Caso contrário, o acesso será apenas aos objetos que pertencem à própria pessoa, verificando se o objeto (`Marriage` ou `Budget`) tem um atributo `user` que corresponde ao usuário autenticado.

Precisamos, então, adicionar um campo de relação com o usuário nos modelos `Marriage` e `Budget`. Para isso, atualize o arquivo `budget/models.py`:

```diff
# budget/models.py


from django.db import models
+ from django.contrib.auth.models import User


class Vendor(models.Model):
    # ...

class Marriage(models.Model):
+   user = models.ForeignKey(User, on_delete=models.CASCADE)
    codename = models.CharField(max_length=50)
    date = models.DateField()

    # ...

class Budget(models.Model):
+   user = models.ForeignKey(User, on_delete=models.CASCADE)
    vendors = models.ManyToManyField(Vendor, related_name='budgets')
    marriage = models.OneToOneField(Marriage, on_delete=models.CASCADE, related_name='budget')

    # ...
```

Com isso, associamos os modelos `Marriage` e `Budget` a um usuário (`User`) através de um relacionamento `1:N`. Se você for no painel de administração do Django irá perceber que, por padrão, **ele já cria** a entidade `User` para você - então usá-la aqui não dará problema, basta importarmos-na.

A seguir, vamos atualizar os Serializers para incluir o campo `user` e configurar os `Viewsets` para usar as permissões personalizadas.

```diff
# budget/serializers.py

# ...

class BudgetSerializer(serializers.ModelSerializer):
+    user = serializers.PrimaryKeyRelatedField(
+           read_only=True, default=serializers.CurrentUserDefault()
+           )

    class Meta:
        model = Budget
-       fields = ["id", "vendors", "marriage"]
+       fields = ["id", "vendors", "marriage", "user"]


class NestedBudgetSerializer(serializers.ModelSerializer):
+    user = serializers.PrimaryKeyRelatedField(
+           read_only=True, default=serializers.CurrentUserDefault()
+           )

    class Meta:
        model = Budget
-       fields = ["id", "vendors"]
+       fields = ["id", "vendors", "user"]


class MarriageSerializer(serializers.ModelSerializer):
    budget = NestedBudgetSerializer()
+   user = serializers.PrimaryKeyRelatedField(
+          read_only=True, default=serializers.CurrentUserDefault()
+          )

    class Meta:
        model = Marriage
-       fields = ["id", "codename", "date", "budget"]
+       fields = ["id", "codename", "date", "budget", "user"]

-   def create(self, validated_data):
-       budget_data = validated_data.pop('budget')
-       budget_data['marriage'] = Marriage.objects.create(**validated_data)
-       BudgetSerializer().create(validated_data=budget_data)
-       return budget_data['marriage']


+   # Sem a inteligência do serializer precisamos unir os dados todos 'na mão'
+   def create(self, validated_data):
+       # Recupera o user que fez a requisição
+       current_user = self.context['request'].user
+
+       '''
+       Recupera os dados de budget da requisição, acrescenta a eles
+       e aos dados de Marriage os dados do usuário
+       '''
+       budget_data = validated_data.pop('budget')
+       budget_data['user'] = current_user
+       validated_data['user'] = current_user
+
+       # Cria marriage, cria budget e retorna Marriage, como a função pede
+       budget_data['marriage'] = Marriage.objects.create(**validated_data)
+       BudgetSerializer().create(validated_data=budget_data)
+       return budget_data['marriage']
```

Tenha especial atenção com a função `create` que fizemos “na mão” para o `MarriageSerializer`. Os demais _serializers_ conseguem, com os acréscimos que fizemos, capturar `user` da requisição e associá-lo à entidade sendo criada. Como em `Marriage` fizemos a criação na mão, precisamos também manualmente recuperar os dados de usuário e acrescentá-los aos dados usados para criar as entidades.

```diff
# budget/views.py



from rest_framework import viewsets
from .models import Vendor, Marriage, Budget
+ from rest_framework.authentication import BasicAuthentication
from .serializers import (AdminVendorSerializer,
                          VendorSerializer,
                          MarriageSerializer,
                          BudgetSerializer)
+ from .permissions import IsOwnerOrAdmin


class VendorViewSet(viewsets.ModelViewSet):
    queryset = Vendor.objects.all()
    serializer_class = AdminVendorSerializer
+   authentication_classes = [BasicAuthentication]

    def get_serializer_class(self):
        if self.action in ("create", "destroy", "update"):
            return AdminVendorSerializer
        return VendorSerializer



class MarriageViewSet(viewsets.ModelViewSet):
    queryset = Marriage.objects.all()
    serializer_class = MarriageSerializer
+   authentication_classes = [BasicAuthentication]
+   permission_classes = [IsOwnerOrAdmin]
+
+
+   def get_queryset(self):
+       """
+       Quem for admin vê todos os casamentos.
+       Caso contrário, a pessoa só vê os próprios casamentos.
+       """
+       if self.request.user.is_superuser:
+           return Marriage.objects.all()
+       else:
+           return Marriage.objects.filter(user=self.request.user)


class BudgetViewSet(viewsets.ModelViewSet):
    queryset = Budget.objects.all()
    serializer_class = BudgetSerializer
+   authentication_classes = [BasicAuthentication]
+   permission_classes = [IsOwnerOrAdmin]
+
+
+   def get_queryset(self):
+       if self.request.user.is_superuser:
+           return Budget.objects.all()
+       else:
+           return Budget.objects.filter(user=self.request.user)
```

Perceba que adicionamos as variáveis `authentication_classes` e `permission_classes` no código acima. Essas variáveis definem a autenticação e permissão necessárias para utilização das `views` em questão. Essa é uma alternativa da configuração padrão usando no `settings.py` para, por exemplo, definir diferentes tipos de permissão e autenticação em sua aplicação.

Agora, quando um usuário autenticado criar um `Marriage` ou `Budget`, a API definirá automaticamente o campo `user` como seus dados. Além disso, quando qualquer requisição tentar acessar um `Marriage` ou `Budget`, a API verificará se ela tem as credenciais da parte proprietária da entidade ou se é `admin` para permitir ou negar o acesso. Além disso, sobrescrevemos ali a função `get_queryset` do `MarriageViewset`. Essa função é responsável por buscar todas as entidades do modelo quando se recebe uma requisição `GET /marriages`. Aqui, falamos que quem for `admin` vê todos os eventos - quem não for só vê os dos quais é dono ou dona.

Para efetivar essas mudanças, nós acrescentamos `user`, um campo obrigatório, às tabelas `Marriage` e `Budget`. Normalmente, para fazer uma migração para aplicá-los ao banco, precisaríamos permitir que esse campo tenha valor nulo ou atribuir um user default a todas as entidades já existentes no banco. Para não ter esse trabalho, aproveitando que ainda estamos desenvolvendo, vamos aprender a **resetar o banco de dados da aplicação**. Rode os seguintes comandos, alterando-os para colocar os nomes das suas aplicações e o ID do seu container:

```bash
docker ps # Para descobrir o ID do seu container com o banco de dados
docker stop <ID do seu container do banco> # Parar o container
docker remove <ID do seu container do banco> # Deletar o container
docker build -t seu-projeto-db .
docker run -d -p 3306:3306 --name=seu-projeto-mysql-container -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=seu-projeto_database marryme-db # Recriar o container
python manage.py migrate seu-app zero # Desfazer todas as migrations do app budget
rm seu-app/migrations/000* # Deletar a migration
python3 manage.py makemigrations # Recriar as migrations - agora com o campo user
python3 manage.py migrate # Efetuar as migrações para criar o banco
python3 manage.py createsuperuser # Recrie seu superuser

```

> **De olho na dica 👀:** tome nota dessa sequência de comandos. Resetar o banco pode te ajudar muito durante o desenvolvimento de uma aplicação num processo seletivo.

Agora abra o painel de admin e confira que tudo está no lugar.

Vamos testar? Primeiramente, vá até o _dashboard_ de admin e crie alguns `User`. Agora crie alguns vendors. Em seguida, através do Thunderbird, faça um `POST /marriages/` com os metadados do modelo - e criando, ao mesmo tempo, o respectivo orçamento:

```json
{
    "codename": "Casamento do Século",
    "date": "2023-12-31",
    "budget": {
        "vendors": [3, 4]
    }
}
```

Na aba `Auth`, da requisição, coloque as credenciais de algum `User`. Sua resposta será:

```json
{
  "id": 3,
  "codename": "Casamento do Século",
  "date": "2023-12-31",
  "budget": {
    "id": 1,
    "vendors": [
      3,
      4
    ],
    "user": 2
  },
  "user": 2
}
```

Agora, quando esse usuário tentar acessar o casamento criado o resultado será a informação do casamento:

```json
{
    "id": 1,
    "user": 1,
    "codename": "Meu Casamento",
    "date": "2023-12-31"
}
```

Se o usuário tentar acessar o casamento de outro usuário o resultado será um erro de permissão:

```json
{
    "detail": "You do not have permission to perform this action."
}
```

Por outro lado, o admin terá acesso a todos os casamentos e orçamentos. Com estes passos, você adicionou a lógica de autenticação onde um usuário só pode acessar os Marriages e Budgets que ele criou, e o admin tem acesso a tudo. Outros usuários não-autenticados ou sem permissões de administração receberão mensagens de erro.

# Token Authentication

## Permitindo a quem usa obter Tokens

A autenticação básica é excelente por ser bem simples de implementar - você coloca as credenciais no cabeçalho da requisição e pronto! Além disso, ela carrega a vantagem de não exigir nenhum armazenamento ou gerenciamento de dados por parte do servidor - ele só precisa saber autenticar uma pessoa de acordo com suas credenciais. Mas há desvantagens também: as credenciais são enviadas no cabeçalho de toda requisição - uma interceptação de dados pode comprometê-las.

A autenticação por token requer um pouco mais de gerenciamento por parte do servidor - o gerenciamento das tokens - mas é mais segura - as credenciais só são enviadas para se obter uma token, e esta pode ser revogada com facilidade. Veja essas diferenças com mais nitidez na tabela abaixo:

|Característica|Autenticação por Token|Autenticação Básica|
|---|---|---|
|Segurança|Oferece melhor segurança, pois os tokens podem ter prazos curtos e ser revogados facilmente. Os tokens também podem ser emitidos com permissões específicas.|Menos seguro, pois as credenciais (nome de usuário/senha) são enviadas com cada requisição e podem ser interceptadas. As credenciais também são armazenadas no servidor, representando um risco potencial caso o servidor seja comprometido.|
|Ausência de Estado|Stateless, não requer armazenamento de sessão no servidor, o que reduz a carga no servidor.|Stateless, não requer armazenamento de sessão no servidor, o que reduz a carga no servidor.|
|Complexidade de Implementação|Mais complexo de implementar em comparação com a Autenticação Básica, pois requer a geração e manipulação de tokens no lado do servidor, além de lidar com a expiração e revogação de tokens.|Mais fácil de implementar, pois envolve apenas a verificação das credenciais em cada requisição. Nenhuma geração ou manipulação de token é necessária.|

No _Django REST Framework_, fazer autenticação por Token é ainda melhor pois dá quase a mesma quantidade de trabalho que fazer autenticação básica. Vamos ver como isso funciona? Primeiro, você precisará acrescentar um app novo à suas configurações e alterar a configuração padrão de autenticação:

```diff
# marryme/settings.py

# ...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'budget',
    'rest_framework',
+   'rest_framework.authtoken',
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
-       'rest_framework.authentication.BasicAuthentication',
+       'rest_framework.authentication.TokenAuthentication',
    ],
    'DEFAULT_PERMISSION_CLASSES': [
        'rest_framework.permissions.IsAuthenticated',
    ],
}
# ...

```

Agora, vamos primeiro criar uma rota para que uma pessoa possa enviar, via requisição, suas credenciais para obter uma token:

```diff
# marryme/urls.py


from django.contrib import admin
from django.urls import path, include
+ from rest_framework.authtoken.views import obtain_auth_token

urlpatterns = [
    path('admin/', admin.site.urls),
+    path('login/', obtain_auth_token, name='login'),
    path('', include('budget.urls')),
]

```

E _pronto!_ O _Django REST Framework_ nos dá toda essa lógica já pronta. Para testar, rode as migrations para termos a lógica de tokens no banco e faça uma requisição à sua nova rota:

```json
// POST /login/

{
  "username": "AlgumUser",
  "password":  "SenhaDesteUser"
}

```

Você obterá sua token.

## Autenticando com Tokens

Para permitir que as Tokens sejam usadas como autenticação, vá em suas views e faça a alteração abaixo:

```diff
# budget/views.py


from rest_framework import viewsets
from .models import Vendor, Marriage, Budget
- from rest_framework.authentication import BasicAuthentication
+ from rest_framework.authentication import TokenAuthentication
from .serializers import (AdminVendorSerializer,
                          VendorSerializer,
                          MarriageSerializer,
                          BudgetSerializer)
from .permissions import IsOwnerOrAdmin


class VendorViewSet(viewsets.ModelViewSet):
    queryset = Vendor.objects.all()
    serializer_class = AdminVendorSerializer
-   authentication_classes = [BasicAuthentication]
+   authentication_classes = [TokenAuthentication]

    def get_serializer_class(self):
        if self.action in ("create", "destroy", "update"):
            return AdminVendorSerializer
        return VendorSerializer


class MarriageViewSet(viewsets.ModelViewSet):
    queryset = Marriage.objects.all()
    serializer_class = MarriageSerializer
-   authentication_classes = [BasicAuthentication]
+   authentication_classes = [TokenAuthentication]
    permission_classes = [IsOwnerOrAdmin]

def get_queryset(self):
    if self.request.user.is_superuser:
        return Marriage.objects.all()
    else:
        return Marriage.objects.filter(user=self.request.user)


class BudgetViewSet(viewsets.ModelViewSet):
    queryset = Budget.objects.all()
    serializer_class = BudgetSerializer
-   authentication_classes = [BasicAuthentication]
+   authentication_classes = [TokenAuthentication]
    permission_classes = [IsOwnerOrAdmin]

def get_queryset(self):
    if self.request.user.is_superuser:
        return Budget.objects.all()
    else:
        return Budget.objects.filter(user=self.request.user)
```

E é isso, sim, só isso, que muda! Substituímos a classe de autenticação de `BasicAuthentication` para `TokenAuthentication`.

Agora faça uma requisição para obter o casamento de uma pessoa usuária sem autenticar e veja que você não tem acesso aos dados. Agora acrescente ao cabeçalho da requisição a chave `Authorization` com o valor `Token SuaTokenAqui`, e veja como funciona! Experimente alterar entre tokens de diferentes users e veja como cada pessoa só acessa o próprio dado!

O próximo passo é repetir essa troca para usarmos o `SimpleJWT`. Bora?

# SimpleJWT

## Instalar, rotear, configurar e pronto!

Todas as técnicas que foram ensinadas até agora podem ser usadas no **Django**, contudo, você precisará implementar toda a lógica dessas autenticações por sua conta. As autenticações `BasicAuthentication` e `TokenAuthentication` que usamos são implementadas no _Django REST Framework_. A `Simple JWT` não: ela vem de um plugin do _Django REST Framework_. Entretanto, ela é poderosa e implementável em pouquíssimos passos.

|Característica|Autenticação por Token|Autenticação Básica|Simple JWT|
|---|---|---|---|
|Segurança|Oferece melhor segurança, pois os tokens podem ter prazos curtos e ser revogados facilmente. Os tokens também podem ser emitidos com permissões específicas.|Menos seguro, pois as credenciais (nome de usuário/senha) são enviadas com cada requisição e podem ser interceptadas. As credenciais também são armazenadas no servidor, representando um risco potencial caso o servidor seja comprometido.|Oferece segurança avançada, geração automática de tokens JWT e suporte a tokens de atualização. Os tokens podem ter tempo de vida configurável e podem ser revogados. Possui integração simples com o Django REST Framework.|
|Ausência de Estado|Stateless, não requer armazenamento de sessão no servidor, o que reduz a carga no servidor.|Stateless, não requer armazenamento de sessão no servidor, o que reduz a carga no servidor.|Stateless, não requer armazenamento de sessão no servidor, o que reduz a carga no servidor.|
|Complexidade de Implementação|Mais complexo de implementar em comparação com a Autenticação Básica, pois requer a geração e manipulação de tokens no lado do servidor, além de lidar com a expiração e revogação de tokens.|Mais fácil de implementar, pois envolve apenas a verificação das credenciais em cada requisição. Nenhuma geração ou manipulação de token é necessária.|Mais complexo que a Autenticação Básica, mas oferece biblioteca completa para lidar com a geração, manipulação e renovação de tokens JWT. Requer configurações adicionais, mas proporciona mais recursos e flexibilidade.|

Para trocar nossa `TokenAuthentication` por `SimpleJWT`, siga os passos adiante. Primeiro, instale o módulo abaixo:

```bash
pip install djangorestframework-simplejwt
```

A seguir, ajuste as configurações, substituindo a autenticação padrão de `TokenAuthentication` por `JWTAuthentication`

```diff
# marryme/settings.py

INSTALLED_APPS = [
    # ...
    'rest_framework',
-   'rest_framework.authtoken',
+   'rest_framework_simplejwt',
    # ...
]

REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
-       'rest_framework.authentication.TokenAuthentication',
+       'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
    # Outras configurações do DRF ...
}
```

A seguir, ajuste as suas rotas:

```diff
# marryme/urls.py


from django.urls import path, include
- from rest_framework.authtoken.views import obtain_auth_token
+ from rest_framework_simplejwt.views import (TokenObtainPairView,
+                                             TokenRefreshView,
+                                             TokenVerifyView)

urlpatterns = [
    path('admin/', admin.site.urls),
-   path('login/', obtain_auth_token, name='login'),
+   path('token/', TokenObtainPairView.as_view(), name='token_obtain_pair'),
+   path('token/refresh/', TokenRefreshView.as_view(), name='token_refresh'),
+   path('token/verify/', TokenVerifyView.as_view(), name='token_verify'),
    path('', include('budget.urls')),
]
```

Como definimos a autenticação JWT como padrão no arquivo `settings.py` podemos remover a variável `authentication_classes` nas _views_. Isso fará com que o _simple JWT_ seja o padrão para toda aplicação. Caso você não defina uma configuração padrão de autenticação e permissão, você precisará indicar com `authentication_classes` e `permission_classes` quais serão essas configurações, caso contrário, não haverá autenticação e todas as pessoas terão as permissões.

```diff
from rest_framework import viewsets
from .models import Vendor, Marriage, Budget
- from rest_framework.authentication import TokenAuthentication
from .serializers import (AdminVendorSerializer,
                          VendorSerializer,
                          MarriageSerializer,
                          BudgetSerializer)
from .permissions import IsOwnerOrAdmin


class VendorViewSet(viewsets.ModelViewSet):
    queryset = Vendor.objects.all()
    serializer_class = AdminVendorSerializer
-   authentication_classes = [TokenAuthentication]

    def get_serializer_class(self):
        if self.action in ("create", "destroy", "update"):
            return AdminVendorSerializer
        return VendorSerializer




class MarriageViewSet(viewsets.ModelViewSet):
    queryset = Marriage.objects.all()
    serializer_class = MarriageSerializer
-   authentication_classes = [TokenAuthentication]
    permission_classes = [IsOwnerOrAdmin]

def get_queryset(self):
    if self.request.user.is_superuser:
        return Marriage.objects.all()
    else:
        return Marriage.objects.filter(user=self.request.user)


class BudgetViewSet(viewsets.ModelViewSet):
    queryset = Budget.objects.all()
    serializer_class = BudgetSerializer
-   authentication_classes = [TokenAuthentication]
    permission_classes = [IsOwnerOrAdmin]

def get_queryset(self):
    if self.request.user.is_superuser:
        return Budget.objects.all()
    else:
        return Budget.objects.filter(user=self.request.user)
```

E tudo já deve funcionar! Faça, primeiro, uma requisição com as credenciais para `/token/`:

```json
// POST /token/

{
  "username": "SeuUser",
  "password": "SuaSenha"
}

```

Você receberá duas tokens: uma na chave `access` e outra na chave `refresh`. Para testar, copie a da chave `access` para o cabeçalho da requisição com a chave `Authorization` e o valor `Bearer SeuToken`. Faça os testes de acesso a rotas protegidas para ver que tudo continua funcionando! Para além disso, o _endpoint_ `token/verify/` que criamos recebe no corpo da requisição a chave `token` com uma de suas tokens e retorna `200 OK` se elas forem válidas, e `401 UNAUTHORIZED` caso contrário. No endpoint `/token/refresh/`, você envia sua token da chave `refresh` e recebe uma nova token `access`, podendo gerar novas tokens sem precisar usar suas credenciais mais do que uma vez!

## Qual autenticação devo usar?!

Lembre-se: **não existe bala de prata**. Não existe tecnologia infalível ou melhor em 100% dos contextos. Dito isso, uma **regra geral** boa de se adotar é: se usar o _Django REST Framework_, use `Simple JWT` - é a opção mais completa, mais segura e, em função do que o framework fornece, a mais fácil de implementar. Caso use somente o **Django** comum, use `TokenAuthentication`. Caso precise somente de algo _realmente_ simples o `BasicAuthentication` quebra um galho.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/15ce2d72-f1f9-4f0a-901c-7eff99291fc5/day/449e98ff-03c4-4f92-85c8-d7f6a4458053/lesson/3084d310-ff05-4e79-8dfa-954801d427db)
