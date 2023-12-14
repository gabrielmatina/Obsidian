# NoSQL

O **Airbnb** é um serviço online que permite que pessoas possam anunciar, pesquisar e reservar acomodações e meios de hospedagem. É também conhecido como a maior rede hoteleira do planeta sem possuir nenhum imóvel físico para este fim.

Você deve estar se perguntando: “Porque estamos falando sobre Airbnb? Esperava estudar sobre NoSQL e MongoDB!” 🤔

Trouxemos o caso do **Airbnb** pela seguinte razão: O **Airbnb** usa em sua solução uma base de dados não relacional!

E agora vamos analisar uma situação na qual o **Airbnb** tem que lidar todos os dias, como representar endereços no banco de dados. Isso mesmo como representar endereços!

> “Mas endereços não são compostos por nome da rua, número, bairro, cidade, estado e CEP, certo?” 🤔 Na maioria das cidades do Brasil sim! Mas existem cidades em que a lógica de endereçamento dos imóveis é diferente do habitual conhecido por nós. Por exemplo, podemos citar a cidade de Brasília.

O sistema de endereços utilizado em Brasília segue uma ordenação matemática, de acordo com o projeto do arquiteto Lúcio Costa (para mais detalhes leia [este artigo](https://g1.globo.com/distrito-federal/noticia/2014/04/nao-entende-enderecos-em-brasilia-g1-decifra-sopa-de-letrinhas-veja.html)). Para quem mora em Brasília consegue compreender e até se deslocar para o seguinte endereço **SMHN Q2**, que é o endereço do **Hospital Regional de Asa Norte**.

Em **Fernando de Noronha**, arquipélago pertencente ao estado do **Pernambuco** existem pontos turísticos e imóveis que não possuem uma rua. Geralmente sua localização se dá por pontos de referência, por ser pequena, fica fácil para as pessoas residentes se localizarem (essa mesma premissa vale para cidades pequenas ou áreas rurais).

Brasília e Fernando de Noronha são apenas dois de inúmeros casos os quais o **Airbnb** tem que lidar todos os dias. Imagine agora quando a operação do **Airbnb** se estende para outros países, que podem possuir maneiras distintas de endereçar imóveis.

Modelar a estrutura de endereços em um banco relacional como, por exemplo, o **MySQL** trás uma série de complicações, pois pense, se para cada nova forma de atender novos endereços, for necessário realizar uma modificação no banco!?! Isso impacta em: parar a aplicação, realizar o backup do banco, alterar as tabelas do banco, verificar se os dados do banco não se corromperam no processo e iniciar novamente a aplicação.

Essa é uma operação que demanda tempo e que faz o serviço ficar indisponível durante esse processo, ou seja, nada de reservar uma acomodação no **Airbnb**, nada de receita sendo gerada para a empresa. 🤨

Nesse contexto surgem os bancos de dados não relacionais ou **NoSQL**, cuja a principal diferença dos bancos de dados relacionais é a estrutura flexível utilizada para armazenar os dados e a alta disponibilidade alcançada quando comparado aos bancos de dados relacionais.

De acordo com o **teorema CAP**, criado pelo cientista da computação **Eric Brewer**, qualquer sistema distribuído de dados pode apenas prover duas das três propriedades seguintes:

- **Consistência (Consistency)**: Significa que todas as pessoas que utilizam o banco de dados visualizam os mesmos dados não importando qual servidor elas estejam conectadas (no caso do banco de dados estar distribuído em mais de um servidor). Para que isso aconteça, sempre que os dados forem gravados em um nó (servidor), ele deve ser instantaneamente replicado para todos os outros nós (servidores) antes que a gravação seja considerada **bem-sucedida**;
- **Disponibilidade (Availability)**: Significa que qualquer pessoa que realizar uma solicitação de dados, obterá uma resposta! Mesmo que um ou mais nós (servidores) estejam desativados. Em outras palavras, todos os nós em funcionamento do banco de dados retornam uma resposta válida para qualquer solicitação, sem exceção;
- **Tolerância a Partição (Partition Tolerance)**: É a quebra de comunicação (e possivelmente de dados) dentro de um banco de dados que funciona sobre vários servidores (nós) permitindo que, em caso de uma ou mais falhas de comunicação entre um ou mais servidores, o sistema deve continuar a funcionar.

Antes de tudo, aplicações que possuem uma carga de acesso muito grande (como o **Airbnb**) não configuram o banco de dados da aplicação em um único servidor. Além de caro, não é escalável e não é seguro (se o servidor apresentar uma falha no hardware, toda a aplicação para).

Naturalmente, a solução é distribuir em vários servidores, também chamados de nós. Toda a operação do banco de dados, ao fazer essa separação, a propriedade **Partition Tolerance**, ou simplesmente **P**, é alcançada pelo banco de dados.

Como o **teorema CAP** dis que qualquer sistema distribuído de dados pode prover apenas duas das três propriedades, temos as seguintes combinações para bancos de dados:

- **Banco de dados CP**: Um banco de dados do tipo **CP (Consistency and Partition Tolerance)** entrega consistência e tolerância a falhas em detrimento da disponibilidade. Quando uma partição (divisão) ocorre entre dois nós, por exemplo, uma divisão de uma tabela em duas, por aplicação de uma forma normal, o nó não consistente deverá ser desativado (indisponível) até que tudo se resolva;
- **Banco de dados AP**: Um banco de dados do tipo **AP (Availability and Partition Tolerance)** entrega disponibilidade e tolerância a falhas em detrimento da consistência. Quando ocorre uma partição, todos os nós permanecem disponíveis, exceto aqueles que estão trabalhando no processo de particionamento. Quando a partição é resolvida, todos os nós realizam uma sincronização dos dados para corrigir as inconsistências do sistema.

Bancos de dados relacionais são do tipo **CP**, pois quando realizamos uma modificação na estrutura do banco de dados ele fica indisponível para garantir a consistência (permitir que todas as pessoas tenham acesso aos dados mais recentes).

Já os bancos de dados **NoSQL** são do tipo **AP**, pois quando realizamos uma modificação na estrutura do banco de dados, o mesmo não fica indisponível. Na pior das hipóteses uma pesquisa no banco de dados não retornará os dados mais recentes, contudo ainda retornará uma resposta.

Por este motivo o **Airbnb** utiliza um banco de dados **NoSQL**, pois, tomando como exemplo o caso dos endereços, se for necessário realizar alguma modificação na forma como os dados estão armazenados, não é necessário parar o banco de dados para realizar a mudança. Os dados existentes podem ser acessados enquanto os novos dados estão sendo atualizados!

Dessa forma a aplicação não fica indisponível por conta de uma atualização nos dados! 😎

> E onde entra o MongoDB nessa história? 🤔 O **MongoDB** é um banco de dados **NoSQL** orientado a documento (falaremos em breve o que isso significa) e, de acordo com uma pesquisa realizada [C# Corner](https://www.c-sharpcorner.com/article/what-is-the-most-popular-database-in-the-world/), o **MongoDB** é o quinto banco de dados mais utilizado mundo e, entre os bancos de dados **NoSQL**, ocupa a primeira colocação.

Ele será o banco de dados **NoSQL** que utilizaremos ao longo desta seção e entraremos em mais detalhes sobre o **MongoDB** na próxima lição! 🚀

Mas antes não se esqueça de beber uma aguá e de arrumar essa postura! 😉


# MongoDB

Como dito anteriormente, o **MongoDB** é um banco de dados **NoSQL** mais utilizado no mundo.

Um registro no **MongoDB** é chamado de **documento**, o qual é uma estrutura de dados composta por pares de chaves e valores. Documentos no **MongoDB** são similares a objetos **JSON**, os quais estamos acostumados a utilizar em nossas **APIs Rest**. Os valores dos campos podem incluir outros documentos, _arrays_ de documentos.

|![Exemplo de documento](https://content-assets.betrybe.com/prod/92871dca-469a-4ab9-877b-3b005e78a257-Exemplo%20de%20documento.svg)
Ilustração de um documento do **MongoDB**|

As vantagens em utilizarmos documentos são:

- Documentos correspondem a tipos de dados nativos em muitas linguagens de programação;
- Documentos embarcados e **arrays** reduzem a necessidade de realizar **JOIN** em pesquisas.

> Documentos embarcados, são documentos, dentro de documentos! Vamos ver muito disso em nosso conteúdo! Documentos embarcados, são documentos, dentro de documentos! Vamos ver muito disso em nosso conteúdo! Um conjunto de documentos é chamado de **coleção**. Coleções são análogas as tabelas nos bancos de dados relacionais.

![Exemplo de Coleção](https://content-assets.betrybe.com/prod/92871dca-469a-4ab9-877b-3b005e78a257-Exemplo%20de%20Cole%C3%A7%C3%A3o.svg)
Uma coleção de documento do **MongoDB**

Os documentos são armazenados em um formato chamado de **BSON** (Binary JSON), que é uma representação binária de documentos JSON, além de possuir mais tipos de dados que um JSON convencional. Caso deseje se aprofundar mais sobre este formato, recomendamos ler este [documento](https://www.mongodb.com/docs/manual/core/document/#bson-document-format).

Podemos organizar nossas coleções no **MongoDB** em bancos de dados, similar ao que é feito no **MySQL**. Contudo, é necessário fornecer uma descrição das coleções, da mesma forma que precisamos no **MySQL** para criar as tabelas antes de executar uma operação. 🙃

No **MongoDB** ao criarmos um banco de dados, podemos adicionar dados da maneira que quisermos, utilizando a estrutura que achamos melhor! 👍

Não se preocupe, vamos ver isso na prática! Mas antes vamos configurar um container Docker com o **MongoDB** para termos um ambiente no qual possamos realizar alguns experimentos! 🚀


# Utilizando MongoDB com Docker

Primeiramente, certifique-se de que o Docker está devidamente instalado em sua máquina e em execução. 😉

Você irá utilizar uma imagem Docker **MongoDB**, a qual irá inicializar um servidor **MongoDB** para podermos realizar nossos experimentos nesse banco de dados **NoSQL**.

> 👀 Observação: Para mais informações sobre a imagem Docker que será utilizada, consulte a documentação da imagem no [Docker Hub](https://hub.docker.com/) Inicialmente, vamos executar o seguinte comando:

```bash
docker run --name mongodb_v6 -d -p 27017:27017 mongo:6.0
```

O comando acima inicializa um container chamado **mongodb_v6**, com a imagem do MongoDB em sua **versão 6** e vincula a **porta 27017** do seu computador local com a mesma porta do container. 😉

## Executando o shell do MongoDB no Docker

Para manipular os banco de dados, coleções e documentos no **MongoDB**, utilizaremos uma ferramenta de linha de comando chamada **mongosh** (Mongo Shell).

Para termos acesso a um shell com o **mongosh** no container Docker que acabamos de iniciar, vamos executar o comando a seguir:

```bash
docker exec -it mongodb_v6 mongosh
```

Após executar o comando acima, você deverá visualizar o terminal do **mongosh** conforme a imagem a seguir:

![Terminal do mongosh](https://content-assets.betrybe.com/prod/e0bc4314-c3ff-4593-a5df-22a928cc8290-Terminal%20do%20mongosh.png)
Terminal do mongosh

> 👀 Observação: Para mais informações sobre o `mongosh`, [acesse sua documentação](https://www.mongodb.com/docs/mongodb-shell/) para mais detalhes.

## Importando uma base de dados para o MongoDB

Estamos perto de iniciar nossos experimentos no **MongoDB**, mas antes vamos popular um banco de dados com alguns documentos!

Tendo como inspiração o **Airbnb**, vamos trabalhar com uma base de dados de imóveis para locação. Vamos chamar nosso banco de dados de **TryBnB**! 👍

Vale ressaltar que estamos utilizando um **container Docker** para usar o MongoDB em nosso computador e, portanto, precisamos copiar os arquivos que desejarmos da nossa máquina para o container!

> **Atenção ⚠️:** Vamos usar o arquivo `.JSON` que está disponível neste **[link](https://lms-assets.betrybe.com/lms/trybnb.json)** Para realizar a cópia de um arquivo do computador local para o container, podemos usar o comando `docker cp`. Esse comando segue a seguinte estrutura:

![Estrutura do comando docker cp](https://content-assets.betrybe.com/prod/e0bc4314-c3ff-4593-a5df-22a928cc8290-Estrutura%20do%20comando%20docker%20cp.svg)
Estrutura do comando docker cp

Então, após realizar o download do arquivo JSON, em uma **nova aba ou janela do terminal**, vá até o diretório onde foi salvo o arquivo JSON após o download e execute o seguinte comando:

```bash
docker cp trybnb.json mongodb_v6:/tmp/trybnb.json
```

Bacana, agora temos o arquivo JSON no **container**! Podemos então realizar o procedimento de carregar o arquivo **JSON** para o **MongoDB**. Logo, vamos utilizar uma ferramenta chamada **mongoimport**.

A ferramenta [`mongoimport`](https://docs.mongodb.com/database-tools/mongoimport/) importa conteúdo de um arquivo `.JSON`, `.CSV` ou `.TSV` criados pela ferramenta utilitária **[mongoexport](https://www.mongodb.com/docs/database-tools/mongoexport/)**.

Vamos utilizar o comando `docker exec` para executar o comando **mongoimport** dentro do container a partir do nosso computador local. Esse comando segue a seguinte estrutura:

![Estrutura do comando docker exec](https://content-assets.betrybe.com/prod/e0bc4314-c3ff-4593-a5df-22a928cc8290-Estrutura%20do%20comando%20docker%20exec.svg)
Estrutura do comando docker exec

Agora podemos realizar a importação do arquivo **JSON** executando o comando a seguir:

```bash
docker exec mongodb_v6 mongoimport -d trybnb -c places --file /tmp/trybnb.json --jsonArray
```

Ao executar o comando acima, será importado 12 registros para a coleção **places**, a qual está no banco de dados **trybnb** e gerenciado pelo **MongoDB**. A imagem abaixo mostra a saída esperada ao executar o comando acima:

![Saída esperada ao executar o comando mongoimport via docker exec](https://content-assets.betrybe.com/prod/e0bc4314-c3ff-4593-a5df-22a928cc8290-Sa%C3%ADda%20esperada%20ao%20executar%20o%20comando%20mongoimport%20via%20docker%20exec.png)
Saída esperada ao executar o comando mongoimport via docker exec

Nesse ponto, já podemos manipular os documentos importados através do **[mongosh](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/61c046bb-0372-49d7-83c1-fe68b9e01d73/day/0bd32ccc-ca93-4dbe-8d42-f782775355e2/lesson/7916e220-d8c3-4c6c-ad4e-062a339ad84a#executando-o-shell-do-mongodb-no-docker)**. Execute os seguinte comandos para listar os documentos cadastrados:

```python
use trybnb
db.places.find()
```

- Na primeira linha, usamos o comando `use trybnb`, para dizer ao mongosh se conectar ao banco de dados **trybnb**;
- Na segunda linha, usamos o comando `db.places.find()`, para listar todos os documentos presentes na coleção **places**.

Ao executar esses dois comandos, você visualizará os documentos que foram importados anteriormente! 🎉

Não se preocupe com os detalhes nesse momento, pois, nas próximas lições, aprofundaremos nossos estudos na recuperação de dados no **MongoDB** 🚀


# Recuperando Dados

Uma das ações mais elementares que podemos realizar em um banco de dados é a de recuperar dados armazenados. Vale salientar que uma pesquisa em uma base de dados, independente de ser realizada em um banco relacional ou não relacional.

Na lição anterior, estudamos como importar uma base de dados no MongoDB. E foi visto como importar uma coleção chamada **places** para dentro de uma base de dados, chamada de **trybnb**, no **MongoDB**.

> **Relembrando 🧠:** Uma **coleção** (equivalente a uma tabela no modelo relacional) é o nome que se dá a um conjunto de documentos e um **documento** é uma estrutura de dados composta por pares de campos e valores (equivalente a um registro no modelo relacional).

## Realizando uma busca por todos os documentos

Antes de tudo, estando no terminal do **mongosh** e vamos nos conectar ao banco de dados **trybnb** executando o seguinte comando:

```python
use trybnb
```

Ao executar o comando acima, você receberá uma mensagem dizendo `switched to db trybnb` e o **prompt** do terminal mudará para `trybnb>`. Isso significa que o mongosh está operando a partir desse momento no banco de dados **trybnb**.

Se houvessem outros bancos de dados e, caso desejasse mudar de banco, bastaria digitar `use <nome_do_banco>` para efetivar tal ação.

A primeira pergunta que podemos realizar ao MongoDB sobre os dados do banco trybnb é: _“quais são os imóveis cadastrados no trybnb?”_. Essa pergunta é o equivalente a realizar uma busca por **todos os documentos cadastrados na coleção places**. Logo, podemos escrever o seguinte comando no mongosh:

```python
db.places.find()
```

Como resposta ao comando, será exibido todos os documentos cadastrados na coleção _places_ que é a resposta da nossa pergunta inicial. O comando anterior serve para recuperar dados de uma coleção. Formalmente ele é composto dos seguintes elementos:

![Estrutura do método find](https://content-assets.betrybe.com/prod/5940df7e-37d1-4781-ad46-df2d2868e133-Estrutura%20do%20m%C3%A9todo%20find.svg)
Estrutura do método find

Baseado na estrutura descrita da figura acima, podemos pesquisar por documentos cadastrados em coleções de um bancos de dados no MongoDB. Em um banco de dados relacional, o correspondente do método `db.places.find()` seria o `SELECT * FROM places`.

> **De olho na dica 👀:** Aproveite que recuperamos todos os documentos para visualizar os campos e valores existentes nos mesmos.

## Realizando a contagem de documentos

Suponha que necessitamos responder a seguinte pergunta: _“Quantos imóveis estão cadastrados no trybnb?”_. Diferente da primeira pergunta que pretendia responder **quais são** os imóveis, a pergunta atual visa responder **quantos são**. Essa pergunta é o mesmo que obter a quantidade de documentos armazenados na coleção _places_ e podemos responde-la utilizando o seguinte comando:

```python
db.places.countDocuments()
```

O método `countDocuments()` realiza a contagem de documentos de uma determinada coleção (no nosso caso, da coleção **places**). Após sua execução, será exibido o valor `12` que é a quantidade de documentos cadastrados na coleção _places_, resposta da nossa pergunta.

## Recuperando documentos baseado em um critério

Também é possível recuperar documentos baseado em algum critério de seleção, similar a clausula `WHERE` dos banco de dados relacionais.

Não foi comentado antes, mas o método `find()` pode receber dois parâmetros:

- Parâmetro **query**;
- Parâmetro **projection**.

Ambos os parâmetros são opcionais e, quando não informados, o método `find()` realiza a busca de todos os documentos (como mostrado anteriormente).

A Figura a seguir, exibe a estrutura do método `find()` com os parâmetros **query** e **projection**:

![Estrutura do método find com os parâmetros query e projection](https://content-assets.betrybe.com/prod/5940df7e-37d1-4781-ad46-df2d2868e133-Estrutura%20do%20m%C3%A9todo%20find%20com%20os%20par%C3%A2metros%20query%20e%20projection.svg)

Estrutura do método find com os parâmetros query e projection

> **Atenção ⚠️:** Se omitirmos os parâmetros **query** e **projection**, ex. `db.places.find()`, ou passarmos objetos vazios como parâmetros representando esses parâmetros, ex. `db.places.find({}, {})`, o resultado será o mesmo, ou seja, será retornado todos os documentos da coleção. Vamos explorar primeiramente o parâmetro _query_ e, em seguida, o parâmetro _projection_! 👍

### Parâmetro query

Suponha que queremos saber qual é o imóvel cujo o campo **_id** seja igual a **7** (uma pergunta a ser respondida pelo banco de dados). Podemos obter essa resposta adicionando o parâmetro _query_ ao método `find()` da seguinte forma:

```python
db.places.find({ '_id': 7 })
```

> **De olho na dica 👀:** Os espaços em branco utilizados entre os parênteses para separar os elementos não são obrigatórios pois possuem um caráter puramente estético, ou seja, se escrevêssemos `db.places.find({'_id':7})`, seria obtido o mesmo resultado. Ao executar o comando acima, receberemos como resultado o documento cujo o campo **_id** é igual a **7**. Em um banco de dados relacional, o correspondente do método `db.places.find({ '_id': 7 })` seria o `SELECT * FROM places WHERE _id = 7`.

O parâmetro _query_ é o objeto `{ '_id': 7 }` e, neste caso, o parâmetro _projection_ foi omitido (mas poderíamos passar um objeto vazio como segundo parâmetro para representa-lo, ficando `db.places.find({ '_id': 7 }, {})`).

> **Atenção ⚠️:** O MongoDB utiliza o campo _id como identificador dos documentos, funcionando de forma similar a coluna id (chave primária) dos bancos de dados relacionais. Agora vamos supor que gostaríamos de saber quais imóveis estão localizados no Brasil, ou seja, uma nova pergunta para ser respondida com os dados do banco **trybnb**. Existe um campo chamado **country_code** cujo valor é o código do país do imóvel. Contudo este campo está dentro de um objeto chamado **address**, o qual também é um campo.

Para responder a pergunta através de uma pesquisa ao banco de dados **trybnb**, temos que utilizar o método `find()` passando o parâmetro _query_ com o campo que desejamos utilizar como critério de seleção e o valor que discriminará os documentos dos demais.

Neste caso, como existe um aninhamento de campos (o campo **country_code** é filho do campo **address**) teremos que utilizar um campo composto (em inglês também acharemos como `dotNotation`) para filtrar os dados. Em outras palavras, temos que executar o seguinte comando no terminal do _mongosh_:

```python
db.places.find({ 'address.country_code': 'BR' })
```

Ao executar o comando acima, receberemos como resultado um conjunto de documentos cujo o valor de `address.country_code` é igual a `BR`. Em um banco de dados relacional seria o equivalente realizar uma consulta `SELECT` com um `JOIN`, dado que, pela terceira forma norma, **places** seria uma tabela e **address** seria outra tabela.

O grande barato, e uma das vantagens, dos bancos de dados não relacionais, é que podemos deixar os dados sobre determinada entidade do mundo real (no nosso exemplo imóveis para locação) em um mesmo documento, ou seja, não precisamos normalizar os dados como fazemos nos bancos de dados relacionais. Isso em muitos casos, facilita a escrita de consultas no banco, além de tornar a operação mais rápida!

Para finalizar, quantos imóveis temos disponíveis para locação no Brasil? Temos uma nova pergunta para ser respondida! Diferente do que vimos no início da lição, onde usamos o método `db.places.countDocuments()` para contar o total de documentos da coleção, agora queremos contar quantos documentos o método `find()` retornou após utilizar o parâmetro _query_.

Basicamente, basta escrevermos a consulta do mesmo modo que já fizemos e adicionar o método `count()` ao final, da seguinte forma:

```python
db.places.find({ 'address.country_code': 'BR' }).count()
```

Note, ao final do comando, a chamada ao método `count()`. Como resultado, será exibido o total de documentos recuperados na consulta do método `find()` que, neste caso, são **6** documentos.

### Parâmetro projection

O parâmetro _projection_ permite especificar quais campos devem ou não serem retornadas em uma consulta utilizando o método `find()`.

Para deixar isso mais nítido, vamos trabalhar com a consulta que recupera o imóvel cujo o campo **_id** seja igual a **7**, ou seja:

```python
db.places.find({ '_id': 7 })
```

Ao executar o comando acima, é exibido o documento cujo o valor do campo **_id** é igual a **7**, conforme já foi visto anteriormente. Mas vamos supor que apenas queremos exibir o nome do imóvel e omitir os demais campos.

Para alcançar este objetivo, utilizaremos o parâmetro _projection_ para definir quais campos devem ser exibidos na resposta da consulta. Para atender ao especificado acima, devemos executar o método `find()` com o seguinte conteúdo:

```python
db.places.find({ '_id': 7 }, { 'name': true })
```

Como resposta, será exibido o campo **_id** e o campo **name** (nome do imóvel)!

A _projection_ é um objeto que pode conter um ou mais campos com valor `true` (ou `false` como veremos mais adiante), onde apenas os campos especificados na _projection_ como `true` serão exibidas na resposta da consulta.

Agora vamos supor que queremos, além de exibir o nome do imóvel, queremos exibir o endereço do imóvel cujo o campo **_id** seja igual a **7**. Sabemos que existe uma campo **address** que contém os dados do endereço do imóvel. Logo o comando para atender o esperado, seria:

```python
db.places.find({ '_id': 7 }, { 'name': true, 'address': true })
```

Nesse caso, o retorno conterá os campos **_id**, **name** e **address**. Como o valor do campo _address_ é um objeto, os campos internas do objeto _address_ serão exibidas também.

> **De olho na dica 👀**: Quando a projeção contêm campos cujo os valores são iguais a `true`, temos uma **projeção de inclusão**, ou seja, na resposta não são incluídos os campos definidos na projeção e as demais são ignoradas. Podemos também, utilizando o parâmetro _projection_, realizar o inverso, ou seja, ao pesquisar um documento, quais campos deseja-se omitir. Para isso, na _projection_ atribuímos o valor **false** ao campo que queremos omitir.

Por exemplo, se escrevermos o seguinte comando no _mongosh_:

```python
db.places.find({ '_id': 7 }, { 'address': false, 'host': false })
```

Será retornado o documento cujo o campo **_id** é igual a **7** mas, nessa resposta, não teremos os campos **address** e **host** na resposta.

> **De olho na dica 👀**: Quando a projeção contêm campos cujo os valores são iguais a `false`, temos uma **projeção de exclusão**, ou seja, na resposta são exibidos todos os campos exceto os campos definidos na projeção.

## Ordenando uma resposta

Você deve ter notado que ao fazer uma consulta no banco de dados, os documentos não são recuperados na ordem do atributo **_id**. Caso você deseje que, em uma consulta, os dados retornem ordenados baseados no valor de uma chave, podemos utilizar o método `sort()` para este propósito. Observe o exemplo abaixo:

```python
db.places.find().sort({'_id': 1})
```

Se executarmos o exemplo acima, será retornado todos os documentos da coleção _places_ ordenados pela chave **_id** de forma **crescente**, pois o valor **1** indica que a ordenação se dará dessa forma. Para realizar essa ordenação de forma decrescente, basta mudarmos o valor **1** para **-1**, ou seja:

```python
db.places.find().sort({'_id': -1})
```

Assim os documentos serão ordenados baseado no valor da chave **_id** mas seguindo uma ordem decrescente.

## Conclusão

Recapitulando, estudamos como realizar pesquisas em uma coleção de documentos através do método `find()`, também estudamos como utilizar os parâmetros _query_ (para adicionar critérios na recuperação de documentos baseados em campos simples e compostas) e o parâmetro _projection_ (para selecionar os campos que deverão ser recuperadas ou omitidas nos documentos). Também estudamos como contar todos os documentos de uma coleção utilizando o método `countDocuments()` e a contar a quantidade de documentos recuperados de uma pesquisa utilizando o método `count()` em conjunto com o método `find()`. Por último, estudamos como ordenar os documentos, de forma crescente ou decrescente, a partir do valor de uma chave com o método `sort()`.

Na próxima lição estudaremos em como utilizar operadores de comparação e operadores lógicos para escrevermos consultas mais complexas, o que nos permitirá obter respostas a perguntas mais complexas. 🚀

### Para fixar

Usando o banco de dados `trybnb` devidamente carregado via container:

- Realize uma busca por todos os imóveis que se localizam na Espanha ordenados, de forma crescente, pelo atributo **_id**;
- Realize uma busca por todos os imóveis que se localizam nos Estados Unidos que retorne a quantidade de imóveis.

**DICA**: No banco os nomes dos países estão armazenados em inglês, ou seja, no caso da Espanha estará **Spain** e no caso dos Estados Unidos estará **United States**.

- Devemos pesquisar na coleção **places** places por todos os imóveis que se localizam na Espanha (**Spain**) através do método `find()` utilizando a _query_ `{'address.country': 'Spain'}` e realizar a ordenação através do método `sort()` através do atributo **_id** de forma crescente, ou seja:

```python
db.places.find({'address.country': 'Spain'}).sort({'_id':1})
```

- Devemos pesquisar na coleção **places** places por todos os imóveis que se localizam nos Estados Unidos (**United States**) através do método `find()` utilizando a _query_ `{'address.country': 'Spain'}` e realizar a contagem através do método `count()`, ou seja:

```python
db.places.find({'address.country': 'United States'}).count()
```

# Operadores de Comparação

Quando realizamos consultas em um banco de dados é necessário, em alguns momentos, estabelecer critérios de consulta que retornem documentos baseado em expressões lógicas utilizando **operadores de comparação** em conjunto com **operadores lógicos** (estes serão abordados no próximo conteúdo).

Por exemplo, utilizando a base de dados **trybnb** suponha que queremos saber quais imóveis no Brasil possuem mais do que três quartos. Ainda quais imóveis oferecem espaço para acomodar três pessoas ou mais? Ou até, quais imóveis no Brasil oferecem três quartos ou que consigam acomodar seis pessoas?

Essas são perguntas corriqueiras nesse tipo de aplicação e o _MongoDB_ oferece suporte para obtermos as respostas a essas perguntas (ou consultas). 👍

## Operadores de comparação

O _MongoDB_ fornece vários operadores de comparação para serem utilizados em consultas ao banco de dados, mas iremos estudar os seguintes operadores de comparação:

- `$eq`: Específica uma condição de **igualdade** (_equal_). O operador `$eq` realiza a correspondência de documentos em que o valor de uma chave **é igual** ao valor especificado.

Sintaxe do operador `$eq`:

```python
{<chave> { $eq: <valor> }}
```

- `$ne`: Especifica uma condição de **não igualdade** (_not equal_). O operador `$ne` realiza a correspondência de documentos em que o valor de uma chave **não é igual** ao valor especificado.

Sintaxe do operador `$ne`:

```python
{<chave> { $ne: <valor> }}
```

- `$gt`: Especifica uma condição de **maior que** (_greater than_). O operador `$gt` realiza a correspondência de documentos em que o valor de uma chave **maior que** o valor especificado.

```python
{<chave> { $gt: <valor> }}
```

- `$gte`: Especifica uma condição de **maior ou igual** (_greater than or equal_). O operador `$gte` realiza a correspondência de documentos em que o valor de uma chave **maior ou igual** ao valor especificado.

```python
{<chave> { $gte: <valor> }}
```

- `$le`: Especifica uma condição de **menor que** (_less than_). O operador `$le` realiza a correspondência de documentos em que o valor de uma chave **menor que** o valor especificado.

```python
{<chave> { $le: <valor> }}
```

- `$lte`: Especifica uma condição de **menor ou igual** (_less than or equal_). O operador `$gte` realiza a correspondência de documentos em que o valor de uma chave **menor ou igual** ao valor especificado.

```python
{<chave> { $lte: <valor> }}
```

No geral, conforme pode ser visto na própria [documentação](https://www.mongodb.com/docs/v6.0/reference/operator/query-comparison/) do _MongoDB_, a estrutura dos operadores de comparação seguem uma estrutura geral ilustrada na figura abaixo:

![Estrutura do operador de comparação](https://content-assets.betrybe.com/prod/969ef140-78d1-4f6f-8261-395e7cfc692c-Estrutura%20do%20operador%20de%20compara%C3%A7%C3%A3o.svg)
Estrutura do operador de comparação

Você deve estar se perguntando: _Como podemos utilizar os operadores de comparação nas consultas de documentos em uma coleção no MongoDB?_

O vídeo abaixo exemplificará o uso dos operadores de comparação junto com o método `find()`.

## Conclusão

Recapitulando, estudamos como utilizar os operadores de comparação em conjunto com o método do método `find()` para refinar nossas pesquisas. Dessa forma, ganhamos a capacidade de buscar documentos a partir de critérios baseados nos valores das chaves dos documentos, aumentando a precisão no processo de recuperação de documentos.

Na próxima lição estudaremos em como inserir dados, ou melhor, documentos no **MongoDB**. 🚀


# Inserindo dados

Até aqui estudamos como recuperar dados, agora vamos dar um passo adiante e estudar como inserir dados no **MongoDB**. Existem dois métodos para realizar a inserção de dados:

- Método `insertOne()`: Para inserir apenas um documento;
- Método `insertMany()`: Para inserir um `array` de documentos.

> **De olho na dica 👀:** Toda e qualquer manipulação de dados no _MongoDB_ se dá através de documentos, ou seja, na inserção serão inseridos documentos assim como em uma consulta recuperamos documentos. A diferença entre os métodos de inserção está na quantidade de documentos que podem ser inseridos por operação. Enquanto que o método `insertOne()` realiza a inserção de um único documento, o método `insertMany()` realiza a inserção de vários documentos. Independente do método utilizado, os documentos podem ter quantas chaves quanto forem necessárias.

Lembre-se que o **MongoDB**, assim como qualquer banco de dados **NoSQL**, não possuem um **schema**. O que isso quer dizer? 🤔

Isso significa que o documento não possui uma estrutura rígida que deve ser seguida durante a inserção ou uma atualização de documentos O legal disso tudo, é que os documentos não precisam ter as mesmas chaves, ou seja, alguns documentos podem possuir chaves a mais, outros podem possuir chaves a menos, ou até mesmos a mesma quantidade de chaves mas com nomes diferentes!

Lembra da nossa discussão inicial sobre como representar as diferentes formas de endereçamento de imóveis no banco de dados? Essa flexibilidade de possuir documentos com formas distintas é que nos permitirá cadastrar imóveis de formas diferentes sem ter que atualizar todos os documentos de uma única vez!

Essa atualização, se for algo estritamente necessária, pode ser realizada aos poucos, conforme os documentos são acessados e, nesse momento, verifica-se a existência ou não das novas chaves, por exemplo, e, no caso de ser necessário adicionar uma nova chave, é feito apenas naquele documento. Os demais documentos são atualizados conforme forem sendo acessados.

_E como se faz a validação da existência ou não de uma chave?_ 🤔

Essa responsabilidade passa a ser da aplicação. Esse é o custo que deve ser pago pela flexibilidade oferecida pelos bancos de dados **NoSQL**.

Usando o banco de dados `trybnb` devidamente carregado via container:

- Realize a inserção de três documento na coleção **places** que contenham os atributos **_id**, **name** e **description** utilizando o método `insertMany()`;

**DICA 01**: No campo **_id** utiliza valores iguais ou maiores de **100** para evitar conflito com registros existentes.

**DICA 02**: Nos campos **name** e **description** pode-se utilizar valores aleatórios. O que é esperado de você é saber utilizar os métodos de inserção independente dos valores utilizados.

## Solução

- Devemos utilizar o método `insertMany()` passando os vários documentos com as chaves definidas no enunciado e seus respectivos valores (os valores especificados por você podem ser diferentes dos apresentados nesta solução), ou seja:

```js
db.places.insertMany(
  [
    { '_id': 101, 'name': 'Casa na Sol', 'description': 'Apesar de distante, um lugar com clima sempre quente!' },
    { '_id': 102, 'name': 'Casa em Marte ', 'description': 'Ambiente calmo e inóspito onde é possível encontrar alguns robôs!'},
    { '_id': 103, 'name': 'Casa em Plutão', 'description': 'Para quem deseja ficar distante de tudo e de todes!'}])
```


# Operadores de Consulta em Arrays

Vamos imaginar a seguinte situação: Uma pessoa chegou em uma cidade e vai ficar por la, apenas dois meses. Nestes dois meses, esta pessoa deseja economizar o máximo possível e um dos gastos que pretende cortar, é alimentação! Para isso, comprar e preparar sua própria comida, é prioridade.

Com o cenário descrito acima, é desejável filtrar casas que já incluem fogão e geladeira no contrato, correto!? Mas como podemos fazer isso com que aprendemos até agora? Eu diria que é bem difícil 🥺 Utilizando operadores lógicos/comparação, não é suficiente para pesquisar fogão (_Stove_) e geladeira (_Refrigerator_), dentro de `amenities`!!! Pois `amenities` é um _array_ de opções…Para isso, o _MongoDB_ nos oferece um operador, que verifica a presença de valores dentro de um _array_, este operador é o `$all`!

## $all

Vamos solucionar o problema da pesquisa dentro de _arrays_, com esse novo operador que estamos vendo. Utilizamos `$all`, sempre que é preciso passar mais de um valor para comparação em um atributo do tipo _array_ e a ordem não desta lista, não importa.

Observe a estrutura do operador `$all`:

|![Estrutura do operador $all](https://content-assets.betrybe.com/prod/cc6827b7-bd75-49ec-94b9-8fbc6f1181ee-Estrutura%20do%20operador%20$all.svg)|
|---|
|Estrutura do operador $all|

Podemos responder a pergunta anterior utilizando o operador `$all` da seguinte maneira:

```python
  db.places.find({ amenities: { $all: ["Stove", "Refrigerator"] } })
```

> 🚦Atenção, com o comando abaixo, o comportamento é diferente do que usar o operador `$all`!!!

```python
db.places.find({ amenities: ["Garagem", "jacuzzi", "Armários", "piscina"] })
```

- A _query_ acima retornará somente os documentos em que o _array_ `amenities` seja **exatamente igual ao passado** como parâmetro no filtro, ou seja, contenha apenas esses elementos e na mesma ordem!
- Já a _query_ utilizando o operador `$all`, analisará o mesmo _array_, **independentemente** da existência de outros valores ou da ordem em que os elementos estejam.

## $elemMatch

Aqui temos mais um operador que pode nos auxiliar na filtragem de _arrays_, este é o operador `elemMatch`. Ele seleciona todos os documentos que satisfaçam pelo menos um critério. Ou seja, com esse operador você pode especificar várias _queries_ para um mesmo _array_. 

# Método Update

Algum tempo atrás, aconteceu uma tragédia com uma família brasileira em uma hospedagem alugada. Depois deste infeliz acontecido, a empresa que prestou o serviço de hospedagem, viu a necessidade de adicionar informações extras a cada um dos posts de seu site!

Se o back-end deste site trabalhar com um banco relacional, talvez esta tarefa de adicionar uma informação a mais, não seja tão simples! Para criarmos colunas em um banco de dados relacional e este estando em produção, temos os seguintes passos:

- Travar o banco (ninguém mais consegue acessar o banco nesse período);
- Realizar um backup do banco;
- Escrever uma operação SQL para alteração da tabela;
- Esperar essa operação terminar (a depender do tamanho da base, pode ir ao mercado fazer a feira do mês 🤣🥲)
- Verificar se o banco está ok (executando scripts de _Health_ na tabela e banco);
- Retirar o _lock_ do banco.

Usando o MongoDB, podemos fazer uma alteração em massa e depois de algum tempo, a base já irá se replicar, se atualizando (caso esteja configurada para isso)! Em alguns casos, não precisamos nos preocupar com a disparidade de informação entre documentos, pois seu esquema flexível propicia alterações inusitadas ou de emergências.

Boa, agora vamos ver como podemos alterar estes dados. Para isso, existem dois métodos, e saber quando e onde aplicar fará toda a diferença quando você estiver codificando!

- Método `updateOne()`: Para alterar apenas um valor;
- Método `updateMany()`: Para alterar vários valores.

> 👀 Observação: Aqui vale a pena citar o [caso](https://noticias.uol.com.br/internacional/ultimas-noticias/2019/05/23/familia-morta-no-chile-airbnb-estava-em-predio-sem-certificado-de-gas.htm) do Airbnb, no qual foi baseada essa lição! E que após esse fato, foi necessário adicionar um novo campo na base de dados para informar se um apartamento possui detector de gás ou não. Novamente ressaltamos como a tecnologia está diretamente ligada a vidas das pessoas e, que se usada incorretamente ou mal planejada, as consequências podem ser gravíssimas.

## updateOne() e updateMany()

Usando o banco de dados `trybnb`…

- Altere a hospedagem de `_id` “12”, para que seu campo `review_scores_rating` receba nota de 65;
- Altere todas as hospedagens do `host` “José Edmílson” para que ele tenha o campo `host_identity_verified` como true;

### Solução

- Altere a hospedagem de `_id` “12”, para que seu campo `review_scores_rating` receba nota de 65;

```js
db.places.updateOne(
  { _id: 12 },
  { $set: { "review_scores.review_scores_rating": 65 } }
)
```

- Altere todas as hospedagens do `host` “José Edmílson” para que ele tenha o campo `host_identity_verified` como true;

```js
db.places.updateMany(
  { "host.host_name": "José Edmílson" },
  { $set: { "host.host_identity_verified": true } }
)
```


# Método Delete

Então, para finalizar todas as letras do CRUD, vamos aprender a deletar documentos de nossas coleções! Para isso, também temos dois métodos:

- Método `deleteOne()`: Para deletar apenas um documento;
- Método `deleteMany()`: Para deletar vários documentos.

## deleteOne()

Imagine o momento em que uma de suas hospedagens cadastradas, não faça mais sentido estar lá. Você decidiu vender ela, ou porque não quer mais aluga-la, tanto faz! Você decidiu que quer remover sua postagem, como podemos fazer isso?

A casa de “Santo Expedito” não esta mais disponível, vamos remover ela:

```python
db.places.deleteOne({ _id: 11 })
```

> 🚦Atenção: Esse método sempre remove apenas um documento que bata com o critério passado! Caso você **não passe filtros** na _query_, ela irá remover o primeiro registro da coleção.

## deleteMany()

Agora por uma questão de segurança, a plataforma deu um prazo de 30 dias, para todas as pessoas donas de hospedagem que não tem suas contas verificadas, que o faça. Após este prazo, a **trybnb** deverá excluir todos os imóveis não verificados de sua base de dados. A _query_ abaixo remove todos os documentos de imóveis que não tenham sido verificados, ou seja, que contenham o valor do campo `host.host_identity_verified` como `false`.

```python
db.places.deleteMany({ "host.host_identity_verified": { $eq: false } })
```

> 🚦Atenção: O comando abaixo remove todos os documentos da coleção:

```python
db.places.deleteMany({})
```

# MongoDB e Crawlers

![python-loves-mongo](https://content-assets.betrybe.com/prod/48936399-a13f-431c-b424-8c9320b36d89-python-loves-mongo.png)

Agora que temos nossos dados, precisamos armazenar esta informação. Para isto utilizaremos o **MongoDB** que, como já estudamos, é um banco de dados de documentos, que armazena dados em formato **JSON** (`BSON`). Precisaremos de uma biblioteca para nos comunicarmos com o sistema de gerenciamento do banco de dados, e a mais popular e robusta é a **`pymongo`**. Podemos instalá-la com o comando:

⚠️Lembre-se que para testar o código abaixo você deve criar um ambiente virtual e instalar o `pymongo` conforme é ensinado abaixo.

```bash
python3 -m venv .venv && source .venv/bin/activate
python3 -m pip install pymongo
```

Após a instalação vamos ver como podemos realizar a escrita e leitura neste banco de dados. O primeiro passo é criar uma conexão com o banco de dados e isto pode ser feito da seguinte maneira:

> ⚠️ Lembre-se que o MongoDB deve estar preparado para ser acessado do “outro lado” dessa operação!.

```python
from pymongo import MongoClient

# Por padrão o host é localhost e porta 27017
# Estes valores podem ser modificados passando uma URI
# client = MongoClient("mongodb://localhost:27017/")
client = MongoClient()
```

Em posse da conexão podemos acessar um banco de dados e posteriormente uma coleção:

```python
from pymongo import MongoClient

client = MongoClient()
# o banco de dados catalogue será criado se não existir
db = client.catalogue
# a coleção books será criada se não existir
students = db.books
client.close()  # fecha a conexão com o banco de dados
```

Para adicionarmos documentos à nossa coleção utilizamos o método `insert_one`:

```python
from pymongo import MongoClient

client = MongoClient()
db = client.catalogue
# book representa um dado obtido na raspagem
book = {
    "title": "A Light in the Attic",
}
document_id = db.books.insert_one(book).inserted_id
print(document_id)
client.close()  # fecha a conexão com o banco de dados
```

Quando um documento é inserido, um `_id` único é gerado e retornado. Também podemos fazer inserção de múltiplos documentos de uma vez da seguinte forma:

```python
from pymongo import MongoClient

client = MongoClient()
db = client.catalogue
documents = [
    {
        "title": "A Light in the Attic",
    },
    {
        "title": "Tipping the Velvet",
    },
    {
        "title": "Soumission",
    },
]
db.books.insert_many(documents)
client.close()  # fecha a conexão com o banco de dados
```

Buscas podem ser feitas utilizando os métodos `find` ou `find_one`:

```python
from pymongo import MongoClient

client = MongoClient()
db = client.catalogue
# busca um documento da coleção, sem filtros
print(db.books.find_one())
# busca utilizando filtros
for book in db.books.find({"title": {"$regex": "t"}}):
    print(book["title"])
client.close()  # fecha a conexão com o banco de dados
```

O nosso cliente é um gerenciador de contexto (_with_), logo podemos utilizá-lo como tal, evitando problemas com o fechamento da conexão com o banco de dados:

```python
from pymongo import MongoClient


with MongoClient() as client:
    db = client.catalogue
    for book in db.books.find({"title": {"$regex": "t"}}):
        print(book["title"])
```

> 🍃 A interface de métodos desta biblioteca é tão natural que parece que estamos no _shell_ do Mongo. Aqui foram apresentados apenas alguns métodos, mas a similaridade é suficiente para conseguirmos aplicar nosso conhecimento prévio, consultando pontualmente a documentação do próprio [**MongoDB**](https://docs.mongodb.com/).



###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/61c046bb-0372-49d7-83c1-fe68b9e01d73/day/0bd32ccc-ca93-4dbe-8d42-f782775355e2/lesson/86f31318-b425-450a-a21f-9e60901fb815)
