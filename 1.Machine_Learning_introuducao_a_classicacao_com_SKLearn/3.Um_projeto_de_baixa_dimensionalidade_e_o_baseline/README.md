**Testando em duas dimensões**

Nosso próximo projeto usará um   [novo arquivo de dados](https://gist.githubusercontent.com/guilhermesilveira/1b7d5475863c15f484ac495bd70975cf/raw/16aff7a0aee67e7c100a2a48b676a2d2d142f646/projects.csv), que possui três colunas diferentes.

Cada linha possui um projeto diferente de site web, como o site de  uma escola, de uma padaria e assim por diante. As pessoas interessadas  em criar sites para seus respectivos negócios submetem o projeto a uma  empresa e estipulam o número de horas para que o projeto seja criado (`expected_hours`) , bem como a quantia a ser paga (`price`).

| unfinished | expected_hours | price |
| ---------- | -------------- | ----- |
| 1          | 26             | 192   |
| 1          | 88             | 9015  |
| 1          | 89             | 2577  |
| 1          | 42             | 275   |

A coluna `unfinished` mostra os projetos que foram finalizados (`0`) e os que não foram finalizados (`1`) - ou seja, se algum web designer pegou tal projeto, concordando com o prazo e o valor estipulados. 

A ideia é, a partir do conjunto de dados fornecido, tentarmos prever  qual projeto será finalizado ou não. Dessa forma, podemos oferecer mais  informações para as pessoas que submetem os projetos à plataforma, de  forma que elas adequem suas estimativas. 

Trabalharemos com os dados puros, em formato `.csv`, como no projeto anterior. Para começar, criaremos um arquivo no Google Colab chamado `Introduçao a Machine Learning 3.ipynb`. Importaremos a biblioteca Pandas, leremos o arquivo `.csv` e imprimiremos os cinco primeiros itens por meio de `head()`.

```
import pandas as pd

uri = "https://gist.githubusercontent.com/guilhermesilveira/1b7d5475863c15f484ac495bd70975cf/raw/16aff7a0aee67e7c100a2a48b676a2d2d142f646/projects.csv"
dados = pd.read_csv(uri)
dados.head()
```

Ao executarmos o código, será exibida a seguinte tabela:

| unfinished | expected_hours | price |
| ---------- | -------------- | ----- |
| 1          | 26             | 192   |
| 1          | 88             | 9015  |
| 1          | 89             | 2577  |
| 1          | 42             | 275   |
| 1          | 39             | 170   |
| ]          |                |       |

Queremos trabalhar com os nomes das tabelas em português, portanto renomearemos os títulos por meio do método `rename()`, passando paras as colunas as definições que escreveremos em uma variável `a_renomear`. 

Traduziremos `expected_hours` como `horas_esperadas`, e `unfinished` como `nao_finalizado`. No caso de `price`, a tradução seria `preço`, com `ç` mas não é comum usar caracteres especiais para nomes de colunas, afinal elas serão referenciadas como variáveis ao longo do código. Portanto,  escreveremos simplesmente `preco`. 

```
a_renomear = {
    'expected_hours' : 'horas_esperadas',
    'price' : 'preco',
    'unfinished' : 'nao_finalizado'
}
dados = dados.rename(columns = a_renomear)
dados.head()
```

Após executarmos o código teremos o seguinte resultado: 

| nao_finalizado | horas_esperadas | preco |
| -------------- | --------------- | ----- |
| 1              | 26              | 192   |
| 1              | 88              | 9015  |
| 1              | 89              | 2577  |
| 1              | 42              | 275   |
| 1              | 39              | 170   |

Contudo, a coluna `nao_finalizado` pode gerar confusões, afinal `0` seriam os projetos finalizados e `1` os não finalizados. Podemos organizar as nossas features com mais  clareza de forma a evitar erros no desenvolvimento do projeto. 

Criaremos uma nova coluna chamada `finalizados`, e trocaremos os valores `0` e `1`, invertendo a função deles nesta coluna. Por fim, mapearemos essa troca de valores usando `map(troca)`. 

