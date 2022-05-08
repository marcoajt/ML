# Lendo dados da internet e manipulando os mesmos

Neste aula, trabalharemos como uma outra [fonte de dados](https://gist.githubusercontent.com/guilhermesilveira/2d2efa37d66b6c84a722ea627a897ced/raw/10968b997d885cbded1c92938c7a9912ba41c615/tracking.csv) para nosso projeto. 

Nela, encontramos uma tabela com quatro colunas diferentes, e na qual cada item representa um usuário que acessou um determinado site. Neste site, temos três páginas diferentes: a principal (*home*); a *how it works*, que explica o funcionamento do site; e *contact*,  para entrar em contato.

Em cada uma dessas três colunas, que são as *features*, temos `1` ou `0`, indicando se um usuário específico acessou essa página ou não, respectivamente. Por enquanto não nos importa saber a ordem de páginas acessada pelo usuário. 

Também temos uma quarta coluna (*bought*) indicando se esse usuário comprou o produto (`1`) ou não (`0`). Abaixo temos uma parte da tabela para demonstração: 

| home | how_it_works | contact | bought |
| ---- | ------------ | ------- | ------ |
| 1    | 1            | 0       | 0      |
| 1    | 1            | 0       | 0      |
| 1    | 1            | 0       | 0      |
| 1    | 1            | 0       | 0      |
| 1    | 0            | 1       | 1      |
| 1    | 1            | 0       | 0      |

Clicando no botão *raw*, podemos acessar o formato padrão do arquivo, que apresenta o seguinte aspecto: 

```
home,how_it_works,contact,bought
1,1,0,0
1,1,0,0
1,1,0,0
1,1,0,0
1,1,1,1
1,1,0,0
```

Nesse formato os elementos são separados por vírgulas, cada linha representa um usuário e cada coluna uma das features ou a informação de que o usuário comprou ou não um produto. 

De volta ao Google Colab, selecionaremos "*File > NewPython 3 notebook*" para criar uma nova área de trabalho. Criado o novo arquivo, o chamaremos de `Introdução a Machine Learning Classificação - 2.ipynb`. 

Nós queremos ler o arquvo cru da tabela anterior (`.csv`). Para isso, usaremos a biblioteca **Pandas**. A maneira padrão de importar essa biblioteca é `import pandas as pd`. Em seguida, com `pd.read_csv(uri)`, faremos a leitura do arquivo `.csv`. Você pode copiar essa `uri` do código abaixo:

```
import pandas as pd

uri = "https://gist.githubusercontent.com/guilhermesilveira/2d2efa37d66b6c84a722ea627a897ced/raw/10968b997d885cbded1c92938c7a9912ba41c615/tracking.csv"
pd.read_csv(uri)
```

Ao pressionarmos "Shift + Enter", será exibida a tabela com os dados do arquivo cru. Neste caso, temos todos os dados disponíveis neste único arquivo, portanto usaremos a variável genérica `dados` para representar essas informações. Nosso objetivo inicial é imprimir somente as primeiras linhas da tabela, o que é feito com `head()`:

```
import pandas as pd

uri = "https://gist.githubusercontent.com/guilhermesilveira/2d2efa37d66b6c84a722ea627a897ced/raw/10968b997d885cbded1c92938c7a9912ba41c615/tracking.csv"
dados = pd.read_csv(uri)
dados.head()
```

Teremos a tabela organizada com os cinco itens: 

| home | how_it_works | contact | bought |
| ---- | ------------ | ------- | ------ |
| 1    | 1            | 0       | 0      |
| 1    | 1            | 0       | 0      |
| 1    | 1            | 0       | 0      |
| 1    | 1            | 0       | 0      |
| 1    | 0            | 1       | 0      |

Contudo, ao refletirmos sobre o modelo que estamos adotando, seria mais interessante que a coluna *bought* fosse separada das outras três, afinal ela corresponde no modelo da função ao **y\*, enquanto nossas features (home, *howitworks e contact) representam **x**. 

Para selecionarmos especificamente as três primeiras colunas, usaremos a sintaxe `dados[["home","how_it_works","contact"]]`. Como são várias colunas, precisamos passar esses conteúdos dentro de um array. Ao executarmos a código, teremos a tabela atualizada:

| home | how_it_works | contact |
| ---- | ------------ | ------- |
| 1    | 1            | 0       |
| 1    | 1            | 0       |
| 1    | 1            | 0       |
| 1    | 1            | 0       |
| 1    | 0            | 1       |

No código, chamaremos essas *features* de `x`. Para `y`, atriburemos `dados[["bought"]]`, ou seja, somente a coluna *bought*:

```
x= dados[["home","how_it_works","contact"]]
y= dados[["bought"]]
```

No caso de `y`, não é necessário utilizar o colchete extra (ou seja, `[[]]`), pois estamos extraindo apenas uma única coluna. Podemos imprimir somente a coluna "bought" utilizando `print(y)` ou `y.head()` (para exibir apenas os cinco primeiros itens dessa lista): 

| Name: bought, dtype: int64 |
| -------------------------- |
| 0                          |
| 0                          |
| 0                          |
| 0                          |
| 0                          |

Algumas vezes preferimos trabalhar com as features em português. Para modificar as nomeações em inglês que já estamos utilizando, usaremos uma funcionalidade do Pandas que possibilita a renomeação das colunas: `dados.rename(columns)`. Em seguida, passaremos `mapa`, um dicionário do Python em que declaramos qual será o nome das respectivas colunas: 

```
mapa = { 
    "home" : "principal",
    "how_it_works" : "como_funciona",
    "contact" : "contato",
    "bought" : "comprou"
}
dados.rename(columns = mapa)
```

Essa renomeção deve dialogar com as necessidades do seu projeto, já que talvez abreviações ou mesmo os nomes em inglês sejam mais interessantes dependendo de cada caso. Essa célula de código deve vir antes da atribuição de `x` e `y`. Além disso, essa alteração não altera a tabela original, portanto precisaremos atribuir `dados.rename()` para a variável `dados`:

```
mapa = { 
    "home" : "principal",
    "how_it_works" : "como_funciona",
    "contact" : "contato",
    "bought" : "comprou"
}
dados = dados.rename(columns = mapa)
```

Agora se executarmos novamente o código, veremos uma mensagem de erro: 

> KeyError:  "['home', 'how_it_works' e 'contact'] not in index"

Isso porque os termos em inglês, que correspondiam às colunas, não existem mais em nosso projeto. No momento em que carregamos o arquivo, a primeira ação a ser tomada é renomear os itens necessários para que o projeto seja executado: 

```
x= dados[["principal","como_funciona","contato"]]
y= dados[["comprou"]]

x.head()
```

Com `x.head()`, imprimiremos os primeiros cinco elementos da tabela para garantirmos que o código continua funcionando:

| principal | como_funciona | contato |
| --------- | ------------- | ------- |
| 1         | 1             | 0       |
| 1         | 1             | 0       |
| 1         | 1             | 0       |
| 1         | 1             | 0       |

Podemos fazer o mesmo procedimento com `y.head()`: 

| Name: comprou, dtype: int64 |
| --------------------------- |
| 0                           |
| 0                           |
| 0                           |
| 0                           |
| 0                           |

Agora que temos `x` e `y` separados, devemos nos questionar:  devemos treinar os algorítimos com todos os dados? Se fizermos isso, não teremos o que testar, pois dessa forma a máquina já terá as respostas corretas previamente, sendo incapaz de prever dados fora desse conjunto. De alguma maneira, **precisamos sempre separar os dados de treino e os de teste** para não enviesarmos os resultados produzidos. 

Primeiramente, com `dados.shape` verificaremos quantos elementos temos nos dados e o formato deles:

```
dados.shape
```

Veremos que em nosso arquivo há `99` linhas e `4` colunas. Separaremos em média 25% para testar o algorítimo, e o restante (cerca de 75% dos dados) para o treinamento. Portanto, para `treino_x`, coletaremos os primeiros 75 elementos (`treino_x = x[:75]`). Podemos utilizar `treino_x.shape` para verificar se o número de elementos está de fato correto: 

```
treino_x = x[:75]
treino_x.shape
```

Como resultado, teremos `75` elementos e `3` colunas. Da mesma forma, `treino_y` deve receber `y[:75]`. Já `teste_x` e  `teste_y` deverão se receber os dados a partir do elemento `75`, ou seja, `x[75:]` e `y[75:]`. 

Para confirmarmos se as matrizes estão com a quantidade correta de elementos `24`), acionaremos `teste_y.shape`. Por fim, registraremos essas informações imprimindo  o tamanho (`len()`) de `treino_x` e `treino_y` utilizando `print()`. 

