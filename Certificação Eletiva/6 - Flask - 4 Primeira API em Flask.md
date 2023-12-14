[[6 - Flask]]

Agora, vamos completar nossa API de cadastro de piadas realizando as 4 operações de um CRUD.

- Create (Post);
- Read (Get);
- Update (Put ou Patch).
- Delete (Delete);

Até este ponto, você viu como criar todas as funcionalidades dentro do arquivo `app.py`. No entanto, com base em experiências anteriores, você pode estar pensando que essa abordagem não é a mais adequada.

Chegou na hora de trazer de volta o conhecimento sobre _Controllers_, _Models_ e _Views_: a arquitetura **MVC**.

## Arquitetura MVC

Vamos dividir as responsabilidades de nosso _software_ em camadas:

- M (_Model_): lógica de negócios e interação com banco de dados (Objetos Principais);
- V (_View_): interface com a pessoa usuária (HTML);
- C (_Controller_): gerencia interação entre View e Model (Requisições HTTP).

O comando utilizado para criar os arquivos anteriormente foi extremamente útil, não acha?

Vamos utilizar mais um comando desses, na raiz do projeto, para finalizar a criação dos nossos arquivos:

```bash
mkdir -p src/controllers && touch src/controllers/jokes_controller.py &&
mkdir -p src/models && touch src/models/db.py &&
mkdir -p src/models && touch src/models/abstract_model.py &&
mkdir -p src/models && touch src/models/joke_model.py
```

Com os arquivos em seus lugares, vamos completar a nossa `Joke API` e, assim, permitir as seguintes funcionalidades:

- Visualizar todas as piadas;
- Visualizar uma piada aleatória;
- Criar piadas;
- Editar piadas;
- Visualizar uma piada especifica;
- Excluir uma piada especifica.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/bcea046e-6619-4f41-8550-cfe778563d0f/lesson/94bb52e7-6a87-400f-8794-591102f8002b)