```
troca = {
    0 : 1,
    1 : 0
}
dados['finalizado'] = dados.nao_finalizado.map(troca)
dados.head()
```

Ao imprimirmos os primeiros itens da tabela, veremos os seguintes resultados: 

| nao_finalizado | horas_esperadas | preco | finalizado |
| -------------- | --------------- | ----- | ---------- |
| 1              | 26              | 192   | 0          |
| 1              | 88              | 9015  | 0          |
| 1              | 89              | 2577  | 0          |
| 1              | 42              | 275   | 0          |
| 1              | 39              | 170   | 0          |

Agora os itens estão organizados de forma mais clara. Podemos verificar, também, os cinco últimos itens por meio de `dados.tail()`:

| nao_finalizado | horas_esperadas | preco | finalizado |
| -------------- | --------------- | ----- | ---------- |
| 1              | 26              | 192   | 1          |
| 1              | 88              | 9015  | 0          |
| 1              | 89              | 2577  | 1          |
| 1              | 42              | 275   | 1          |
| 1              | 39              | 170   | 0          |

O que queremos agora é visualizar as informações de outra forma. No projeto anterior, havia três *features* e um `y`. Neste caso, temos duas features (`horas_esperadas` e `preco`) e a classificação (`finalizado`). Não usaremos a coluna `nao_finalizado`.

Com essas duas *features*, podemos desenhar em um eixo as `horas_esperadas` e em outro o `preco`. Para isso, usaremos uma famosa biblioteca do Python chamada **Seaborn**. Vamos importá-la como `sns` (que é a importação padrão), e plotaremos os pontos distribuídos por meio do `scatterplot()`. 

Para este método, precisamos passar o parâmetro referente às coluna `x` (`horas_esperadas`) e `y` (`preco`). Por fim, devemos passar os `dados`, de onde vem nosso *dataframe* do Pandas. 

```
import seaborn as sns

sns.scatterplot(x="horas_esperadas", y="preco", data=dados)
```

Ao tentarmos executar, teremos um problema: o Google Colab utiliza uma versão antiga do Seaborn que não possui o método `scatterplot()`. 

```
AttributeError module 'seaborn' has no attribute 'scatterplot'
```

Solucionaremos esse problema atualizando o Seaborn com o comando `!pip install`, já suportado pelo Google Colab. Para esta lição, instalaremos a versão `0.9.0` do Seaborn. 

```
!pip install seaborn==0.9.0
```

Contudo, ainda há um detalhe que devemos nos ater: ainda que executemos a  biblioteca atualizada agora, ela já foi carregada anteriormente, o que  faz com que a versão antiga fique na memória e continuemos recebendo a  mensagem de erro. Uma boa prática é subir a célula de código que instala a versão mais recente do Seaborn, de forma que ela seja a primeira do  arquivo. Assim feito, clicaremos em "*Runtime > Restart runtime*" para reiniciarmos a máquina virtual, e executaremos todo o código novamente. 

Com o reinicialização, a nova versão do Seaborn estará ativa e  conseguiremos ver os resultados plotados. A ideia não é fazer um gráfico muito preciso, mas apenas apreender visualmente algumas informações. É  possível perceber, por exemplo, que a medida em que os projetos vão  ficando mais caros, os agrupamentos diminuem:

![grafico em duas dimensões. No eixo X "horas_esperadas" temos uma numeração de 0 a 100. No eixo Y "preco"  temos uma numeração de 0 a 30000. No gráfico há um agrupamento maior de projetos de valores 5000-10000, com as horas esperadas de 60-80 ](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_1_1_grafico.png)

O que gostaríamos de avaliar agora não é apenas o número de horas  esperadas e o preço, mas sim explorar mais quais são os projetos  finalizados ou não.  

Existem várias maneiras de tentarmos visualizar as duas classes (`0` ou `1` para `finalizado`) no gráfico. Uma delas é por meio de cores. Utilizaremos a mesma estrutura do método `scatterplot`, mas dessa vez passando o parâmetro `hue`, que será baseado na coluna `finalizado`, pintando os pontos de acordo com a classe atribuída à essa coluna: 

