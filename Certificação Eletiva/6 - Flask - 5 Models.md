[[6 - Flask]]


As _models_ no padrão MVC são responsáveis por representar a lógica de negócios da aplicação e cuidar do acesso e da manipulação dos dados armazenados no banco de dados. Elas desempenham um papel fundamental como uma camada de abstração entre a aplicação e o banco de dados.

Usaremos o banco `MongoDB`, com a biblioteca `Pymongo` ao nosso lado.

Vamos realizar a conexão completando o arquivo `src/models/db.py`:

```python
from pymongo import MongoClient
from os import environ

# Conecta no Mongo, pela variável ambiente definida no Docker Compose
client = MongoClient(environ.get("MONGO_URL"))

# Cria um banco de dados chamado db_chat
db = client.db_chat
```

Agora é hora de criar a model de piadas, chamada `JokeModel`, que será responsável por lidar com a comunicação com o banco de dados e atender às necessidades da nossa futura controller. Essa model será projetada levando em consideração as regras de negócio da nossa aplicação.

Como nosso foco é estudar o Flask sem aprofundar tanto no MongoDB no momento, vamos construir um código de fácil manutenção, abstraindo a lógica de comunicação com o banco de dados, em uma `AbstractModel`, que será herdada pela nossa _model_ `JokeModel`.

Assim, a `JokeModel` reaproveitará o código da classe-mãe `AbstractModel`, abstraindo a necessidade de conectar ao banco de dados.

```python
# joke_model.py
import random
from .db import db
from .abstract_model import AbstractModel

# Herdado a classe Abstrata que domina o uso do Mongodb
class JokeModel(AbstractModel):
    # Define que a Coleção do Banco se chamara jokes.
    # Uma coleção é o equivalente a uma tabela no Mysql
    _collection = db["jokes"]

    # Nosso construtor receberá um dicionário (JSON)
    # para instanciar um objeto
    def __init__(self, json_data):
        super().__init__(json_data)

    # Retornar uma piada aleatória, é uma regra de negócio especifica
    # Fazendo sentido manter somente para a model Joke
    @classmethod
    def get_random(cls):
        data = cls.find()
        if not data: return
        return random.choice(data)

    # Define as regras de como o objeto JokeModel pode virar um Dict
    def to_dict(self):
        return {
            '_id': str(self.data['_id']),
            'joke': self.data['joke'],
        }
```

Agora, vamos criar a `src/controllers/abstract_model.py`.

No momento em que construímos a _model_ concreta `JokeModel`, preenchemos o atributo `_collection`. Ele representa uma coleção no banco MongoDB e é responsável por estabelecer a comunicação com o banco de dados, fornecendo as funcionalidades necessárias para nossa tarefa. Existem alguns métodos específicos do `Pymongo` que utilizaremos:

- `find`: Retorna uma lista com todos registros filtrados por uma _query_ (que é opcional);
- `find_one`: Equivalente ao `find`, mas retorna somente um registro;
- `find_one_and_update`: Procura um registro e o atualiza com os dados informados;
- `delete_one`: Excluir o primeiro registro que encontrar com a _query_ informada;
- `insert_one`: Insere um novo registro.

Uma _query_ no MongoDB segue o formato `JSON`. Para retornar o registro com o ID 1000, podemos utilizar a seguinte estrutura:

```python
db["jokes"].find({"_id": 1000})
```

O equivalente à `Mysql` seria:

```sql
"select * from jokes where id = 1000;"
```

Ao utilizar a classe `AbstractModel` como base para suas _models_, você está abstraindo o conhecimento específico do MongoDB e simplificando seu código. Isso significa que, ao herdar a `AbstractModel`, você automaticamente terá acesso aos métodos de busca, salvamento, atualização e exclusão de registros.

```python
# abstract_model.py
from pymongo.collection import ReturnDocument, Collection


class AbstractModel:
    _collection: Collection = None

    def __init__(self, data: dict):
        self.data = data

    def save(self):
        result = self._collection.insert_one(self.data)
        inserted_document = self._collection.find_one(
            {"_id": result.inserted_id}
        )
        self.data = inserted_document
        return self.data

    def update(self, data: dict):
        # Por padrão o find_one_and_update retorna o estado anterior do
        # registro, por isso, utilizamos o ReturnDocument.AFTER para
        # retornar a versão pós atualização
        result = self._collection.find_one_and_update(
            {"_id": self.data["_id"]},
            {"$set": data},
            return_document=ReturnDocument.AFTER,
        )

        self.data = result
        return self.data

    def delete(self):
        self._collection.delete_one({"_id": self.data["_id"]})

    @classmethod
    def find(cls, query: dict = {}):
        data = cls._collection.find(query)
        return [cls(d) for d in data]

    @classmethod
    def find_one(cls, query: dict = {}):
        data = cls._collection.find_one(query)
        return cls(data) if data else None
```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/bcea046e-6619-4f41-8550-cfe778563d0f/lesson/94bb52e7-6a87-400f-8794-591102f8002b)