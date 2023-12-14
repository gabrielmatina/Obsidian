[[Complexidade de Algoritmos]]
[[Python]]

Relembrando🧠:

- A **Ordem de Complexidade** diz respeito à taxa de crescimento do tempo de execução (ou espaço de memória ocupado) de um algoritmo, na medida em que aumentamos o tamanho da sua entrada;
- Uma complexidade é `O(1)` (constante), quando o tempo de execução do algoritmo independe do tamanho da entrada;
- Uma complexidade é `O(n)` (linear) quando a taxa de crescimento em seu tempo de execução é **linear**: se aumentamos a entrada em _2 vezes_, aumentamos o tempo de execução em _2 vezes_;
- Uma complexidade é `O(n²)` (quadrática) quando a taxa de crescimento do tempo de execução do algoritmo é **quadrática**: se aumentamos a entrada em **2 vezes**, aumentamos o tempo de execução em **4 (ou 2²) vezes**;
- Uma complexidade é `O(n³)` (cúbica) quando a taxa de crescimento do tempo de execução do algoritmo é **cúbica**: se aumentamos a entrada em **2 vezes**, aumentamos o tempo de execução em **8 (2³) vezes**.


![Vamos com calma para os números não confundirem!](https://content-assets.betrybe.com/prod/Vamos%20com%20calma%20para%20os%20n%C3%BAmeros%20n%C3%A3o%20confundirem!.gif)|
Vamos com calma para os números não confundirem!

Falamos aqui de algoritmos `O(n)`, `O(n²)` e até de `O(n³)`. Mas, para ilustrar melhor a matemática dos algoritmos, vamos mostrar o que eles significam de outra forma.

Observe, o gráfico abaixo e veja como o **tempo de execução** de um algoritmo _cúbico_ cresce muito mais para uma entrada, **muito menor** que a do algoritmo _linear_:

![Gráfico da relação entre as ordens de complexidade linear, quadrática e cúbica](https://content-assets.betrybe.com/prod/Gr%C3%A1fico%20da%20rela%C3%A7%C3%A3o%20entre%20as%20ordens%20de%20complexidade%20linear,%20quadr%C3%A1tica%20e%20c%C3%BAbica.png)
Gráfico da relação entre as ordens de complexidade linear, quadrática e cúbica

> Para exemplificar, vamos pensar do seguinte modo: para um algoritmo linear, com `n = 1000`, teremos mil operações a serem realizadas. Quando o algoritmo é `O(n²)`, um `n = 1000` gera **um milhão de operações** (ou n² de operações). Essa mesma quantidade (um milhão) para `O(n³)`, se atinge com `n = 100`.

- Está entendendo como alguns algoritmos podem ficar rapidamente inviáveis de se executar? Por isso, compreender a taxa de crescimento de um algoritmo é tão importante!

A seguir, vamos explorar outros tipos de complexidades de algoritmos!

De olho na dica👀: Caso você se confunda com a quantidade de números, rode exemplos na sua máquina contando o número de iterações! É uma excelente forma de visualizar a complexidade acontecendo. E não deixe de falar com a gente no Slack se algum exemplo estiver te confundindo!

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/1e72f959-dcab-4e2d-948b-6f6e5aef58ab/day/cb6a6831-db64-41b7-9197-6656dc970b75/lesson/4b991161-f07a-4281-87b9-38793e84db79)
