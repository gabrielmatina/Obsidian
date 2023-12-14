[[3 - POO em Python]]

Agora que vimos como criar nossos objetos, é essencial sabermos simplificar seu uso e esconder os detalhes de implementação.

O encapsulamento é um dos **pilares da orientação a objetos**. Por meio dele, é possível simplificar bastante a implementação da abstração. Assim, segmentamos nossos atributos e métodos em 3 categorias:

![Encapsulamento](https://content-assets.betrybe.com/prod/a7e50ab7-a9da-46e1-8994-6fac930d9e52-Encapsulamento.png)

Em Python, existe uma convenção de nomenclatura para definir a acessibilidade de cada recurso. Veja a seguir.

- Nomes iniciados com `_` (underline): são considerados “**protegidos**“, como os atributos `_potencia` e `_tensao`.
    
    - ⚠️ Isso é apenas uma convenção entre pessoas desenvolvedoras Python, pois ainda será possível fazer um acesso direto por fora da classe;
- Nomes iniciados com `__` (**dunder**/duplo underline): são considerados “**privados**“, como os atributos `__ligado` e `__velocidade`.
    
    - ⚠️ Não será possível fazer o acesso diretamente por fora da classe, mas existem formas de burlar isso (caso queira saber mais pesquise _name mangling_).
- Quaisquer outros nomes válidos são **públicos**.

Para ilustrar, vamos voltar ao exemplo do liquidificador.

> Como podemos proteger o atributo ligado e, ao mesmo tempo, fazer com que fique simples e seguro alterá-lo? Resposta: **com os métodos**.

Para isso, podemos criar os métodos `ligar` e `desligar` e, assim, daremos poderes para que eles consigam manipular os atributos.

Observe o código a seguir:

```python
class Liquidificador:
    def __init__(self, cor: str, potencia: str, tensao: str, preco: str) -> None:
        self.preco = preco
        self.cor = cor
        self._potencia = potencia
        self._tensao = tensao
        self.__ligado = False
        self.__velocidade = 0
        self.__velocidade_maxima = 3
        self.__corrente_atual_no_motor = 0

    def ligar(self, velocidade: int) -> None:
        if velocidade > self.__velocidade_maxima or velocidade < 0:
            raise ValueError(
                f"Velocidade deve estar entre 0 e {self.__velocidade_maxima}"
            )

        self.__velocidade = velocidade
        self.__corrente_atual_no_motor = (
            (self._potencia / self._tensao) / self.__velocidade_maxima
        ) * velocidade
        self.__ligado = True

    def desligar(self):
        self.__ligado = False
        self.__velocidade = 0

    def esta_ligado(self):
        return self.__ligado
```

## Qual visibilidade usar?

Diferentemente de linguagens mais rigorosas como o Java, em que a filosofia é criar atributos privados por padrão, no Python foi adotada a filosofia oposta: – por padrão os atributos e os métodos são públicos. A ideia é que isso facilita a vida de quem está desenvolvendo, sem inserir complexidades desnecessárias.

Muitas vezes, entretanto, precisamos indicar que algo não deve ser utilizado por quem não está dentro do contexto da classe, visto que algo inesperado pode acontecer se isso for feito. Um exemplo é quando um atributo foi feito para ser somente lido por quem está fora da classe, mas sua valoração deve ser feita seguindo alguma lógica interna. Para esses casos, a convenção é utilizar **somente um underline**, sem proibir ninguém de acessar caso queira, mas sinalizando que tal acesso não é encorajado.

### _Getters_ e _Setters_

Existe uma outra forma de manipular atributos privados de maneira indireta, utilizando métodos denominados _getters_ e _setters_.

Um método _setter_ implementa a lógica de como alterar um valor. Um método _getter_ implementa a lógica de como recuperar um valor.

Copiar

```python
class Liquidificador:
    def get_cor(self):
        return self.__cor.upper()

    def set_cor(self, nova_cor: str) -> None:
        if nova_cor.lower() == "turquesa":
            raise ValueError("Não existe liquidificador turquesa")

        self.__cor = nova_cor

    def __init__(self, cor: str, potencia: str, tensao: str, preco: str) -> None:
        # Observe que usamos o setter para já validarmos o primeiro valor
        self.set_cor(cor)

        # Demais variáveis omitidas para este exemplo


liquidificador = Liquidificador("Azul", "110", "127", "200")

# print(f"A cor atual é {liquidificador.__cor}")
# AttributeError: 'Liquidificador' object has no attribute '__cor'

print(f"A cor atual é {liquidificador.get_cor()}")
# A cor atual é AZUL
liquidificador.set_cor("Preto")
print(f"Após pintarmos, a nova cor é {liquidificador.get_cor()}")
# Após pintarmos, a nova cor é PRETO

```

Como podemos ver, o acesso ao atributo privado `liquidificador.__cor` gera um erro, mas chamar o método `get_cor()` funciona perfeitamente. O comportamento é similar para o acesso: `liquidificador.__cor = "alguma cor"` gera um erro, mas `liquidificador.set_cor("alguma cor")` não (desde que a cor não seja _turquesa_).

### Especificidades do Python

Métodos com prefixos `get_` e `set_` costumam, em Python, ser substituídos por uma forma de acesso mais transparente, para que possam ser utilizados como “atributos públicos”. Para isso, são utilizados os decoradores (_decorators_) `@property` e `@propriedade.setter`, como no exemplo a seguir:

Copiar

```python
class Liquidificador:
    # Getter
    @property
    def cor(self):
        return self.__cor.upper()

    @cor.setter # Repare que é @cor.setter, e não @property.setter
    def cor(self, nova_cor: str) -> str:
        if nova_cor.lower() == "turquesa":
            raise ValueError("Não existe liquidificador turquesa")

        self.__cor = nova_cor

    def __init__(self, cor: str, potencia: str, tensao: str, preco: str) -> None:
        # Observe que usamos o setter para já validarmos o primeiro valor:
        # usamos self.cor, que chama o setter, e não self.__cor que manipula
        # o atributo diretamente
        self.cor = cor

        # Demais variáveis omitidas para este exemplo


liquidificador = Liquidificador("Rosa", "110", "127", "200")

print(liquidificador.cor) # ROSA
liquidificador.cor = "Vermelho"
print(liquidificador.cor) # VERMELHO

```

Com isso, podemos acessar o método `cor` e decorá-lo com o `@property` para facilitar o acesso de fora `liquidificador.cor`. É possível criar um outro método também com o nome `cor` e decorar com `@cor.setter`. Ele deve receber, além de `self`, um segundo parâmetro que é o novo valor de cor. Com base nisso, a atribuição `liquidificador.cor = "Vermelho"` passa a funcionar.

## Para fixar

## Exercício 7

Crie uma classe `GastoMensal` que contenha os seguintes atributos:

`internet`: público `supermercado`: público `luz`: protegido `agua`:protegido `aluguel`: privado

### Solução

```python
class GastoMensal:
    def __init__(self,
        internet: float,
        supermercado: float,
        luz: float,
        agua: float,
        aluguel: float
    ) -> None:
        self.internet = internet
        self.supermercado = supermercado
        self._luz = luz
        self._agua = agua
        self.__aluguel = aluguel

```

## Exercício 8

Utilize o código que você criou no exercício anterior e crie os métodos `get` e `set` para os atributos `luz` e `agua`.

### Solução

```python
class GastoMensal:
    def __init__(self,
        internet: float,
        supermercado: float,
        luz: float,
        agua: float,
        aluguel: float
    ) -> None:
        self.internet = internet
        self.supermercado = supermercado
        self._luz = luz
        self._agua = agua
        self.__aluguel = aluguel

    def get_luz(self):
        return self._luz

    def set_luz(self, novo_valor: float) -> float:
        self._luz = novo_valor

    def get_agua(self):
        return self._agua

    def set_agua(self, novo_valor: float) -> float:
        self._agua = novo_valor

```

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/3d93d491-e3ed-409f-bdb6-3a5dcd11f8d2/section/24e9e68e-2143-463b-93a8-397951889272/day/cae79849-85dd-4853-b0c3-065f657bfee4/lesson/2cf2daab-ae4d-420d-a2d8-e35d79552d89)
