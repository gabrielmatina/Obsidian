[[7 - Django]]

Já somos capazes de criar novos registros de músicas e pessoas cantoras. Entretanto, ainda não temos como identificar quais são as músicas de uma pessoa cantora, ou mesmo quem canta determinada música. O que acha de fazer um _upgrade_ nos modelos para resolver isso?

Como mencionado no começo do conteúdo de hoje, os modelos que estão sendo usados possuem vínculos que ainda não foram estabelecidos, estes vínculos são os relacionamentos que temos entre as tabelas.

Para recordar, os modelos usados até o momento são `Singer`, `Music` e `Playlist`

Bora estabelecer os vínculos entre nossos modelos?

## Relacionamento 1 para N

Refletindo sobre os modelos acima, é possível perceber que essa relação se encaixa bem com os modelos `Singer` <1:N> `Music`, dado que uma mesma pessoa cantora pode ter várias músicas, certo?.

Ao se analisar a implementação dos modelos acima, se nota que nenhum dos campos descritos é uma chave primária. Quando não criamos esse campo explicitamente o Django, automaticamente, cria uma nova coluna para cada modelo, chamada `id`, que será a chave primária, caso algum dos campos seja designado como chave primária (`primary_key = True`), o Django não criará a coluna `id`.

Para criar o relacionamento entre os modelos `Singer` e `Music`, será utilizado o campo `models.ForeignKey` no modelo `Music`, onde será implementado que uma música pode possuir apenas uma pessoa cantora. Dessa forma, se `N` músicas diferentes referenciam a mesma pessoa cantora, podemos notar a relação `Singer` <1:N> `Music`.

No campo `models.ForeignKey` será necessário passar o modelo a ser referenciado e logo em seguida outros dois parâmetros: `on_delete`, que define o que acontecerá com os registros que estão associados ao registro que está sendo excluído e `related_name`, que será um atributo do modelo referenciado para permitir o acesso no sentido inverso do relacionamento.

Além disso, se existirem registros no banco de dados, será necessário definir um valor padrão para que as colunas adicionais sejam preenchidas. Algumas estratégias que podem ser usadas:

- Criar um objeto que representará o valor padrão e passar seu `id` como valor padrão. (Usaremos essa aqui)
- Permitir que a coluna seja nula e, posteriormente, preencher os valores.
- Ou caso ainda esteja em fase de desenvolvimento, apagar o banco e as migrações e criar tudo novamente.

Crie um objeto do tipo `Singer` usando o terminal interativo do Django `python3 manage.py shell`:

```python
from playlists.models import Singer


default = Singer.objects.create(name="Pessoa desconhecida")  # Retorna o objeto criado <Singer: Pessoa desconhecida>

default.id # Retorna o id do objeto criado, 2, por exemplo
```

Agora, veja como fica a classe `Music` com o relacionamento:

Copiar

```python
# playlists/models.py
from django.db import models
from playlists.validators import validate_music_length,


class Music(models.Model):
    name = models.CharField(max_length=50)
    recorded_at = models.DateField()
    length_in_seconds = models.IntegerField(validators=[validate_music_length])
    singer = models.ForeignKey(
        Singer,
        on_delete=models.CASCADE,
        related_name="musics",
        default=2, # Se não houver o objeto com esse id em seu banco você terá um erro ao criar um objeto Music
    )

    def __str__(self):
        return self.name
```

