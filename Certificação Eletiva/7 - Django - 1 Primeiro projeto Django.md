[[7 - Django]]

Depois de entender o que é o Django, as bases de seu design e suas principais vantagens, é hora de adicionarmos um pouco de prática aos seus aprendizados!

No exemplo da aula de hoje, desenvolveremos uma aplicação simples de um e-commerce, no qual será possível cadastrar e consultar produtos em estoque. Ainda não trabalharemos a criação de APIs (não se preocupe, essa hora vai chegar muito em breve! 😉). Por ora, apenas exploraremos os recursos e configurações básicas do Django, mas você já pode vislumbrar o mar de possibilidades que este _framework_ oferece!

## Preparando o ambiente

Antes de começarmos a desenvolver, precisamos preparar o ambiente de desenvolvimento. A primeira coisa a se fazer é verificar a versão do Python que estamos utilizando. Você pode fazer isso com o comando:

```bash
python3 --version
```

Caso a versão seja inferior a 3.10, você precisará atualizar o Python. Para isso, você pode utilizar o **Pyenv**, basta seguir nosso [tutorial do Guia de configuração de ambiente](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/f04cdb21-382e-4588-8950-3b1a29afd2dd/section/aa76abc8-b842-40d9-b5cc-baa960952129/lesson/0fe67ea0-1046-4b55-a37c-44afcfa9ed0a). Isso é necessário porque mais à frente utilizaremos uma biblioteca que **não funciona bem com a versão 3.9 ou inferiores** do Python.

Para começar, vamos criar um novo diretório para o nosso projeto e entrar nele:

```bash
mkdir ecommerce && cd ecommerce
```

Em seguida, vamos criar um ambiente virtual para o nosso projeto e ativá-lo:

```bash
python3 -m venv .venv
source .venv/bin/activate
```

Agora, vamos instalar o Django:

```bash
pip install django
```

E, finalmente, vamos iniciar um projeto chamado **ecommerce**, no diretório atual:

```bash
django-admin startproject ecommerce .
```

Simples assim e nosso primeiro projeto foi criado! 🎉

Logo, logo veremos melhor a estrutura de arquivos que o Django criou para nós, mas agora, vamos ver como é simples executá-lo?

## Executando o projeto

Para executar o projeto, basta executar o comando:

```bash
python3 manage.py runserver
```

> Num instante falamos da mensagem em vermelho que apareceu pra você! 😉

Você deve ter notado que um arquivo de banco de dados `db.sqlite3` foi criado _automagicamente_ no diretório do projeto. Geralmente, precisamos configurar uma conexão com o banco de dados em nossas aplicações, mas isso é um pouco diferente no Django.

Lembra que falamos que um dos diferenciais deste framework é a variedade de recursos prontos para uso? Aqui está um exemplo prático disso, pois o Django já vem com um banco de dados SQLite configurado por padrão, para facilitar o desenvolvimento.

Mais adiante, veremos como configurar um banco de dados diferente, mas, por ora, vamos seguir com o SQLite mesmo.

