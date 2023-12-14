[[Recursividade]]

Agora, vamos ver que é possível ter funções tanto recursivas, quanto iterativas para o mesmo problema. Para isso, utilizaremos os exemplos que já usamos nesse conteúdo.

Vamos começar olhando para a função recursiva de contagem regressiva. Conseguimos montar uma função iterativa para ela? Sim! Vamos ver como fazer isso:

```python
def iterative_countdown(n):
    while n > 0:
        print(n)
        n = n - 1
    print("FIM!")

iterative_countdown(5)
```

Vamos ver agora como fica o código iterativo de cálculo de fatorial:

```python
def iterative_factorial(n):
    fact = 1

    for i in range(1, n + 1):
        fact = fact * i

    return fact

iterative_factorial(5)
```

Como vimos anteriormente, escolher entre uma solução recursiva ou iterativa depende muito do que estamos buscando.

- Escolher uma solução recursiva não significa ganho de performance, muito pelo contrário, grande parte das vezes, a solução iterativa será mais performática.
- Escolher a solução recursiva terá um ganho na diminuição de complexidade do código do seu projeto. Aqui, complexidade significa legibilidade. Ou seja, nosso código fica mais simples, mais harmonioso, quando utilizamos a recursividade.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/bfc198bb-2ceb-4271-b137-2388dddbeee2/lesson/51847865-44bd-4f4c-b6a1-bad22c72531f)
