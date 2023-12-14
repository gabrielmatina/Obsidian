[[4 - Raspagem de Dados]]

**Beautiful Soup é uma biblioteca Python para extrair dados de arquivos HTML e XML**. Ela é de grande valia para analisar esses arquivos e fornecer maneiras mais simples de navegar, pesquisar e modificar a árvore de análise, podendo economizar várias horas de trabalho.

Veremos como utilizar alguns dos recursos do Beautiful Soup 4, mas antes de analisar as informações precisamos baixar o conteúdo da página que queremos utilizar.

## Primeiros passos

Com base no que você já sabe sobre _web scraping_, já pode ter uma ideia de ferramentas que podemos usar para fazer a requisição para a página e baixar seu conteúdo HTML.

Para nosso exemplo, usaremos a biblioteca `requests`(especificamente na versão 2.27.1) e faremos uma requisição do tipo `get` para uma página de frases.

> bsoup_example.py

```python
import requests

url = "https://quotes.toscrape.com"
page = requests.get(url)
print(page.content)

```

Com o conteúdo da página baixado, vamos ao que interessa, ver como utilizar o Beautiful Soup para fazer sua análise!

## Instalação das bibliotecas

> ⚠️ Lembre-se de estar em um ambiente virtual antes de fazer as instalações de bibliotecas.

Para instalar as bibliotecas `Beautiful Soup` e `requests` basta digitar em seu terminal:

```bash
pip3 install beautifulsoup4==4.11.1 requests==2.27.1
```

Agora podemos importar a lib em nosso arquivo e começar a explorar as funcionalidades e facilidades que a ferramenta oferece.

```python
# import requests
from bs4 import BeautifulSoup

# url = "https://quotes.toscrape.com"
# page = requests.get(url)
html_content = page.text

# Cria uma instância do objeto Beautiful Soup e usa o parser de HTML nativo
# do Python
soup = BeautifulSoup(html_content, "html.parser")

# Utiliza o método prettify para melhorar a visualização do conteúdo
print(soup.prettify())

```

O retorno desse código é o HTML da página, já formatado de uma forma muito amigável para a leitura, não concorda?

## Tipos de objetos do Beautiful Soup