Se você acessar o endereço [http://localhost:8000](http://localhost:8000/) no seu navegador, você verá essa página:

![Página inicial do Django](https://content-assets.betrybe.com/prod/f2a5bbcb-65ab-4058-b8bc-1687e1167533-P%C3%A1gina%20inicial%20do%20Django.png)


Bem mais legal que uma simples página em branco, não é mesmo? 😎

Ao executar o projeto, algo que pode ter chamado sua atenção é a mensagem em vermelho no terminal:

```bash
You have 18 unapplied migration(s). Your project may not work properly until you apply the migrations for app(s): admin, auth, contenttypes, sessions.
# Em português: Você tem 18 migrations não aplicadas. Seu projeto pode não funcionar corretamente até que você aplique as migrations para os apps: admin, auth, contenttypes, sessions.
```

Apesar de a mensagem estar em vermelho, ela não representa um erro, apenas um aviso (_warning_). Ele acontece porque o Django possui algumas _migrations_ internas que ainda não foram aplicadas ao banco de dados. Para aplicá-las, **abra um novo terminal**, ative o ambiente virtual e execute o comando:

```bash
python3 manage.py migrate
```

![Terminal após rodar migrations](https://content-assets.betrybe.com/prod/f2a5bbcb-65ab-4058-b8bc-1687e1167533-Terminal%20ap%C3%B3s%20rodar%20migrations.png)


Se dermos uma olhada no banco de dados agora, veremos que ele foi criado e que o Django criou algumas tabelas internas para nós.

> Como o banco de dados padrão é o SQLite, a forma mais fácil de ver as tabelas é no próprio VS Code utilizando a extensão `SQLite viewer`, mas como substituiremos este banco de dados daqui a pouco, fique a vontade para apenas observar a imagem abaixo, que mostra como é a visualização com a extensão:

![Tabelas do banco de dados SQLite](https://content-assets.betrybe.com/prod/f2a5bbcb-65ab-4058-b8bc-1687e1167533-Tabelas%20do%20banco%20de%20dados%20SQLite.png)
Tabelas do banco de dados SQLite

## Entendendo a estrutura do projeto

Já demos início a um projeto, o executamos, porém, agora surge a pergunta: o que são todos aqueles arquivos? Vamos agora aprofundar nosso entendimento sobre esse assunto!

Até agora, a estrutura de diretórios do projeto é basicamente a seguinte:

```bash
ecommerce
│   ├── .venv
│   ├── ecommerce
│      ├── __pycache__
│      ├── __init__.py
│      ├── asgi.py
│      ├── settings.py
│      ├── urls.py
│      └── wsgi.py
└── db.sqlite3
└── manage.py
```

Passando rapidamente por cada um dos arquivos dentro do diretório `ecommerce`, que é o diretório do projeto em si, temos os arquivos:

- `manage.py`: é o arquivo usado internamente quando **executamos comandos do Django** - como o `runserver` que executamos anteriormente.
- `__init__.py`: arquivo que indica que o diretório é um pacote Python - já utilizamos este arquivo lá na seção 1, lembra? 😉
- `asgi.py`: arquivo de configuração do ASGI (Asynchronous Server Gateway Interface), que é o protocolo usado pelo Django para comunicação entre servidores web e aplicações web para lidar com solicitações assíncronas e em tempo real.
- `settings.py`: arquivo de **configuração do projeto**, que contém todas as configurações do Django para o projeto. É aqui que configuramos, por exemplo, o banco de dados que será usado, o idioma padrão da aplicação, etc. Veremos este arquivo com mais atenção daqui a pouco. 🤓
- `urls.py`: arquivo de configuração de **rotas do projeto**. Vamos explorar este arquivo com mais detalhes em breve. 🤩
- `wsgi.py`: arquivo de configuração do WSGI (Web Server Gateway Interface), que é o protocolo usado pelo Django para comunicação entre servidores web e aplicações web para lidar com solicitações HTTP.
- `__pycache__`: diretório que contém arquivos gerados automaticamente pelo Python para otimizar o carregamento de módulos.

Dois arquivos valem uma atenção especial: `settings.py` e `urls.py`. Bora dar uma olhada neles?

### Arquivo `settings.py`

Este é o arquivo que reúne as principais configurações do projeto, com várias dessas configurações já definidas com valores-padrão. Vamos entender melhor algumas dessas _configs_?

- `SECRET_KEY` é uma chave de segurança que o Django utiliza para criptografar dados sensíveis, como senhas de pessoas usuárias, por exemplo. Ela já vem com um valor por padrão, mas explicitamente dada como insegura e por isso, é recomendável substitui-la por uma chave personalizada forte, especialmente em ambientes de produção.
- `DEBUG` é um booleano que indica se o modo de depuração (_debug_) está ativado ou não. Durante o desenvolvimento, ter esse modo ativado é muito útil para ajudar a identificar e corrigir bugs, o valor default (padrão) dessa variável é true justamente por isso. Contudo, ele pode trazer algumas vulnerabilidades à segurança, como, por exemplo, mostrar informações sensíveis do projeto - algo ruim se mostrado para uma pessoa usuária. Por isso, é importante que ele esteja desativado quando o projeto estiver em produção.
- `ALLOWED_HOSTS` é uma lista de nomes de domínios, subdomínios ou endereços IP que o Django permite que acessem o projeto. Você pode usar o valor `'*'`, caso queira dar acesso a todos, ou definir uma lista com os grupos que terão acesso ao projeto, por exemplo, `['exemplo.com', 'subdomínio.exemplo.com', '192.168.1.1']`.
- `INSTALLED_APPS` é uma lista de apps que serão acoplados no projeto Django. Alguns já vêm instalados por padrão, mas os _apps_ criados por você para o projeto podem compor essa variável também. Veremos como fazer isso em breve! 🤩
- `MIDDLEWARE` é uma lista de middlewares que o Django utiliza para fazer algumas coisas como, por exemplo, o middleware de autenticação de pessoa usuária. Sua lógica é similar a dos Middlewares do Express, mas entraremos em detalhes sobre eles apenas na próxima seção.
- `TEMPLATES` é uma lista de diretórios em que o Django irá procurar por templates HTML.
- `DATABASES` é a configuração de banco de dados do projeto. Como o Django já vem com o SQLite instalado por padrão, ele já vem com a configuração do **SQLite**, mas podemos trocar por outros.
- `LANGUAGE_CODE` é a configuração de idioma padrão do projeto. Por padrão, ele vem com o inglês, mas podemos alterar para qualquer outro.

> **De olho na dica 👀:** você pode alterar a linguagem padrão do projeto Django para português apenas, alterando a variável `language_code` para `pt-br`. Experimente fazer isso e atualizar a página para ver a tela inicial está traduzida! 🤩

### Arquivo `urls.py`

Já acessamos a rota raiz do projeto quando rodamos o servidor e acessamos a URL `localhost:8000`. Apesar de não termos definido nenhuma rota até aquele momento, a URL raiz já traz por padrão um retorno visual: uma página com o foguetinho informando que deu tudo certo com a instalação.

Como dito anteriormente, este arquivo reúne as rotas do projeto, com alguns valores já definidos por padrão. Vamos entender melhor como uma rota é definida?

A primeira coisa que temos é a função `path`, que define uma rota. Como parâmetro ela recebe a URL que será acessada e a função que será executada quando a URL for acessada.

Uma surpresa é que já temos uma rota definida no arquivo, a `admin/`, que é a interface administrativa que o Django fornece para o projeto. Vamos explorar ela com mais detalhes em breve. 😎

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/32af04d3-9bec-4278-85dd-bee03a8887fb/lesson/e589a72a-8419-4fc8-b3dd-f76fa7b7cbe4)
