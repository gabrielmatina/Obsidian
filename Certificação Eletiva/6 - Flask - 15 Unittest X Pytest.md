[[6 - Flask]]

Como vimos nas lições anteriores, tanto o unittest quanto o pytest são capazes de testar nossas aplicações, mas existem algumas diferenças entre eles, vamos falar sobre elas:

Unittest e pytest são duas estruturas populares de teste em Python. Ambas as estruturas são usadas para escrever e executar testes, mas apresentam algumas diferenças em termos de sintaxe, recursos e abordagem para testes.

![[Pasted image 20230815101441.png]]

Em resumo, embora o unittest seja uma estrutura robusta de testes incluída na biblioteca padrão do Python, o pytest oferece uma sintaxe mais concisa e expressiva, recursos avançados, fixtures poderosos e um ecossistema maior de plugins. O pytest é frequentemente preferido por sua simplicidade, extensibilidade e experiência geral do desenvolvedor. No entanto, a escolha entre unittest e pytest depende principalmente da preferência pessoal e dos requisitos do projeto.

Ainda tem o fato de que a própria documentação do Flask sugere que o Pytest, como você pode verificar [aqui](https://flask.palletsprojects.com/en/2.3.x/testing/), por mais que seja uma recomendação, outras ferramentas como o unittest podem ser utilizados a depender dos requisitos do projeto, logo, é interessante que você tenha conhecimento de mais de uma forma de testar seus códigos.