**O Beautiful Soup transforma um documento HTML complexo em uma árvore de objetos Python.** Os quatro tipos de objetos que podemos lidar são `Tag`, `NavigableString`, `BeautifulSoup` e `Comment`. Na documentação, que está disponível inclusive em português, existe uma seção inteira dedicada aos [tipos de objetos](https://www.crummy.com/software/BeautifulSoup/bs4/doc.ptbr/#tipos-de-objetos), mas destacaremos aqui apenas os dois primeiros.

### Tag

Em suma, **um objeto do tipo `Tag` corresponde a uma tag `XML` ou `HTML` do documento original**. Toda _tag_ possui um nome acessível através de `.name`. Por exemplo, quando vemos `<header>`, ele é um elemento do tipo _tag_ e o nome dessa tag é `header`.

As _tags_ também podem ter atributos, como classes, ids e etc. Esses atributos são acessíveis considerando _tag_ como um dicionário e como podem receber múltiplos valores, são apresentados em forma de lista.

```python
# import requests
# from bs4 import BeautifulSoup

# url = "https://quotes.toscrape.com"
# page = requests.get(url)
# html_content = page.text

# soup = BeautifulSoup(html_content, "html.parser")


# acessando a tag 'title'
title = soup.title

# retorna o elemento HTML da tag
print(title)

# o tipo de 'title' é tag
print(type(title))

# o nome de 'title' é title
print(title.name)

# acessando a tag 'footer'
footer = soup.footer

# acessando o atributo classe da tag footer
print(footer['class'])

```

### NavigableString

Uma string corresponde a um texto dentro de uma _tag_ e esse texto fica armazenado na classe `NavigableString`.

```python
# import requests
# from bs4 import BeautifulSoup

# url = "https://quotes.toscrape.com"
# page = requests.get(url)
# html_content = page.text

# soup = BeautifulSoup(html_content, "html.parser")

# title = soup.title
# footer = soup.footer

# retorna o elemento HTML da tag
print(title)

# Acessando a string de uma tag
print(title.string)

# Verificando o tipo dessa string
print(type(title.string))

```

## Buscando na árvore

Assim como nas outras ferramentas apresentadas até aqui, o **Beautiful Soup também possui dois métodos principais para encontrar elementos**. Eles são o **`find()` e `find_all()`** e a essa altura você já deve ter presumido que a diferença básica entre eles é que o primeiro retorna apenas o primeiro elemento que corresponder ao filtro, enquanto o segundo retorna a lista de todos os elementos que baterem com o filtro.

Há várias possibilidades de filtros a serem utilizados dentro dos métodos descritos acima, de _strings_ e _regex_, até funções, e [ler a documentação](https://www.crummy.com/software/BeautifulSoup/bs4/doc.ptbr/#buscando-na-arvore) é essencial para garantir que você está utilizando o método mais adequado para buscar os dados que deseja.

Existem algumas informações que são bem comuns de querermos extrair, como os valores das ocorrências de determinada tag, de um atributo ou mesmo todo o texto da página.

> 👀 **De olho na dica:** Ao executar o código abaixo, tente executar uma impressão por vez, deixando os demais `print`s comentados enquanto isso, para ter uma melhor visualização dos retornos. 😉

```python
# import requests
# from bs4 import BeautifulSoup

# url = "https://quotes.toscrape.com"
# page = requests.get(url)
# html_content = page.text

# soup = BeautifulSoup(html_content, "html.parser")

# Imprime todas as ocorrências da tag "p" da página ou uma lista vazia,
# caso nenhum elemento corresponda a pesquisa
print(soup.find_all("p"))

# Imprime o elemento com o id especificado ou "None",
# caso nenhum elemento corresponda a pesquisa
print(soup.find(id="quote"))

# Imprime todo o texto da página
print(soup.get_text())

# Imprime todas as "divs" que possuam a classe "quote" ou uma lista vazia,
# caso nenhum elemento corresponda a pesquisa
print(soup.find_all("div", {"class": "quote"}))

```

Por debaixo dos panos, `soup.find_all("p")` e `soup.find_all(name="p")` são a mesma coisa, da mesma forma que `soup.find(id="quote")` é o mesmo que `soup.find(attrs={"id": "quote"})`. Isso se deve ao fato de argumentos nomeados diferentes de `name`, `attrs`, `recursive`, `string` e `limit` serem todos colocados no dicionário dentro do parâmetro `attrs`.

Para dar uma visão geral do que podemos utilizar e da simplicidade de fazer _scraping_ com o Beautiful Soup, vamos fazer algo similar ao que fizemos no exemplo de Selenium e raspar as informações de uma página de notícias do site Tecmundo.

> scrape_bsoup.py

```python
import requests
from bs4 import BeautifulSoup


# Acessa uma URL e retorna um objeto do Beautiful Soup
def get_html_soup(url):
    page = requests.get(url)
    html_page = page.text

    soup = BeautifulSoup(html_page, "html.parser")
    soup.prettify()
    return soup


# Recebe um objeto soup e retorna informações das notícias de uma página
def get_page_news(soup):

    # Define uma lista vazia a ser populada com os dados do scraping
    news = []

    # Percorre todos os elementos da tag 'article' com a classe especificada
    for post in soup.find_all(
        "article", {"class": "tec--card tec--card--medium"}
    ):

        # Cria um dicionário para guardar os elementos a cada iteração
        item = {}

        # Cria um item chamado tag no dicionário para guardar a tag do post
        # Primeiro pesquisa pela div com a classe específica
        # Depois pela tag 'a' dentro dos resultados do primeiro filtro
        # Por fim, traz o resultado da string dentro da tag a
        item["tag"] = post.find("div", {"class": "tec--card__info"}).a.string

        # Mesma lógica da busca anterior
        item["title"] = post.find("h3", {"class": "tec--card__title"}).a.string

        # Parecido com o que foi feito anteriormente, mas dessa vez pega
        # o atributo 'href' dentro da tag 'a'
        item["link"] = post.find("h3", {"class": "tec--card__title"}).a["href"]

        # Mesma lógica da primeira busca, mas trazendo a string dentro da 'div'
        # direto
        item["date"] = post.find(
            "div", {"class": "tec--timestamp__item z--font-semibold"}
        ).string

        # Mesma lógica da busca anterior
        item["time"] = post.find(
            "div", {"class": "z--truncate z-flex-1"}
        ).string

        # Adiciona os itens criado no dicionário à lista 'news'
        news.append(item)

    return news


print(get_page_news(get_html_soup("https://www.tecmundo.com.br/novidades")))

```

Para fazer a paginação e extrair todas as notícias do site, a lógica é bem similar a utilizada com o `Parsel` e o `Selenium`.

```python
# import requests
# from bs4 import BeautifulSoup


# # Acessa uma URL e retorna um objeto do Beautiful Soup
# def get_html_soup(url):
#     page = requests.get(url)
#     html_page = page.text

#     soup = BeautifulSoup(html_page, "html.parser")
#     soup.prettify()
#     return soup


# # Recebe um objeto soup e retorna informações das notícias de uma página
# def get_page_news(soup):

#     news = []

#     for post in soup.find_all(
#         "article", {"class": "tec--card tec--card--medium"}
#     ):

#         item = {}

#         item["tag"] = post.find("div", {"class": "tec--card__info"}).a.string

#         item["title"] = post.find("h3", {"class": "tec--card__title"}).a.string

#         item["link"] = post.find("h3", {"class": "tec--card__title"}).a["href"]

#         item["date"] = post.find(
#             "div", {"class": "tec--timestamp__item z--font-semibold"}
#         ).string

#         item["time"] = post.find(
#             "div", {"class": "z--truncate z-flex-1"}
#         ).string

#         news.append(item)

#     return news


# Recebe um objeto soup e retorna o link que redireciona
# para a página seguinte, caso ele exista
def get_next_page(soup_page):
    try:

        # Busca pela tag 'a' com as classes específicas do link desejado
        return soup_page.find(
            "a",
            {"class": "tec--btn"},
        )["href"]
    except TypeError:
        return None


# Recebe uma URL e retorna uma lista com todas as notícias do site
def scrape_all(url):
    all_news = []

    # Enquanto a pesquisa pelo link que vai para a página seguinte existir
    while get_next_page(get_html_soup(url)) is not None:

        # Imprime a URL da página seguinte
        print(get_next_page(get_html_soup(url)))

        # Adiciona os elementos da lista com as notícias de cada
        # página na lista 'all_news'
        all_news.extend(get_page_news(get_html_soup(url)))

        # define a página seguinte como URL para a próxima iteração
        url = get_next_page(get_html_soup(url))

    return all_news


# Vamos começar perto das últimas páginas pra não ter que fazer a requisição
# do site inteiro
print(scrape_all("https://www.tecmundo.com.br/novidades?page=11030"))

```

As duas ferramentas que você viu hoje te deram mais possibilidades de fazer raspagem de dados e podem ampliar seu ferramental para ir muito além na sua carreira, explorando outras funcionalidades delas, buscando como integrá-las ou até mesmo procurando outras ferramentas que tragam ainda mais simplicidade e praticidade ao que você precisa fazer.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/61c046bb-0372-49d7-83c1-fe68b9e01d73/day/da613349-45be-4061-a2e9-f651479a2a51/lesson/19b63fed-4017-4848-9668-efa9631d2b43)

