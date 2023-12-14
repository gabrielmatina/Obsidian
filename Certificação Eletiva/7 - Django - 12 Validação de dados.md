[[7 - Django]]

A classe `Form` implementa o método `is_valid()`, que retorna um booleano para informar se os dados do formulários são válidos ou não.

Além disso, a classe `Form` também implementa o atributo `errors` que retorna um dicionário com os erros de validação de cada campo do formulário. Veja o exemplo abaixo:

```python
from playlists.forms import CreatePlaylistForm

form = CreatePlaylistForm({}) # formulário instanciado com um dicionário vazio
form.is_valid() # retorna False
form.errors # retorna {'name': ['Este campo é obrigatório.'], 'is_active': ['Este campo é obrigatório.']}

form_2 = CreatePlaylistForm({"name":"Essa playlist tem um nome com mais de cinquenta caracteres, o que você acha que vai acontecer?", "is_active": True})
form_2.is_valid() # retorna False
form_2.errors # retorna {'name': ['Certifique-se de que o valor tenha no máximo 50 caracteres (ele possui 94).']}

form_3 = CreatePlaylistForm({"name":"Playlist de Estudo", "is_active": True})
form_3.is_valid() # retorna True
form_3.errors # retorna {}

unbound_form = CreatePlaylistForm() #  formulário não vinculado
unbound_form.is_valid() #  retorna False
unbound_form.errors #  retorna {} Esse tipo de formulário não passa por validação
```

Muito legal e prático, não é mesmo? 😎

Mas e se a validação envolver uma regra de negócio mais complexa? 🤔

## Para fixar

### Exercício 1

Crie o formulário `CreateSingerForm` usando o modelo `Singer` como referência para o(s) campo(s) do formulário. Depois, abra o terminal interativo do Django e teste a validação desse formulário. Tente passar dados que não representam nomes de fato e veja o que acontece 👀

#### Solução

**Passo 1**: Como o modelo `Singer` possui um único campo para sua criação (`name`), o formulário `CreateSingerForm` terá apenas um campo também. Observe:

```python
# playlists/forms.py
class CreateSingerForm(forms.Form):
    name = forms.CharField(max_length=50)
```

**Passo 2**: Abra o terminal interativo do Django: no mesmo diretório que `manage.py` está localizado, execute o comando `python3 manage.py shell`. **Passo 3**: Importe o formulário criado por você `from playlists.forms import CreateSingerForm`. **Passo 4**: Instancie o formulário com um dicionário vazio `form = CreateSingerForm({})`. **Passo 5**: Digite `form.errors` e verá que foi levantado um erro de validação, pois o campo `name` é obrigatório. **Passo 6**: Instancie um novo formulário passando um dicionário com um nome muito grande `form = CreateSingerForm({"name": "Esse nome é muito grande, o que você acha que vai acontecer?"})`. **Passo 7**: Digite `form.errors` e verá que foi levantado um erro de validação, pois o campo `name` possui mais caracteres do que o permitido. **Passo 8**: Instancie um novo formulário passando apenas números no dicionário `form = CreateSingerForm({"name": 123456789})`. **Passo 9**: Ao digitar `forms.is_valid()`, verá que o formulário é considerado válido, pois o campo `name` aceita apenas strings, contudo, será que é válido mesmo? 🤔 (veremos mais sobre isso adiante) **Passo 10**: Instancie um novo formulário passando um nome válido no dicionário `form = CreateSingerForm({"name": "Felps"})`. **Passo 11**: Ao digitar `forms.is_valid()`, verá que o formulário é considerado válido.

## Criando validações personalizadas

É possível criar suas próprias funções de validação para os campos de um formulário, isso permite que você aplique a regra de negócio que quiser para validar um campo.

Para trazer o exemplo prático, vamos considerar que a duração de uma música, `length_in_seconds`, precisa ser um número inteiro entre 1 e 3600 segundos. A função de validação precisa levantar uma exceção `ValidationError`, que será implementada no módulo `django.core.exceptions` e que receberá como parâmetro a mensagem de erro que será exibida caso a validação falhe.

Crie um arquivo `validators.py` dentro da aplicação `playlists` e implemente uma função que faz a checagem se um número inteiro está entre 1 e 3600 segundos:

