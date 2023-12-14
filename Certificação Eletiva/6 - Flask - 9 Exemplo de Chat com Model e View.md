[[6 - Flask]]

Agora, vamos criar um ChatBot utilizando o Flask MVC com _templates_ Jinja2. Nosso ChatBot não será tão inteligente quanto um ChatGPT, porém ChatBots com fluxo direcionados também são úteis para as empresas, pois (_além de terem implementação mais simples/barata_) podem ajudar com:

- Atendimento automático;
- Respostas de perguntas frequentes (FAQ);
- Segunda via de boletos;
- Notificação de eventos;
- Acompanhar pós-atendimento;
- Contar piadas (Será o nosso caso).

Bora lá, uma camada por vez! #MVC

## Criando a _model_ do Chat

Para persistir as mensagens, vamos definir uma _model_ chamada `chat`. Como já temos nossa Abstract Model, responsável pelos métodos de persistência no banco, a MessageModel terá somente a missão de escolher o nome da coleção.

> src/models/message_model.py


```python
from .db import db
from .abstract_model import AbstractModel


# Herdando a classe Abstrata que domina o uso do Mongodb
class MessageModel(AbstractModel):
    # Define que a Coleção do Banco se chamará 'chat'.
    # Uma coleção é o equivalente a uma tabela no Mysql
    _collection = db["chat"]

    # Vamos reaproveitar o construtor da classe superior implicitamente

    # Define as regras de como o objeto MessageModel pode virar um Dict
    def to_dict(self):
        return {
            "content": self.data["content"],
            "to": self.data["to"],
            "from": self.data["from"],
            "time": self.data["time"],
        }

```

## Criando a _view_ do Chat

Para a _view_, receberemos uma variável chamada `session_messages`, que representa as mensagens da sessão. E, por meio de um `placeholder`, aplicaremos um `for` em Python, que percorrerá cada mensagem realizando um `if` para verificar se ela foi escrita pelo `Trybot`, para definir se ela será alinhada à esquerda e qual a imagem exibir.

> src/views/templates/chat.html


```python
<!-- Section é uma tag html para criar um componente -->
<section class="messenger">
  <!-- cabeçalho -->
  <header class="messenger-header">
    <meta charset="UTF-8">
    <title>Chat Trybot</title>
    
    <!-- Carrega o CSS estático -->
    <link rel="stylesheet" href="{{ url_for('static', filename='css/chat.css') }}">

    <div class="messenger-header-title">
      <i class="fas fa-comment-alt"> Trybot</i>
    </div>

    <div class="messenger-header-options">
      <span><i class="fas fa-cog"></i></span>
    </div>
  </header>

  <main class="messenger-chat" id="chat">
    <!-- loop for nas mensagens recebidas da controller -->
    {% for message in session_messages %}
      <!-- se mensagem é do Trybot, deve alinhar a esquerda e definir avatar-->
      {% if message['from'] == 'Trybot' %}
        {% set class_message = 'left-msg' %}
        {% set file_name = 'images/avatar.png' %}
      {% else %}
        {% set class_message = 'right-msg' %}
        {% set file_name = 'images/avatar-user.png' %}
      {% endif %}
      
      <div class="msg {{ class_message }}">
        <div class="msg-avatar">
          <img src="{{ url_for('static', filename=file_name)}}" width="100%">
        </div>
  
        <div class="msg-ballon">
          <div class="msg-info">
            <div class="msg-info-name">{{ message['from'] }}</div>
            <div class="msg-info-time">{{ message['time'] }}</div>
          </div>
  
          <div class="msg-text">
            {{message['content']|safe}}
          </div>
        </div>
      </div>
    {% endfor %}
  </main>

  <form method="post" class="messenger-inputarea">
    <input type="text" name="message-content" class="messenger-input" placeholder="Escreva sua mensagem...">
    <input type="hidden" name="username" value="{{ username }}">
    <button type="submit" class="messenger-send-btn">Enviar</button>
  </form>
</section>

<!-- Não somos contra Javascript! Podemos usar também 😄 -->
<!-- Aqui, ele serve para descer a barra de rolagem ao carregar o chat-->
<script>
  if (window.history.replaceState) {
    window.history.replaceState(null, null, window.location.href);
  }
  var chatDiv = document.getElementById("chat");
  chatDiv.scrollTop = chatDiv.scrollHeight;
</script>
```

Importante observar que a sintaxe de marcação do `for` exige o fechamento com um `{% endfor %}`, e a do `if` exige o fechamento com `{% endif %}`. Isso se assemelha ao formato do próprio HTML nativo, que exige abertura e fechamento de algumas _tags_.

Outro detalhe é que em alguns locais é usado `{% comando %}` e em outros, apenas `{{ variavel }}`. O uso do símbolo `%` serve para indicar que o comando não retornará uma saída para o HTML. Por isso, é usado nos operadores, e não é usado em variáveis a expor para o HTML.

> **Atenção ⚠️:** comandos são marcados com `{% %}` (chave simples + operador de porcentagem) e variáveis com `{{ }}` (chaves duplas).

Por fim, percebe-se que foi usado o filtro `safe` em `{{message['content']|safe}}`, para prevenir um _HTML injection_. Assim evitamos bugs que _“só uma pessoa usuária poderia encontrar”_, ou até mesmo que uma pessoa maliciosa envie um Javascript como mensagem no _input_ e nosso código o consuma.

Agora que temos a _view_ e a _model_, precisamos criar a _controller_ e unir todas as peças! 🧩

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/0c01be06-7742-4513-ab65-a29fde93f3c2/lesson/0bd1995c-c5e9-4880-880c-ddce5fd12a9a)
