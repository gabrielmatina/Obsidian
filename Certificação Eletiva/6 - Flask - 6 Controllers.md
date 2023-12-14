[[6 - Flask]]

Avançando na nossa construção, vamos criar outra camada essencial da arquitetura MVC: a _controller_. Ela é responsável por receber e processar as requisições HTTP, manipulando os dados necessários e retornando uma resposta apropriada para o cliente.

Nossa intenção é manter o arquivo `app.py` o mais limpo e organizado possível. Portanto, vamos separar a lógica de manipulação de requisições em _controllers_ individuais. Dessa forma, o `app.py` terá apenas a responsabilidade de iniciar o servidor e registrar os módulos das _controllers_.

Para registrar uma _controller_ no Flask, utilizaremos o método `app.register_blueprint`, que é disponibilizado pelo Flask e nos permite conectar os módulos da aplicação.

```python
-from flask import Flask, jsonify
+from flask import Flask
+from controllers.jokes_controller import jokes_controller
-import random
from os import environ
from waitress import serve

app = Flask(__name__)
+app.register_blueprint(jokes_controller, url_prefix="/jokes")

- # Removeremos nosso exemplo estático
-joke_list = [
-  "Por que o bombeiro não gosta de andar? <br> Porque ele socorre.",
-  "Sabe como chama a sorveteria do Michel Teló? <br> Ice te Pego.",
-  "Por que o espanador não luta caratê? <br> Porque ele luta capoeira"
-]
-@app.route("/api/joke")
-def joke():
-    return jsonify({'joke': random.choice(joke_list)})

def start_server(host: str = "0.0.0.0", port: int = 8000):
    if environ.get("FLASK_ENV") == "dev":
        # Servidor de desenvolvimento do Kit Werkzeug
        return app.run(debug=True, host=host, port=port)
    else:
        # Este é o waitress, otimizado para produção
        serve(app, host=host, port=port)

if __name__ == "__main__":
    start_server()
```

Agora, podemos finalmente criar nossa `Controller`, que retornará nossa API completa.

Usaremos alguns recursos interessantes do Flask:

- **Blueprint:** permite modularizar e reutilizar rotas em diferentes partes do código;
- **jsonify:** retorna uma resposta JSON para uma requisição, com o cabeçalho completo;
- **request:** encapsula a requisição HTTP, fornecendo os parâmetros da URL e o corpo da requisição.


```python
# jokes_controller.py
from bson import ObjectId
from flask import Blueprint, jsonify, request
from models.joke_model import JokeModel

jokes_controller = Blueprint("jokes", __name__)
# O primeiro parâmetro determina o nome da blueprint, já o segundo
# parâmetro é o caminho de importação do pacote com a blueprint,
# normalmente utilizamos __name__ referindo-se ao próprio módulo.
#
# Caso você tente registrar outra blueprint com o nome "jokes",
# você se irá se deparar com o seguinte erro:
#
# ValueError: The name 'jokes' is already registered for a different
# blueprint. Use 'name=' to provide a unique name.
#
# Ou seja: O nome 'jokes' já está registrado, mas podemos usar 'name='
# para fornecer um nome diferente para a sua nova Blueprint.
#

# ---------
# Funções protegidas da camada de Controller
def _get_all_jokes():
    jokes = JokeModel.find()
    return [joke.to_dict() for joke in jokes]


def _get_joke(id: str):
    # ObjectId transforma uma string em ID do MongoDb
    return JokeModel.find_one({"_id": ObjectId(id)})


# ---------
# Rotas HTTP para nossa API
@jokes_controller.route("/", methods=["GET"])
def joke_index():
    jokes_list = _get_all_jokes()
    return jsonify(jokes_list)


@jokes_controller.route("/random", methods=["GET"])
def joke_random():
    joke = JokeModel.get_random()
    # Exemplo de Validação
    if joke is None:
        # O Flask entende que o número após o jsonify, representa o Status HTTP
        return jsonify({"error": "No jokes available"}), 404

    return jsonify(joke.to_dict()), 200


@jokes_controller.route("/", methods=["POST"])
def joke_post():
    new_joke = JokeModel(request.json)
    new_joke.save()
    return jsonify(new_joke.to_dict()), 201


@jokes_controller.route("/<id>", methods=["PUT"])
def joke_update(id: str):
    joke = _get_joke(id)
    # Exemplo de Validação
    if joke is None:
        return jsonify({"error": "Joke not found"}), 404
    joke.update(request.json)
    return jsonify(joke.to_dict()), 200


@jokes_controller.route("/<id>", methods=["GET"])
def joke_show(id: str):
    joke = _get_joke(id)
    if joke is None:
        return jsonify({"error": "Joke not found"}), 404
    return jsonify(joke.to_dict()), 200


@jokes_controller.route("/<id>", methods=["DELETE"])
def joke_delete(id: str):
    joke = _get_joke(id)
    if joke is None:
        return jsonify({"error": "Joke not found"}), 404

    joke.delete()
    return jsonify(joke.to_dict()), 204
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/bcea046e-6619-4f41-8550-cfe778563d0f/lesson/4ae9d016-f79a-4f16-ad22-bf67c30a7fa3)