```
sns.scatterplot(x="horas_esperadas", y="preco", hue="finalizado", data=dados)
```

Ao executarmos o código teremos um gráfico com a mesma estrutura, mas com  diferentes tonalidades: laranja para projetos finalizados e azul para  projetos não finalizados.

Perceba que a medida em que `horas_esperadas` aumenta, os agrupamentos de projetos finalizados se deslocam para faixas maiores de `preco`.

![ gráfico com três dimensões: eixo X,Y e tonalidades divergentes. No eixo X "horas_esperadas" temos  uma numeração de 0 a 100. No eixo Y "preco"  temos uma numeração de 0 a 30000. No gráfico há um agrupamento maior de pontos laranjas a medida que o preço e horas esperadas são maiores](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_1_2_grafico+em+tonalidades.png)

Dessa forma, aprendemos que algumas informações podem ser  visualizadas de forma mais clara apenas adicionando diferenças de cor.  Uma outra alternativa é realizar uma plotagem relativa por meio de `relplot()`. Com esse método, ao invés das cores, queremos plotar os dados em colunas (`col`) diferentes, uma para os projetos finalizados e outra para os não-finalizados:

```
sns.relplot(x="horas_esperadas", y="preco", col="finalizado", data=dados)
```

Analisando o gráfico resultante, podemos perceber que parece existir uma faixa mínima de preço para os projetos finalizados: 

![Dois gráficos são apresentados, em ambos os casos No eixo X "horas_esperadas" temos uma numeração de 0 a 100. No eixo Y "preco"  temos uma numeração de 0 a 30000. No primeiro gráfico, temos os projetos não finalizados, com um agrupamento maior de pontos entre valores de 5000-1000, e horas 40-60. No segundo gráfico que se refere aos projetos finalizados, temos um agrupamento menor de pontos em projetos com custo entre 0-5000, com tempo entre 80 -100](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_1_3_graficos+relativos.png)

Também é possível inserir cores em plotagens relativas, adicionando  outro recurso visual para a organização dos dados. Para tanto,  voltaremos a inserir o parâmetro `hue`no código: 

```
sns.relplot(x="horas_esperadas", y="preco", hue="finalizado", col="finalizado", data=dados)
```

Teremos como resultado duas colunas com cores diferentes.

![gráficos com cores diferentes - Dois gráficos são apresentados, em ambos os casos No eixo X "horas_esperadas" temos uma numeração de 0 a 100. No eixo Y "preco" temos uma numeração de 0 a 30000. No primeiro gráfico, temos os projetos não finalizados apresentados por pontos azuis, com um agrupamento maior de pontos entre valores de 5000-1000, e horas 40-60. No segundo gráfico que se refere aos projetos finalizados apresentados em pontos laranjas, temos um agrupamento menor de pontos em projetos com custo entre 0-5000, com tempo entre 80 -100](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_1_4_graficos+com+cores+diferentes.png).

Nosso próximo passo é realizar a modelagem. Primeiro, coletaremos nossos `dados` no eixo `x`, que são as colunas `horas_esperadas` e `preco`. Em seguida, o eixo `y`, referente à coluna `finalizado`.

```
x = dados[['horas_esperadas', 'preco']]
y = dados['finalizado']
```

Agora criaremos um modelo, separaremos os dados de treino e teste,  treinaremos efetivamente o modelo e por fim verificaremos a taxa de  acerto. Fizemos todos esses processos no projeto anterior, portanto  podemos aproveitar a última versão do nosso código.

```
from sklearn.model_selection import train_test_split
from sklearn.svm import LinearSVC
from sklearn.metrics import accuracy_score

SEED = 20

treino_x, teste_x, treino_y, teste_y = train_test_split(x, y,
                                                         random_state = SEED, test_size = 0.25,
                                                         stratify = y)
print("Treinaremos com %d elementos e testaremos com %d elementos" % (len(treino_x), len(teste_x)))

modelo = LinearSVC()
modelo.fit(treino_x, treino_y)
previsoes = modelo.predict(teste_x)

acuracia = accuracy_score(teste_y, previsoes) * 100
print("A acurácia foi %.2f%%" % acuracia)
```

