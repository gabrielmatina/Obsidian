[[7 - Django]]

Você já viu que, por padrão, um projeto Django vem com um banco de dados SQLite. Mas, e se você quiser usar outro banco de dados, como você faz? É exatamente isso que vamos ver agora!

Você pode iniciar apagando o arquivo `db.sqlite3` do seu projeto, pois ele não será mais utilizado. Faremos as alterações no projeto para que ele use como banco de dados nosso conhecido MySQL, via Docker.

Para isso, o primeiro passo é alterar a variável `DATABASE`, no arquivo `settings.py`, para que ela tenha as configurações de acesso ao banco necessárias. De acordo com a [documentação](https://docs.djangoproject.com/en/4.2/ref/settings/#engine), a variável deve ficar assim:

Copiar

```python
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.mysql',
        'NAME': 'ecommerce_database',
        'USER': 'root',
        'PASSWORD': 'password',
        'HOST': '127.0.0.1',
        'PORT': '3306',
        }
}
```

Em seguida, criaremos um arquivo que conterá o script SQL que criará o banco de dados `ecommerce_database`. Ele ficará dentro do diretório `./database`:

```bash
mkdir database && cd database
touch 01_create_database.sql
```

Por ora, o banco de dados não terá nenhuma tabela, portanto, o script de criação do banco de dados `ecommerce_database` deve ficar assim:

```sql
CREATE DATABASE IF NOT EXISTS ecommerce_database;

USE ecommerce_database;
```

Com isso feito, é hora de criar um arquivo `Dockerfile` na raiz do projeto (no mesmo nível do arquivo `manage.py`), com o seguinte conteúdo:

```yaml
FROM mysql:8.0.32

ENV MYSQL_ROOT_PASSWORD password

# Copia o script SQL que acabamos de criar para um determinado diretório no container
COPY ./database/01_create_database.sql /docker-entrypoint-initdb.d/data.sql01
```

Para _buildar_ a imagem, basta rodar o comando dentro da pasta do projeto que contém o arquivo Dockerfile.

```bash
docker build -t ecommerce-db .
```

Para executar o container e o script de criação do banco copiado no Dockerfile, é preciso passar algumas as variáveis de acesso definidas na variável `DATABASES`, do arquivo `settings.py`, para o container. Para isso, vamos usar o comando:

```bash
docker run -d -p 3306:3306 --name=ecommerce-mysql-container -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=ecommerce_database ecommerce-db
```

Neste momento, você já pode acessar o banco de dados pelo _Workbench_ e verificar se ele foi criado corretamente.

Mas ainda não acabou! Lembra das _migrations_ iniciais que geraram o famigerado aviso em vermelho no início do projeto? Elas ainda não foram executadas neste banco de dados. Para isso, é preciso executar o comando `migrate` do Django:

```bash
python3 manage.py migrate
```

Você provavelmente se deparou com um erro agora, né? **Don’t panic!** O erro acontece porque o Django não consegue se comunicar com o banco de dados, pois não tem o pacote `mysqlclient` instalado. Resolver isso é bem simples, basta instalar o pacote exigido:

```bash
pip install mysqlclient
```

> **De olho na dica 👀:** Caso ainda ocorra algum erro, pode ser porque um pacote adicional chamado `pkg-config` seja necessário. Nesse caso, tente seguir todos os passos sugeridos pela [documentação oficial](https://github.com/PyMySQL/mysqlclient#install) do `mysqlclient` para a instalação do pacote.

Por fim, basta rodar novamente as _migrations_ e ver que agora tudo está funcionando corretamente! 🤩

Fique a vontade para conferir quais tabelas foram criadas no banco de dados utilizando o _Workbench_!

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/32af04d3-9bec-4278-85dd-bee03a8887fb/lesson/3d56e490-8557-4e0f-b47e-16de706dbf1f)