```
treino_x = x[:75]
treino_y = y[:75]
teste_x = x[75:]
teste_y = y[75:]
teste_y.shape

print("Treinaremos com %d elementos e testaremos com %d elementos" % (len(treino_x), len(teste_x)))
```

Para treinarmos e executarmos o algorítimo usaremos a mesma motodologia do exemplo de porcos e cachorros; do `sklearn.svm` importaremos `LinearSVC`, e treinaremos o modelo com os dados `treino_x` e `treino_y`. 

```
from sklearn.svm import LinearSVC

modelo = LinearSVC()
modelo.fit(treino_x, treino_y)
```

Feito o treino, podemos fazer as `previsoes` baseadas no `teste_x`, passando `modelo.predict()`. Em seguida, compararemos as previsões com `teste_y`. Para isso, usaremos o `accuracy_score()` (sem nos esquecermos de importar esse método de `sklearn.metrics`), passando como argumentos `teste_y` e `previsoes`. 

Esse método deve nos retornar a acurácia, ou seja, a taxa de acertos do modelo. Neste momento, já podemos formatar o resultado como porcentagem e imprimi-lo na tela: 

```
from sklearn.svm import LinearSVC
from sklearn.metrics import accuracy_score

modelo = LinearSVC()
modelo.fit(treino_x, treino_y)
previsoes = modelo.predict(teste_x)

acuracia = accuracy_score(teste_y, previsoes) * 100
print("A acurácia foi %.2f%%" % acuracia)
```

