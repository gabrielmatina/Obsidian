[[3 - POO em Python]]

A abstração de dados é outro **pilar da orientação a objetos**, que oculta os detalhes da implementação mostrando apenas a funcionalidade para quem acessa os métodos, a fim de reduzir a complexidade do código.

```python
liquidificador_vermelho = Liquidificador("Vermelho", 250, 220, 100)
liquidificador_vermelho.ligar(1)
print("Está ligado?", liquidificador_vermelho.esta_ligado())
# Está ligado? True
liquidificador_vermelho.desligar()
print("Está ligado?", liquidificador_vermelho.esta_ligado())
# Está ligado? False
```

Perceba que, ao chamarmos o método `ligar`, não existe a necessidade de conhecer o cálculo interno que define se o liquidificador está ligado ou não. Estamos aplicando a abstração nesse caso, pois apenas o código da classe `Liquidificador` precisa se preocupar com a regra de negócio.

Quando utilizarmos a classe em outra parte da aplicação, precisaremos apenas saber quais são os parâmetros necessários de cada método.

## Para fixar

## Exercício 5

Crie uma classe chamada `Retangulo` que represente um retângulo com os atributos `base` e `altura`. A classe deve ter métodos para calcular a área e o perímetro do retângulo.

### Solução

```python
class Retangulo:
    def __init__(self, base: int, altura: int) -> None:
        self.base = base
        self.altura = altura

    def calcular_area(self):
        return self.base * self.altura

    def calcular_perimetro(self):
        return 2 * (self.base + self.altura)

```

## Exercício 6

Com base no exercício anterior, instancie um objeto da classe `Retangulo` com os valores `base = 5` e `altura = 10` e chame os métodos `calcular_area` e `calcular_perimetro`. Observe como quem usa a classe não precisa entender como o calculo é feito.

### Solução

```python
retangulo = Retangulo(5, 10)
print("Área:", retangulo.calcular_area()) # Área: 50
print("Perímetro:", retangulo.calcular_perimetro()) # Perímetro: 30

```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/cae79849-85dd-4853-b0c3-065f657bfee4/lesson/5339bd52-a3d6-43f2-9d32-e0387b61af4f)

