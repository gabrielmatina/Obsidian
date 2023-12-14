[[7 - Django]]

# Setup inicial

Hoje veremos como utilizar vários recursos legais do Django com formulários. Para conduzir o aprendizado de forma mais dinâmica, usaremos um exemplo prático para guiar o conteúdo.

Para isso, hoje desenvolveremos uma aplicação para gerenciar _playlists_!

Como sempre, o primeiro passo é criar o ambiente virtual que será utilizado e fazer a instalação dos pacotes que serão utilizados:

```bash
python3 -m venv .venv && source .venv/bin/activate
pip install django
pip install mysqlclient
```

Em seguida, crie o projeto Django `playlist_manager` e a aplicação `playlists`:

```bash
django-admin startproject playlist_manager .
django-admin startapp playlists
```

No arquivo `settings.py`, adicione a aplicação ao projeto e altere as configurações para usar o MySQL:

```diff
# playlist_manager/settings.py
# ...

INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
+   'playlists',
]

# ...

DATABASES = {
    'default': {
-       'ENGINE': 'django.db.backends.sqlite3',
+       'ENGINE': 'django.db.backends.mysql',
-       'NAME': BASE_DIR / 'db.sqlite3',
+       'NAME': 'playlist_manager_database',
+       'USER': 'root',
+       'PASSWORD': 'password',
+       'HOST': '127.0.0.1',
+       'PORT': '3306',
    }
}
```

> O simples ato de adicionar a aplicação ao projeto, incluindo-a na variável `INSTALLED_APPS`, equivale a instalar a aplicação no projeto. Por isso, é comum que este passo seja descrito da segunda forma em alguns fóruns.

Crie o arquivo para o script SQL dentro do diretório `./database`:

```bash
mkdir database && cd database
touch 01_create_database.sql
```

Adicione o conteúdo do script para criação do banco de dados `playlist_manager_database`:

```sql
/* database/01_create_database.sql */
CREATE DATABASE IF NOT EXISTS playlist_manager_database;

USE playlist_manager_database;
```

Crie o Dockerfile na raiz do projeto:

```yaml
# Dockerfile
FROM mysql:8.0.32

ENV MYSQL_ROOT_PASSWORD password
COPY ./database/01_create_database.sql /docker-entrypoint-initdb.d/data.sql01
```

Agora você já pode fazer o _build_ da imagem. Basta rodar o comando a seguir **dentro da pasta do projeto que contém o arquivo Dockerfile**:

```bash
docker build -t playlist-manager-db .
```

O próximo passo é executar o container e o script de criação do banco de dados:

```bash
docker run -d -p 3306:3306 --name=playlist-manager-mysql-container -e MYSQL_ROOT_PASSWORD=password -e MYSQL_DATABASE=playlist_manager_database playlist-manager-db
```

> Caso queira, verifique pelo _Workbench_ se o banco de dados foi criado corretamente. 😉

Ao longo deste conteúdo, utilizaremos três modelos: `Singer`, `Music` e `Playlist`. O primeiro representará pessoas cantoras das músicas, o segundo representará a música em si, enquanto o terceiro será a representação de um conjunto de músicas que compõe uma _playlist_.

A implementação do arquivo `playlists/models.py` é apresentada abaixo:

```python
# playlists/models.py

from django.db import models


class Singer(models.Model):
    name = models.CharField(max_length=50)

    def __str__(self):
        return self.name


class Playlist(models.Model):
    name = models.CharField(max_length=50)
    is_active = models.BooleanField(default=False)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.name


class Music(models.Model):
    name = models.CharField(max_length=50)
    recorded_at = models.DateField()
    length_in_seconds = models.IntegerField()

    def __str__(self):
        return self.name
```

Perceba que não há nada novo na implementação destes modelos.

Você deve ter notado também que, por existir uma conexão entre eles, poderíamos adicionar relacionamento entre essas tabelas. Pode acalmar seu coração, nós faremos isso! Só que um pouquinho mais adiante! 😅

> **Relembrando 🧠:** depois da implementação dos modelos, lembre-se de criar as migrações e migrá-las para o banco de dados. Para isso, execute os comando: `python3 manage.py makemigrations` e `python3 manage.py migrate`.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/af829dac-8a8e-40e3-86b9-d39072392aa6/lesson/8a15314c-3669-4b65-b9a0-e1acd56878ef)
