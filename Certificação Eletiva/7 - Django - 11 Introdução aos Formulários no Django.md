[[7 - Django]]

No Django, existe uma classe que permite que você consiga receber e validar dados de uma maneira rápida e prática. Essa é a classe `Form`, que está implementada no módulo `django.forms.forms`.

Em resumo, um formulário pode ser criado para receber e validar dados que chegarão em uma requisição. Isso possibilita a criação ou atualização de registros no banco de dados de forma mais confiável.

## Criando um formulário

Quando pensamos em criar um formulário, a primeira coisa a se fazer é definir qual será seu propósito. Como ele se encaixa na lógica da aplicação que estamos desenvolvendo para conseguirmos delimitar o que ele irá conter.

iniciaremos construindo um formulário cujo propósito é adicionar novas músicas ao banco.

Para isso, crie um arquivo `forms.py` dentro da aplicação `playlists`. É nesse arquivo que serão construídos os formulários da aplicação. Depois de criado, adicione o seguinte código:

```python
# playlists/forms.py
from django import forms


class CreateMusicForm(forms.Form):
    name = forms.CharField(max_length=50)
    recorded_at = forms.DateField()
    length_in_seconds = forms.IntegerField()
```

Percebeu que os atributos do formulário que criado têm praticamente a mesma sintaxe dos que foram criados no modelo `Music`?

Isso acontece porque para criar um novo registro na tabela `music` é obrigatório fornecer os três campos. Já para o modelo `Playlist`, por exemplo, os campos `created_at` e `updated_at` não precisam ser passados, então não precisamos desses campos:

```diff
# playlists/forms.py
from django import forms


class CreateMusicForm(forms.Form):
    name = forms.CharField(max_length=50)
    recorded_at = forms.DateField()
    length_in_seconds = forms.IntegerField()


+ class CreatePlaylistForm(forms.Form):
+     name = forms.CharField(max_length=50)
+     is_active = forms.BooleanField()
```

Uma grande vantagem de se usar um formulário é a maneira eficaz que ele proporciona a validação dos dados em cada campo.

Observe: o atributo `name = forms.CharField(max_length=50)` indica que o formulário deve ter uma entrada `name` do tipo _String_ com no máximo 50 caracteres. Por outro lado, o atributo `duration_in_seconds = forms.IntegerField()` indica que o formulário deve ter uma entrada `duration_in_seconds` cujo valor correspondente deve ser do tipo inteiro.

## Formulários vinculados vs não vinculados

Para o Django, formulários podem ser classificados como vinculados ou não vinculados.

Um formulário é considerado como não vinculado caso seja instanciado sem nenhum dado, caso contrário, ele é vinculado. A própria classe `Form` apresenta um atributo `is_bound` que indica se o formulário é vinculado ou não. Observe o exemplo abaixo:

```python
from playlists.forms import CreatePlaylistForm


form = CreatePlaylistForm()
form.is_bound # retorna False

form = CreatePlaylistForm({"name":"Playlist de Estudo", "is_active": True})
form.is_bound # retorna True
```

> **De olho na dica 👀:** qualquer dicionário passado como parâmetro já faz com que o formulário seja considerado como vinculado.

E afinal, qual a diferença? 🤔

Formulários vinculados podem validar os dados passados por parâmetro. Já formulários não vinculados não podem fazer isso. Veremos sobre isso a seguir!

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/46c281c4-d64c-4a06-8011-c845f4959389)