Lembre-se que, quando estamos explorando possibilidades e realizando testes, é normal utilizarmos os *notebooks* dessa maneira. Porém, quando queremos colocar um projeto em produção, a prática é criar arquivos Python específicos, com os módulos que fazem  sentido para aquela situação.

Vamos verificar rapidamente nosso código:  estamos realizando o `train_test_split()` para dividir os dados de teste e de treino, com o tamanho de `0.25` (`25%`) para o teste; estratificando esses dados de acordo com as classes; criando nosso modelo `LinearSVC`; treinando esse modelo; prevendo os resultados e testando a acurácia do estimador. 

Ao executamos nosso código, teremos o seguinte resultado: 

> Treinaremos com 1617 elementos e testaremos com 540 elementos
>
> A acurácia foi 54.26%

A acurácia não parece ser boa,  mas essa é a base de um algoritmo  para tentar prever resultados. Mas devemos nos perguntar: como saber se `54%` é de fato ruim? 

Para obtermos uma resposta satisfatória, precisamos de um recurso  comparativo. Portanto, inventaremos um algoritmo bem simples que terá  como previsão que todos os projetos são finalizados, isto é, os `540` elementos de teste serão `1`.

Com o Numpy, a biblioteca que gera matrizes,  faremos com que `540` elementos tenham o valor `1`. Feito isso, estipularemos que essas são as previsões, as chamaremos de `previsoes_do_guilherme`, e testaremos sua acurácia. 

```
import numpy as np
previsoes_do_guilherme = np.ones(540)
acuracia = accuracy_score(teste_y, previsoes_do_guilherme) * 100
print("A acurácia do Guilherme foi %.2f%%" % acuracia)
```

Na tela, receberemos: 

> A acurácia do Guilherme foi 52.59%

Ou seja, com esse algoritmo simples, tivemos um resultado de `52.59%` - um valor muito próximo ao do algoritmo anterior, com uma diferença de apenas `1,67%`. Isto é, ele é pouco melhor do que sempre prever `1`.

O que chamamos de `previsoes_do_guilherme`, na verdade é a linha de base, ou *baseline*, e é o parâmetro que devemos superar quando construímos estimadores.  Devemos ser muito melhores que as porcentagens apresentadas na *baseline*, e por isso é muito importante que ele exista ainda que teste sempre a  mesma classe.  Nas próximas aulas iremos analisar detalhadamente o que  erramos para termos um acurácia tão baixa.

#  Curva de decisão

Agora iremos analisar cada uma das classificações que realizamos. Para isso, copiaremos o mesmo código de `scatterplot()` que utilizamos anteriormente, mas, ao invés de `dados`, usaremos `teste_x`. Também precisaremos alterar o campo que serve de parâmetro para `hue` - no caso, `teste_y`. 

```
sns.scatterplot(x="horas_esperadas", y="preco", hue=teste_y, data=teste_x) 
```

Estamos passando um conjunto de dados para analisar as duas colunas, mas a cor está de acordo com outro conjunto. O `scatterplot()` suporta esse tipo de função. 

![gráfico de duas dimensões. O eixo X, contém valores de 0 a 100, e o eixo y contém valores de 0 a 25000. Há uma série de pontos distrubuídos em formato de uma curva ascendente ao longo do eixo X. Há pontos de cores diferentes](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_2_1_gr%C3%A1fico+array.png) 

Repare que, aparentemente, estamos seguindo o mesmo padrão de treino e teste. Então o que há de errado com o nosso teste?

Quando estamos trabalhando com duas dimensões (e somente nesses  casos), podemos colorir todos os pixels do gráfico de acordo com as  possíveis previsões do classificador que criamos. Por exemplo, será ou  não vendido um projeto na faixa de valor 25000 com tempo de construção  de 60h? 

O algoritmo deve ser capaz de aprender a curva ascendente que vemos  no gráfico: quanto mais bem pago e com maior tempo de elaboração, maior  chance do projeto ser de fato concluído. 

