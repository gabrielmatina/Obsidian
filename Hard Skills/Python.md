
# Introdução (O que é Python?)

Já estamos avançando com os aprendizados em desenvolvimento Web e chegou a hora de aprender sobre a linguagem Python. Ao final do material, vamos te mostrar como escrever seus primeiros arquivos de código. Continue a leitura para conferir!

**Python** é uma linguagem de programação que busca simplicidade em sua sintaxe, resultando assim em legibilidade do código e maior produtividade. Seu interpretador pode ser executado em diversos sistemas operacionais como Linux, MacOS e Windows, quase sempre sem mudanças no código.

Tudo começou no final dos anos 80, quando [Guido van Rossum](https://en.wikipedia.org/wiki/Guido_van_Rossum) começou a trabalhar no Python como um sucessor da linguagem de programação ABC. O primeiro lançamento oficial da linguagem foi em 1991 com o `Python 0.9.0.`, e em 2000 houve o lançamento da segunda versão, `Python 2.0`, com a introdução de novos recursos, tais como: compreensão de lista, coleta de lixo com detecção de ciclo, contagem de referência e suporte a Unicode.

Em 2008, o `Python 3.0` foi lançado como uma revisão importante que não era totalmente compatível com as versões anteriores. Em 2020, o Python 2.0 foi descontinuado com a versão 2.7.18. Fonte: [Wikipedia](https://en.wikipedia.org/wiki/Python_(programming_language))

Devido a sua simplicidade e didática, **Python** foi escolhida como a linguagem principal a ser ensinada na maioria das universidades dos Estados Unidos, por exemplo no Instituto de Tecnologia de Massachusetts (_MIT_).

Muito popular hoje em dia devido à área de ciência de dados, essa linguagem pode ser utilizada em:

- criação de aplicações web;
- automação de tarefas repetitivas;
- aplicativos desktop;
- aplicações para dispositivos móveis (embora para essa finalidade Python não seja tão popular e nem recomendada).

Veja a imagem a seguir que mostra as principais empresas mundiais que usam Python.

|![Empresas mundiais que usam Python](https://content-assets.betrybe.com/prod/Empresas%20mundiais%20que%20usam%20Python.jpeg)|
|---|
|Empresas mundiais que usam Python|

No Brasil, empresas grandes como Globo.com, Olist, Luizalabs e Jusbrasil utilizam essa linguagem em diversos projetos. Aqui na Trybe usamos para realizar a análise de dados!

Além da sua simplicidade e didática, já mencionados anteriormente, Python também é conhecida por suas “_baterias já inclusas_“, ou seja, ela traz consigo um conjunto de bibliotecas úteis para diversas tarefas, como manipular dados no formato JSON e CSV. A linguagem também possui um servidor web simples, ferramenta para emails e muito mais!

|![Captura de tela do site do Google de 1999 mostrando Python](https://content-assets.betrybe.com/prod/Captura%20de%20tela%20do%20site%20do%20Google%20de%201999%20mostrando%20Python.png)|
|---|
|Captura de tela do site do Google de 1999 mostrando Python|

Por fim, mas não menos importante, Python possui um ecossistema que vai além da linguagem. Há uma comunidade que se apoia na filosofia de “pessoas > tecnologia”, possuindo assim uma preocupação relevante com inclusão e diversidade. As pessoas “pythonistas”, como assim são chamadas, tendem a ser pessoas sempre dispostas a ajudar o próximo. Por isso, é comum escutarmos a frase: _“venha pela linguagem, fique pela comunidade”_.

Bora começar? Vamos abrir o terminal e digitar alguns comandos. 🖥️

# Terminal Interativo (REPL)

> Você pode estar se perguntando: “Não precisaremos instalar nada?!”

> Resposta: As distribuições do sistema operacional **Linux** e **Mac**, normalmente, já vem com uma versão **Python** instalada, pois utilizam a linguagem em diversos programas essenciais.

Vamos verificar isto abrindo um terminal e digitando `python3`. A saída deve ser semelhante a esta:

Copiar

```bash
1Python 3.8.2 (default, Jun  2 2020, 13:51:17)
2[GCC 9.3.0] on linux
3Type "help", "copyright", "credits" or "license" for more information.
4>>>
```

Este tipo de terminal é chamado de `REPL` (_Read-Eval-Print Loop_), sendo traduzido para o português como _loop de leitura-avaliação-impressão_ ou terminal interativo. Esse terminal recebe uma entrada digitada pela pessoa, avalia sua execução e imprime seu resultado.

⚠️ Aviso: Todos os exercícios de fixação do conteúdo deverão ser realizados utilizando o terminal interativo.

Normalmente, o primeiro programa escrito em uma linguagem de programação é o `Olá Mundo`. Porém, isso é tão rápido quanto digitar `print("Olá Mundo")`, portanto vamos começar com algo diferente.

Digite `import antigravity` e aperte `enter`.

Copiar

```python
1import antigravity
```

Leia a tirinha abaixo que explica essa maneira pythônica de dizer “Olá Mundo”.🐍

|![Tirinha xkcd sobre python](https://content-assets.betrybe.com/prod/Tirinha%20xkcd%20sobre%20python.png)|
|---|
|Tirinha xkcd sobre python|

Agora, aproveitando que estamos usando o terminal interativo, vamos ver a filosofia da linguagem, por meio dela podemos entender um pouco mais sobre como o **Python** foi escrito.

Digite no terminal interativo:

Copiar

```python
1import this
```

Anota aí ✏️: A fim de padronizar códigos escritos em Python, foi lançado um guia de estilo conhecido como **_PEP 8_** ou **_Python Enhancement Proposal 8_**. A **_PEP 8_** é um documento desenvolvido pela comunidade _pythônica_ que visa aprimorar a legibilidade dos códigos.

⚠️ Aviso: À medida que avançamos nos dias da seção, vamos destacar quais estilos este documento estabelece com a paleta.🎨

# Recursos adicionais (opcional)

- [Python Para Zumbis](https://www.youtube.com/channel/UCripRddD4BnaMcU833ExuwA)
- [Tuplas Mutantes em Python](http://pythonclub.com.br/tuplas-mutantes-em-python.html)
- [Pense em Python](https://penseallen.github.io/PensePython2e/01-jornada.html)
- [Introdução à Programação com Python](https://python.nilo.pro.br/)
- [Introdução ao Python - Microsoft](https://docs.microsoft.com/pt-br/learn/modules/intro-to-python)
- [Tutorial Python](https://docs.python.org/pt-br/3/tutorial/index.html)
- [Documentação do módulo csv](https://docs.python.org/3/library/csv.html)  
- [Documentação do módulo json](https://docs.python.org/3/library/json.html)
- [Python F-strings](https://realpython.com/python-f-strings/)
- [Trabalhando com arquivos em python](https://realpython.com/working-with-files-in-python/)
- [Guia do mochileiro para Python - tests](https://python-guide-pt-br.readthedocs.io/pt_BR/latest/writing/tests.html)
- [Live de Python - Testes com Python](https://www.youtube.com/watch?v=5hL9T3jintE)
- [Unit testing in Visual Studio](https://docs.microsoft.com/pt-br/visualstudio/python/unit-testing-python-in-visual-studio?view=vs-2019)
- [Dublês de teste](https://cassiobotaro.dev/post/dubles-de-teste/)
- [Awesome Python](https://github.com/vinta/awesome-python)
- [Awesome Python Scripts](https://github.com/prathimacode-hub/Awesome_Python_Scripts)




###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/bee44ac6-0782-48cd-9ce8-1811980e558e/lesson/7310b8c3-dcb0-40e4-94af-29a0ad522604)