[[Python]]
[[Python - 7 Manipulação de arquivos]]

O formato **CSV** (_Comma Separated Values_) é muito comum em exportações de planilhas de dados e base de dados. Foi utilizado por muito tempo antes de sua definição formal, o que gerou uma despadronização deste formato e o surgimento de vários dialetos.

Cada dialeto tem seus próprios delimitadores e caracteres de citação, porém o formato geral é semelhante o suficiente para utilizarmos o mesmo módulo para este processamento.

Ainda que seu nome indique que o delimitador seja a “`,`“ (vírgula), existem variações que utilizam “`;`“ (ponto e vírgula) ou até mesmo tabulações (“`\t`“).

🎲 Sem dúvidas, análise de dados é o que se destaca quando estamos falando sobre manipular arquivos **CSV**.

Vamos analisar uma base de dados a respeito dos cursos de graduação oferecidos pela Universidade de Brasília (UnB). O arquivo utilizado é o [graduacao_unb.csv](https://lms-assets.betrybe.com/lms/graduacao_unb.csv).

> 💡 Para fazer o exemplo, cole o arquivo `graduacao_unb.csv` na raiz do diretório em que estará o seu _script_.

O módulo [`csv`](https://docs.python.org/3/library/csv.html), contém duas principais funções:

- Um leitor (`reader`) que nos ajuda a ler o conteúdo, já fazendo as transformações dos valores para Python;
- E um escritor (`writer`) para facilitar a escrita nesse formato.

```python
import csv

with open("graduacao_unb.csv", encoding = "utf-8") as file:
    # Os valores padrão de 'delimiter' e 'quotechar' são os mesmos utilizados 
    # abaixo. Você pode removê-los ou alterá-los conforme necessidade
    graduacao_reader = csv.reader(file, delimiter=",", quotechar='"')

    # Usando o conceito de desempacotamento
    header, *data = graduacao_reader

print(data)
```

Além do arquivo `file`, o leitor recebe parâmetros opcionais como os listados [aqui](https://docs.python.org/3/library/csv.html#csv-fmt-params).

Um leitor de `.csv` pode ser percorrido utilizando o laço de repetição `for` e, a cada iteração, retorna uma nova linha já transformada em uma lista Python com seus respectivos valores.

Podemos fazer uma análise e verificar quantos cursos são ofertados por departamento. Em seguida salvamos o resultado também no formato `.csv`:

```python
import csv

with open("graduacao_unb.csv", encoding="utf8") as file:
    graduacao_reader = csv.reader(file, delimiter=",", quotechar='"')
    # Usando o conceito de desempacotamento
    header, *data = graduacao_reader

group_by_department = {}
for row in data:
    department = row[15]
    if department not in group_by_department:
        group_by_department[department] = 0
    group_by_department[department] += 1

# Escreve o relatório em .csv
# Abre um arquivo para escrita
with open("department_report.csv", "w", encoding = "utf-8") as file:
    writer = csv.writer(file)

    # Escreve o cabeçalho
    headers = [
        "Departamento",
        "Total de Cursos",
    ]
    writer.writerow(headers)

    # Escreve as linhas de dados
    # Desempacotamento de valores
    for department, grades in group_by_department.items():
        # Agrupa o dado com o turno
        row = [
            department,
            grades,
        ]
        writer.writerow(row)

```

Existem ainda o leitor e o escritor baseados em dicionários. A principal vantagem é que não precisamos manipular os índices para acessar os dados das colunas. A desvantagem é o espaço ocupado em memória (que é maior que o comum), devido à estrutura de dados utilizada.

Copiar

```python
import csv

# lê os dados
with open("graduacao_unb.csv", encoding = "utf-8") as file:
    graduacao_reader = csv.DictReader(file, delimiter=",", quotechar='"')

    # A linha de cabeçalhos é utilizada como chave do dicionário
    # agrupa cursos por departamento
    group_by_department = {}
    for row in graduacao_reader:
        department = row["unidade_responsavel"]
        if department not in group_by_department:
            group_by_department[department] = 0
        group_by_department[department] += 1

```

> 💡 Ainda que a manipulação de arquivos seja algo trivial, caso precise fazer análises de dados, leve em consideração bibliotecas como [Pandas](https://pandas.pydata.org/). Elas foram construídas e são mantidas justamente para atender e facilitar este objetivo.

###### Fonte: [Curse](https://app.betrybe.com/learn/course/5e938f69-6e32-43b3-9685-c936530fd326/module/290e715d-73e3-4b2d-a3c7-4fe113474070/section/b436f9e0-dfde-4a16-9bad-82f0c559dd45/day/61e88b4a-b97a-4f96-b5a0-abaa50651e37/lesson/293603be-ede4-41d6-8921-963ecdb0bc44)
