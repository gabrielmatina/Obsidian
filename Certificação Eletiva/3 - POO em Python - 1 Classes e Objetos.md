[[3 - POO em Python]]

## Classes

Na orientação a objetos, os códigos geralmente começam numa **Classe**. Na Programação Orientada a Objetos, toda classe tem como finalidade modelar com precisão a representação de uma entidade, dizendo quais características essa entidade tem e quais ações pode realizar. Uma analogia do que uma classe pode representar é o conceito de Liquidificador. Existem vários liquidificadores no mundo, de várias marcas e vários modelos, com várias funcionalidades diferentes, mas todos têm algumas coisas em comum.

Para criar uma classe em Python, utilizamos a palavra reservada `class`, seguida do nome da classe a ser criada e por fim colocamos dois pontos:

```python
class Liquidificador:
    pass
```

## Objetos

**Objeto**, ou instância da classe, é algo específico. Vamos supor que tanto eu quanto você tenhamos um liquidificador em casa. Ambos são liquidificadores, mas o meu é diferente do seu. Na verdade, mesmo que seja igual (mesma marca, mesmo modelo, mesma cor etc.), não são **o mesmo**. Na nossa analogia, isso quer dizer que são **objetos** diferentes de uma mesma classe.

Para criar um objeto, basta colocar o nome da classe seguido de parênteses:

```python
liquidificadorDeCasa = Liquidificador()
```

![Classes e Objetos](https://content-assets.betrybe.com/prod/f1893d0b-75e4-4dea-89e6-7a7495fbcb25-Classes%20e%20Objetos.png)

## Para fixar

## Exercício 3

Crie a classe `Database`. Não precisa colocar nada dentro dela, somente a palavra reservada `pass`.

### Solução

```python
class Database:
    pass
```

## Exercício 4

Crie dois objetos da classe `Database`, criada no exercício anterior, instanciando-os.

### Solução

```python
class Database:
    pass


database_1 = Database()
database_2 = Database()
```


###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/d530112b-87a8-4577-acd2-1a9651a89888/lesson/a9d40ee2-489f-4de2-87d8-9c7bf0c8f40a)
