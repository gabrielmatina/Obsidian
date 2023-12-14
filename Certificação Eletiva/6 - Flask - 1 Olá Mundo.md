[[6 - Flask]]

# “Olá, mundo!” com Flask

## Exemplo de instalação do Flask

O primeiro passo consiste em configurar e ativar o ambiente virtual, seguido da instalação do Flask.

```bash
# Crie a pasta do projeto para organização
mkdir hello-flask && cd hello-flask
# Carregue o ambiente virtual para isolar as dependências do projeto
python3 -m venv .venv && source .venv/bin/activate
# Instale o Flask usando o gerenciador de pacotes pip
pip install Flask
```

Um _framework_ é um conjunto de bibliotecas que oferece funcionalidades e ferramentas para facilitar o desenvolvimento de _software_. No caso do Flask, não é diferente, pois ele inclui várias bibliotecas adicionais em sua instalação. Você pode visualizar essas bibliotecas executando o comando `pip freeze`. Nesta seção, vamos explorar a importância dessas bibliotecas extras e, também, discutir outras que podem ser úteis durante o desenvolvimento com o Flask.

```bash
pip freeze
# Retorno
# blinker==1.6.2 (Sinais e eventos para Python)
# click==8.1.3 (CLI para Python)
# Flask==2.3.2 (Framework Web Python)
# itsdangerous==2.1.2 (Geração de tokens seguros)
# Jinja2==3.1.2 (Mecanismo de templates Python)
# MarkupSafe==2.1.2 (Strings HTML seguras)
# Werkzeug==2.3.3 (Utilitários para Debug e WSGI)
```

## Executando o Flask

O arquivo principal de uma aplicação Flask, denominado `app.py`, é responsável por criar a instância da aplicação _web_ e configurar sua execução, incluindo definições como porta, URL e modo de depuração. Nesse arquivo, você pode personalizar as configurações de sua aplicação e controlar como ela será executada e acessada.

No arquivo `app.py`, é possível configurar as rotas da aplicação Flask, que são definidas por meio de métodos Python decorados com `@app.route("/")`. Essas rotas determinam os diferentes caminhos ou URLs da aplicação, permitindo que o Flask saiba qual função executar quando um determinado caminho for acessado. Por exemplo:

```bash
touch app.py
```

```python
# app.py
from flask import Flask
app = Flask(__name__) # Cria a instância da aplicação

@app.route("/") # Cria uma rota, para a raiz do projeto. (GET por padrão)
def hello_world(): # Método a ser executado ao navegar
  return 'Hello World!'

if __name__ == '__main__':
  # debug = True, reinicia automaticamente a cada mudança de arquivo
  # mude a porta, caso ela estiver em uso
  app.run(debug = True, host='0.0.0.0', port=8000)
```

Experimente, agora, rodar no console:

```bash
python3 app.py
```

E _voilà!_ A saída deve ser semelhante a essa:

```bash
 * Serving Flask app 'app'
 * Debug mode: on
 WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:8000
 * Running on http://192.168.1.104:8000
```

Com esses passos concluídos, seu servidor estará em execução e você poderá acessá-lo diretamente do navegador digitando a seguinte URL: [http://localhost:8000](http://localhost:8000/). Agora, está tudo preparado para você interagir com sua aplicação Flask e explorar suas funcionalidades.

Retornou uma página `HTML`?

![Página html Hello World!](https://content-assets.betrybe.com/prod/5b5f5533-5b29-4b47-81e2-aca7ad4b95b7-P%C3%A1gina%20html%20Hello%20World!.png)|
Página HTML Hello World!

Mesmo ao retornar apenas uma _string_, o Flask é capaz de gerar uma página HTML graças à biblioteca Werkzeug, que fornece um servidor WSGI (Web Server Gateway Interface), assunto que abordaremos em breve. Isso demonstra a capacidade do Flask de processar a resposta e transformá-la em uma página _web_ que possa ser exibida no navegador.

### Retorno em JSON

Você pode estar se perguntando: “Será que é complicado retornar um JSON API?”.

Na verdade, é muito simples com o Flask! A função `jsonify` é uma verdadeira ajuda, pois ela retorna um objeto `Response` do Flask com o cabeçalho `Content-Type: application/json`. Ou seja: boa parte dos detalhes de implementação é abstraído, tornando o desenvolvimento da sua API ainda mais rápido e eficiente.

Agora, vamos criar uma nova rota em `localhost:8000/api`, que irá retornar uma resposta em formato JSON usando a classe `flask.Response`.

Copiar

```python
# app.py
from flask import Flask, jsonify

#...
@app.route("/api/")
def api_hello_world():
    return jsonify({'mensagem': 'Hello World!'})
#...
```

![API JSON Hello World](https://content-assets.betrybe.com/prod/5b5f5533-5b29-4b47-81e2-aca7ad4b95b7-API%20JSON%20Hello%20World.png)
Retorno Json

Não se esqueça de testar este GET usando o Thunder Client, uma ferramenta que permite realizar solicitações HTTP e verificar a resposta diretamente em seu ambiente de desenvolvimento. Isso facilitará a verificação do funcionamento correto de sua rota e a visualização dos resultados.

![Retorno Thunder Client](https://content-assets.betrybe.com/prod/5b5f5533-5b29-4b47-81e2-aca7ad4b95b7-Retorno%20Thunder%20Client.png)Retorno Thunder Client


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/bcea046e-6619-4f41-8550-cfe778563d0f/lesson/30939699-3a6f-4087-89eb-60b230d9d7c7)