Na tela, teremos:

> A acurácia foi 95.83%

Por meio desse exemplo, tivemos um contato mais realista com tratamentos de algorítimos de *machine learning*, uma vez que importamos a fonte de dados e realizamos os tratamentos necessários, renomeando colunas e separando os dados de treino e teste até que finalmente executamos o modelo. Ainda há elementos que podem ser melhorados, e é justamente isso que estudaremos a seguir.

# Dados da URI

Conforme foi dito na aula 2.1 sobre a URI, o código para visualizar os dados da URI está abaixo:

```
import pandas as pd 

uri='https://gist.githubusercontent.com/guilhermesilveira/2d2efa37d66b6c84a722ea627a897ced/raw/10968b997d885cbded1c92938c7a9912ba41c615/tracking.csv'

dados = pd.read_csv(uri)
```

# Para saber mais

Olá caros alunos(a),

Caso tenha vontade de saber mais sobre dicionário e tuplas, temos alguns artigos sobre no nosso blog.

[Trabalhando com dicionário](http://blog.alura.com.br/trabalhando-com-o-dicionario-no-python/)

[Conhecendo as tuplas no Python](http://blog.alura.com.br/conhecendo-as-tuplas-no-python/)

# Estratificando splits

Nas aulas anteriores, aprendemos que o testes para medir a acurácia são tão comuns que muitas das bibliotecas do Python já os realizam pra nós. 

Todas as vezes que trabalhamos com *machine learning* e analisamos dados, faremos algum tipo de separação desses dados para treino e teste, e também existem recursos disponíveis para isso nessas bibliotecas. 

No caso, usaremos novamente o **SkLearn**, que contém os elementos necessários para trabalharmos com *machine learning* e algorítimos similares. Ao fazermos uma pesquisa simples com os termos "sklearn train test",encontraremos  [a documentação](https://scikit-learn.org/stable/modules/generated/sklearn.model_selection.train_test_split.html) correspondente a prática de separar dados de treino e de teste, o que é chamado de *split*. 

Na máquina virtual do Python, importaremos `train_test_split` do `sklearn.model_selection`, e passaremos como argumento as duas matrizes (*arrays*), que são `x` e `y` inteiros. Em seguida, declararemos o tamanho do teste (`test_size`) que será `0.25`.

```
from sklearn.model_selection import train_test_split

train_test_split(x, y, test_size =0.25)
```

Ao executarmos o código, teremos como resposta os quatro valores que precisamos: `treino_x`, `teste_x_`, `treino_y`  e `teste_y`, nessa ordem: 

| principal | como_funciona | contato |
| --------- | ------------- | ------- |
| 1         | 1             | 1       |
| 1         | 1             | 1       |
| 1         | 1             | 0       |
| 1         | 0             | 1       |
| 1         | 1             | 0       |
| 1         | 1             | 0       |
| 0         | 0             | 1       |
| 1         | 1             | 0       |

Podemos imprimir `treino_x.shape` para verificarmos quantos elementos de fato temos nesse conjunto. O resultado será `74, 3`. Da mesma forma, se imprimirmos `teste_x.shape`, teremos como retorno `25, 3`. 

```
from sklearn.model_selection import train_test_split

treino_x, teste_x, treino_y, teste_y =train_test_split(x, y, test_size =0.25)
print(treino_x.shape)
print(teste_x.shape)
```

A soma dos dois conjuntos dá `99`, que é o número de itens na nossa tabela original. 

Com isso conseguimos rapidamente separar o treino do teste, sem precisarmos de outras linhas de código adicionais. Agora, incluiremos uma mensagem que acompanha o resultado, para que sua apresentação fique mais elegante, além do código do algorítimo sendo executado e o modelo sendo treinado com a impressão da acurácia. 

```
from sklearn.model_selection import train_test_split
from sklearn.svm import LinearSVC
from sklearn.metrics import accuracy_score

treino_x, teste_x, treino_y, teste_y = train_test_split(x, y, test_size = 0.25)
print("Treinaremos com %d elementos e testaremos com %d elementos" % (len(treino_x), len(teste_x)))

modelo = LinearSVC()
modelo.fit(treino_x, treino_y)
previsoes = modelo.predict(teste_x)

acuracia = accuracy_score(teste_y, previsoes) * 100
print("A acurácia foi %.2f%%" % acuracia)
```

Como retorno da execção desse código, teremos:

> Treinaremos com 74 elementos e testaremos com 25 elementos
>
> A acurácia foi 100.00%

Mas esse resultado é um pouco estranho, não? Após uma verificação, perceberemos que não parece existir nada de errado no código. Porém, o executarmos novamente, teremos resultados variáveis, como `96%`, `92%` e até `100%` novamente.

Isso ocorre porque o algorítimo `train_test_split`, por padrão, realiza aleatoriamente a separação de dados de treino e teste. Desse modo, todas as vezes que ele é executado podemos ter um resultado diferente. 

Então como podemos fazer com que o nosso experimento seja replicável? 

Precisaremos definir um número inicial para os algorítimos de geração de números aleatórios. Esse número inicial é chamado `SEED`, e nesse caso usaremos o número `20`. Em seguida, definiremos para o `train_test_split` o uso do `random_state = SEED`. 

```
from sklearn.model_selection import train_test_split
from sklearn.svm import LinearSVC
from sklearn.metrics import accuracy_score

SEED = 20

treino_x, teste_x, treino_y, teste_y = train_test_split(x, y, random_state = SEED, test_size = 0.25)
print("Treinaremos com %d elementos e testaremos com %d elementos" % (len(treino_x), len(teste_x)))

modelo = LinearSVC()
modelo.fit(treino_x, treino_y)
previsoes = modelo.predict(teste_x)

acuracia = accuracy_score(teste_y, previsoes) * 100
print("A acurácia foi %.2f%%" % acuracia)
```

Esse `SEED` será utilizado para definir a ordem dos números aleatórios (que deixam de ser tão aleatórios assim). Com isso, ao rodarmos novamente o código, teremos sempre o mesmo resultado: 

> Treinaremos com 74 elementos e testaremos com 25 elementos
>
> A acurácia foi 96%

**Esse é um processo muito importante, afinal faz com que o nosso teste seja replicável**.

Agora vamos analisar um último detalhe acerca de separação, e que envolve o `treino_y`. Neste conjunto, temos diversos `0` e `1`. Mas quantos? Descobriremos esse número com `value_counts()`:

```
treino_y.value_counts()
```

Como retorno, teremos:

> 0  47
>
> 1  27
>
> Name: comprou, dtype: int64

Ou seja, temos `47` pessoas que não compraram um produto no site, representadas por `0`, e `27` que compraram, representadas pelo número `1`. 

Já se formos analisar os dados de teste (`teste_y.value_counts()`), teremos o seguinte resultado: 

> 0  19
>
> 1  6
>
> Name: comprou, dtype: int64

Se dividirmos `47` por `27` teremos `1,74`. Portanto, para cada pessoa que comprou o produto, temos duas que não compraram. Já no teste temos `19` dividido por `6`, que totaliza `3,1` - três pessoas que não compraram para cada uma que comprou. 

Isso significa que a separação entre os dados de treino e teste não está proporcional de acordo com as  nossas categorias, o que é bastante arriscado. Por exemplo, se treinarmos apenas com pessoas que não compraram o produto, o algorítimo só saberá que pessoas não compram e esse será o seu palpite padrão pois ele nunca aprendeu que usuários de fato compram o produto. 

Portanto, é importante que a proporção dos nossos dados seja proporcional. Para isso,, inseriremos mais um argumento na separação de dados (`train_test_split`): o `stratify = y`, que irá estratificar os dados proporcionalmente de acordo com `y`. 

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

Com isso, nossa acurácia continuará a mesma: 

> Treinaremos com 74 elementos e testaremos com 25 elementos
>
> A acurácia foi 96.00%

Porém, se imprimirmos a contagem de valores de `treino_x` e `teste_y`, teremos a mesma proporção de 2-1.

```
treino_y.value_counts()
```

> 0    49
>
> 1    25
>
> Name: comprou, dtype: int64

```
teste_y.value_counts()
```

> 0    17
>
> 1     8
>
> Name: comprou, dtype: int64

Essa é uma técnica que utilizamos para manter a proporção na divisão dos dados. Contudo, se uma das classes aparecesse de forma muito pontual, não seria interessante esse recurso. Existem diversas maneiras de separarmos treino e teste, e essa é apenas uma delas - mas que foi suficiente para resolvermos nosso problema atual. A partir de agora resolveremos questões mais complexas.

# O que aprendemos?

Nessa aula aprendemos a:

- Abrir arquivo CSV;
- Imprimir as primeiras linhas com a função *head*;
- Renomear as colunas;
- Utilizar a função **shape** para ver a quantidade de elementos;
- Separar dados para treino e teste;
- Definir a ordem para os números aleatórios;
- Utilizar a função **value_counts**.