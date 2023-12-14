[[7 - Django]]

O formulário renderizado no template ainda não está dentro do que é esperado. Os nomes que designam cada um dos campos ainda estão em inglês e, além disso, é necessário modificar os campos que são renderizados. Por exemplo, `recorded_at`, que representa uma data, está sendo renderizado como um campo de texto.

Essas configurações podem ser feitas diretamente no formulário, no momento de se definir a classe. Podemos usar o parâmetro `labels` para indicar qual deverá ser o nome de cada um dos campos. Ainda, podemos usar o parâmetro `initial` para sugerir um dado inicial caso faça sentido para aquele campo. Veja como fica a implementação do formulário `CreateMusicForm` ao usarmos esses parâmetros:

```python
# playlists/forms.py

from django import forms
from playlists.validators import validate_music_length, validate_name


class CreateMusicForm(forms.Form):
    name = forms.CharField(
        max_length=50,
        validators=[validate_name],
        label="Nome da música",
    )
    recorded_at = forms.DateField(
        label="Data de gravação",
        initial="2023-07-06",
    )
    length_in_seconds = forms.IntegerField(
        validators=[validate_music_length],
        label="Duração em segundos",
    )
```

> **De olho na dica 👀:** também é possível usar o parâmetro `help_text` para indicar uma frase de auxílio no preenchimento do campo. Experimente!

Colocar um valor inicial pode ajudar no preenchimento do campo, mas isso não necessariamente melhora a experiência da pessoa usuária. Contudo, é possível melhorar essa experiência modificando a aparência dos campos do formulário com um _widget_.

Um _widget_ nada mais é do que uma representação HTML mais elaborada de um campo `input`. Felizmente, o Django tem diversos _widgets_ já implementados e prontos para serem usados. Além disso, ele também permite que você crie seus próprios _widgets_! 🤯

Para usar um _widget_, basta passá-lo como parâmetro ao definir o campo, assim como é feito para o parâmetro `label`.

Para fazer as melhores escolhas, é necessário conhecer os _widgets_ disponíveis e você pode ver a lista completa de _widgets_ nativos do Django na [documentação oficial](https://docs.djangoproject.com/en/4.2/ref/forms/widgets/#built-in-widgets). Aqui, usaremos o `DateInput()`:

```diff
# playlists/forms.py

from django import forms
from playlists.validators import validate_music_length, validate_name


class CreateMusicForm(forms.Form):
    name = forms.CharField(
        max_length=50,
        validators=[validate_name],
        label="Nome da música",
    )
    recorded_at = forms.DateField(
        label="Data de gravação",
+         widget=forms.DateInput(attrs={"type": "date"}),
        initial="2023-07-06",
    )
    length_in_seconds = forms.IntegerField(
        validators=[validate_music_length],
        label="Duração em segundos",
    )
```

> **De olho na dica 👀:** o parâmetro `attrs` passado para o _widget_ é usado para atribuir um conjunto `chave: valor` à _tag_ que está sendo inserida no _template_. Nesse caso, definimos o tipo do input como data `type: date`, mas poderíamos, adicionalmente, definir uma classe: `class: inputDate`.

Execute o servidor antes e depois da adição do novo _widget_. Essa implementação diminui a probabilidade de _bugs_ relacionados à entrada de dados do tipo data, que precisam ser digitados em um formato específico. Além disso, ainda houve uma melhora na experiência de quem usa o formulário.

![Print da página home com formulário personalizado](https://content-assets.betrybe.com/prod/ab153ff3-0c1e-4813-acda-57f1d2d8a578-Print%20da%20p%C3%A1gina%20home%20com%20formul%C3%A1rio%20personalizado.png)
Print da página home com formulário personalizado

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/8b4cbea1-2e63-43e3-a9fc-63e7f54e353b)
