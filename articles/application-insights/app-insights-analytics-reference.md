<properties 
    pageTitle="Referência na análise na aplicação informações | Microsoft Azure" 
    description="Referência para as instruções no Analytics, a ferramenta de pesquisa avançada de informações da aplicação. " 
    services="application-insights" 
    documentationCenter=""
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/27/2016" 
    ms.author="awills"/>

# <a name="reference-for-analytics"></a>Referência para análise

[Análise](app-insights-analytics.md) é a funcionalidade de pesquisa avançada do [Informações de aplicação](app-insights-overview.md). Estas páginas descrevem a linguagem de consulta de análise.

> [AZURE.NOTE] [Unidade de teste Analytics nos nossos dados simulados](https://analytics.applicationinsights.io/demo) se a sua aplicação não está a enviar dados para informações de aplicação ainda.

## <a name="index"></a>Índice remissivo


**Permitir que** [Permitir que](#let-clause)


**Consultas e dos operadores** [contar](#count-operator)  |  [avaliar](#evaluate-operator) | [Expandir](#extend-operator) | [associação](#join-operator) | [limite](#limit-operator) | [mvexpand](#mvexpand-operator) | [Analisar](#parse-operator) | [projeto](#project-operator) | [ausente do projeto](#project-away-operator) | [intervalo](#range-operator) | [reduzir](#reduce-operator) | [Compor directiva](#render-directive) | [restringir cláusula](#restrict-clause) | [Ordenar](#sort-operator) | [Resumir](#summarize-operator) | [tomar](#take-operator) | [início](#top-operator) | [aninhada início](#top-nested-operator) | [União](#union-operator) | [onde](#where-operator) | [onde-in](#where-in-operator)

**Agregações** [qualquer](#any)  |  [argmax](#argmax) | [argmin](#argmin) | [avg](#avg) | [buildschema](#buildschema) | [contar](#count) | [contar.se](#countif) | [BDCONTAR](#dcount) | [dcountif](#dcountif) | [makelist](#makelist) | [makeset](#makeset) | [max](#max) | [mínimo](#min) | [percentil](#percentile) | [percentis](#percentiles) | [percentilesw](#percentilesw) | [percentilew](#percentilew) | [DESVPAD](#stdev) | [Soma](#sum) | [variância](#variance)

**Scalars** [Booleanos literais](#boolean-literals)  |  [Operadores booleanos](#boolean-operators) | [casting](#casts) | [comparações escalares](#scalar-comparisons) | [gettype](#gettype) | [hash](#hash) | [iff](#iff) | [isnotnull](#isnotnull) | [ÉNulo](#isnull) | [notnull](#notnull) | [toscalar](#toscalar)

**Números** [Operadores aritméticos](#arithmetic-operators)  |  [Literais numéricos](#numeric-literals) | [abs](#abs) | [posição](#bin) | [exp](#exp) | [piso](#floor) | [gama](#gamma) | [registo](#log) | [aleatório](#rand) | [RAIZQ](#sqrt) | [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

**Data e hora** [Data e hora expressões](#date-and-time-expressions)  |  [Data e hora literais](#date-and-time-literals) | [há](#ago) | [datepart](#datepart) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) | [dayofyear](#dayofyear) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth) | [getyear](#getyear) | [agora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

**Cadeia** [GUID](#guids)  |  [Encobertos cadeias literais](#obfuscated-string-literals) | [Cadeias literais](#string-literals) | [comparações de cadeias](#string-comparisons) | [countof](#countof) | [extrair](#extract) | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty)| [parseurl](#parseurl) | [Substituir](#replace) | [Dividir](#split) | [strcat](#strcat) | [strlen](#strlen) | [subcadeia](#substring) | [tolower](#tolower) | [toupper](#toupper)

**Matrizes, objetos e dinâmicos** [Matriz e objeto literais](#array-and-object-literals)  |  [Funções de objecto dinâmico](#dynamic-object-functions) | [objectos dinâmicos em cláusulas permitem-](#dynamic-objects-in-let-clauses) | [JSON caminho expressões](#json-path-expressions) | [nomes](#names) | [arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [intervalo](#range) | [todynamic](#todynamic) | [treepath](#treepath)



## <a name="let"></a>Permitir que

### <a name="let-clause"></a>permitir que a cláusula

**Tabela informe - atribuir nomes a uma tabela**

    let recentReqs = requests | where timestamp > ago(3d); 
    recentReqs | count

**Escalar, informe - atribuir nomes a um valor**

    let interval = 3d; 
    requests | where timestamp > ago(interval)

**Lambda informe - atribuir nomes a uma função**

    let Recent = 
       (interval:timespan) { requests | where timestamp > ago(interval) };
    Recent(3h) | count

    let us_date = (t:datetime) { strcat(getmonth(t),'/',dayofmonth(t),'/',getyear(t)) }; 
    requests | summarize count() by bin(timestamp, 1d) | project count_, day=us_date(timestamp)

Uma cláusula deixe que liga-se um [nome](#names) para uma tabela resultado, função ou valor escalar. A cláusula é um prefixo a uma consulta e o âmbito da ligação for essa consulta. (Permitem não fornece uma maneira às opções de nome que utiliza mais tarde na sessão.)

**Sintaxe**

    let name = scalar_constant_expression ; query

    let name = query ; query

    let name = (parameterName : type [, ...]) { plain_query }; query

    let name = (parameterName : type [, ...]) { scalar_expression }; query

* *tipo:* `bool`, `int`, `long`, `double`, `string`, `timespan`, `datetime`, `guid`,[`dynamic`](#dynamic-type)
* *plain_query:* Uma consulta não o prefixo por uma cláusula permitem.

**Exemplos**

    let rows = (n:long) { range steps from 1 to n step 1 };
    rows(10) | ...


Associação automática:

    let Recent = events | where timestamp > ago(7d);
    Recent | where name contains "session_started" 
  	| project start = timestamp, session_id
  	| join (Recent 
        | where name contains "session_ended" 
        | project stop = timestamp, session_id)
      on session_id
  	| extend duration = stop - start 


## <a name="queries-and-operators"></a>Consultas e dos operadores

Uma consulta ao longo do seu telemetria é constituída uma referência a uma sequência de origem, seguida de um pipeline de filtros. Por exemplo:


```AIQL
requests // The request table starts this pipeline.
| where client_City == "London" // filter the records
   and timestamp > ago(3d)
| count 
```
    
Cada filtro do carácter de pipe o prefixo `|` é uma instância de um *operador*, com algumas parâmetros. A entrada o operador é a tabela que é o resultado do pipeline de anterior. Na maioria dos casos, quaisquer parâmetros são [expressões escalares](#scalars) nas colunas de entrada. Em alguns casos, os parâmetros são os nomes das colunas de entrada e, em alguns casos, o parâmetro é uma segunda tabela. O resultado de uma consulta é sempre uma tabela, mesmo se só tiver uma coluna e uma linha.

Consultas podem conter quebras de linha única, mas são terminadas por uma linha em branco. Podem conter comentários entre `//` e de fim da linha.

Uma consulta pode ter o prefixo por uma ou mais [permitem cláusulas](#let-clause), que definem scalars, tabelas ou funções que podem ser utilizadas na consulta.

```AIQL

    let interval = 3d ;
    let city = "London" ;
    let req = (city:string) {
      requests
      | where client_City == city and timestamp > ago(interval) };
    req(city) | count
```

> `T`é utilizada em exemplos de consulta abaixo para assinalar a tabela de tubagem ou origem anterior.
> 

### <a name="count-operator"></a>operador de contagem

O `count` operador devolve o número de registos (linhas) num conjunto de registos entrado.

**Sintaxe**

    T | count

**Argumentos**

* *T*: os dados de tabela cujos registos devem ser contados.

**Devolve**

Esta função devolve uma tabela com um único registo e a coluna do tipo de `long`. O valor da célula só é o número de registos no *T*. 

**Exemplo**

```AIQL
requests | count
```

### <a name="evaluate-operator"></a>avaliar operador

`evaluate`é um dispositivo de extensão que lhe permite algoritmos especializados a ser anexado às consultas.

`evaluate`tem de ser o último operador no pipeline de consulta (exceto um possível `render`). Não tem de aparecer no corpo de uma função.

[avaliar autocluster](#evaluate-autocluster) | [avaliar cesto](#evaluate-basket) | [avaliar diffpatterns](#evaluate-diffpatterns) | [avaliar extractcolumns](#evaluate-extractcolumns)

#### <a name="evaluate-autocluster"></a>avaliar autocluster

     T | evaluate autocluster()

AutoCluster localiza comuns padrões de atributos descontínuos (dimensões) dos dados e irá reduzir os resultados da consulta original (se se trata de 100 ou 100 linhas k) para um pequeno número de padrões. AutoCluster foi desenvolvido para o ajudar a analisar falhas (por exemplo, exceções, causa uma falha), mas pode potencialmente trabalhar em qualquer conjunto de dados filtrado. 

**Sintaxe**

    T | evaluate autocluster( arguments )

**Devolve**

AutoCluster devolve um conjunto de padrões Capturar partes de dados com valores comuns partilhados através de vários atributos descontínuos (normalmente pequeno). Cada padrão é representado por uma linha nos resultados de. 

As duas primeiras colunas são a contar e a percentagem de linhas terminar a consulta original que são captadas pelo padrão. As colunas restantes são de consulta original e os respetivos valor é um valor específico da coluna ou ' *' que significa os valores de variáveis. 

Note que os padrões de não são distintas: poderão ser sobrepostos e, normalmente não abrangem todas as linhas originais. Algumas linhas não podem ser em qualquer padrão.

**Sugestões**

* Utilizar `where` e `project` no encaminhamento para reduzir os dados para apenas o que está interessado em Introdução.
* Quando encontrar uma linha interessante, poderá pretender pormenorizá-lo ainda mais ao adicionar os valores específicos para sua `where` filtro.

**Argumentos (todos os opcional)**

* `output=all | values | minimal` 

    O formato dos resultados. As colunas de contar e percentagem sempre são apresentados nos resultados de. 

 * `all`-todas as colunas da entrada são o resultado
 * `values`-filtros de colunas com apenas ' *' nos resultados de
 * `minimal`-Filtra também colunas que são idênticas para todas as linhas na consulta original. 


* `min_percent=`*dupla* (predefinido: 1)

    Cobertura percentagem mínima das linhas geradas.

    Exemplo:`T | evaluate autocluster("min_percent=5.5")`


* `num_seeds=`*int* (predefinido: 25) 

    O número de sementes determina o número de pontos de pesquisa local inicial do algoritmo de. Em alguns casos, dependendo da estrutura dos dados, aumentar o número de sementes aumenta o número (ou qualidade) dos resultados através do espaço de pesquisa um aumento de variação de consulta mais lento. O argumento num_seeds tem resultados em ambos os sentidos em diminuição para diminui-lo abaixo 5 atingir melhorias de desempenho pouco significativo e aumentar acima 50 raramente irá gerar padrões adicionais.

    Exemplo:`T | evaluate autocluster("num_seeds=50")`


* `size_weight=`*0 < duplo < 1*+ (predefinido: 0,5)

    Dá-lhe algumas controlo sobre o equilíbrio entre genérico (cobertura de alta) e informativos (muitos valores partilhados). Size_weight crescente normalmente reduz o número de padrões e cada padrão tem tendência para cobrir uma percentagem de maior. Diminuir size_weight normalmente gera padrões mais específicas com mais valores partilhados e cobertura de percentagem mais pequena. O em definições avançadas fórmula é uma média geométrica ponderada entre a pontuação genérica normalizada e pontuação informativa com size_weight e 1 size_weight como a espessura. 

    Exemplo:`T | evaluate autocluster("size_weight=0.8")`


* `weight_column=`*nome_coluna*

    Considerar cada linha na entrada de dados de acordo com a espessura especificada (por predefinição cada linha tem uma espessura de '1'), a utilização comum de uma coluna de espessura é atender a recolha de conta ou bucketing/agregação dos dados que já esteja incorporadas para cada linha.

    Exemplo:`T | evaluate autocluster("weight_column=sample_Count")` 



#### <a name="evaluate-basket"></a>avaliar cesto

     T | evaluate basket()

Cesto localiza todos os padrões de frequentes de atributos descontínuos (dimensões) dos dados e irá devolver todos os padrões de frequentes passou o limiar de frequência na consulta original. Cesto garante para encontrar todos os padrões de frequentes nos dados, mas não é garantia ter tempo de execução polinomial. O tempo de execução da consulta é linear o número de linhas, mas em alguns casos, poderá ser exponencial do número de colunas (dimensões). Cesto baseia o algoritmo de Apriori originalmente desenvolvido para extração de dados de análise de cesto. 

**Devolve**

Todos os padrões de a aparecer em mais do que uma fração especificada (predefinição 0.05) dos eventos.

**Argumentos (todos os opcional)**


* `threshold=`*0.015 < duplo < 1* (predefinido: 0,05) 

    Define o rácio das linhas sejam considerados frequente mínimo (não serão devolvidos padrões com rácio mais pequeno).

    Exemplo:`T | evaluate basket("threshold=0.02")`


* `weight_column=`*nome_coluna*

    Considerar cada linha na entrada de dados de acordo com a espessura especificada (por predefinição cada linha tem uma espessura de '1'), a utilização comum de uma coluna de espessura é atender a recolha de conta ou bucketing/agregação dos dados que já esteja incorporadas para cada linha.

    Exemplo: T | avaliar basket("weight_column=sample_Count")


* `max_dims=`*1 < int* (predefinição: 5)

    Define o número máximo de dimensões uncorrelated por cesto, limitado por predefinição para diminuir o tempo de execução de consulta.


* `output=minimize` | `all` 

    O formato dos resultados. As colunas de contar e percentagem sempre são apresentados nos resultados de.

 * `minimize`-filtros de colunas com apenas ' *' nos resultados de.
 * `all`-todas as colunas da entrada são o resultado.




#### <a name="evaluate-diffpatterns"></a>avaliar diffpatterns

     requests | evaluate diffpatterns("split=success")

Diffpatterns compara dois conjuntos de dados da mesma estrutura e localiza padrões de atributos descontínuos (dimensões) caracterizam diferenças entre dois conjuntos de dados. Diffpatterns foi desenvolvido para o ajudar a analisar falhas (por exemplo, falhas de comparação para que não sejam falhas num determinado período de tempo), mas pode potencialmente encontrar as diferenças entre os dois conjuntos de dados da mesma estrutura. 

**Sintaxe**

`T | evaluate diffpatterns("split=`*BinaryColumn*`" [, arguments] )`

**Devolve**

Diffpatterns devolve um conjunto (normalmente pequeno) de padrões capturar diferentes partes dos dados em dois conjuntos (ou seja, um padrão capturar uma grande percentagem das linhas do primeiro conjunto de dados e percentagem baixa das linhas no conjunto de segundo). Cada padrão é representado por uma linha nos resultados de.

As primeiras quatro colunas são a contar e a percentagem de linhas terminar a consulta original que são captadas por padrão em cada conjunto, quinta coluna é a diferença (em pontos de percentagem absoluta) entre os dois conjuntos. As colunas restantes são de consulta original e os respetivos valor é um valor específico da coluna ou * que significa os valores de variáveis. 

Note que os padrões de não são distintos: poderão ser sobrepostos e, normalmente não abrangem todas as linhas originais. Algumas linhas não podem ser em qualquer padrão.

**Sugestões**

* Utilize onde e do project no encaminhamento para reduzir os dados para apenas o que está interessado em Introdução.

* Quando encontrar uma linha interessante, poderá pretender pormenorizá-lo ainda mais ao adicionar os valores específicos para filtrar os seus onde.

**Argumentos**

* `split=`*nome da coluna* (obrigatório)

    A coluna tem de ter exatamente dois valores. Se for necessário, crie uma coluna deste tipo:

    `requests | extend fault = toint(resultCode) >= 500` <br/>
    `| evaluate diffpatterns("split=fault")`

* `target=`*cadeia*

    Indica o algoritmo para procurar apenas padrões que tenham percentagem mais alta no conjunto de dados de destino, o destino tem de ser um de dois valores da coluna dividido.

    `requests | evaluate diffpatterns("split=success", "target=false")`

* `threshold=`*0.015 < duplo < 1* (predefinido: 0,05) 

    Define a diferença de mínimas padrão (rácio) entre os dois conjuntos de.

    `requests | evaluate diffpatterns("split=success", "threshold=0.04")`

* `output=minimize | all`

    O formato dos resultados. As colunas de contar e percentagem sempre são apresentados nos resultados de. 

 * `minimize`-filtros de colunas com apenas ' *' nos resultados de
 * `all`-todas as colunas da entrada são o resultado

* `weight_column=`*nome_coluna*

    Considerar cada linha na entrada de dados de acordo com a espessura especificada (por predefinição cada linha tem uma espessura de '1'). Uma utilização comum de uma coluna de espessura é atender a recolha de conta ou bucketing/agregação dos dados que já esteja incorporadas para cada linha.

    `requests | evaluate autocluster("weight_column=itemCount")`






#### <a name="evaluate-extractcolumns"></a>avaliar extractcolumns

     exceptions | take 1000 | evaluate extractcolumns("details=json") 

Extractcolumns é utilizada para enriquecer uma tabela com várias colunas simples que são extraídas dinamicamente fora (semitransparente) estruturadas uma ou mais colunas com base no respetivo tipo. Atualmente suporta apenas, colunas json ambas as dinâmicos e serialização de jsons de cadeia.


* `max_columns=`*int* (predefinição: 10) 

    O número de colunas adicionadas novas for dinâmico e pode ser muito grande (realmente é o número de chaves distintos em todos os registos de json) para que recomendamos tem de limitá-lo. As novas colunas são ordenadas por ordem com base na sua frequência descendente e até max_columns são adicionadas à tabela.

    `T | evaluate extractcolumns("json_column_name=json", "max_columns=30")`


* `min_percent=`*dupla* (predefinido: 10.0) 

    Outra forma de limitar novas colunas ignorando colunas cuja frequência for inferior a min_percent.

    `T | evaluate extractcolumns("json_column_name=json", "min_percent=60")`


* `add_prefix=`*Booleano* (predefinido: VERDADEIRO) 

    Se for verdadeiro o nome da coluna complexa será adicionado como um prefixo para os nomes de colunas extraída.


* `prefix_delimiter=`*cadeia* (predefinido: "_") 

    Se add_prefix = true este parâmetro define o delimitador que será utilizado para concatenar os nomes das colunas de novos.

    `T | evaluate extractcolumns("json_column_name=json",` <br/>
    `"add_prefix=true", "prefix_delimiter=@")`


* `keep_original=`*Booleano* (predefinido: FALSO) 

    Se for verdadeiro as colunas (json) originais serão mantidas na tabela de resultados.


* `output=query | table` 

    O formato dos resultados. 

 * `table`-O resultado é a mesma tabela como é recebido menos as colunas entradas especificadas plus novas colunas foram extraídas as colunas entradas.
 * `query`-O resultado é uma cadeia que representa a consulta que tornaria para obter o resultado como tabela. 




### <a name="extend-operator"></a>expandir o operador

     T | extend duration = stopTime - startTime

Acrescente uma ou mais colunas calculadas numa tabela. 


**Sintaxe**

    T | extend ColumnName = Expression [, ...]

**Argumentos**

* *T:* A tabela de entrada.
* *ColumnName:* O nome de um colunas para adicionar. [Nomes](#names) estão entre maiúsculas e minúsculas e podem conter alfabéticos, numéricos ou de '_' carateres. Utilizar `['...']` ou `["..."]` para palavras-chave ou nomes com outros carateres da proposta.
* *Expressão:* Um cálculo nas colunas existentes.

**Devolve**

Uma cópia da tabela entrada, com as colunas adicionais especificadas.

**Sugestões**

* Utilizar [`project`](#project-operator) em vez disso, se também pretende largar ou mudar o nome algumas colunas.
* Não utilize `extend` simplesmente para obter um nome curto para utilizar numa expressão de tempo. `...| extend x = anonymous_user_id_from_client | ... func(x) ...` 

    As colunas da tabela nativas foram indexadas; o novo nome da define uma coluna adicional que não é indexada, pelo que a consulta é provável que lento.

**Exemplo**

```AIQL
traces
| extend
    Age = now() - timestamp
```


### <a name="join-operator"></a>operador de associação

    Table1 | join (Table2) on CommonColumn

Intercala as linhas das duas tabelas por valores correspondentes da coluna especificada.


**Sintaxe**

    Table1 | join [kind=Kind] (Table2) on CommonColumn [, ...]

**Argumentos**

* *Tabela1* - 'esquerda lado' da associação.
* *Tabela2* - 'lado direito' da associação. Pode ser uma expressão de consulta aninhada que devolve uma tabela.
* *CommonColumn* - uma coluna que tem o mesmo nome nas duas tabelas.
* *Tipo* - Especifica como linhas das duas tabelas estão deverá ser correspondido.

**Devolve**

Uma tabela com:

* Uma coluna para cada coluna em cada uma das duas tabelas, incluindo as teclas correspondentes. As colunas do lado direito nome serão automaticamente mudadas se existirem conflitos de nome.
* Uma linha para cada correspondência entre as tabelas de entrada. Uma correspondência é uma linha seleccionada de uma tabela que tem o mesmo valor para todos os `on` campos como uma linha na outra tabela. 

* `Kind`não for especificado

    Apenas uma linha a partir do lado esquerdo é correspondida para cada valor da `on` chave. O resultado contém uma linha para cada correspondência desta linha com linhas da direita.

* `Kind=inner`
 
     Existe uma linha no resultado para todas as combinações de linhas com correspondência a partir da esquerda e direita.

* `kind=leftouter`(or `kind=rightouter` or `kind=fullouter`)

     Para além das correspondências internas, existe uma linha para cada linha na esquerda (e/ou direita), mesmo não tem correspondência. Nesse caso, as células de saída não correspondentes contêm valores nulos.

* `kind=leftanti`

     Devolve todos os registos do lado esquerdo que não tenham correspondências da direita. A tabela de resultados tem apenas as colunas a partir do lado esquerdo. 
 
Se existirem várias linhas com os mesmos valores para esses campos, irá obter linhas para todas as combinações.

**Sugestões**

Para um melhor desempenho:

* Utilizar `where` e `project` para reduzir o número de linhas e colunas em tabelas de entrada, antes de `join`. 
* Se uma tabela sempre for menor que o outro, utilizá-la como (encaminhado) lado esquerdo da associação.
* As colunas para a correspondência de associação tem de ter o mesmo nome. Se for necessário para mudar o nome de uma coluna de uma das tabelas, utilize o operador de projeto.

**Exemplo**

Obter expandido atividades a partir de um registo em que alguns marcar entradas do início e de fim de uma atividade. 

```AIQL
    let Events = MyLogTable | where type=="Event" ;
    Events
  	| where Name == "Start"
  	| project Name, City, ActivityId, StartTime=timestamp
  	| join (Events
           | where Name == "Stop"
           | project StopTime=timestamp, ActivityId)
        on ActivityId
  	| project City, ActivityId, StartTime, StopTime, Duration, StopTime, StartTime

```


### <a name="limit-operator"></a>operador de limite

     T | limit 5

Devolve por excesso para o número especificado de linhas a partir da tabela de entrada. Não há garantias quais os registos que são devolvidos. (Para devolver os registos específicos, utilize [`top`](#top-operator).)

**Alias**`take`

**Sintaxe**

    T | limit NumberOfRows


**Sugestões**

`Take`é uma forma simple e eficiente para ver uma amostra dos resultados da quando estiver a trabalhar de forma interativa. Tenha em atenção que não garante para produzir linhas específicas ou para produzi-los numa ordem particular.

Existe um limite implícito no número de linhas devolvidas para o cliente, mesmo que não utilize `take`. Para levantar este limite, utilize o `notruncation` opção de pedido de cliente.



### <a name="mvexpand-operator"></a>operador de mvexpand

    T | mvexpand listColumn 

Expande-se uma lista a partir de uma célula para escreveu dinâmicas (JSON) para que cada entrada tem uma linha separada. Todas as outras células numa linha expandida são duplicadas. 

(Consulte também [`summarize makelist`](#summarize-operator) que executa a função oposta.)

**Exemplo**

Vamos assuma que a tabela de entrada é:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Olá"|{"chave": "valor"}|
|2|"world"|[0,1, "k", "v"]|

    mvexpand D

Resultado é:

|A:int|B:String|D:Dynamic|
|---|---|---|
|1|"Olá"|{"chave": "valor"}|
|2|"world"|0|
|2|"world"|1|
|2|"world"|"k"|
|2|"world"|"v"|


**Sintaxe**

    T | mvexpand  [bagexpansion=(bag | array)] ColumnName [limit Rowlimit]

    T | mvexpand  [bagexpansion=(bag | array)] [Name =] ArrayExpression [to typeof(Typename)] [limit Rowlimit]

**Argumentos**

* *ColumnName:* No resultado, matrizes na coluna com nome são expandidas para várias linhas. 
* *ArrayExpression:* Uma expressão que produzam uma matriz. Se este formulário é utilizado, é adicionada uma nova coluna e o existente é preservado.
* *Nome:* Um nome para a nova coluna.
* *NomeTipo:* Converte a expressão expandida para um tipo específico
* *RowLimit:* O número máximo de linhas gerado a partir de cada linha original. A predefinição é 128.

**Devolve**

Várias linhas para cada um dos valores em qualquer matriz na coluna com nome ou na expressão de matriz.

A coluna expandida tem sempre tipo dinâmico. Utilize um cast como `todatetime()` ou `toint()` se pretender calcular ou agregar valores.

Dois modos de expansões saco de propriedade são suportados:

* `bagexpansion=bag`: Matrizes de propriedades são expandidas para matrizes de propriedades de entrada individual. Esta é a expansão predefinido.
* `bagexpansion=array`: Se encontram expandidas no elemento de duas matrizes de propriedades `[` *chave*`,`*valor* `]` matriz estruturas de permissão do acesso uniforme ao chaves e valores (assim como, por exemplo, a executar uma agregação de contagem distinta sobre o nome de propriedade). 

**Exemplos**


    exceptions | take 1 
  	| mvexpand details[0]

Divide um registo de exceção em linhas de cada item no campo de detalhes.



### <a name="parse-operator"></a>analisar o operador

    T | parse "I got 2 socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long *


    T | parse kind=relaxed
          "I got no socks for my birthday when I was 63 years old" 
    with * "got" counter:long " " present "for" * "was" year:long * 

    T |  parse kind=regex "I got socks for my 63rd birthday" 
    with "(I|She) got" present "for .*?" year:long * 

Retira os valores de uma cadeia. Pode utilizar a correspondência de expressão simple ou normal.

**Sintaxe**

    T | parse [kind=regex|relaxed] SourceText 
        with [Match | Column [: Type [*]] ]  ...

**Argumentos**

* `T`: A tabela de entrada.
* `kind`: 
 * `simple`(predefinido): a `Match` cadeias são cadeias simples.
 * `relaxed`: se o texto não analisar como o tipo de uma coluna, a coluna é definida como nulo e a análise continua 
 * `regex`: o `Match` cadeias são expressões normais.
* `Text`: Uma coluna ou outros expressão que for avaliada como ou pode ser convertido para uma cadeia.
* *Correspondência:* Corresponder a parte da cadeia seguinte e rejeitá-lo.
* *Coluna:* Atribua a parte seguinte da cadeia a esta coluna. Se não existir, é criada a coluna.
* *Tipo:* Analisar a parte seguinte da cadeia de como o tipo especificado, tal como int, data, duplo. 


**Devolve**

A tabela de entrada, expandida de acordo com a lista de colunas.

Os elementos na `with` cláusula são comparados com o texto de origem sucessivamente. Cada elemento chews desativar um bloco de texto de origem: 

* Uma cadeia literal ou expressão regular move o cursor correspondente pela duração da correspondência.
* Em análise de regex, uma expressão regular pode utilizar o operador de minimização '?' para mover mais cedo possível para a correspondência seguinte.
* Nome de uma coluna com um tipo de analisa o texto como o tipo de especificado. A menos que tipo = média, uma análise ter êxito invalida correspondentes ao padrão de todo.
* Um nome de coluna sem um tipo de ou com o tipo 'cadeia', copia o número mínimo de caracteres para ir para a correspondência seguinte.
* ' *' Ignora o número mínimo de caracteres para ir para a correspondência seguinte. Pode utilizar '*' no início e no fim do padrão, ou depois de um tipo diferente de cadeia ou entre correspondências de cadeia.

Todos os elementos num padrão de análise tem de corresponder à corretamente; caso contrário, não vai ser produzido nenhum resultado. A exceção para esta regra é que, quando espécie = média, se uma variável escrita de análise falhar, os restantes a análise continua.

**Exemplos**

*Simples:*

```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse "I got 2 socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```

x | contador | apresentar | Ano
---|---|---|---
1 | 2 | SOCKS | 63

*Reduzida:*

Quando a entrada contém uma correspondência correta para cada coluna escrita, uma análise média produz os mesmos resultados como uma análise simple. Mas se uma das colunas escritas não analisar corretamente, uma análise média continua a processar o resto do padrão, Considerando que uma análise simple parar e deixar gerar qualquer resultado.


```AIQL

// Test without reading a table:
 range x from 1 to 1 step 1 
 | parse kind="relaxed"
        "I got several socks for my birthday when I was 63 years old" 
    with 
     *   // skip until next match
     "got" 
     counter: long // read a number
     " " // separate fields
     present // copy string up to next match
     "for" 
     *  // skip until next match
     "was" 
     year:long // parse number
     *  // skip rest of string
```


x  | apresentar | Ano
---|---|---
1 |  SOCKS | 63


*RegEx:*

```AIQL

// Run a test without reading a table:
range x from 1 to 1 step 1 
// Test string:
| extend s = "Event: NotifySliceRelease (resourceName=Scheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)" 
// Parse it:
| parse kind=regex s 
  with ".*?[a-zA-Z]*=" resource 
       ", total.*?sliceNumber=" slice:long *
       "lockTime=" lock
       ",.*?releaseTime=" release 
       ",.*?previousLockTime=" previous:date 
       ".*\\)"
| project-away x, s
```

recurso | setor | bloquear | lançamento | anterior
---|---|---|---|---
Programador | 16 | 17/02/2016 08:41:00 | 17/02/2016 08:41 | 2016-02-17T08:40:00Z

### <a name="project-operator"></a>operador de projeto

    T | project cost=price*quantity, price

Selecione as colunas para incluir, mudar o nome ou largar e inserir novas colunas calculadas. A ordem das colunas no resultado é especificada pela ordem os argumentos. Apenas as colunas especificadas nos argumentos estão incluídas no resultado: quaisquer outros na entrada de dados desaparecem.  (Consulte também `extend`.)


**Sintaxe**

    T | project ColumnName [= Expression] [, ...]

**Argumentos**

* *T:* A tabela de entrada.
* *ColumnName:* O nome de uma coluna que seja apresentado no resultado. Se não existirem sem *expressão*, uma coluna desse nome tem de aparecer na entrada de dados. [Nomes](#names) estão entre maiúsculas e minúsculas e podem conter alfabéticos, numéricos ou de '_' carateres. Utilizar `['...']` ou `["..."]` para palavras-chave ou nomes com outros carateres da proposta.
* *Expressão:* Expressão escalar opcional referenciar as colunas entradas. 

    É legal para devolver uma nova coluna calculada com o mesmo nome como uma coluna existente na entrada de dados.

**Devolve**

Uma tabela que tenha colunas nomeadas como argumentos e tantas linhas como sendo a tabela entrada.

**Exemplo**

O exemplo seguinte mostra os vários tipos de manipulações que podem ser executadas utilizando o `project` operador. A tabela de entrada `T` tem três colunas do tipo de `int`: `A`, `B`, e `C`. 

```AIQL
T
| project
    X=C,               // Rename column C to X
    A=2*B,             // Calculate a new column A from the old B
    C=strcat("-",tostring(C)), // Calculate a new column C from the old C
    B=2*B,              // Calculate a new column B from the old B
    ['where'] = client_City // rename, using a keyword as a column name
```

### <a name="project-away-operator"></a>operador de projeto ausente

    T | project-away column1, column2, ...

Exclua colunas especificadas. O resultado contém todas as colunas entradas, exceto aqueles que dê um nome.

### <a name="range-operator"></a>operador de intervalo

    range LastWeek from ago(7d) to now() step 1d

Gera uma tabela de uma coluna de valores. Repare que o mesmo não tem uma entrada de tubagem. 

|LastWeek|
|---|
|2015-12-05 09:10:04.627|
|09:10:04.627 2015-12 06|
|...|
|09:10:04.627 2015-12-12|



**Sintaxe**

    range ColumnName from Start to Stop step Step

**Argumentos**

* *ColumnName:* O nome da coluna única na tabela de resultados.
* *Iniciar:* O menor valor na saída.
* *Parar:* O valor mais alto a ser gerado na saída (ou um limite no valor mais alto, se os passos *passo* sobre este valor).
* *Passo:* A diferença entre dois valores consecutivas. 

Os argumentos têm de ser valores numéricos, de data ou de timespan. Estes não podem referenciar as colunas de uma tabela. (Se pretender calcular o intervalo baseado numa tabela de entrada, utilize a [ *função*de intervalo](#range), talvez com o [operador mvexpand](#mvexpand-operator).) 

**Devolve**

Uma tabela com uma única coluna denominada *ColumnName*, cujos valores são *Iniciar*, *comece* + *passo*,... até e incluindo *Parar*.

**Exemplo**  

```AIQL
range Steps from 1 to 8 step 3
```

Uma tabela com uma única coluna denominado `Steps` cujo tipo é `long` e cujos valores são `1`, `4`, e `7`.

**Exemplo**

    range LastWeek from bin(ago(7d),1d) to now() step 1d

Uma tabela de meia-noite nos últimos sete dias. A função de posição (Arred) reduz sempre para o início do dia.

**Exemplo**  

```AIQL
range timestamp from ago(4h) to now() step 1m
| join kind=fullouter
  (traces
      | where timestamp > ago(4h)
      | summarize Count=count() by bin(timestamp, 1m)
  ) on timestamp
| project Count=iff(isnull(Count), 0, Count), timestamp
| render timechart  
```

Mostra como o `range` operador pode ser utilizado para criar um pequeno, ad-hoc, tabela de dimensão, em seguida, é utilizada para apresentar zeros à esquerda onde os dados de origem não tem valores.

### <a name="reduce-operator"></a>reduzir o operador

    exceptions | reduce by outerMessage

Tenta agrupar registos semelhantes. Para cada grupo, o operador exporta o `Pattern` considera melhor descreva a esse grupo bem como a `Count` dos registos nesse grupo.


![](./media/app-insights-analytics-reference/reduce.png)

**Sintaxe**

    T | reduce by  ColumnName [ with threshold=Threshold ]

**Argumentos**

* *ColumnName:* A coluna examinar. Tem de ser do tipo de cadeia.
* *Limiar:* Um valor no intervalo {0..1}. A predefinição é 0,001. Para grandes entradas, limiar deve ser pequenas. 

**Devolve**

Duas colunas, `Pattern` e `Count`. Em muitos casos, o padrão será um valor completo da coluna. Em alguns casos, pode identificar termos comuns e substituir as partes variáveis com ' *'.

Por exemplo, o resultado da `reduce by city` podem incluir: 

|Padrão | Contar |
|---|---|
| Castelo * | 5182 |
| Santa * | 2846 |
| Moscovo | 3726 |
| \*-on-\* | 2730 |
| Faro | 27163 |


### <a name="render-directive"></a>compor Directiva

    T | render [ table | timechart  | barchart | piechart ]

Composição do indica a camada de apresentação como mostrar a tabela. Deverá ser o último elemento do pipe. É uma alternativa conveniente para utilizar os controlos no monitor, permitindo-lhe guardar uma consulta com um método de apresentação em particular.

### <a name="restrict-clause"></a>restringir cláusula 

Especifica o conjunto de nomes de tabelas disponíveis para os operadores que se seguem. Por exemplo:

    let e1 = requests | project name, client_City;
    let e2 =  requests | project name, success;
    // Exclude predefined tables from the union:
    restrict access to (e1, e2);
    union * |  take 10 

### <a name="sort-operator"></a>operador de ordenação 

    T | sort by country asc, price desc

Ordene as linhas da tabela entrada para ordem por uma ou mais colunas.

**Alias**`order`

**Sintaxe**

    T  | sort by Column [ asc | desc ] [ `,` ... ]

**Argumentos**

* *T:* A tabela de entrada para ordenar.
* *Coluna:* Coluna de *T* pelo qual pretende ordenar. Tem de ser o tipo dos valores numéricos, data, hora ou cadeia.
* `asc`Ordenar por para por ordem ascendente, baixa para alto. A predefinição é `desc`, descendente alto para baixo.

**Exemplo**

```AIQL
Traces
| where ActivityId == "479671d99b7b"
| sort by Timestamp asc
```
Todas as linhas de rastreios de tabela que têm um específico `ActivityId`, ordenados pelo respetivo carimbo de data/hora.

### <a name="summarize-operator"></a>resumir operador

Produz uma tabela que agrega o conteúdo da tabela entrada.
 
    requests
  	| summarize count(), avg(duration), makeset(client_City) 
      by client_CountryOrRegion

Uma tabela que mostra o número, a duração de pedido de média e o conjunto de cidades em cada país. Existe uma linha no resultado de cada país distintos. As colunas de saída mostram a contagem, média duração, cidades e país/região. Todas as outras colunas entradas são ignoradas.


    T | summarize count() by price_range=bin(price, 10.0)

Uma tabela que mostra o número de itens têm os preços em cada intervalo [0,10.0], [10.0,20.0], e assim sucessivamente. Este exemplo tem uma coluna para a contagem de e para o intervalo de preços. Todas as outras colunas entradas são ignoradas.


**Sintaxe**

    T | summarize
         [  [ Column = ] Aggregation [ `,` ... ] ]
         [ by
            [ Column = ] GroupExpression [ `,` ... ] ]

**Argumentos**

* *Coluna:* Nome opcional para uma coluna de resultado. Derivado predefinições a um nome de uma expressão. [Nomes](#names) estão entre maiúsculas e minúsculas e podem conter alfabéticos, numéricos ou de '_' carateres. Utilizar `['...']` ou `["..."]` para palavras-chave ou nomes com outros carateres da proposta.
* *Agregação:* Uma chamada para uma função de agregação, como `count()` ou `avg()`, com nomes das colunas como argumentos. Consulte o artigo [agregações](#aggregations).
* *GroupExpression:* Uma expressão nas colunas, que fornece um conjunto de valores distintos. Normalmente, é um de um nome de coluna que já fornece um conjunto de valores, restrito ou `bin()` com uma coluna numérica ou de hora como argumento. 

Se fornecer uma expressão numérica ou de tempo sem utilizar `bin()`, Analytics aplica automaticamente com um intervalo de `1h` para horas, ou `1.0` para números.

Se não fornecer uma *GroupExpression,* toda a tabela é resumida na linha único resultado.



**Devolve**

As linhas de entrada estão organizadas em grupos ter os mesmos valores da `by` expressões. Em seguida, as funções de agregação especificada são processadas sobre cada grupo, que produza uma linha para cada grupo. O resultado contém o `by` colunas e também pelo menos uma coluna para cada calculada agregado. (Regressar várias colunas de algumas funções de agregação.)

O resultado tem o número de linhas existem distintas combinações de `by` valores. Se pretende resumir sobre intervalos de valores numéricos, utilize `bin()` para reduzir intervalos descontínuos valores.

**Nota**

Apesar de pode fornecer arbitrários expressões de agregação e expressões de agrupamento, é mais eficiente para utilizar os nomes das colunas simples ou aplicar `bin()` a uma coluna numérica.



### <a name="take-operator"></a>tomar operador

Alias do [limite](#limit-operator)


### <a name="top-operator"></a>operador superior

    T | top 5 by Name desc nulls first

Devolve os registos de *N* primeiros ordenados por colunas especificadas.


**Sintaxe**

    T | top NumberOfRows by Sort_expression [ `asc` | `desc` ] [`nulls first`|`nulls last`] [, ... ]

**Argumentos**

* *NumberOfRows:* O número de linhas de *T* para devolver.
* *Sort_expression:* Uma expressão ao qual pretende ordenar as linhas. É normalmente apenas um nome de coluna. Pode especificar mais do que um sort_expression.
* `asc`ou `desc` (a predefinição) pode aparecer para controlar se seleção é realmente a partir da "parte inferior" ou "início" do intervalo.
* `nulls first`ou `nulls last` controlos onde valores nulos são apresentados. `First`é a predefinição para `asc`, `last` é a predefinição para `desc`.


**Sugestões**

`top 5 by name`é superficialmente equivalente a `sort by name | take 5`. No entanto, é executada mais rapidamente e devolve ordenado sempre os resultados, Considerando que `take` não garante essas.

### <a name="top-nested-operator"></a>as funções aninhadas início operador

    requests 
  	| top-nested 5 of name by count()  
    , top-nested 3 of performanceBucket by count() 
    , top-nested 3 of client_CountryOrRegion by count()
  	| render barchart 

Gera resultados hierárquicos, onde cada nível é uma desagregação a partir do nível anterior. É útil para responder a questões que o som, como "o que são os pedidos de 5 superiores e para cada um deles, quais são os registos de 3 desempenho superior e, para cada um deles, que são início 3 países/regiões os pedidos de vir de?"

**Sintaxe**

   T | as funções aninhadas início N da coluna pela agregação [,...]

**Argumentos**

* N:int - número de linhas a devolver ou passar para o próximo nível. Numa consulta com três níveis em que N é 3, 3 e 5, o número total de linhas será 45.
* COLUNA - uma coluna para agrupar por para agregação. 
* AGREGAÇÃO - uma [função de agregação](#aggregations) para aplicar a cada grupo de linhas. Os resultados destes agregações vai determinar os grupos superiores para que seja apresentado.


### <a name="union-operator"></a>operador de União

     Table1 | union Table2, Table3

Leva-o até duas ou mais tabelas e devolve as linhas das mesmas. 

**Sintaxe**

    T | union [ kind= inner | outer ] [ withsource = ColumnName ] Table2 [ , ...]  

    union [ kind= inner | outer ] [ withsource = ColumnName ] Table1, Table2 [ , ...]  

**Argumentos**

* *Tabela1*, *tabela2* ...
 *  O nome de uma tabela, tais como `requests`, ou uma tabela definido num [permitem cláusula](#let-clause); ou
 *  A consulta expressão, tais como`(requests | where success=="True")`
 *  Um conjunto de tabelas especificadas com um carácter universal. Por exemplo, `e*` seria formar a União de todas as tabelas definida na anterior cláusulas informe cujo nome começou com 'and', juntamente com a tabela 'exceções'.
* `kind`: 
 * `inner`-O resultado tem o subconjunto de colunas que são comuns a todas as tabelas de entrada.
 * `outer`-O resultado tem todas as colunas que ocorrem em qualquer uma das entradas. Células que não foram definidas por uma linha de entrada estão definidas para `null`.
* `withsource=`*ColumnName:* Se especificado, o resultado irá incluir uma coluna denominada *ColumnName* cujo valor indica qual a tabela origem contribuiu com determinadas cada linha.

**Devolve**

Uma tabela com linhas tantas vez que existem estão em todas as tabelas de entrada e o número de colunas como aí é nomes de colunas exclusivas nas entradas.

**Exemplo**

```AIQL

let ttrr = requests | where timestamp > ago(1h);
let ttee = exceptions | where timestamp > ago(1h);
union tt* | count
```
União de todas as tabelas cujos nomes começam "tt".


**Exemplo**

```AIQL

union withsource=SourceTable kind=outer Query, Command
| where Timestamp > ago(1d)
| summarize dcount(UserId)
```
O número de utilizadores distintos que tenham produzidos quer um `exceptions` evento ou um `traces` evento ao longo do dia passado. No resultado, a coluna 'SourceTable' irá indicar "Consulta de" ou "Comando".

```AIQL
exceptions
| where Timestamp > ago(1d)
| union withsource=SourceTable kind=outer 
   (Command | where Timestamp > ago(1d))
| summarize dcount(UserId)
```

Esta versão mais eficiente produz o mesmo resultado. Filtra cada tabela antes de criar a União.

### <a name="where-operator"></a>onde operador

     requests | where resultCode==200

Filtra uma tabela para o subconjunto de linhas que satisfaçam um predicado.

**Alias**`filter`

**Sintaxe**

    T | where Predicate

**Argumentos**

* *T:* A tabela Entrada cujos registos estão a ser filtrada.
* *Predicado:* A `boolean` [expressão](#boolean) nas colunas de *T*. É avaliado para cada linha de *T*.

**Devolve**

Linhas na *T* para o qual é o *predicado* `true`.

**Sugestões**

Para obter o desempenho mais rápido:

* **Comparações simples utilização** entre os nomes das colunas e constantes. ('Constante' significa que constante sobre a tabela - para `now()` e `ago()` estão bem e, por isso, são escalares atribuídos valores utilizando um [ `let` cláusula](#let-clause).)

    Por exemplo, prefere `where Timestamp >= ago(1d)` para `where floor(Timestamp, 1d) == ago(1d)`.

* **Termos de Simplest primeiro**: Se tiver várias cláusulas conjoined com `and`, colocar pela primeira vez cláusulas que envolvam apenas uma coluna. Por isso, `Timestamp > ago(1d) and OpId == EventId` é melhor do que a outra forma à volta.


**Exemplo**

```AIQL
traces
| where Timestamp > ago(1h)
    and Source == "Kuskus"
    and ActivityId == SubActivityIt 
```

Registos que estão não anteriores a 1 hora e vêm da origem denominada "Kuskus" e têm de duas colunas do mesmo valor. 

Repare que recomendamos colocar a comparação entre duas colunas por último, tal como não pode aproveitar o índice e força uma pesquisa.


### <a name="where-in-operator"></a>operador de onde

    requests | where resultCode !in (200, 201)

    requests | where resultCode in (403, 404)

**Sintaxe**

    T | where col in (expr1, expr2, ...)
    T | where col !in (expr1, expr2, ...)

**Argumentos**

* `col`: Uma coluna na tabela.
* `expr1`...: Uma lista de expressões escalares.

Utilizar `in` é utilizado para incluir apenas as linhas na qual `col` for igual a uma das expressões `expr1...`.

Utilizar `!in` para incluir apenas as linhas na qual `col` não é igual a qualquer uma das expressões `expr1...`.  


## <a name="aggregations"></a>Agregações

Agregações são funções utilizadas para combinar valores em grupos criados no [Resumir operação](#summarize-operator). Por exemplo, nesta consulta, dcount() é uma função de agregação:

    requests | summarize dcount(name) by success

### <a name="any"></a>qualquer 

    any(Expression)

Aleatoriamente seleciona uma linha do grupo e devolve o valor da expressão especificado.

Isto é útil, por exemplo, quando algumas coluna tem um grande número de valores idênticos (por exemplo, uma coluna de "texto de erro") e pretender dessa coluna, uma vez por um valor exclusivo da chave composta grupo de exemplo. 

**Exemplo**  

```

traces 
| where timestamp > now(-15min)  
| summarize count(), any(message) by operation_Name 
| top 10 by count_level desc 
```

<a name="argmin"></a>
<a name="argmax"></a>
### <a name="argmin-argmax"></a>argmin, argmax

    argmin(ExprToMinimize, * | ExprToReturn  [ , ... ] )
    argmax(ExprToMaximize, * | ExprToReturn  [ , ... ] ) 

Localiza uma linha no grupo que minimiza/maximises *ExprToMaximize*e devolve o valor de *ExprToReturn* (ou `*` para devolver toda a linha).

**Sugestão**: as colunas passou através de são automaticamente cujo nome foi mudadas. Para se certificar de que está a utilizar os nomes à direita, inspecionar os resultados através de `take 5` antes de encaminhar os resultados para outro operador.

**Exemplos**

Para cada nome pedido, mostrar quando ocorreu o pedido da célula mais longo:

    requests | summarize argmax(duration, timestamp) by name

Mostre todos os detalhes do pedido de célula mais longo, não apenas o carimbo de hora:

    requests | summarize argmax(duration, *) by name


Encontre o valor mais baixo de cada métrica, juntamente com o seu carimbo de data/hora e outros dados:

    metrics 
  	| summarize minValue=argmin(value, *) 
      by name


![](./media/app-insights-analytics-reference/argmin.png)
 


### <a name="avg"></a>AVG

    avg(Expression)

Calcula a média de *expressão* através do grupo.

### <a name="buildschema"></a>buildschema

    buildschema(DynamicExpression)

Devolve o esquema mínimo que o admita todos os valores de *DynamicExpression*. 

O tipo de coluna parâmetro deve ser `dynamic` -saco de uma matriz ou de propriedade. 

**Exemplo**

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Repare que `indexer` é utilizado para marcar onde deve utilizar um índice numérico. Para este esquema, algumas caminhos válidos seria ser (partindo do princípio que esses índices de exemplo estiverem no intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)

**Exemplo**

Vamos assuma que a coluna entrada tem três valores dinâmicos:

| |
|---|
|`{"x":1, "y":3.5}`
|`{"x":"somevalue", "z":[1, 2, 3]}`
|`{"y":{"w":"zzz"}, "t":["aa", "bb"], "z":["foo"]}`


Esquema de resultante seria:

    { 
      "x":["int", "string"], 
      "y":["double", {"w": "string"}], 
      "z":{"`indexer`": ["int", "string"]}, 
      "t":{"`indexer`": "string"} 
    }

Esquema de explica--nos que:

* O objeto de raiz é um contentor com as propriedades de quatro denominada x, y, z e t.
* A propriedade denominada "x" que pode ser do tipo "int" ou do tipo "cadeia".
* A propriedade denominado "y" que poderia de qualquer um dos tipo "double" ou outro contentor com uma propriedade denominado "w" tipo "cadeia".
* O ``indexer`` palavra-chave indica que "z" e "t" são matrizes.
* Cada item na matriz "z" é um int ou uma cadeia.
* "t" é uma matriz de cadeias.
* Todas as propriedades são implicitamente opcional e qualquer matriz pode estar vazia.

##### <a name="schema-model"></a>Modelo de esquema

A sintaxe do esquema devolvida é:

    Container ::= '{' Named-type* '}';
    Named-type ::= (name | '"`indexer`"') ':' Type;
    Type ::= Primitive-type | Union-type | Container;
    Union-type ::= '[' Type* ']';
    Primitive-type ::= "int" | "string" | ...;

São equivalentes a um subconjunto de anotações de tipo à máquina, codificado como um valor dinâmico. À máquina, o esquema de exemplo seria:

    var someobject: 
    { 
      x?: (number | string), 
      y?: (number | { w?: string}), 
      z?: { [n:number] : (int | string)},
      t?: { [n:number]: string } 
    }


### <a name="count"></a>contar

    count([ Predicate ])

Devolve uma contagem de linhas para os quais *predicado* devolver o valor `true`. Se for especificado nenhum *predicado* , devolve o número total de registos no grupo. 

**Sugestão de desempenho**: utilizar `summarize count(filter)` em vez de`where filter | summarize count()`

> [AZURE.NOTE] Evite utilizar count() para encontrar o número de pedidos de, exceções ou outros eventos que tenham ocorrido. Quando estiver [amostragem](app-insights-sampling.md) numa operação, o número de pontos de dados mantidas na aplicação informações será menor que o número de eventos originais. Em alternativa, utilize `summarize sum(itemCount)...`. A propriedade itemCount apresenta o número de eventos originais que são representados por cada ponto de dados guardada.

### <a name="countif"></a>função contar.Se

    countif(Predicate)

Devolve uma contagem de linhas para os quais *predicado* devolver o valor `true`.

**Sugestão de desempenho**: utilizar `summarize countif(filter)` em vez de`where filter | summarize count()`

> [AZURE.NOTE] Evite utilizar contar.se () para encontrar o número de pedidos de, exceções ou outros eventos que tenham ocorrido. Quando estiver [amostragem](app-insights-sampling.md) numa operação, o número de pontos de dados será menor que o número de eventos reais. Em alternativa, utilize `summarize sum(itemCount)...`. A propriedade itemCount apresenta o número de eventos originais que são representados por cada ponto de dados guardada.

### <a name="dcount"></a>BDCONTAR

    dcount( Expression [ ,  Accuracy ])

Devolve uma estimativa do número de valores distintos *expr* no grupo. (Para listar os valores distintos, utilize [`makeset`](#makeset).)

*Precisão*, se especificado, controla o equilíbrio entre velocidade e precisão.

 * `0`= cálculo pelo menos, preciso e rápido.
 * `1`o valor predefinido, que equilibra o tempo de precisão e cálculo; Acerca de 0,8% de erro.
 * `2`= mais precisão de cálculo e mais lento um cálculo; Acerca de 0,4% de erro.

**Exemplo**

    pageViews 
  	| summarize cities=dcount(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/dcount.png)


### <a name="dcountif"></a>dcountif

    dcountif( Expression, Predicate [ ,  Accuracy ])

Devolve uma estimativa do número de valores distintos *Expr* de linhas no grupo para o qual *predicado* é verdadeira. (Para listar os valores distintos, utilize [`makeset`](#makeset).)

*Precisão*, se especificado, controla o equilíbrio entre velocidade e precisão.

 * `0`= cálculo pelo menos, preciso e rápido.
 * `1`o valor predefinido, que equilibra o tempo de precisão e cálculo; Acerca de 0,8% de erro.
 * `2`= mais precisão de cálculo e mais lento um cálculo; Acerca de 0,4% de erro.

**Exemplo**

    pageViews 
  	| summarize cities=dcountif(client_City, client_City startswith "St") 
      by client_CountryOrRegion


### <a name="makelist"></a>makelist

    makelist(Expr [ ,  MaxListSize ] )

Devolve uma `dynamic` matriz (JSON) de todos os valores de *Expr* no grupo. 

* *MaxListSize* é um limite de número inteiro opcional o número máximo de elementos devolvido (a predefinição é *128*).

### <a name="makeset"></a>makeset

    makeset(Expression [ , MaxSetSize ] )

Devolve uma `dynamic` matriz (JSON) do conjunto de valores distintos que *Expr* leva no grupo. (Sugestão: para contar apenas valores distintos, utilize [`dcount`](#dcount).)
  
*  *MaxSetSize* é um limite de número inteiro opcional o número máximo de elementos devolvido (a predefinição é *128*).

**Exemplo**

    pageViews 
  	| summarize cities=makeset(client_City) 
      by client_CountryOrRegion

![](./media/app-insights-analytics-reference/makeset.png)

Consulte também o [ `mvexpand` operador](#mvexpand-operator) para a função oposta.


### <a name="max-min"></a>Máx, Mín

    max(Expr)

Calcula o número máximo de *Expr*.
    
    min(Expr)

Calcula o mínimo de *Expr*.

**Sugestão**: Isto dá-lhe o mínimo ou máximo na sua própria - por exemplo, o maior ou menor preço. Mas se pretender que as outras colunas na linha - por exemplo, o nome do fornecedor com o preço mais baixo - utilizar [argmin ou argmax](#argmin-argmax).


<a name="percentile"></a>
<a name="percentiles"></a>
<a name="percentilew"></a>
<a name="percentilesw"></a>
### <a name="percentile-percentiles-percentilew-percentilesw"></a>percentil, os percentis, percentilew, percentilesw

    percentile(Expression, Percentile)

Devolve uma estimativa para a *expressão* do percentil especificado no grupo. A precisão depende densidade da população na região do percentil.
    
    percentiles(Expression, Percentile1 [ , Percentile2 ...] )

Como `percentile()`, mas calcula um número de valores de percentil (que é mais rápido que calcular individualmente cada percentil).

    percentilew(Expression, WeightExpression, Percentile)

Percentil ponderada. Utilize esta opção para dados agregados previamente.  `WeightExpression`é um número inteiro que indica o número de linhas originais são representadas por cada linha agregada.

    percentilesw(Expression, WeightExpression, Percentile1, [, Percentile2 ...])

Como `percentilew()`, mas calcula um número de valores de percentil.

**Exemplos**


O valor de `duration` maior do que 95% da amostra definida e mais pequena do que 5% do conjunto de exemplo, o método de cálculo para cada nome de pedido:

    request 
  	| summarize percentile(duration, 95)
      by name

Omitir "ao..." para calcular o para a tabela inteira.

Em simultâneo calcule os percentis várias para nomes pedido diferente:

    
    requests 
  	| summarize 
        percentiles(duration, 5, 20, 50, 80, 95) 
      by name

![](./media/app-insights-analytics-reference/percentiles.png)

Os resultados mostram que para /Events/Index o pedido, 5% de pedidos de são respondeu em menos de 2.44s, metade-las no 3.52s, e 5% são mais lento do que 6.85s.

Calcule estatísticas várias:

    requests 
  	| summarize 
        count(), 
        avg(Duration),
        percentiles(Duration, 5, 50, 95)
      by name

#### <a name="weighted-percentiles"></a>Percentis ponderados

Utilize as funções de percentil ponderada nos casos onde os dados tem sido previamente agregados. 

Por exemplo, suponha que executa a sua aplicação muitos milhares de operações por segundo e que pretender saber os respetivos latência. A solução simple seria gerar um pedido de informações da aplicação ou evento personalizado para cada operação. Isto pretende criar muitas tráfego, embora amostragem ajustável seria entre em vigor para reduzi-lo. Mas decidiu implementar uma solução ainda melhor: escreverá algum código na sua aplicação para agregar os dados antes de o enviar para informações de aplicação. O resumo agregado será enviado a intervalos regulares, reduzir a taxa de dados, talvez a alguns pontos por minuto.

O código leva-o até uma sequência de medidas de latência em milissegundos. Por exemplo:
    
     { 15, 12, 2, 21, 2, 5, 35, 7, 12, 22, 1, 15, 18, 12, 26, 7 }

Conta-as medidas nas posições de seguintes:`{ 10, 20, 30, 40, 50, 100 }`

Periodicamente, faz uma série de chamadas TrackEvent, uma para cada balde, com as medidas personalizadas em cada chamada: 

    foreach (var latency in bins.Keys)
    { telemetry.TrackEvent("latency", null, 
         new Dictionary<string, double>
         ({"latency", latency}, {"opCount", bins[latency]}}); }

Em análise, verá um desse grupo de eventos da seguinte forma:

`opCount` | `latency`| significado
---|---|---
8 | 10 | = 8 operações na Reciclagem 10ms
6 | 20 | = 6 operações na Reciclagem 20ms
3 | 30 | = 3 operações na Reciclagem 30ms
1 | 40 | = operações 1 na posição de 40 MS

Para obter uma imagem precisa da distribuição original do latências de evento, utilizamos `percentilesw`:

    customEvents | summarize percentilesw(latency, opCount, 20, 50, 80)

Os resultados são os mesmos como se tinha utilizámos simples `percentiles` no conjunto original de medidas.

> [AZURE.NOTE] Os percentis ponderados não são aplicáveis a [amostra de dados](app-insights-sampling.md), em que cada linha de amostra representa uma amostra aleatória de originais linhas em vez de uma reciclagem. As funções de percentil simples são adequadas para os dados recolhidos.

#### <a name="estimation-error-in-percentiles"></a>Erro de estimativa no percentis

A função de agregação percentis fornece um valor aproximado utilizando [T resumo](https://github.com/tdunning/t-digest/blob/master/docs/t-digest-paper/histo.pdf). 

Alguns pontos importantes: 

* Os limites o erro estimativa variam com o valor do percentil pedido. A melhor precisão é nas extremidades dos [0..100] dimensionar os percentis 0 e 100 são o exato valores mínimos e máximos da distribuição. A precisão gradualmente diminui no meio da escala. É pior na mediana e está limitado a 1%. 
* Limites de erro são observados na classificação e não no valor. Imaginemos percentil (X, 50) devolvido o valor de Xm. A estimativa garante que pelo menos 49% e no máximo 51% dos valores de X são inferiores Xm. Não existe nenhum limite teórica a diferença entre Xm e real valor médio de X.

### <a name="stdev"></a>DESVPAD

     stdev(Expr)

Devolve o desvio-padrão de *Expr* ao longo do grupo.

### <a name="variance"></a>variância

    variance(Expr)

Devolve a variância da *Expr* ao longo do grupo.

### <a name="sum"></a>soma

    sum(Expr)

Devolve a soma dos *Expr* ao longo do grupo.                      


## <a name="scalars"></a>Scalars

[casting](#casts) | [comparações](#scalar-comparisons)
<br/>
[GETTYPE](#gettype) | [hash](#hash) | [iff](#iff) |  [ÉNulo](#isnull) | [isnotnull](#isnotnull) | [notnull](#notnull) | [toscalar](#toscalar)

Os tipos de suportados são:

| Tipo      | Nomes dos adicionais   | Tipo de .NET equivalente |
| --------- | -------------------- | -------------------- |
| `bool`    | `boolean`            | `System.Boolean`     |
| `datetime`| `date`               | `System.DateTime`    |
| `dynamic` |                      | `System.Object`      |
| `guid`    | `uuid`, `uniqueid`   | `System.Guid`        |
| `int`     |                      | `System.Int32`       |
| `long`    |                      | `System.Int64`       |
| `double`  | `real`               | `System.Double`      |
| `string`  |                      | `System.String`      |
| `timespan`| `time`               | `System.TimeSpan`    |

### <a name="casts"></a>Casting

Pode haver de um tipo para outro. Em geral, se a conversão faz sentido, irá funcionar:

    todouble(10), todouble("10.6")
    toint(10.6) == 11
    floor(10.6) == 10
    toint("200")
    todatetime("2016-04-28 13:02")
    totimespan("1.5d"), totimespan("1.12:00:00")
    toguid("00000000-0000-0000-0000-000000000000")
    tostring(42.5)
    todynamic("{a:10, b:20}")

Verificar se uma cadeia pode ser convertida num tipo específico:

    iff(notnull(todouble(customDimensions.myValue)),
       ..., ...)

### <a name="scalar-comparisons"></a>Comparações escalares

||
---|---
`<` |Menos
`<=`|Menor ou igual a
`>` |Maior
`>=`|Maior ou igual a
`<>`|Não é igual a
`!=`|Não é igual a 
`in`| Operando à direita é uma matriz (dinâmica) e operando esquerdo é igual a um dos seus elementos.
`!in`| Operando à direita é uma matriz (dinâmica) e não é igual a qualquer um dos seus elementos operando da esquerda.




### <a name="gettype"></a>GETTYPE

**Devolve**

Uma cadeia que representa o tipo de armazenamento subjacente do respetivo argumento único. Isto é particularmente útil quando tem valores de tipo `dynamic`: neste caso `gettype()` revelarem como um valor é codificado.

**Exemplos**

|||
---|---
`gettype("a")` |`"string" `
`gettype(111)` |`"long" `
`gettype(1==1)` |`"int8"`
`gettype(now())` |`"datetime" `
`gettype(1s)` |`"timespan" `
`gettype(parsejson('1'))` |`"int" `
`gettype(parsejson(' "abc" '))` |`"string" `
`gettype(parsejson(' {"abc":1} '))` |`"dictionary"` 
`gettype(parsejson(' [1, 2, 3] '))` |`"array"` 
`gettype(123.45)` |`"real" `
`gettype(guid(12e8b78d-55b4-46ae-b068-26d7a0080254))` |`"guid"` 
`gettype(parsejson(''))` |`"null"`
`gettype(1.2)==real` | `true`

### <a name="hash"></a>hash

**Sintaxe**

    hash(source [, mod])

**Argumentos**

* *origem*: A origem escalar o hash é calculado no.
* *resto*: O módulo valor a ser aplicado no resultado de hash.

**Devolve**

O valor de xxhash (longo) da determinado escalar, módulo do valor determinado mod (se especificado).

**Exemplos**

```
hash("World")                   // 1846988464401551951
hash("World", 100)              // 51 (1846988464401551951 % 100)
hash(datetime("2015-01-01"))    // 1380966698541616202
```
### <a name="iff"></a>IFF

O `iff()` função avalia o primeiro argumento (o predicado) e devolve o valor de argumentos a segunda ou terceira dependendo se o predicado é `true` ou `false`. Os argumentos de segundo e terceiros tem de ser do mesmo tipo.

**Sintaxe**

    iff(predicate, ifTrue, ifFalse)


**Argumentos**

* *predicado:* Uma expressão avaliada como um `boolean` valor.
* *ifTrue:* Uma expressão que é avaliada e o seu valor devolvido da função se *predicado* avaliado para `true`.
* *ifFalse:* Uma expressão que é avaliada e o seu valor devolvido da função se *predicado* avaliado para `false`.

**Devolve**

Esta função devolve o valor de *ifTrue* se *predicado* devolver o valor `true`, ou o valor de *ifFalse* contrário.

**Exemplo**

```
iff(floor(timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```

<a name="isnull"/></a>
<a name="isnotnull"/></a>
<a name="notnull"/></a>
### <a name="isnull-isnotnull-notnull"></a>ÉNulo isnotnull, notnull

    isnull(parsejson("")) == true

Leva-o até um único argumento e indica se é nulo.

**Sintaxe**


    isnull([value])


    isnotnull([value])


    notnull([value])  // alias for isnotnull

**Devolve**

TRUE ou false dependendo de se o valor é nulo ou não é nulo.


|x|IsNull(x)
|---|---
| "" | FALSO
|"x" | FALSO
|parsejson("")|VERDADEIRO
|parsejson("[]")|FALSO
|parsejson("{}")|FALSO

**Exemplo**

    T | where isnotnull(PossiblyNull) | count

Repare que existem outras formas de atingir este efeito:

    T | summarize count(PossiblyNull)

### <a name="toscalar"></a>toscalar

Avalia uma consulta ou uma expressão e devolve o resultado que um único valor. Esta função é útil para cálculos faseados; Por exemplo, a calcular uma contagem total de eventos e, em seguida, utilizar esse argumento como um plano base.

**Sintaxe**

    toscalar(query)
    toscalar(scalar)

**Devolve**

O argumento avaliado. Se o argumento for uma tabela, devolve a primeira coluna da primeira linha. (É aconselhável para dispor a que o argumento tem apenas uma coluna e linha.)

**Exemplo**

```AIQL

    // Get the count of requests 5 days ago:
    let baseline = toscalar(requests  
        | where floor(timestamp, 1d) == floor(ago(5d),1d) | count);
    // List the counts relative to that baseline:
    requests | summarize daycount = count() by floor(timestamp, 1d)  
  	| extend relative = daycount - baseline
```




### <a name="boolean-literals"></a>Booleanos literais

    true == 1
    false == 0
    gettype(true) == "int8"
    typeof(bool) == typeof(int8)

### <a name="boolean-operators"></a>Operadores booleanos

    and 
    or 

    

## <a name="numbers"></a>Números

[abs](#abs) | [bin](#bin) | [exp](#exp) | [floor](#floor) | [gamma](#gamma) |[log](#log) | [rand](#rand) | [range](#range) | [sqrt](#sqrt) 
| [todouble](#todouble) | [toint](#toint) | [tolong](#tolong)

### <a name="numeric-literals"></a>Literais numéricos

|||
|---|---
|`42`|`long`
|`42.0`|`real`

### <a name="arithmetic-operators"></a>Operadores aritméticos

|| |
|---|-------------|
| + | Adicionar         |
| - | Subtrair    |
| * | Multiplicar    |
| / | Dividir      |
| % | Módulo      |
||
|`<` |Menos
|`<=`|Menor ou igual a
|`>` |Maior
|`>=`|Maior ou igual a
|`<>`|Não é igual a
|`!=`|Não é igual a 


### <a name="abs"></a>Abs

**Sintaxe**

    abs(x)

**Argumentos**

* x - um número inteiro, real ou timespan

**Devolve**

    iff(x>0, x, -x)

<a name="bin"></a><a name="floor"></a>
### <a name="bin-floor"></a>posição, piso

Arredonda valores para baixo para um número inteiro múltiplo de tamanho de uma determinada posição. Muitas utilizados na [`summarize by`](#summarize-operator) consulta. Se tiver um conjunto de valores difundido, estes serão agrupados num conjunto mais pequeno de valores específicos.

Alias `floor`.

**Sintaxe**

     bin(value, roundTo)
     floor(value, roundTo)

**Argumentos**

* *valor:* Um número, data ou timespan. 
* *roundTo:* O "tamanho posição". Um número, data ou timespan divide *valor*. 

**Devolve**

O múltiplo mais próximo *roundTo* abaixo do *valor*.  
 
    (toint((value/roundTo)-0.5)) * roundTo

**Exemplos**

Expressão | Resultado
---|---
`bin(4.5, 1)` | `4.0`
`bin(time(16d), 7d)` | `14d`
`bin(datetime(1953-04-15 22:25:07), 1d)`|  `datetime(1953-04-15)`


A seguinte expressão calcula um histograma das durações das, com um tamanho de balde de 1 segundo:

```AIQL

    T | summarize Hits=count() by bin(Duration, 1s)
```

### <a name="exp"></a>EXP

    exp(v)   // e raised to the power v
    exp2(v)  // 2 raised to the power v
    exp10(v) // 10 raised to the power v


### <a name="floor"></a>piso

Um alias para [`bin()`](#bin).

### <a name="gamma"></a>gama

A [função gama](https://en.wikipedia.org/wiki/Gamma_function)

**Sintaxe**

    gamma(x)

**Argumentos**

* *x:* Um número real

Para números inteiros positivos, `gamma(x) == (x-1)!` por exemplo, `gamma(5) == 4 * 3 * 2 * 1`.

Consulte também [loggamma](#loggamma).


### <a name="log"></a>registo

    log(v)    // Natural logarithm of v
    log2(v)   // Logarithm base 2 of v
    log10(v)  // Logarithm base 10 of v


`v`deve ser um número real > 0. Caso contrário, é devolvido nulo.

### <a name="loggamma"></a>loggamma


O logaritmo natural do valor absoluto da [função gama](#gamma).

**Sintaxe**

    loggamma(x)

**Argumentos**

* *x:* Um número real


### <a name="rand"></a>aleatório

Gerador de números aleatórios.

* `rand()`-um número real entre 0,0 e 1,0
* `rand(n)`-um número inteiro entre 0 e n-1




### <a name="sqrt"></a>RAIZQ

A função de raiz quadrada.  

**Sintaxe**

    sqrt(x)

**Argumentos**

* *x:* Um número real > = 0.

**Devolve**

* Um número positivo dessa`sqrt(x) * sqrt(x) == x`
* `null`Se o argumento for negativo ou não é possível converter uma `real` valor. 




### <a name="toint"></a>toint

    toint(100)        // cast from long
    toint(20.7) == 21 // nearest int from double
    toint(20.4) == 20 // nearest int from double
    toint("  123  ")  // parse string
    toint(a[0])       // cast from dynamic
    toint(b.c)        // cast from dynamic

### <a name="tolong"></a>tolong

    tolong(20.7) == 21 // conversion from double
    tolong(20.4) == 20 // conversion from double
    tolong("  123  ")  // parse string
    tolong(a[0])       // cast from dynamic
    tolong(b.c)        // cast from dynamic


### <a name="todouble"></a>todouble

    todouble(20) == 20.0 // conversion from long or int
    todouble(" 12.34 ")  // parse string
    todouble(a[0])       // cast from dynamic
    todouble(b.c)        // cast from dynamic



## <a name="date-and-time"></a>Data e hora


[há](#ago) | [dayofmonth](#dayofmonth) | [dayofweek](#dayofweek) |  [dayofyear](#dayofyear) |[datepart](#datepart) | [endofday](#endofday) | [endofmonth](#endofmonth) | [endofweek](#endofweek) | [endofyear](#endofyear) | [getmonth](#getmonth)|  [getyear](#getyear) | [agora](#now) | [startofday](#startofday) | [startofmonth](#startofmonth) | [startofweek](#startofweek) | [startofyear](#startofyear) | [todatetime](#todatetime) | [totimespan](#totimespan) | [weekofyear](#weekofyear)

### <a name="date-and-time-literals"></a>Data e hora literais

|||
---|---
**data/hora**|
`datetime("2015-12-31 23:59:59.9")`<br/>`datetime("2015-12-31")`|Horas são sempre no UTC. Por omitir a data dá uma hora para hoje.
`now()`|A hora atual.
`now(`-*TimeSpan*`)`|`now()-`*TimeSpan*
`ago(`*TimeSpan*`)`|`now()-`*TimeSpan*
**TimeSpan**|
`2d`|2 dias
`1.5h`|hora 1,5 
`30m`|30 minutos
`10s`|10 segundos
`0.1s`|segundo 0.1
`100ms`| milissegundos 100
`10microsecond`|
`1tick`|100ns
`time("15 seconds")`|
`time("2")`| 2 dias
`time("0.12:34:56.7")`|`0d+12h+34m+56.7s`

### <a name="date-and-time-expressions"></a>Expressões de data e hora

Expressão |Resultado
---|---
`datetime("2015-01-02") - datetime("2015-01-01")`| `1d`
`datetime("2015-01-01") + 1d`| `datetime("2015-01-02")`
`datetime("2015-01-01") - 1d`| `datetime("2014-12-31")`
`2h * 24` | `2d`
`2d` / `2h` | `24`
`datetime("2015-04-15T22:33") % 1d` | `timespan("22:33")`
`bin(datetime("2015-04-15T22:33"), 1d)` | `datetime("2015-04-15T00:00")`
||
`<` |Menos
`<=`|Menor ou igual a
`>` |Maior
`>=`|Maior ou igual a
`<>`|Não é igual a
`!=`|Não é igual a 




### <a name="ago"></a>há

Subtrai timespan determinado a partir da hora do relógio UTC atual. Como `now()`, esta função pode ser utilizada várias vezes numa instrução e a hora do relógio UTC a ser referenciada serão os mesmos para todas as instâncias.

**Sintaxe**

    ago(a_timespan)

**Argumentos**

* *a_timespan*: intervalo para subtrair a hora do relógio UTC atual (`now()`).

**Devolve**

    now() - a_timespan

**Exemplo**

Todas as linhas com um carimbo de data/hora na última hora:

```AIQL

    T | where timestamp > ago(1h)
```

### <a name="datepart"></a>DatePart

    datepart("Day", datetime(2015-12-14)) == 14

Extrai de uma parte específica de uma data como um número inteiro.

**Sintaxe**

    datepart(part, datetime)

**Argumentos**

* `part:String`-{"Ano", "Mês", "Dia", "Hora", "Minuto", "Segundo", "Milissegundos", "Microssegundos", "Nanosegundos"}
* `datetime`

**Devolve**

Valor longo que representa a parte especificada.


### <a name="dayofmonth"></a>dayofmonth

    dayofmonth(datetime("2016-05-15")) == 15 

O número de ordinal do dia do mês.

**Sintaxe**

    dayofmonth(a_date)

**Argumentos**

* `a_date`: A `datetime`.


### <a name="dayofweek"></a>DAYOFWEEK

    dayofweek(datetime("2015-12-14")) == 1d  // Monday

O número de número inteiro de dias desde a anterior Domingo, como um `timespan`.

**Sintaxe**

    dayofweek(a_date)

**Argumentos**

* `a_date`: A `datetime`.

**Devolve**

O `timespan` desde a meia-noite no início do anterior Domingo, arredondado por defeito para um número inteiro de dias.

**Exemplos**

```AIQL
dayofweek(1947-11-29 10:00:05)  // time(6.00:00:00), indicating Saturday
dayofweek(1970-05-11)           // time(1.00:00:00), indicating Monday
```

### <a name="dayofyear"></a>DAYOFYEAR

    dayofyear(datetime("2016-05-31")) == 152 
    dayofyear(datetime("2016-01-01")) == 1 

O número de ordinal do dia do ano.

**Sintaxe**

    dayofyear(a_date)

**Argumentos**

* `a_date`: A `datetime`.

<a name="endofday"></a><a name="endofweek"></a><a name="endofmonth"></a><a name="endofyear"></a>
### <a name="endofday-endofweek-endofmonth-endofyear"></a>endofday, endofweek, endofmonth, endofyear

    dt = datetime("2016-05-23 12:34")

    endofday(dt) == 2016-05-23T23:59:59.999
    endofweek(dt) == 2016-05-28T23:59:59.999 // Saturday
    endofmonth(dt) == 2016-05-31T23:59:59.999 
    endofyear(dt) == 2016-12-31T23:59:59.999 


### <a name="getmonth"></a>getMonth

Obter o número do mês (1 a 12) a partir de uma data/hora.

**Exemplo**

    ... | extend month = getmonth(datetime(2015-10-12))

    --> month == 10

### <a name="getyear"></a>getyear

Obter o ano a partir de uma data/hora.

**Exemplo**

    ... | extend year = getyear(datetime(2015-10-12))

    --> year == 2015

### <a name="now"></a>agora

    now()
    now(-2d)

A hora do relógio UTC atual, opcionalmente, deslocamento por um determinado timespan. Esta função pode ser utilizada várias vezes numa instrução e a hora do relógio a ser referenciada serão os mesmos para todas as instâncias.

**Sintaxe**

    now([offset])

**Argumentos**

* *deslocamento:* A `timespan`, adicionadas à hora do relógio UTC atual. Predefinido: 0.

**Devolve**

A hora do relógio UTC atual como um `datetime`.

    now() + offset

**Exemplo**

Determina o intervalo de uma vez que o evento identificado pelo predicado:

```AIQL
T | where ... | extend Elapsed=now() - timestamp
```

<a name="startofday"></a><a name="startofweek"></a><a name="startofmonth"></a><a name="startofyear"></a>
### <a name="startofday-startofweek-startofmonth-startofyear"></a>startofday, startofweek, startofmonth, startofyear

    date=datetime("2016-05-23 12:34:56")

    startofday(date) == datetime("2016-05-23")
    startofweek(date) == datetime("2016-05-22") // Sunday
    startofmonth(date) == datetime("2016-05-01")
    startofyear(date) == datetime("2016-01-01")



### <a name="todatetime"></a>ToDateTime

Alias `datetime()`.

     todatetime("2016-03-28")
     todatetime("03/28/2016")
     todatetime("2016-03-28 14:34:00")
     todatetime("03/28/2016 2:34pm")
     todatetime("2016-03-28T14:34.5Z")
     todatetime(a[0]) 
     todatetime(b.c) 

Verificar se uma cadeia é uma data válida:

     iff(notnull(todatetime(customDimensions.myDate)),
         ..., ...)


### <a name="totimespan"></a>ToTimeSpan

Alias `timespan()`.

    totimespan("21d")
    totimespan("21h")
    totimespan(request.duration)

### <a name="weekofyear"></a>weekofyear

    weekofyear(datetime("2016-05-14")) == 21
    weekofyear(datetime("2016-01-03")) == 1
    weekofyear(datetime("2016-12-31")) == 53

O resultado de número inteiro representa o número da semana pela ISO 8601 padrão. O primeiro dia da semana é Domingo e a primeira semana do ano é a semana que contém Quinta-feira primeiro essa ano. (Os últimos dias de um ano, por conseguinte, podem conter algumas dos dias da semana 1 do ano seguinte, ou os primeiros dias podem conter algumas das semana 52 ou 53 do ano anterior.)


## <a name="string"></a>Cadeia

[countof](#countof) | [extrair](#extract) | [extractjson](#extractjson)  | [isempty](#isempty) | [isnotempty](#isnotempty) | [notempty](#notempty) | [parseurl](#parseurl) | [Substituir](#replace) | [Dividir](#split) | [strcat](#strcat) | [strlen](#strlen) | [subcadeia](#substring) | [tolower](#tolower) | [tostring](#tostring) | [toupper](#toupper)


### <a name="string-literals"></a>Cadeias literais

As regras são os mesmos tal como JavaScript.

Poderão ser colocada entre cadeias num único ou de caracteres de proposta dupla. 

Barra invertida (`\`) é utilizado para caracteres de escape tais como `\t` (separador), `\n` (nova linha) e instâncias da envolvente carácter de aspas.

* `'this is a "string" literal in single \' quotes'`
* `"this is a 'string' literal in double \" quotes"`
* `@"C:\backslash\not\escaped\with @ prefix"`

### <a name="obfuscated-string-literals"></a>Oculto cadeias literais

Oculto cadeias literais são cadeias que Analytics irá ocultem quando exportar a cadeia (por exemplo, quando o rastreio). O processo de obfuscation substitui carateres todos os oculto por um início (`*`) caráter.

Para formar uma cadeia oculto literal, anexar como prefixo `h` ou «H». Por exemplo:

```
h'hello'
h@'world' 
h"hello"
```

### <a name="string-comparisons"></a>Comparações de cadeias

Operador|Descrição|Maiúsculas/minúsculas|Exemplo verdadeiro
---|---|---|---
`==`|É igual a |Sim| `"aBc" == "aBc"`
`<>` `!=`|Não é igual a|Sim| `"abc" <> "ABC"`
`=~`|É igual a |N| `"abc" =~ "ABC"`
`!~`|Não é igual a |N| `"aBc" !~ "xyz"`
`has`|Direita-hand lado (RHS) é um termo todo esquerda-hand lado (LHS)|N| `"North America" has "america"`
`!has`|RHS não é um termo completo LHS|N|`"North America" !has "amer"` 
`hasprefix`|RHS é um prefixo de um termo LHS|N|`"North America" hasprefix "ame"`
`!hasprefix`|RHS não é um prefixo do qualquer termo no LHS|N|`"North America" !hasprefix "mer"`
`hassuffix`|RHS é um sufixo de um termo LHS|N|`"North America" hassuffix "rth"`
`!hassuffix`|RHS não é um sufixo do qualquer termo no LHS|N|`"North America" !hassuffix "mer"`
`contains` | RHS ocorre como uma subcadeia LHS|N| `"FabriKam" contains "BRik"`
`!contains`| RHS não ocorrer em LHS|N| `"Fabrikam" !contains "xyz"`
`containscs` | RHS ocorre como uma subcadeia LHS|Sim| `"FabriKam" contains "Kam"`
`!containscs`| RHS não ocorrer em LHS|Sim| `"Fabrikam" !contains "Kam"`
`startswith`|RHS é uma subcadeia inicial da LHS.|N|`"Fabrikam" startswith "fab"`
`!startswith`|RHS não se encontra uma subcadeia inicial da LHS.|N|`"Fabrikam" !startswith "abr"`
`endswith`|RHS é uma subcadeia terminal LHS.|N|`"Fabrikam" endswith "kam"`
`!endswith`|RHS não se encontra uma subcadeia terminal LHS.|N|`"Fabrikam" !endswith "ka"`
`matches regex`|LHS contém uma correspondência para RHS|Sim| `"Fabrikam" matches regex "b.*k"`
`in`|Igual a qualquer um dos elementos|Sim|`"abc" in ("123", "345", "abc")`
`!in`|Não é igual a qualquer um dos elementos|Sim|`"bc" !in ("123", "345", "abc")`

Utilizar `has` ou `in` se estiver a testar a presença de um termo todo lexical - ou seja, um símbolo ou uma palavra alfanumérica delimitado por carateres não-alfanuméricos ou início ou fim do campo. `has`executa mais rápido que `contains`, `startswith` ou `endswith`. O primeiro destas consultas é executado mais rápido:

    EventLog | where continent has "North" | count;
    EventLog | where continent contains "nor" | count





### <a name="countof"></a>countof

    countof("The cat sat on the mat", "at") == 3
    countof("The cat sat on the mat", @"\b.at\b", "regex") == 3

Conta as ocorrências de uma subcadeia numa cadeia. Cadeia simples correspondências podem sobrepor-se; RegEx correspondências não suportam.

**Sintaxe**

    countof(text, search [, kind])

**Argumentos**

* *texto:* Uma cadeia.
* *pesquisa:* A cadeia simples ou expressão normal para que correspondam aos dentro do *texto*.
* *tipo:* `"normal"|"regex"` Predefinido `normal`. 

**Devolve**

O número de vezes que deverá ser correspondida cadeia de pesquisa no contentor. Cadeia simples correspondências podem sobrepor-se; RegEx correspondências não suportam.

**Exemplos**

|||
|---|---
|`countof("aaa", "a")`| 3 
|`countof("aaaa", "aa")`| 3 (não 2!)
|`countof("ababa", "ab", "normal")`| 2
|`countof("ababa", "aba")`| 2
|`countof("ababa", "aba", "regex")`| 1
|`countof("abcabc", "a.c", "regex")`| 2
    



### <a name="extract"></a>extrair

    extract("x=([0-9.]+)", 1, "hello x=45.6|wo") == "45.6"

Obter uma correspondência para uma [expressão normal](#regular-expressions) a partir de uma cadeia de texto. Opcionalmente, em seguida, converte a subcadeia extraída com o tipo de indicada.

**Sintaxe**

    extract(regex, captureGroup, text [, typeLiteral])

**Argumentos**

* *regex:* Uma [expressão regular](#regular-expressions).
* *captureGroup:* Um positivo `int` constante que indica o grupo captura para extrair. 0 designa a correspondência inteira, 1 para o valor correspondido pela primeira '(' Parêntese')' na expressão regular, 2 ou mais para parênteses subsequentes.
* *texto:* A `string` para procurar.
* *typeLiteral:* Um literal tipo opcional (por exemplo, `typeof(long)`). Se fornecido, a subcadeia extraída é convertida para este tipo. 

**Devolve**

Se *regex* localiza uma correspondência em *formato de texto*: subcadeia comparada com de grupo captura indicada *captureGroup*, opcionalmente, convertidas em *typeLiteral*.

Se não existir correspondência, ou se a conversão falha: `null`. 

**Exemplos**

O exemplo de cadeia `Trace` são procurados para obter uma definição para `Duration`. A correspondência é convertida `real`, em seguida, multiplicados-la por uma constante de tempo (`1s`) para que `Duration` é do tipo `timespan`. Neste exemplo, é igual a segundos 123.45:

```AIQL
...
| extend Trace="A=1, B=2, Duration=123.45, ..."
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s) 
```

Este exemplo é equivalente ao `substring(Text, 2, 4)`:

```AIQL
extract("^.{2,2}(.{4,4})", 1, Text)
```

<a name="notempty"></a>
<a name="isnotempty"></a>
<a name="isempty"></a>
### <a name="isempty-isnotempty-notempty"></a>IsEmpty isnotempty, notempty

    isempty("") == true

TRUE se o argumento for uma cadeia vazia ou é nulo.
Consulte também [ÉNulo](#isnull).


**Sintaxe**

    isempty([value])


    isnotempty([value])


    notempty([value]) // alias of isnotempty

**Devolve**

Indica se o argumento é uma cadeia vazia ou ÉNulo.

|x|IsEmpty(x)
|---|---
| "" | VERDADEIRO
|"x" | FALSO
|parsejson("")|VERDADEIRO
|parsejson("[]")|FALSO
|parsejson("{}")|FALSO


**Exemplo**


    T | where isempty(fieldName) | count


### <a name="parseurl"></a>parseurl

Divida um URL em partes da mesma.

**Sintaxe**

    parseurl(urlstring)

**Argumentos**

* *urlstring:* UM URL.

**Devolve**

Um objeto que contém as partes como cadeias.

**Exemplo**

    parseurl("http://user:pass@contoso.com/icecream/buy.aspx?a=1&b=2#tag")

    {
    "Scheme" : "http",
    "Host" : "contoso.com",
    "Port" : "80",
    "Path" : "/icecream/buy.aspx",
    "Username" : "user",
    "Password" : "pass",
    "Query Parameters" : {"a":"1","b":"2"},
    "Fragment" : "tag"
    }

### <a name="replace"></a>substituir

Substitua todas as regex correspondências com outra cadeia.

**Sintaxe**

    replace(regex, rewrite, text)

**Argumentos**

* *regex:* A [expressão normal](https://github.com/google/re2/wiki/Syntax) para procurar *texto*. Pode conter grupos captura parênteses' (' ')'. 
* *reescrever:* O regex de substituição para qualquer correspondência efetuada por *matchingRegex*. Utilizar `\0` para consultar a correspondência de toda, `\1` para o primeiro grupo captura, `\2` e assim sucessivamente para grupos de captura subsequentes.
* *texto:* Uma cadeia.

**Devolve**

*texto* após a substituir todas as correspondências de *regex* com avaliações de *reescrever*. Correspondências não se sobrepõem.

**Exemplo**

Esta declaração de:

```AIQL
range x from 1 to 5 step 1
| extend str=strcat('Number is ', tostring(x))
| extend replaced=replace(@'is (\d+)', @'was: \1', str)
```

Tem os seguintes resultados:

| x    | Str | substituído|
|---|---|---|
| 1    | Número for 1.000000  | Número foi: 1.000000|
| 2    | Número for 2.000000  | Número foi: 2.000000|
| 3    | Número for 3.000000  | Número foi: 3.000000|
| 4    | Número for 4.000000  | Número foi: 4.000000|
| 5    | Número for 5.000000  | Número foi: 5.000000|
 



### <a name="split"></a>Dividir

    split("aaa_bbb_ccc", "_") == ["aaa","bbb","ccc"]

Divide-se uma cadeia determinada de acordo com um determinado delimitador e devolve uma matriz de cadeia com as subcadeias conatined. Opcionalmente, uma subcadeia específica pode ser devolvido se existe.

**Sintaxe**

    split(source, delimiter [, requestedIndex])

**Argumentos**

* *origem*: A cadeia de origem que será dividida, de acordo com o delimitador determinado.
* *delimitador*: O delimitador que será utilizado para dividir a cadeia de origem.
* *requestedIndex*: um índice baseado em zero opcional `int`. Se tiver fornecido, a matriz de cadeia devolvida irá conter a subcadeia pedida se existir. 

**Devolve**

Matriz de cadeia que contém as subcadeias da cadeia de origem dado que são delimitadas pelo delimitador determinado.

**Exemplos**

```
split("aa_bb", "_")           // ["aa","bb"]
split("aaa_bbb_ccc", "_", 1)  // ["bbb"]
split("", "_")                // [""]
split("a__b")                 // ["a","","b"]
split("aabbcc", "bb")         // ["aa","cc"]
```




### <a name="strcat"></a>strcat

    strcat("hello", " ", "world")

Concatena entre 1 e 16 argumentos, que tem de ser cadeias.

### <a name="strlen"></a>strlen

    strlen("hello") == 5

Comprimento de uma cadeia.

### <a name="substring"></a>subcadeia

    substring("abcdefg", 1, 2) == "bc"

Extrai uma subcadeia de uma cadeia de determinada origem a partir de um determinado índice. Opcionalmente, o comprimento da subcadeia pedida pode ser especificado.

**Sintaxe**

    substring(source, startingIndex [, length])

**Argumentos**

* *origem:* A cadeia de origem que será executada a subcadeia a partir de.
* *startingIndex:* Baseado em zero caráter posição inicial da subcadeia pedida.
* *comprimento:* Um parâmetro opcional que pode ser utilizado para especificar o número de carateres pedido a subcadeia. 

**Devolve**

Uma subcadeia da cadeia de determinado. A subcadeia inicia posição de caráter startingIndex (baseado em zero) e continua até ao fim dos carateres cadeia ou o comprimento, se especificado.

**Exemplos**

```
substring("123456", 1)        // 23456
substring("123456", 2, 2)     // 34
substring("ABCD", 0, 2)       // AB
```

### <a name="tolower"></a>tolower

    tolower("HELLO") == "hello"

Converte uma cadeia para minúsculas.

### <a name="toupper"></a>toupper

    toupper("hello") == "HELLO"

Converte uma cadeia para maiúsculas.



### <a name="guids"></a>GUID

    guid(00000000-1111-2222-3333-055567f333de)


## <a name="arrays-objects-and-dynamic"></a>Matrizes, objetos e dinâmicos

[literais](#dynamic-literals) | [Moldagem](#casting-dynamic-objects) | [operadores](#operators) | [permitem cláusulas](#dynamic-objects-in-let-clauses)
<br/>
[arraylength](#arraylength) | [extractjson](#extractjson) | [parsejson](#parsejson) | [intervalo](#range) | [treepath](#treepath) | [todynamic](#todynamic) | [postal](#zip)


Eis o resultado de uma consulta numa exceção de informações da aplicação. O valor na `details` é uma matriz.

![](./media/app-insights-analytics-reference/310.png)

**Indexação:** Indexar matrizes e objetos apenas tal como JavaScript:

    exceptions | take 1
  	| extend 
        line = details[0].parsedStack[0].line,
        stackdepth = arraylength(details[0].parsedStack)

* Mas utilizar `arraylength` e outras funções de análise (não ".length"!)

**Produtos** Em alguns casos é necessário haver um elemento que extraia um objeto, uma vez que o seu tipo pode variar. Por exemplo, `summarize...to` necessita de um tipo específico:

    exceptions 
  	| summarize count() 
      by toint(details[0].parsedStack[0].line)

    exceptions 
  	| summarize count() 
      by tostring(details[0].parsedStack[0].assembly)

**Literais** Para criar uma matriz explícita ou objeto saco de propriedade, escrevê-la como uma cadeia JSON e cast:

    todynamic('[{"x":"1", "y":"32"}, {"x":"6", "y":"44"}]')


**mvexpand:** Para separar pelas propriedades de um objeto em linhas separadas, utilize mvexpand:

    exceptions | take 1 
  	| mvexpand details[0].parsedStack[0]


![](./media/app-insights-analytics-reference/410.png)


**treepath:** Para encontrar todos os caminhos num objeto complexo:

    exceptions | take 1 | project timestamp, details 
  	| extend path = treepath(details) 
  	| mvexpand path


![](./media/app-insights-analytics-reference/420.png)

**buildschema:** Para localizar o esquema de mínimo admissões todos os valores da expressão na tabela:

    exceptions | summarize buildschema(details)

Resultado:

    { "`indexer`":
     {"id":"string",
       "parsedStack":
       { "`indexer`": 
         {  "level":"int",
            "assembly":"string",
            "fileName":"string",
            "method":"string",
            "line":"int"
         }},
      "outerId":"string",
      "message":"string",
      "type":"string",
      "rawStack":"string"
    }}

Repare que `indexer` é utilizado para marcar onde deve utilizar um índice numérico. Para este esquema, algumas caminhos válidos seria ser (partindo do princípio que esses índices de exemplo estiverem no intervalo):

    details[0].parsedStack[2].level
    details[0].message
    arraylength(details)
    arraylength(details[0].parsedStack)



### <a name="array-and-object-literals"></a>Matriz e objeto literais

Para criar um literal dinâmico, utilize `parsejson` (alias `todynamic`) com um JSON cadeia argumento:

* `parsejson('[43, 21, 65]')`-uma matriz de números
* `parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')` 
* `parsejson('21')`-um valor único tipo dinâmica que contém um número
* `parsejson('"21"')`-um valor único tipo dinâmica que contém uma cadeia

Nota que, ao contrário JavaScript, JSON exige a utilização de aspas (`"`) à volta de cadeias. Por conseguinte, é geralmente mais fáceis de proposta uma cadeia codificada JSON literais utilizar aspas simples (`'`).

Este exemplo cria um valor dinâmico e, em seguida, utiliza os campos:

```

T
| extend person = parsejson('{"name":"Alan", "age":21, "address":{"street":432,"postcode":"JLK32P"}}')
| extend n = person.name, add = person.address.street
```


### <a name="dynamic-object-functions"></a>Funções de objecto dinâmico

|||
|---|---|
| *valor* `in` *matriz*| VERDADEIRO se existir um elemento de *matriz* que = = *valor*<br/>`where City in ('London', 'Paris', 'Rome')`
| *valor* `!in` *matriz*| TRUE se não existe nenhuma elemento de *matriz* que = = *valor*
|[`arraylength(`matriz`)`](#arraylength)| Nulo se não for uma matriz
|[`extractjson(`o caminho, objeto`)`](#extractjson)|Utiliza o caminho para navegar para o objeto.
|[`parsejson(`origem`)`](#parsejson)| Uma cadeia JSON transforma-se um objeto dinâmico.
|[`range(`a partir de, para, passo`)`](#range)| Uma matriz de valores
|[`mvexpand`listColumn](#mvexpand-operator) | Replica uma linha para cada valor numa lista numa célula específica.
|[`summarize buildschema(`coluna`)`](#buildschema) |Infere o esquema de tipo de conteúdo de coluna
|[`summarize makelist(`coluna`)`](#makelist)| Organiza grupos de linhas e coloca os valores da coluna numa matriz.
|[`summarize makeset(`coluna`)`](#makeset) | Organiza grupos de linhas e coloca os valores da coluna na forma de matriz, sem duplicação.

### <a name="dynamic-objects-in-let-clauses"></a>Objectos dinâmicos em cláusulas permitem-


[Permitir que cláusulas](#let-clause) loja valores dinâmicos como cadeias, por isso, estas duas orações são equivalentes e necessita de ambos os `parsejson` (ou `todynamic`) antes de ser utilizada:

    let list1 = '{"a" : "somevalue"}';
    let list2 = parsejson('{"a" : "somevalue"}');

    T | project parsejson(list1).a, parsejson(list2).a




### <a name="arraylength"></a>arraylength

O número de elementos de uma matriz dinâmico.

**Sintaxe**

    arraylength(array)

**Argumentos**

* *matriz:* A `dynamic` valor.

**Devolve**

O número de elementos em *matriz*, ou `null` se *a matriz* não é uma matriz.

**Exemplos**

```
arraylength(parsejson('[1, 2, 3, "four"]')) == 4
arraylength(parsejson('[8]')) == 1
arraylength(parsejson('[{}]')) == 1
arraylength(parsejson('[]')) == 0
arraylength(parsejson('{}')) == null
arraylength(parsejson('21')) == null
```



### <a name="extractjson"></a>extractjson

    extractjson("$.hosts[1].AvailableMB", EventText, typeof(int))

Obter um elemento especificado fora de um texto JSON utilizando uma expressão do caminho. Opcionalmente, converta a cadeia extraída para um tipo específico.


**Sintaxe**

```

    string extractjson(jsonPath, dataSource) 
    resulttype extractjson(jsonPath, dataSource, typeof(resulttype))
```


**Devolve**

Esta função executa uma consulta de JsonPath na origem de dados que contém uma cadeia JSON válida, opcionalmente, converter esse valor para outro tipo de consoante o terceiro argumento.



**Exemplo**

A notação [Reto de abertura] e notação por pontos são equivalentes:

    ... | extend AvailableMB = extractjson("$.hosts[1].AvailableMB", EventText, typeof(int)) | ...

    ... | extend AvailableMD = extractjson("$['hosts'][1]['AvailableMB']", EventText, typeof(int)) | ...



**Sugestões sobre o desempenho**

* Aplicar cláusulas where antes de utilizar`extractjson()`
* Considere utilizar uma correspondência de expressão normal em vez disso, com [extrair](#extract) . Isto pode executar muito mais rápido e, em vigor se o JSON produzido a partir de um modelo.
* Utilizar `parsejson()` se precisar de extrair mais de um valor da JSON.
* Considere ter JSON analisado na ingestão pelo declarar o tipo da coluna para ser dinâmicos.

### <a name="json-path-expressions"></a>Expressões JSON caminho

|||
|---|---|
|`$`|Objeto de raiz|
|`@`|Objecto actual|
|`[0]`|Inferior à linha de matriz|
|`.`ou`[0]` | Subordinado|

*(Recomendamos não atualmente implementar carateres universais, recursão, União ou os setores do gráfico.)*




### <a name="parsejson"></a>parsejson

Interpreta um `string` como um [valor JSON](http://json.org/)) e devolve o valor como `dynamic`. É superior a utilizar o `extractjson()` quando precisa de mais de um elemento de um objecto composto JSON deverão ser extraídas.

**Sintaxe**

    parsejson(json)

**Argumentos**

* *json:* Um documento JSON.

**Devolve**

Um objeto do tipo de `dynamic` especificado pelo *json*.

**Exemplo**

No exemplo seguinte, quando `context_custom_metrics` é uma `string` que este aspeto: 

```
{"duration":{"value":118.0,"count":5.0,"min":100.0,"max":150.0,"stdDev":0.0,"sampledValue":118.0,"sum":118.0}}
```

em seguida, o fragmento de seguinte obtém o valor da `duration` ranhura no objeto e de que a mesma obtém duas ranhura, `duration.value` e  `duration.min` (`118.0` e `110.0`, respetivamente).

```AIQL
T
| ...
| extend d=parsejson(context_custom_metrics) 
| extend duration_value=d.duration.value, duration_min=d["duration"]["min"]
```



### <a name="range"></a>intervalo

O `range()` função (não devem ser confundidas com as `range` operador) gera uma matriz dinâmica exploração numa série de valores espaçados.

**Sintaxe**

    range(start, stop, step)

**Argumentos**

* *Iniciar:* O valor do primeiro elemento na matriz resultante. 
* *Parar:* O valor do último elemento na matriz resultante ou o valor, pelo menos, que é maior que o último elemento na matriz resultante e dentro de um número inteiro múltiplo de *passo* a partir do *início*.
* *passo:* A diferença entre dois elementos consecutivos a matriz.

**Exemplos**

O exemplo seguinte devolve `[1, 4, 7]`:

```AIQL
range(1, 8, 3)
```

O exemplo seguinte devolve uma matriz mantém todos os dias no ano 2015:

```AIQL

    range(datetime(2015-01-01), datetime(2015-12-31), 1d)
```

### <a name="todynamic"></a>todynamic

    todynamic('{"a":"a1", "b":["b1", "b2"]}')

Converte uma cadeia com um valor dinâmico.

### <a name="treepath"></a>treepath

    treepath(dynamic_object)

Enumera todas as expressões de caminho identificam folhas num objecto dinâmico. 

**Devolve**

Uma matriz de expressões do caminho.

**Exemplos**

    treepath(parsejson('{"a":"b", "c":123}')) 
    =>       ["['a']","['c']"]
    treepath(parsejson('{"prop1":[1,2,3,4], "prop2":"value2"}'))
    =>       ["['prop1']","['prop1'][0]","['prop2']"]
    treepath(parsejson('{"listProperty":[100,200,300,"abcde",{"x":"y"}]}'))
    =>       ["['listProperty']","['listProperty'][0]","['listProperty'][0]['x']"]

Nota que "[0]" indica a presença de uma matriz, mas não especificar o índice utilizado por um caminho específico.

### <a name="zip"></a>Postal

    zip(list1, list2, ...)

Combina um conjunto de listas numa só lista da cadeia de identificação.

* `list1...`: Uma lista de valores

**Exemplos**

    zip(parsejson('[1,3,5]'), parsejson('[2,4,6]'))
    => [ [1,2], [3,4], [5,6] ]

    
    zip(parsejson('[1,3,5]'), parsejson('[2,4]'))
    => [ [1,2], [3,4], [5,null] ]


### <a name="names"></a>Nomes

Nomes podem ser até 1024 caracteres. Estes são entre maiúsculas e minúsculas e podem conter letras, dígitos e sublinhado (`_`). 

Proposta de um nome utilizando ['... '] or [" ... "] para incluir outros carateres ou utilizar uma palavra-chave como um nome. Por exemplo:

```AIQL

    requests | 
    summarize  ["distinct urls"] = dcount(name) // non-alphanumerics
    by  ['where'] = client_City, // using a keyword as a name
        ['outcome!'] = success // non-alphanumerics
```


|||
|---|---|
|[' caminho\\file\n\'x\''] | Utilizar \ caracteres de escape|
|["d-e.=/f#\n"] | |
|[@'path\file'] | Sem sai - \ é literal|
|[@"\now&, em seguida,\"] | |
|[onde] | Utilizar uma palavra-chave idioma como um nome|

[AZURE.INCLUDE [app-insights-analytics-footer](../../includes/app-insights-analytics-footer.md)]


