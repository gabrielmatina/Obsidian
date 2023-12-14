[[7 - Django]]

Agora que você já compreende como funcionam os formulários, será apresentado um outro tipo de formulário, muito útil para quando se possui um modelo que tem muitos atributos obrigatórios para a criação de um novo registro.

Pode até ser que você já tivesse se questionado quanto à isso, mas imagine: você tem um modelo que tem 10 atributos necessários para a criação de um novo registro, você precisaria fazer a implementação de cada um desses atributos no modelo e depois “repetir” todos os atributos no formulário de criação. Isso não parece muito eficiente, e se fossem 20, 30 ou 50 atributos? 😵‍💫

O `ModelForm` tem em sua implementação uma maneira para lidar com esse tipo de problema que foi mencionado. Ele é um formulário que usa como base um modelo já criado, no qual você pode explicitar os campos que deseja que apareçam para a pessoa usuária.

## `ModelForm` na prática

Usando como base o projeto construído até aqui, você vai implementar o primeiro `ModelForm` que será usado para a criação de novos registros de `Music`. Comece uma nova classe com o nome `CreateMusicModelForm` e faça a herança de `form.ModelForm`. Além disso, para fazer esse formulário funcionar corretamente, será necessário implementar a classe `Meta` dentro da classe `CreateMusicModelForm` (Isso mesmo, uma classe dentro da outra 🤯) e nessa segunda classe implementar dois atributos: `model` e `fields`.

O atributo `model` é usado para indicar o modelo que será usado como base, basta informar a classe. Já o atributo `fields` pode receber a string `__all__` ou uma lista com os nomes dos atributos do modelo que você deseja que apareçam no formulário, sendo que a primeira opção faz com que todos os atributos apareçam. Veja a implementação como fica:

```python
# playlists/forms.py
class CreateMusicModelForm(forms.ModelForm):
    class Meta:
        model = Music
        fields = '__all__'
```

Com o novo formulário implementado basta fazer a substituição na função `create_music` dentro do arquivo `views.py`.

```python
# playlists/views.py
def create_music(request):
    # form = CreateMusicForm()
    form = CreateMusicModelForm()

    if request.method == "POST":
        # form = CreateMusicForm(request.POST)
        form = CreateMusicModelForm(request.POST)

        if form.is_valid():
            Music.objects.create(**form.cleaned_data)
            return redirect("home-page")

    context = {"form": form}

    return render(request, "index.html", context)
```

Você verá que o formulário já estará funcionando 🤩, inclusive, as validações. Se lembra de quando foi falado que seria útil indicar validações para o campo no próprio modelo? Pois é, esse momento é agora. O `ModelForm` já estrutura seus campos inserindo as validações. Tente criar uma música com duração maior que 3600 e verá a mensagem na tela.

Vale lembrar que a classe `ModelForm` não é a solução para todos os problemas. Perceba que toda a personalização que havia sido feita foi perdida - note a ausência do _widget_ de data, por exemplo. Isso porque a instanciação acontece com os dados padrão, assim como era para a classe `Form` antes de ser personalizada.

Uma forma de corrigir isso é personalizar o método inicializador da classe `CreateMusicModelForm`. Neste método é necessário assegurar que o `ModelForm` inicialize corretamente e, em seguida, personalizar os campos que você tem interesse através do atributo `fields`. Esse atributo representa um dicionário, onde as chaves são os nomes dos campos e os valores são classes do tipo `Field`. As classes do tipo `Field` possuem atributos como `label`, `help_text`, `required` e _widget_, que possibilitam assim a personalização. Observe a implementação:

```python
# playlists/forms.py
class CreateMusicModelForm(forms.ModelForm):
    class Meta:
        model = Music
        fields = '__all__'
    
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)
        self.fields["name"].label = "Nome da música"
        self.fields["recorded_at"].label = "Data de gravação"
        self.fields["recorded_at"].widget = forms.DateInput(
                attrs={"type": "date"})
        self.fields["recorded_at"].initial = "2023-07-06"
        self.fields["length_in_seconds"].label = "Duração em segundos"
```

Agora sim! O `ModelForm` está idêntico ao `Form` construído anteriormente. É importante retomar o ponto que não há implementação certa ou errada nesse cenário, tudo depende da aplicação que será construída. Por exemplo, se os nomes padrões fossem bons o suficiente para a aplicação, seguir com a implementação da `ModelForm` seria mais interessante e pouparia algumas linhas de código na aplicação.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/8f109fee-0480-4f28-9e98-bd801b6bdba0)
