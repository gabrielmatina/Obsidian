[[Python]]
[[1 - Debug com Python]]

## Onde estamos?

Olá!

Você já sabe como buscar bugs de forma estruturada para corrigi-los.

Agora vai aprender a como evitar ao máximo que esses bugs voltem a aparecer, por meio da construção de testes automatizados.

Você irá aprender a:

- Criar seus próprios testes automatizados com o módulo `doctest`.
- Criar seus próprios testes automatizados com o módulo `pytest`.
- Simular cenários de _bugs_ por meio de testes.
- Executar conjuntos de testes de forma automática.
- Utilizar _fixtures_ de testes do `pytest`.
- Criar suas próprias _fixtures_ de teste.
- Criar testes parametrizados.
- Utilizar ferramentas de depuração em conjunto com testes.

---

## Por que isso é importante?

Aprender a criar testes automatizados é fundamental para garantir a qualidade do software desenvolvido, uma vez que esses testes permitem identificar problemas e bugs no código antes mesmo de serem lançados em produção.

Quando temos uma suíte de testes devidamente configurada e um bug novo é reportado, podemos criar um teste que simula o comportamento que faz o bug aparecer. Desta forma, ao resolver o problema, o teste irá passar e assegurar que o mesmo bug não ocorrerá novamente.

Além disso, a automação de testes gera uma série de benefícios, como a economia de tempo e recursos na execução de testes repetitivos, a facilitação da detecção de erros e a possibilidade de realizar testes em diferentes cenários e situações.

Nesse sentido, os módulos `doctest` e `pytest` oferecem ferramentas poderosas para a criação de testes automatizados em Python, permitindo que a pessoa desenvolvedora possa testar o código de forma eficiente e precisa.

Com essas ferramentas, é possível simular diferentes cenários de bugs, executar conjuntos de testes de forma automática e criar testes parametrizados, entre outras funcionalidades que tornam o processo de teste mais ágil e eficaz.

## Referências

- [Guia de referências do Pytest](https://docs.pytest.org/en/7.3.x/reference/index.html)
- [Guias de uso do Pytest](https://docs.pytest.org/en/7.3.x/how-to/index.html)
- [Documentação da Pathlib](https://docs.python.org/3/library/pathlib.html)
- [Packaging Your Python Code With pyproject.toml | Complete Code Conversation](https://www.youtube.com/watch?v=v6tALyc4C10)
- Livro [Python Testing With Pytest](https://www.amazon.com.br/Python-Testing-Pytest-Effective-Scalable/dp/1680508601/), do Brian Okken
- [Documentação Python - decorador](https://docs.python.org/pt-br/3/glossary.html#term-decorator)
- [Vídeo: “Gerando amostra de dados profissionais com Faker em Python”](https://www.youtube.com/watch?v=7M2Q5wrqC7I)
- Alguns plugins legais para o `Pytest`:
    - [`pytest-sugar`](https://github.com/Teemu/pytest-sugar)
    - [`hypothesis`](https://hypothesis.readthedocs.io/en/latest/quickstart.html)


## Referências

- [Documentação do módulo `unittest.mock`](https://docs.python.org/3/library/unittest.mock.html)
- [Documentação da biblioteca `Faker`](https://faker.readthedocs.io/en/master/)
- [Documentação da biblioteca `pytest-cov`](https://pytest-cov.readthedocs.io/en/latest/readme.html)

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/18498288-db33-45a4-9189-b7a282d99538/day/eeb316f5-4729-427e-960b-1397a647e5d7/lesson/a14ce2d2-e6ca-49c9-9358-042c3622799a)
