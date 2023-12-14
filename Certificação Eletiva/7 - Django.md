[[Python]]

# O que é o Django?

**Django é um framework de alto nível para desenvolvimento web em Python**. Sua utilização pode trazer eficiência, robustez e escalabilidade às suas aplicações web, graças aos seus recursos prontos para uso, que possibilitam o desenvolvimento rápido e eficaz de aplicações complexas com menos código.

Alguns princípios fundamentais guiam o design e desenvolvimento do Django, fornecendo uma visão geral das facilidades que o _framework_ possibilita. Entre estes princípios, podemos destacar:

1- **Modularização**: os aplicativos Django são compostos por diversos módulos (chamados de _apps_), cada um com sua própria responsabilidade específica, o que facilita a organização e a manutenção do código, além de sua reutilização.

2- **Padrão Model-Template-View (MTV)**: o Django segue um padrão de **arquitetura em três camadas** - model, template e view, popularmente referido pela comunidade como Model-Template-View ou MTV. Nessa arquitetura, os modelos (_models_) representam a estrutura dos dados (mapeamento do banco de dados), as visualizações (_views_) são responsáveis por concentrar a lógica (regras de negócio) e os _templates_ lidam com a apresentação dos dados para a pessoa usuária (HTML que será renderizado no _browser_).

![Arquitetura MVT](https://content-assets.betrybe.com/prod/bc86340f-2447-46ab-a0b9-24be769b171c-Arquitetura%20MVT.png)


3- **ORM** (Object-Relational Mapping): o próprio Django já fornece um ORM que permite o uso de código Python para fazer as consultas e manipulações de banco de dados, tornando o desenvolvimento mais ágil por eliminar a necessidade de escrever consultas _SQL_ manualmente.

4- **Interface de administração pronta para uso**: a interface de administração automática oferecida pelo Django lê metadados nos modelos para fornecer uma interface que permite gerenciar seus dados - adicionar, editar e excluir registros do banco de dados, por exemplo, sem a necessidade de escrever um código.

5- **Roteamento de URLs**: o Django usa um sistema de mapeamento simples entre os padrões de URL de requisições _HTTP_ e as funções que definem suas _views_ (exibições) correspondentes, facilitando além da organização do código, a manutenção e a expansão da aplicação.

6- **Autenticação**: O Django vem com um sistema de autenticação completo de fácil uso, que lida com contas de usuário, grupos, permissões e sessões baseadas em cookies, permitindo a criação de sites em que pessoas usuárias possam criar contas e fazer _login/logout_ com segurança.

7- **Segurança**: o Django tem alguns [recursos integrados](https://docs.djangoproject.com/en/4.2/topics/security/) para ajudar a proteger as aplicações contra vulnerabilidades comuns, como injeção de **SQL**, **CSRF** (_Cross-Site Request Forgery_) e **XSS** (_Cross-Site Scripting_), por exemplo.

Quantos recursos, não é mesmo? 🤯

> **Para refletir 💭:** Você consegue pensar em algum projeto seu em que o desenvolvimento poderia ter sido bem mais rápido se você tivesse esses recursos disponíveis? 🤔

## Django vs Flask

Agora que você já teve uma visão geral dos recursos que o Django oferece, pode estar com a curiosidade aguçada para compará-lo com o Flask e saber quando utilizar cada um.

Como tudo na vida - e no desenvolvimento de software, **não existe bala de prata**, mas pelo contexto que temos dos dois _frameworks_, podemos determinar alguns casos de uso em que um se destaca em detrimento do outro.

Como dito anteriormente, o Django é um _framework_ de alto nível, que possui inúmeros recursos prontos para uso, o que acelera o desenvolvimento de aplicações web robustas. Sendo assim, ele é uma ótima opção para projetos complexos, que exigem inúmeros recursos e para aplicações cujo objetivo é criar um painel de administração para gerenciar dados de forma eficiente, uma vez que já oferece uma solução rápida e fácil para isso.

Por sua vez, por seu minimalismo e flexibilidade, o Flask pode é uma ótima opção para projetos pequenos ou médios, nos quais a simplicidade e a flexibilidade são desejáveis, além da criação de APIs e microsserviços leves e personalizáveis.

Ainda assim, é importante ressaltar que vários aspectos devem ser considerados para definir a escolha do _framework_ que será adotado em um projeto, desde a complexidade dele, seu escopo e seus requisitos, por exemplo, até questões de familiaridade e curva de aprendizado da equipe que irá desenvolvê-lo. 😉

## Referências

1. [Documentação oficial](https://docs.djangoproject.com/).
2. [Documentação Oficial do Django](https://docs.djangoproject.com/pt-br/4.2/)
3. [Tags e filtros nativos do Django](https://docs.djangoproject.com/pt-br/4.2/ref/templates/builtins/)
4. [Documentação do Bootstrap - Framework CSS que pode ser usado em templates](https://getbootstrap.com/docs/5.0/getting-started/introduction/)
5. [Documentação do Tailwind CSS - Framework CSS que pode ser usado em templates](https://tailwindcss.com/docs/installation)
6. [Documentação oficial do Django](https://docs.djangoproject.com/).
7. [Documentação oficial do Django REST Framework](https://www.django-rest-framework.org/).
8. [Como implementar autenticação de token no _django rest framework_](https://simpleisbetterthancomplex.com/tutorial/2018/11/22/how-to-implement-token-authentication-using-django-rest-framework.html) (texto em inglês)

## Recursos adicionais

Se você deseja se aprofundar nesses conteúdos, recomendamos os seguintes recursos:

1. [Configurações de Database](https://docs.djangoproject.com/en/3.2/ref/settings/#databases).
2. [Migrations](https://docs.djangoproject.com/en/3.2/topics/migrations/). [Recursos integrados de segurança](https://docs.djangoproject.com/en/4.2/topics/security/).
3. [Guia de configuração de ambiente](https://www.python.org/downloads/)
4. [Documentação oficial do Django REST Framework](https://www.django-rest-framework.org/).
5. [DRF Serializers](https://www.django-rest-framework.org/api-guide/serializers/#metadata).
6. [Pesquisa do Jet Brains](https://lp.jetbrains.com/django-developer-survey-2022/).
7. [Documentação do Django REST Framework](https://www.django-rest-framework.org/)
8. [Documentação do Django](https://docs.djangoproject.com/en/4.2/)
9. [Documentação do Simple JWT](https://django-rest-framework-simplejwt.readthedocs.io/en/latest/)



###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/d6669182-df2d-4db1-a5c3-ee927fee22d7/day/32af04d3-9bec-4278-85dd-bee03a8887fb/lesson/b2affae7-4136-43b7-b279-61d622a8fe9f)