> **De olho na dica 👀:** Para o parâmetro `on_delete` existem algumas opções de valor já implementadas pelo Django dentro de `models`. Você encontra essas opções na [documentação oficial](https://docs.djangoproject.com/en/3.2/ref/models/fields/#django.db.models.ForeignKey.on_delete).

Com a implementação acima, o modelo `Music` referencia o modelo `Singer`. Já que modificamos o modelo é necessário aplicar as migrações para o banco `python3 manage.py makemigrations` e `python3 manage.py migrate`.

Na prática, será criada uma coluna adicional na tabela `music` com o nome `singer_id` que armazenará a chave primária do registro da tabela `singer` que está sendo referenciado, independentemente se essa chave primária é um `id` ou não. Além disso, foi usada a configuração `on_delete=models.CASCADE`, indicando que, caso o registro da tabela `singer` seja excluído, todos os registros da tabela `music` que possuem o `singer_id` igual ao `id` do registro excluído, também serão excluídos.

Um ponto importante a ser observado é que o atributo `singer` da classe `Music` precisa receber um objeto do tipo `Singer` para ser criado e não um `id` ou qualquer outra chave primária. O ORM do Django se encarrega da tarefa de, a partir do objeto `Singer`, escrever a chave primária no banco de dados e, ao fazer o resgate do banco, resgatar o objeto `singer` a partir do `id` registrado no banco.

Na prática, através de um objeto `Music` podemos acessar o objeto `Singer` através do atributo `singer`. Já através de um objeto `Singer`, podemos acessar todos os objetos `Music` associados à ele através do atributo `musics`, definido em `related_name` do relacionamento e, em seguida, usando o método `all()`.

Observe o exemplo abaixo do relacionamento `1:N` para entender melhor essa relação:

```python
from playlists.models import Music, Singer

corey = Singer.objects.create(name="Corey Taylor") # cria objeto Singer com id = 1 e salva em corey

music_1 = Music.objects.create(name="Snuff", recorded_at="2008-06-17", length_in_seconds=270, singer=corey) # cria objeto Music com id = 1 e salva em music_1

music_2 = Music.objects.create(name="Through Glass", recorded_at="2006-07-01", length_in_seconds=240, singer=corey) # cria objeto Music com id = 2 e salva em music_2

music_1.singer # retorna o objeto Singer associado ao objeto Music music_1
# saída: <Singer: Corey Taylor>

music_2.singer # retorna o objeto Singer associado ao objeto Music music_2
# saída: <Singer: Corey Taylor>

corey.musics.all() # retorna todos os objetos Music associados ao objeto Singer corey
# saída: <QuerySet [<Music: Snuff>, <Music: Through Glass>]>
```

## Relacionamento N para N

O relacionamento N para N representa uma relação onde um registro de uma tabela pode estar associado a vários registros de outra tabela e vice-versa. No caso aqui, podemos fazer transpor essa relação para os modelos `Music` e `Playlist`, dado que uma música pode estar em várias playlists e uma playlist pode ter várias músicas.

Para implementar esse relacionamento no Django, será usado o campo `models.ManyToManyField`, que recebe o modelo a ser referenciado e o parâmetro `related_name`, com o mesmo intuito anterior, ser possível fazer o acesso reverso ao modelo que está sendo referenciado.

```python
# playlists/models.py
class Playlist(models.Model):
    name = models.CharField(max_length=50)
    is_active = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    musics = models.ManyToManyField("Music", related_name="playlists")

    def __str__(self):
        return self.name
```

O único motivo pelo qual o modelo `Music` se encontra entre aspas, como se fosse uma string, no parâmetro `models.ManyToManyField` é que, no momento da criação do modelo `Playlist`, o modelo `Music` ainda não foi declarado. Dessa forma, o Django busca pelo modelo `Music` apenas depois que todos os modelos forem declarados.

No Django, quando um relacionamento `N:N` é criado, o atributo responsável por esse relacionamento se torna uma espécie de `set` que pode receber objetos do tipo do modelo referenciado. Assim, é possível adicionar, usando o método `add()`, ou remover, usando o método `remove()` objetos do atributo de relacionamento.

Uma vez que uma música é adicionada à uma playlist, é preciso salvar novamente a playlist para que as atualizações sejam refletidas no banco de dados. Por essa razão, pode-se implementar métodos que encapsulam essa lógica e facilitam o gerenciamento dos objetos. Observe:

```python
# playlists/models.py
from django.db import models


class Playlist(models.Model):
    name = models.CharField(max_length=50)
    is_active = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    musics = models.ManyToManyField("Music", related_name="playlists")

    def add_music(self, music):
        self.musics.add(music)
        self.save()
    
    def remove_music(self, music):
        self.musics.remove(music)
        self.save()

    def __str__(self):
        return self.name
```

Assim, todos os objetos do tipo `Playlist` serão capazes de usar os métodos `add_music()` e `remove_music()` que facilitam a adição e remoção de músicas de uma playlist. Para conseguir visualizar todas as músicas de uma playlist, basta acessar o atributo `musics` do objeto `Playlist` e, em seguida, usar o método `all()`. Já, se o intuito é visualizar todas as playlists que uma música está associada, basta acessar o atributo `playlists` do objeto `Music`, também definido em `related_name` do relacionamento e, em seguida, usar o método `all()`.

Novamente, foram feitas alterações nos modelos e para que sejam observadas no banco, é preciso criar e executar as migrações `python3 manage.py makemigrations` e `python3 manage.py migrate`. Observe o exemplo abaixo do relacionamento `N:N` para entender melhor essa relação:

```python
from playlists.models import Music, Playlist

music_1 = Music.objects.get(id=1) # retorna objeto Music com id = 1 e salva em music_1

music_2 = Music.objects.get(id=2) # cria objeto Music com id = 2 e salva em music_2

playlist_1 = Playlist.objects.create(name="Codando na Paz", is_active=True) # cria objeto Playlist com id = 1 e salva em playlist_1

playlist_2 = Playlist.objects.create(name="Bora Treinar", is_active=True) # cria objeto Playlist com id = 2 e salva em playlist_2

playlist_1.musics.all() # retorna todos os objetos Music associados ao objeto Playlist
# saída: <QuerySet []>

playlist_2.musics.all() # retorna todos os objetos Music associados ao objeto Playlist
# saída: <QuerySet []>

playlist_1.add_music(music_1) # adiciona objeto Music music_1 ao objeto Playlist

playlist_1.musics.all() # retorna todos os objetos Music associados ao objeto Playlist
# saída: <QuerySet [<Music: Snuff>]>

playlist_2.add_music(music_1) # adiciona objeto Music music_1 ao objeto Playlist

playlist_2.musics.all() # retorna todos os objetos Music associados ao objeto Playlist
# saída: <QuerySet [<Music: Snuff>]>

playlist_2.add_music(music_2) # adiciona objeto Music music_2 ao objeto Playlist

playlist_2.musics.all() # retorna todos os objetos Music associados ao objeto Playlist
# saída: <QuerySet [<Music: Snuff>, <Music: Through Glass>]>

music_1.playlists.all() # retorna todos os objetos Playlist associados ao objeto Music
# saída: <QuerySet [<Playlist: Codando na Paz>, <Playlist: Bora Treinar>]>

music_2.playlists.all() # retorna todos os objetos Playlist associados ao objeto Music
# saída: <QuerySet [<Playlist: Bora Treinar>]>
```

## Como ficam os formulários agora?

Na última implementação realizada dos formulários, foi utilizada a classe `ModelForm` que, automaticamente, cria os campos do formulário com base nos campos do modelo. Você chegou a visualizar como ficou o formulário depois que as alterações de relacionamento foram feitas? Se não, dê uma olhada agora:

![Print do formulário com novo campo](https://content-assets.betrybe.com/prod/db9c19f7-8bdb-4f8a-92f1-58a4232e659d-Print%20do%20formul%C3%A1rio%20com%20novo%20campo.png)
Print do formulário com novo campo

O nome que designa o novo campo ainda não foi personalizado mas, sem alterar nada da implementação do formulário, temos um novo campo funcional que já resgata todos os objetos do tipo `Singer` do banco e coloca na lista de seleção.

Caso houvesse a intenção de mostrar apenas alguns dos objetos `Singer`, seria possível personalizar o _widget_ do campo `singers` para que ele fosse um `form.Select` passando o parâmetro `choices` com o valor de uma lista de tuplas, onde cada tupla contém, respectivamente, o valor a ser submetido no formulário e o valor exibido para a pessoa usuária. Observe:

```python
# music/forms.py
class CreateMusicModelForm(forms.ModelForm):
    class Meta:
        model = Music
        fields = "__all__"

    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fields["name"].label = "Nome da música"
        self.fields["recorded_at"].label = "Data de gravação"
        self.fields["recorded_at"].widget = forms.DateInput(
                attrs={"type": "date"})
        self.fields["recorded_at"].initial = "2023-07-06"
        self.fields["length_in_seconds"].label = "Duração em segundos"
        self.fields["singer"].label = "Artista"
        self.fields["singer"].widget = forms.Select(
            choices=[
                (singer.id, singer.name)
                for singer in Singer.objects.filter(name__contains="a")
            ]
        )
```

Com a modificação acima, o campo `singer` do formulário passa a exibir os nomes dos objetos `Singer` que possuem a letra “a” no nome, entretanto, ao submeter o formulário não será o nome do objeto que será passado adiante, mas sim o seu `id`.

Execute o servidor e veja as alterações feitas em funcionamento: `python3 manage.py runserver` e acesse [localhost:8000/musics](localhost:8000/musics).

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/e628894f-9cff-453b-a890-c355762124fc)
