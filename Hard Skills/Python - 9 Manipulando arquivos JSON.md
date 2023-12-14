[[Python]]
[[Python - 7 Manipulação de arquivos]]

**JSON** é um formato textual muito utilizado para integração de sistemas. Ele é baseado em um subconjunto de regras **JavaScript**, embora seja independente de linguagem.

Por sua legibilidade e tamanho (é bem leve), além da facilidade de leitura e escrita por seres humanos e máquinas, vem sendo bastante utilizado na web e para troca de informações entre sistemas.

Alguns exemplos de utilização incluem comunicação _back-end_ e _front-end_, e comunicação com sistemas externos (_gateways_ de pagamento, por exemplo) ou internos (como um sistema de autenticação).

A linguagem **Python** já inclui um módulo para manipulação desse tipo de arquivo e seu nome é `json` ([documentação aqui](https://docs.python.org/3/library/json.html)).

Seus principais métodos para manipulação são: `load`, `loads`, `dump`, `dumps`.

🐭 Para demonstrar como é feita a manipulação de arquivos **JSON**, vamos utilizar o arquivo [pokemons.json](https://lms-assets.betrybe.com/lms/pokemons.json) que é uma lista de pokemons com suas informações chave.

> 💡 Para fazer o exemplo, coloque o arquivo `pokemons.json` no mesmo diretório em que estará o seu _script_.

```python
import json  # json é um modulo que vem embutido, porém precisamos importá-lo


with open("pokemons.json") as file:
    content = file.read()  # leitura do arquivo
    pokemons = json.loads(content)["results"]  # o conteúdo é transformado em estrutura python equivalente, dicionário neste caso.
    # acessamos a chave results que é onde contém nossa lista de pokemons

print(pokemons[0])  # imprime o primeiro pokemon da lista
```

A leitura pode ser feita diretamente do arquivo, utilizando o método `load` ao invés de `loads`. O `loads` carrega o `JSON` a partir de um texto e o `load` carrega o `JSON` a partir de um arquivo.

```python
import json


with open("pokemons.json") as file:
    pokemons = json.load(file)["results"]

print(pokemons[0])  # imprime o primeiro pokemon da lista
```

A escrita de arquivos no formato **JSON** é similar à escrita de arquivos comuns, porém temos que transformar os dados primeiro.

```python
import json

# Leitura de todos os pokemons
with open("pokemons.json") as file:
    pokemons = json.load(file)["results"]

# Separamos somente os do tipo grama
grass_type_pokemons = [
    pokemon for pokemon in pokemons if "Grass" in pokemon["type"]
]

# Abre o arquivo para escrevermos apenas o pokemons do tipo grama
with open("grass_pokemons.json", "w") as file:
    json_to_write = json.dumps(
        grass_type_pokemons
    )  # conversão de Python para o formato json (str)
    file.write(json_to_write)
```

Assim como a desserialização, que faz a transformação de texto em formato **JSON** para **Python**, a serialização (caminho inverso) possui um método equivalente para escrever em arquivos de forma direta.

```python
import json

# leitura de todos os pokemons
with open("pokemons.json") as file:
    pokemons = json.load(file)["results"]

# separamos somente os do tipo grama
grass_type_pokemons = [
    pokemon for pokemon in pokemons if "Grass" in pokemon["type"]
]

# abre o arquivo para escrita
with open("grass_pokemons.json", "w") as file:
    # escreve no arquivo já transformando em formato json a estrutura
    json.dump(grass_type_pokemons, file)
```

> 💡 Arquivos JSON não seguem a nomenclatura habitual de leitura e escrita (`write` e `read`), pois são considerados formatos de serialização de dados. Seguem então as mesmas nomenclaturas utilizadas em módulos como [`marshal`](https://docs.python.org/3/library/marshal.html#module-marshal) e [pickle](https://docs.python.org/3/library/pickle.html#module-pickle), que também são formatos de serialização.


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/61e88b4a-b97a-4f96-b5a0-abaa50651e37/lesson/a5c29cd4-90db-444f-8513-ab1823658254)
