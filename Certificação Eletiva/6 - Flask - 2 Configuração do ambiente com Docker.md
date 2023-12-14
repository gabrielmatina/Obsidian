[[6 - Flask]]

## Build Docker

Vamos recomeçar e aplicar a “magia” do Docker para criar um _container_ que executará nossa aplicação, garantindo o mesmo ambiente em desenvolvimento e em produção.

Para estruturar a aplicação, precisaremos criar alguns arquivos. Execute este comando no terminal e tudo estará pronto para prosseguir.

```bash
mkdir projeto-flask && cd projeto-flask
mkdir -p src && touch src/app.py &&
touch src/requirements.txt && touch src/dev-requirements.txt
touch Dockerfile && touch docker-compose.yml
```

Agora, vamos criar uma API com um propósito maior: uma Json API REST para retornar piadas aleatórias. E, quem sabe, podemos construir um robozinho contador de piadas?

![Imagem de um Chat](https://content-assets.betrybe.com/prod/ae58f755-0852-49ff-8f58-15113f6478ae-Imagem%20de%20um%20Chat.png)Será que eu podia dar esse _spoiler_?

Abra o VS Code e preencha o `app.py`!

```python
from flask import Flask, jsonify
import random

app = Flask(__name__)

joke_list = [
    "Por que o bombeiro não gosta de andar? <br> Porque ele socorre.",
    "Sabe como chama a sorveteria do Michel Teló? <br> Ice te Pego.",
    "Por que o espanador não luta caratê? <br> Porque ele luta capoeira",
]


@app.route("/api/joke")
def joke():
    return jsonify({"joke": random.choice(joke_list)})


def start_server(host: str = "0.0.0.0", port: int = 8000):
    app.run(debug=True, host=host, port=port)


if __name__ == "__main__":
    start_server()

```

Os arquivos `requirements.txt` e `dev-requirements.txt` servem para organizar as bibliotecas que devem ser instaladas em nosso projeto. Por hora, somente a Flask é necessária, porém, para adiantar, colocaremos algumas bibliotecas que serão utilizadas futuramente nesta seção.

> requirements.txt

```txt
Flask==2.3.1
pymongo==4.3.3
waitress==2.1.2
```

> dev-requirements.txt

```txt
-r requirements.txt

black==23.3.0
flake8==4.0.1
```

Vamos começar nosso Dockerfile com base em uma imagem Python padrão da comunidade `python:3-alpine3.17`, na qual realizaremos a instalação de nossas bibliotecas listadas nos arquivos `requirements.txt`.

Observe: temos um bloco `if` que verifica a variável de ambiente `$FLASK_ENV`. Isso garante que apenas as bibliotecas não necessárias em produção sejam instaladas durante o desenvolvimento. Para alcançar esse comportamento, apresentamos o comando `ARG`, que reconhece um argumento que será passado pelo arquivo `docker-compose`. Dessa forma, podemos controlar de forma flexível quais bibliotecas serão instaladas com base na configuração de ambiente.

```dockerfile
# Dockerfile
FROM python:3-alpine3.17

WORKDIR /src

# Dica: instale primeiro as dependências antes de copiar todo projeto
COPY src/*requirements.txt ./
RUN pip install --no-cache-dir -r requirements.txt

# Este argumento será passado dentro do docker-compose
ARG FLASK_ENV
RUN if [ "$FLASK_ENV" = "dev" ] ; then pip install --no-cache-dir -r dev-requirements.txt  ; fi

COPY ./src .

CMD ["python3", "app.py"]
```

Agora, já podemos orquestrar o ambiente de desenvolvimento criando nosso `docker-compose.yml`.

E aqui está mais uma surpresa incrível: para criar as APIs desta seção, utilizaremos o banco de dados não relacional `MongoDB`. No exemplo construído até o momento ele não será necessário, mas vamos configurá-lo para que tudo já esteja pronto e funcionando perfeitamente.

Copiar

```yml
# docker-compose
version: '3.4'

services:
  flask-api:
    container_name: flask-api
    build:
      # A configuração do 'args' possibilita o nosso IF funcionar no Dockerfile
      args:
        - FLASK_ENV=dev
      context: .
    volumes:
      - ./src:/src
    ports:
      - 8000:8000
    depends_on:
      - mongodb
    networks:
      - flask_net
    environment:
      - MONGO_URL=mongodb://mongodb:27017
      - FLASK_ENV=dev
    restart: always

  mongodb:
    image: mongo:5.0.7
    container_name: mongo_db
    restart: always
    ports:
      - 27017:27017
    networks:
      - flask_net

networks:
  flask_net:
    driver: bridge
```

_Finalmente_!! Com os arquivos criados agora você pode subir a aplicação com o comando:

```bash
docker compose up flask-api
```

Teste o _endpoint_ acessando-o pelo navegador ou Thunder Client ([http://localhost:8000/api/joke](http://localhost:8000/api/joke)).

![Retorno Thunder Client](https://content-assets.betrybe.com/prod/ae58f755-0852-49ff-8f58-15113f6478ae-Retorno%20Thunder%20Client.png)
Retorno Thunder Client


Pronto, ambiente configurado! ☺️


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/bcea046e-6619-4f41-8550-cfe778563d0f/lesson/3f565854-5c6b-4030-9f44-05739033c689)