Existem diversas maneiras de realizarmos esse procedimento, mas são  maneiras de trabalhar com matrizes e que não necessariamente serão  executadas explicitamente no nosso dia-a-dia. Como estamos em um caso  bastante específico, iremos abordá-las apenas com duas dimensões, já que nos ajudarão a compreender com clareza qual é o problema do nosso  algoritmo.

Do conjunto `teste_x`, coletaremos o valor mínimo (`min()`) para `horas_esperadas`, e atribuiremos o nome `x_min` a esse valor. Faremos um procedimento similar para `x_max`, mas dessa vez coletando o valor máximo. Repetiremos essa estrutura para `teste_y`, mas agora trabalhando com `preco` mínimo e máximo. Por fim, imprimiremos o resultado.

```
x_min = teste_x.horas_esperadas.min()
x_max = teste_x.horas_esperadas.max()
y_min = teste_x.preco.min()
y_max = teste_x.preco.max()
print(x_min, x_max,y_min,y_max)
```

Na tela, teremos quatro valores - respectivamente, os mínimos e máximos de `x` e `y`. 

> 1 100 101 27738

O `x` está indo de `1` a `100`, enquanto `y` varia de `101` a `27738`. Agora definiremos quantos pixels haverá em cada dimensão; se  multiplicamos 100 x 100, isto é, 100 pixels para cada dimensão (altura e largura), teremos um total de 10.000 pontos, o que é o suficiente.