```python
# playlists/validators.py

from django.core.exceptions import ValidationError


def validate_music_length(value):
    if not 1 >= value >= 3600:
        raise ValidationError("A duração da música deve ser um número"
                              " inteiro entre 1 e 3600 segundos. O valor "
                              "{value} não é válido.")
```

O próximo passo é indicar no campo do formulário que o dado recebido ali deve ser validado pela função criada, para além das validações padrão. Essa tarefa é feita por meio do parâmetro `validators` que recebe uma lista com todas as funções personalizadas para validação do campo. Veja abaixo:

```diff
# playlists/forms.py

from django import forms
+ from playlists.validators import validate_music_length


class CreateMusicForm(forms.Form):
    name = forms.CharField(max_length=50)
    recorded_at = forms.DateField()
+    length_in_seconds = forms.IntegerField(validators=[validate_music_length])
```

Agora, se você tentar criar uma música com uma duração menor que 1 ou maior que 3600 segundos, o formulário não será considerado válido e a mensagem de erro será exibida. Veja o exemplo abaixo:

> Execute o código abaixo no terminal interativo do Django (`python3 manage.py shell`) ⚠️ Se você já estiver com um terminal interativo aberto, é necessário fechá-lo (`exit()`) e abrir um novo, pois, do contrário, as modificações feitas não serão consideradas.

```python
from playlists.forms import CreateMusicForm


form = CreateMusicForm({"name":"The sound of silence", "recorded_at":"2023-07-05", "length_in_seconds":0}) # formulário instanciado com um dado inválido
form.is_valid() # retorna False
form.errors # retorna {'length_in_seconds': ['A duração da música deve ser um número inteiro entre 1 e 3600 segundos. O valor 0 não é válido.']}
```

> **De olho na dica 👀:** o Django possui uma série de validações prontas para serem usadas, você pode conferir a lista com as validações na [documentação oficial.](https://docs.djangoproject.com/en/4.2/ref/validators/#built-in-validators)

Além de indicar os validadores nos campos do formulário, também é possível indicar os validadores dentro do modelo da aplicação, utilizando o mesmo parâmetro (`validators`) na função que define cada campo.

Entretanto, é importante dizer que, mesmo que você indique os validadores no modelo, eles não serão executados automaticamente e ainda será possível criar registros com dados que não passam nas validações desejadas. Por isso, indicar os validadores no modelo pode parecer inútil, mas acredite, isso trará benefícios quando explorarmos outros tipos de formulários. 😉

Veja como fica o modelo com a validação:

```diff
# playlists/models.py

from django.db import models
+ from playlists.validators import validate_music_length

# ...

class Music(models.Model):
    name = models.CharField(max_length=50)
    recorded_at = models.DateField()
+    length_in_seconds = models.IntegerField(validators=[validate_music_length])

    def __str__(self):
        return self.name
```

> **Relembrando 🧠:** como foi feita uma modificação no modelo, lembre-se de criar as migrações e migrá-las para o banco de dados. Para isso, execute os comando: `python3 manage.py makemigrations`e `python3 manage.py migrate`.

### Para fixar

### Exercício 2

Implemente uma função de validação que faz com que números não sejam permitidos no campo `name` do formulário criado por você: `CreateSingerForm`. Depois de implementado, faça alguns testes no terminal do Django e veja se funcionou.

#### Solução

**Passo 1**: Implemente a função de validação no arquivo `validators.py`:

```python
# playlists/validators.py

from django.core.exceptions import ValidationError


def validate_name(value):
    if any(char.isdigit() for char in value):  # checa se algum dos caracteres é um dígito
        raise ValidationError("O nome não pode conter números.")
```

**Passo 2**: Insira a função de validação no campo `name` do formulário:

```python
# playlists/forms.py
from playlists.validators import validate_music_length, validate_name

class CreateSingerForm(forms.Form):
    name = forms.CharField(max_length=50, validators=[validate_name])
```

**Passo 3**: Abra o terminal interativo do Django, no mesmo diretório que `manage.py` está localizado, e execute o comando `python3 manage.py shell`.

**Passo 4**: Importe o formulário criado por você `from playlists.forms import CreateSingerForm`.

**Passo 5**: Instancie um novo formulário passando números no dicionário `form = CreateSingerForm({"name": 123456789})`.

**Passo 6**: Digite `form.errors` e verá que foi levantado um erro de validação, pois o campo `name` não pode conter números.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/53b48482-474d-4b11-8082-4a9044a0604c)
