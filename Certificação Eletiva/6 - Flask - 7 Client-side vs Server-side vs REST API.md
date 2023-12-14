[[6 - Flask]]

## _Server-side_

Do lado do servidor, o desenvolvimento é geralmente mais fácil porque há menos preocupações com a compatibilidade e porque a camada _view_ fica dentro de um mesmo projeto.

Quando falamos de escalabilidade do sistema, temos mais controle no lado do servidor pois é mais fácil adicionar recursos de _hardware_. Se sua aplicação ficar muito pesada e houver preocupação com o funcionamento em celulares mais antigos, pense em renderizar mesmo do lado do servidor.

> **De olho na dica 👀:** O ranking dos resultados na busca do Google dá prioridade para páginas que carregam mais rápido!

Falando de segurança, é melhor gerenciar autenticação e autorização do lado do servidor porque é mais seguro e fácil de controlar aquilo que pode ou não pode ser exibido.

Entretanto, existe o outro lado da moeda que veremos a seguir.

## _Client-side_

Quando o processamento da página é feito no lado do cliente, o servidor pode enviar os arquivos para o navegador mais rapidamente, resultando em um tempo de carregamento mais rápido, diminuindo a latência.

A técnica de _client-side_ permite criar interfaces mais interativas e responsivas. Além disso, o usuário pode ter maior controle sobre como o aplicativo é executado. Por exemplo, você gosta de ativar o Dark Mode, não é?

Imagina o servidor ter de retornar todo um HTML, só porque mudou a cor de fundo? Com isso, o _client-side_ fornece mais poderes.

> Curiosidade secreta: O Express roda do lado do servidor, mas tudo bem, pois ele é Back-end. Contudo, instalando uma biblioteca de view engine, como EJS, Handlebars ou Pug por exemplo, também é possível ao cliente renderizar um _template_ que foi criado do lado do servidor. Se quiser saber mais sobre, temos [esse artigo](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/f04cdb21-382e-4588-8950-3b1a29afd2dd/section/c1b04371-140f-442d-9287-0d3962e33c03/lesson/9290f1af-a3c5-4904-a544-2512c216793d) disponível demonstrando o uso de Express + EJS para server side rendering.

Mas, não vamos perder o foco aqui no Python.

## REST API

O _client-side_ também não vive sozinho. Como você já deve ter conhecido, as REST APIs servem de apoio para retornar com segurança as informações do Back-end/Banco de Dados, dando muito mais dinamismo à aplicação. No entanto, isso pode acabar aumentando o tempo de renderização se houver constantes requisições.

Você se lembra da REST API de piadas vista anteriormente?

Hoje vamos incrementar nossa aplicação com um front-end, aplicando a técnica de _server-side_.

## Em Resumo

Para aplicativos em que a renderização rápida é fundamental, e a interatividade não é tão importante, SSR pode ser a melhor opção. Para aplicativos que requerem muita interatividade e atualizações frequentes, a abordagem de _client-side_ + REST API pode ser mais adequada.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/2502e0b8-be2f-4307-aff9-46932f8f13dc/day/0c01be06-7742-4513-ab65-a29fde93f3c2/lesson/cfd00426-b3f7-43d7-9545-ca779e799853)