Entre `x_min` e `x_max`, cada espaço terá o tamanho máximo do gráfico dividido pelo total de pixels (`(x_max - x_min)/pixels`.

Por fim, adicionaremos o método `arange()` da biblioteca do Numpy.

```
pixels = 100
np.arange(x_min, x_max, (x_max - x_min)/pixels)
```

A executarmos o código, teremos o seguinte conteúdo impresso: 

```
array ([1. , 1.99, 2.98, 2.97, 4.96, 5.95, 6.94, 7.93, 8.92,
        9.91, 10.9, 11.89. 12.88, 13.87, 14.86, 15.85, 16.84, 17.83,
        18.82, 19.81, 20.8, 21.79, 22.78, 23.77, 24.76, 25.75, 26.74,
        27.73, 28.72, 29.71, 30.7, 31.69, 32.68, 33.67, 34.66, 35.65,
        36.64, 37.63, 38.62, 39,61, 40.6, 41.59, 42.58, 43.57, 44.56,
        45.55, 46.54, 47.53, 48.52, 49.51, 50.5, 51.49, 52.48, 53.47,
        54.46, 55.45, 56.44, 57.43, 58.42, 59.41, 60.4, 61.39, 62.38,
        63.37, 64.36, 65.35, 66.34, 67.33, 68.32, 69.31, 70.3, 71.29,
        72.28, 73.27, 74.26, 75.25, 76.24, 77.23, 78.22, 79.21, 80.2,
        81.19, 82.18, 83.17, 84.16, 85.15, 86.14, 87.13, 88.12, 89.11,
        90.1, 91.09, 92.08, 93.07, 94.06, 95.05, 96.04, 97.03, 98.02,
        99.01])
```

A partir de `1`, foram realizadas somas de `0.99` até chegar ao número`99.01`, dividindo proporcionalmente o espaço do `eixo_x` no gráfico. Faremos o mesmo procedimento para o `eixo_y`, aproveitando para nomear cada uma dessas operações:

```
pixels = 100
eixo_x = np.arange(x_min, x_max, (x_max - x_min)/ pixels)
eixo_y = np.arange(y_min, y_max, (y_max - y_min)/ pixels)
```

Agora a ideia é criarmos um *grid* entre os dois eixos, multiplicando as opções de acordo com as  diferentes possibilidade de custo de projeto e tempo de execução. Temos  na biblioteca do Numpy o `np.meshgrid()`, que consegue mesclar um *grid* . Esse método recebe como parâmetros o `eixo_x` e `eixo_y`, e devolve o conteúdo é `xx` e `yy`. Vamos analisar `xx`: 

```
xx, yy = np.meshgrid(eixo_x, eixo_y)
xx
```

Executando o código, será exibido na tela:

```
array([[1. , 1.99, 2.98, ..., 97.03, 98.02, 99.01],
      [1. , 1.99, 2.98, ..., 97.03, 98.02, 99.01],
      [1. , 1.99, 2.98, ..., 97.03, 98.02, 99.01],
      ...,
      [1. , 1.99, 2.98, ..., 97.03, 98.02, 99.01],
      [1. , 1.99, 2.98, ..., 97.03, 98.02, 99.01],
      [1. , 1.99, 2.98, ..., 97.03, 98.02, 99.01],
```

Com o código estruturado desta maneira, o que temos é a repetição do eixo x 100 vezes, e o mesmo ocorre com eixo y. Ou seja, ainda não mesclamos  realmente esses dados. Para fazermos isso, usaremos o `xx.ravel()`: 

```
xx, yy = np.meshgrid(eixo_x, eixo_y)
xx.ravel()
```

Esse método irá concatenar os elementos, como podemos verificar a seguir:

```
array ([1. , 1.99, 2.98, 2.97, 4.96, 5.95, 6.94, 7.93, 8.92,
        9.91, 10.9, 11.89. 12.88, 13.87, 14.86, 15.85, 16.84, 17.83,
        18.82, 19.81, 20.8, 21.79, 22.78, 23.77, 24.76, 25.75, 26.74,
        27.73, 28.72, 29.71, 30.7, 31.69, 32.68, 33.67, 34.66, 35.65,
        36.64, 37.63, 38.62, 39,61, 40.6, 41.59, 42.58, 43.57, 44.56,
        45.55, 46.54, 47.53, 48.52, 49.51, 50.5, 51.49, 52.48, 53.47,
        54.46, 55.45, 56.44, 57.43, 58.42, 59.41, 60.4, 61.39, 62.38,
        63.37, 64.36, 65.35, 66.34, 67.33, 68.32, 69.31, 70.3, 71.29,
        72.28, 73.27, 74.26, 75.25, 76.24, 77.23, 78.22, 79.21, 80.2,
        81.19, 82.18, 83.17, 84.16, 85.15, 86.14, 87.13, 88.12, 89.11,
        90.1, 91.09, 92.08, 93.07, 94.06, 95.05, 96.04, 97.03, 98.02,
        99.01, 1. , 1.99, 2.98, 2.97, 4.96, 5.95, 6.94, 7.93, 8.92,
        9.91,])
```

Faremos o mesmo procedimento para `yy`, e então concatenaremos `xx` e `yy` por meio de  `np.c_`. Essa operação nos devolverá os `pontos`.

Ao executarmos o código veremos a concatenação, com os devidos pares em cada eixo. 

```
xx, yy = np.meshgrid(eixo_x, eixo_y)
pontos = np.c_[xx.ravel(), yy.ravel()]
pontos
array([[1.000000e+00, 1.010000e+02],
       [1.990000e+00, 1.010000e+02],
       [2.980000e+00, 1.010000e+02],
       ...,
       [9.703000e+01, 2.746163e+04],
       [9.802000e+01, 2.746163e+04],
       [9.901000e+01, 2.746163e+04]])
```

Agora, nosso trabalho é, a partir do `modelo`, fazer as previsões (`predict()` para todos esses `pontos`. Como resultado, teremos uma série de números, que são as classificações. Chamaremos essas classificações de `Z`.

```
Z = modelo.predict(pontos)
```

Precisamos nos atentar para o formato (`shape`) do array, que é `10000`, mas se analisarmos `xx`, verificaremos que suas dimensões são `100, 100`: 

```
Z = modelo.predict(pontos)
Z.shape
```

>  10000

```
xx.shape
```

> (100, 100)

Precisamos redimensionar (`reshape`) o array de `10000` de acordo com `xx`.

```
Z = modelo.predict(pontos)
Z = Z.reshape(xx.shape)
Z
```

Com isso, teremos:

```
array([1, 1, 1, ..., 0, 0, 0],
      [1, 1, 1, ..., 1, 1, 1],
      [1, 1, 1, ..., 1, 1, 1],
     ...,
     [1, 1, 1, ..., 1, 1, 1],
     [1, 1, 1, ..., 1, 1, 1],
     [1, 1, 1, ..., 1, 1, 1]]),
```

Finalmente, poderemos plotar esses dados. Para isso, usaremos a biblioteca **Matplotlib**, cujas importações costumam ser `plt`. Novamente, queremos que os pontos sejam espalhados (`scatter()`), mas não usaremos o `scatterplot()` do Seaborn pois, nesse caso, queremos um controle mais refinado dessa plotagem.

O método `scatter()` deverá receber `teste_x.horas_esperadas` e `teste_x.preco`. Em seguida, definiremos a cor por meio do argumento`c=` recebendo `teste_y`. 

```
import matplotlib.pyplot as plt

plt.scatter(teste_x.horas_esperadas, teste_x.preco, c=teste_y)
```

Ao executarmos o código, veremos que os pontos que constituem nosso gráfico estão muito grandes. 

![gráfico de duas dimensões. O eixo X, contém valores de 0 a 100, e o eixo y contém valores de 0 a 25000. Há uma série de pontos distribuídos em formato de uma curva ascendente ao longo do eixo X. Há pontos de cores diferentes.](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_2_2_gr%C3%A1fico+pontos+grandes.png)

Para reduzirmos o tamanho dos pontos e melhorarmos a visualização dos dados, usaremos o parâmetro `s=1` (de *size*):

```
import matplotlib.pyplot as plt

plt.scatter(teste_x.horas_esperadas, teste_x.preco, c=teste_y, s=1)
```

Agora, teremos pontos menores:

![gráfico de duas dimensões. O eixo X, contém valores de 0 a 100, e o eixo y contém valores de 0 a 25000. Há uma série de pontos distribuídos em formato de uma curva ascendente ao longo do eixo X. Há pontos de cores diferentes e menores com relação ao gráfico anterior](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_2_5_pontos+menores.png)

Já temos o *plot* básico, nossa próxima tarefa é desenhar a classificação que estipulamos: se o valor for `0`, pintaremos de uma cor, se for `1`, de outra. Além disso, entre esses números, adicionaremos uma cor  diferente para que possamos visualizar a curva que separa as duas  classificações do algoritmo. 

Para tanto, usaremos `plt.contourf()` (de *countor*, ou "contorno"), que receberá `xx`, `yy` e `Z`. Para que a tonalidade não seja muito forte, escreveremos `alpha=0.3`, aumentando a transparência.

```
plt.contourf(xx, yy, Z, alpha=0.3)
plt.scatter(teste_x.horas_esperadas, teste_x.preco, c=teste_y, s=1)
```

Perceba que a curva de decisão do algoritmo quase não é visível, o que quer  dizer que os palpites para a finalização do projeto estão enviesados.

![gráfico de duas dimensões. O eixo X, contém valores de 0 a 100, e o eixo y contém valores de 0 a 25000. Há uma série de pontos distribuídos em formato de uma curva ascendente ao longo do eixo X. Há pontos de cores diferentes, e o plano de fundo  possui outra tonalidade que diverge dos pontos. Há uma quase imperceptível linha paralela ao eixo X](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_2_3_grafico+com+fundo+colorido.png) 

Nós poderíamos obter um novo resultado executando novamente o código  com alterações no SEED (a variância de ordenação), mas ainda assim  teríamos um resultado nada satisfatório. No caso abaixo, por exemplo (`SEED = 5`), a curva de decisão (*decision boundary*) mostra que todos os projetos receberam a classificação `0`.

![ gráfico em duas dimensões, que apresenta uma curva acendente que sai do ponto 10000 do eixo Y e termina no ponto 80 do eixo X.](https://s3.amazonaws.com/caelum-online-public/1033-ml-class-intro-pt/03/3_2_4_curva+de+decisao+todos+0.png)

Precisamos de algum modelo estimador que seja capaz de aprender um  padrão mais inteligente. O algoritmo que estamos utilizando só é capaz  de aprender uma linha reta, que não serve para classificação. É isso que faremos nas próximas aulas!