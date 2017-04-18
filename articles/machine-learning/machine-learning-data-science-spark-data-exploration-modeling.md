<properties
    pageTitle="Informações detalhadas de dados e a modelação com motores | Microsoft Azure"
    description="Demonstra as capacidades de informações detalhadas e modelação de dados do toolkit motores MLlib."
    services="machine-learning"
    documentationCenter=""
    authors="bradsev"
    manager="jhubbard"
    editor="cgronlun" />

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/07/2016"
    ms.author="deguhath;bradsev;gokuma" />

# <a name="data-exploration-and-modeling-with-spark"></a>Informações detalhadas de dados e a modelação com motores

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este tutorial utiliza HDInsight motores fazer exploração de dados e classificação binária regressão modelação tarefas numa amostra do pt táxi viagem e importância a pagar 2013 conjunto de dados.  Orienta-o através dos passos do [Processo de ciências de dados](http://aka.ms/datascienceprocess), ponto a ponto, utilizando um motores HDInsight cluster de transformação e blobs do Azure para armazenar os dados e os modelos. O processo de explora e apresenta dados importados partir de um Blob de armazenamento do Azure e, em seguida, prepara os dados para criar modelos de aspeto do Office. Estes modelos são compilação utilizando o toolkit de motores MLlib fazer binária classificação e regressão tarefas de modelação.

- A tarefa de **classificação binária** é prever ou não uma sugestão é paga a viagem. 
- A tarefa de **regressão** é prever a quantidade de sugestão com base em outras funcionalidades de sugestão. 

Os modelos de que utilizamos incluem regressão linear e logístico, florestas aleatórias e árvores aumentados gradação:

- [Regressão linear com SGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é um modelo de regressão linear que utiliza um método de Stochastic gradação descida (SGD) e para Otimização e funcionalidade dimensionamento para prever os montantes de sugestão pagos. 
- Regressão [regressão logística com LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) ou "logit", é um modelo de regressão pode ser utilizado quando a variável de dependentes é categorias para efetuar a classificação de dados. LBFGS é um algoritmo de otimização de quasi-Nuno que aproxime o algoritmo de Broyden – ainda – Goldfarb – Shanno (BFGS) utilizando uma quantidade limitada da memória do computador e que amplamente é utilizado em aprender máquina.
- [Aleatório florestas](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são fatos de árvores de decisão.  Que combinam muitos árvores de decisão para reduzir o risco de overfitting. Aleatórias florestas são utilizadas para regressão e classificação e podem processar funcionalidades categorias e podem ser prolongadas para a definição de classificação multiclass. Se não requer funcionalidade dimensionamento e de podem capturar não modo e funcionalidade interações. Aleatórias florestas são um da modelos de classificação e regressão de aprendizagem automática mais bem sucedida.
- [Árvores de aumentado com gradação de cores](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são fatos de árvores de decisão. GBTs formar árvores de decisão iterativamente para minimizar uma função de perda. GBTs são utilizados para regressão e classificação e pode processar funcionalidades categorias, não requer funcionalidade dimensionamento e podem capturar não modo e funcionalidade interações. Também pode ser utilizados numa definição multiclass classificação.

Os passos de modelação também contenham código que mostra como formar, avaliar e guardar cada tipo de modelo. Python foi utilizado para a solução de código e para mostrar as representações relevantes.   


>[AZURE.NOTE] Apesar do toolkit de motores MLlib foi concebido para trabalhar em grandes conjuntos de dados, uma amostra relativamente pequena (~ 30 Mb utilizando 170K linhas, cerca de 0,1% do conjunto de dados de pt original) é utilizada aqui para maior conveniência. Exercício atribuído aqui é executado eficazmente (em cerca de 10 minutos) num cluster de HDInsight com 2 nós de trabalho. O mesmo código, com pequenas alterações, pode ser utilizado para processar grandes-conjuntos de dados, com as alterações adequadas para colocação em cache de dados de memória e alterar o tamanho de cluster.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de uma conta do Azure e um cluster de necessidade de uma 1.6 de motores de 3.4 HDInsight HDInsight motores que para concluir este tutorial. Consulte o artigo [Descrição geral dos dados de ciência utilizando motores no Azure HDInsight](machine-learning-data-science-spark-overview.md) para obter instruções sobre como de cumprir estes requisitos. Esse tópico também contém uma descrição dos dados de pt 2013 veículo utilizados aqui e instruções sobre como executar código a partir de um bloco de notas Jupyter no cluster motores. **PySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb** bloco de notas que contém os exemplos de código neste tópico está disponível no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark). 


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuração: localizações de armazenamento, bibliotecas e o contexto de motores predefinido

Motores são capaz de leitura e escrita para Azure armazenamento Blob (também conhecido como WASB). Para que qualquer um dos seus dados existentes armazenados aí podem ser processados utilizando motores e os resultados novamente armazenados no WASB.

Para guardar ficheiros de modelos de ou WASB, o caminho tem de ser especificado corretamente. O contentor predefinido anexado ao cluster motores pode ser referenciado utilizando um caminho que começa com: "wasb: / / /". Outras localizações são referenciadas pela "wasb: / /".


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Definir caminhos de diretório para localizações de armazenamento no WASB

O código de exemplo seguinte especifica a localização dos dados serem lidos e o caminho para o diretório de armazenamento de modelo para o qual o resultado de modelo é guardado:


    # SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

    # LOCATION OF TRAINING DATA
    taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 


### <a name="import-libraries"></a>Bibliotecas de importação

Configurar o também requer a importação de bibliotecas necessárias. Definir o contexto de motores e importar necessárias bibliotecas com o seguinte código:


    # IMPORT LIBRARIES
    import pyspark
    from pyspark import SparkConf
    from pyspark import SparkContext
    from pyspark.sql import SQLContext
    import matplotlib
    import matplotlib.pyplot as plt
    from pyspark.sql import Row
    from pyspark.sql.functions import UserDefinedFunction
    from pyspark.sql.types import *
    import atexit
    from numpy import array
    import numpy as np
    import datetime


### <a name="preset-spark-context-and-pyspark-magics"></a>Contexto de motores predefinido e PySpark magics

Miolo PySpark que é fornecidos com Jupyter blocos de notas têm um contexto predefinido. Por isso, não é necessário definir os motores ou contextos ramo explicitamente antes de começar a trabalhar com a aplicação estão a desenvolver. Estes contextos estão disponíveis para si por predefinição. Estes contextos são:

- SC - para motores 
- sqlContext - para ramo

O kernel PySpark fornece alguns predefinidas "magics", que são comandos especiais que pode ligar com %. Existem duas essas comandos que são utilizados nestes exemplos de código.

- **% local** Especifica que o código nas linhas subsequentes está a ser executado localmente. Código tem de ser código Python válido.
- **%%sql -o <variable name>** Executa uma consulta de ramo contra a sqlContext. Se o parâmetro + o lhe é transmitido, o resultado da consulta persiste na % local contexto Python como um DataFrame Pandas.
 

Para obter mais informações sobre kernels para Jupyter blocos de notas e o predefinido "magics" que fornecer, consulte o artigo [Kernels disponíveis para Jupyter blocos de notas com clusters de HDInsight motores Linux no HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).
 

## <a name="data-ingestion-from-public-blob"></a>Ingestão de dados a partir do blob público

O primeiro passo no processo de ciências de dados é ingerir esta última os dados a analisar a partir de origens onde é reside para o ambiente de informações detalhadas e modelação de dados. O ambiente é motores neste tutorial. Esta secção contém o código para concluir uma série de tarefas:

- ingerir esta última o exemplo de dados para ser modelados
- Leia o conjunto de dados de entrada (armazenado como um ficheiro. tsv)
- Formatar e limpar os dados
- criar e objetos (RDDs ou pacotes de dados) na memória em cache
- registá-la como uma tabela temp no contexto de SQL.

Eis o código para ingestão de dados.

    # INGEST DATA

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT FILE FROM PUBLIC BLOB
    taxi_train_file = sc.textFile(taxi_train_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_train_file.first()
    fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
    fields[7].dataType = IntegerType() #Pickup hour
    fields[8].dataType = IntegerType() # Pickup week
    fields[9].dataType = IntegerType() # Weekday
    fields[10].dataType = IntegerType() # Passenger count
    fields[11].dataType = FloatType() # Trip time in secs
    fields[12].dataType = FloatType() # Trip distance
    fields[19].dataType = FloatType() # Fare amount
    fields[20].dataType = FloatType() # Surcharge
    fields[21].dataType = FloatType() # Mta_tax
    fields[22].dataType = FloatType() # Tip amount
    fields[23].dataType = FloatType() # Tolls amount
    fields[24].dataType = FloatType() # Total amount
    fields[25].dataType = IntegerType() # Tipped or not
    fields[26].dataType = IntegerType() # Tip class
    taxi_schema = StructType(fields)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
    taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
    
    
    # CREATE DATA FRAME
    taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:**

Tempo despendido para executar acima célula: 51.72 segundos


## <a name="data-exploration--visualization"></a>Informações detalhadas de dados e visualização

Assim que os dados tem foram importados para o motores, o próximo passo no processo de ciências de dados é obter mais aprofundada compreensão dos dados através de informações detalhadas e visualização. Nesta secção, examine os dados de veículo com consultas SQL e desenhar as variáveis de destino e potenciais funcionalidades para inspeção visual. Especificamente, podemos Traçar a frequência de passageiros contagens no veículo viagens, a frequência das quantidades de sugestão e como sugestões variam consoante o montante do pagamento e tipo.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Desenhar um histograma da frequência de contagem de passageiros na amostra de viagens de veículo

Este código e fragmentos subsequentes utilizam mágico SQL para consultar da amostra e mágico local para desenhar os dados.

- **Mágico SQL (`%%sql`)** O kernel HDInsight PySpark suporta inline fácil HiveQL consultas contra a sqlContext. O (-the VARIABLE_NAME) argumento persistir o resultado da consulta SQL como um DataFrame Pandas no servidor Jupyter. Isto significa que está disponível no modo de local.
- O ** `%%local` mágico** é utilizado para executar código localmente no servidor Jupyter, que é o headnode do HDInsight cluster. Normalmente, pode utilizar `%%local` mágico em conjunto com o `%%sql` mágico com parâmetro + o. O parâmetro + o seria persistirem o resultado da consulta SQL localmente e, em seguida, % mágico local seria acionar o próximo conjunto de fragmento de código para executar o localmente contra o resultado das consultas SQL que é mantido localmente

O resultado é automaticamente visualizado depois de executar o código.

Esta consulta obtém viagens pela contagem passageiros. 

    # PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT passenger_count, COUNT(*) as trip_counts 
    FROM taxi_train 
    WHERE passenger_count > 0 and passenger_count < 7 
    GROUP BY passenger_count 

Este código cria uma moldura de dados local a partir do resultado da consulta e desenha os dados. O `%%local` mágico cria uma moldura de dados local, `sqlResults`, que pode ser utilizado para desenhar com matplotlib. 

>[AZURE.NOTE] Este mágico PySpark é utilizado várias vezes neste tutorial. Se a quantidade de dados for grande, deverá de exemplo para criar uma moldura de dados que pode ajustar na memória local.

    #CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
    # CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
    sqlResults

Eis o código para desenhar viagens por passageiros contagens

    # PLOT PASSENGER NUMBER VS. TRIP COUNTS
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline
    
    x_labels = sqlResults['passenger_count'].values
    fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
    fig.set_xticklabels(x_labels)
    fig.set_title('Counts of trips by passenger count')
    fig.set_xlabel('Passenger count in trips')
    fig.set_ylabel('Trip counts')
    plt.show()

**RESULTADO:**

![Frequência de viagem pela contagem passageiros](./media/machine-learning-data-science-spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Pode selecionar entre vários tipos de visualizações (tabela, circular, linhas, área ou barra) ao utilizar os botões de menu **tipo** no bloco de notas. O desenho de barra é mostrado aqui.
    
### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Desenhe um histograma de sugestão quantidades e como a quantidade de sugestão varia pelos montantes de contar e tarifa passageiros.

Utilize uma consulta SQL para dados de exemplo.

    #PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE
    
    # HIVEQL QUERY AGAINST THE sqlContext
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped 
    FROM taxi_train 
    WHERE passenger_count > 0 
    AND passenger_count < 7 
    AND fare_amount > 0 
    AND fare_amount < 200 
    AND payment_type in ('CSH', 'CRD') 
    AND tip_amount > 0 
    AND tip_amount < 25


Esta célula código utiliza a consulta SQL para criar três representações os dados.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local
    
    # HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()
    
    # TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()
    
    # TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 100, -2, 20])
    plt.show()


**RESULTADO:** 

![Sugestão quantidade distribuição](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-distribution.png)

![Sugestão quantidade pela contagem passageiros](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Sugestão quantidade pelo montante de corrida](./media/machine-learning-data-science-spark-data-exploration-modeling/tip-amount-by-fare-amount.png)


## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Preparação de engenharia, da transformação e dados para modelação de funcionalidades
Esta secção descreve e fornece o código de procedimentos utilizados para preparar dados para utilização em modelação ML. Mostra como efetuar as seguintes tarefas:

- Criar uma nova funcionalidade ao horas binning num tráfego tempo registos
- Índice remissivo e descodificar funcionalidades categorias
- Criar objetos de ponto com nome para introduzidos funções ML
- Criar uma sub-sondagem dos dados e dividir formação e conjuntos de testes
- Funcionalidade de dimensionamento
- Objetos de cache na memória


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar uma nova funcionalidade ao horas binning num tráfego tempo registos

Este código mostra como criar uma nova funcionalidade por binning horas para o tráfego tempo registos e, em seguida, como a moldura de dados resultante na memória em cache. Onde e são distribuídos conjuntos de dados (RDDs) e pacotes de dados são utilizados repetidamente, colocação em cache orienta para os tempos de execução melhorados. Por conseguinte, podemos em cache RDDs e molduras de dados em várias fases no tutorial. 

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train 
    """
    taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    # THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
    # MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()

**RESULTADO:** 

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Índice remissivo e descodificar funcionalidades categorias para introduzidos modelação funções

Esta secção mostra como indexar ou codificar funcionalidades categorias para entrada para as funções de modelação. A modelação e prever funções de MLlib necessitam de funcionalidades com dados de entrada categorias para ser indexadas ou codificado antes de utilizar. Consoante o modelo, terá de índice remissivo ou codificá-los de diferentes formas:  

- **Modelação de árvore** requer categorias para ser codificado como valores numéricos (por exemplo, uma funcionalidade com três categorias pode ser codificada com 0; 1; 2). Isto é fornecido pela função de [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) do MLlib. Esta função codifica uma coluna de cadeia de etiquetas para uma coluna de índices de etiqueta que são ordenadas por frequência de etiqueta. Apesar de indexado com valores numéricos para entrada e processamento de dados, podem ser especificados dos algoritmos com base em árvore para tratá-los corretamente como categorias. 

- **Modelos de Logistic e de regressão Linear** exigir quente um codificação, onde, por exemplo, uma funcionalidade com três categorias pode ser expandida por três colunas de funcionalidade, com cada contendo 0 ou 1 consoante a categoria de uma observação. MLlib fornece [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) função fazer quente um codificação. Este codificador mapas uma coluna de índices de etiqueta a uma coluna de vectores binários, no máximo um único um valor. Esta codificação permite algoritmos esperava funcionalidades valores numéricas, como regressão logística, para serem aplicados a funcionalidades de categorias.

Eis o código para indexar e descodificar funcionalidades categorias:


    # INDEX AND ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES    
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # INDEX AND ENCODE VENDOR_ID
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_train_with_newFeatures)
    encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
    encoded1 = encoder.transform(indexed)
    
    # INDEX AND ENCODE RATE_CODE
    stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
    model = stringIndexer.fit(encoded1)
    indexed = model.transform(encoded1)
    encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
    encoded2 = encoder.transform(indexed)
    
    # INDEX AND ENCODE PAYMENT_TYPE
    stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
    model = stringIndexer.fit(encoded2)
    indexed = model.transform(encoded2)
    encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
    encoded3 = encoder.transform(indexed)
    
    # INDEX AND TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tempo despendido para executar acima célula: 1.28 segundos

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Criar objetos de ponto com nome para introduzidos funções ML

Esta secção contém código que mostra como indexar dados de texto de categorias como um tipo de dados de ponto com nome e descodificá-lo para que possa ser utilizado para formar e testar regressão logística MLlib e outros modelos de classificação. Objetos de ponto com nome são formatadas de uma forma que é necessário como dados de entrada para a maior parte dos algoritmos ML no MLlib com a conjuntos de dados de distribuído e são (RDD). Um [ponto de rótulo](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um local vector denso ou disperso, associados a uma etiqueta/resposta.  

Esta secção contém código que mostra como indexar dados de texto de categorias como um tipo de dados [denominada ponto](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) e descodificá-lo para que possa ser utilizado para formar e testar regressão logística MLlib e outros modelos de classificação. Objetos de ponto com nome são constituídos por uma etiqueta (variável de destino/resposta) e vector funcionalidade com a conjuntos de dados de distribuído e são (RDD). Este formato é necessária, como entrada por muitos ML dos algoritmos no MLlib.

Eis o código para indexar e descodificar funcionalidades de texto para a classificação binária.

    # FUNCTIONS FOR BINARY CLASSIFICATION

    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array

    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        labPt = LabeledPoint(line.tipped, features)
        return  labPt
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tipped, features)
        return  labPt


Eis o código para codificar e funcionalidades de texto categorias para análise de regressão linear de índice.

    # FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])

        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        labPt = LabeledPoint(line.tip_amount, features)
        return  labPt


### <a name="create-a-random-sub-sampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Criar uma sub-sondagem dos dados e dividir formação e conjuntos de testes

Este código cria uma sondagem dos dados (25% é utilizado aqui). Apesar de não é necessária para deste exemplo devido ao tamanho do conjunto de dados, podemos demonstram como pode de exemplo aqui para saber como utilizá-la para o seu próprio problema quando for necessário. Quando são grandes amostras, isto pode poupar tempo significativo enquanto modelos de formação. Seguinte podemos dividido a amostra de uma peça de formação (75% aqui) e uma peça de teste (25% aqui) para utilizar nas classificação e modelação de regressão.


    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.sql.functions import rand

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    samplingFraction = 0.25;
    trainingFraction = 0.75; testingFraction = (1-trainingFraction);
    seed = 1234;
    encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)
    
    # SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
    # INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
    dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
    trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
    indexedTESTbinary = testData.map(parseRowIndexingBinary)
    oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
    oneHotTESTbinary = testData.map(parseRowOneHotBinary)
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg = trainData.map(parseRowIndexingRegression)
    indexedTESTreg = testData.map(parseRowIndexingRegression)
    oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
    oneHotTESTreg = testData.map(parseRowOneHotRegression)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tempo despendido para executar acima célula: 0,24 segundos


### <a name="feature-scaling"></a>Funcionalidade de dimensionamento

Funcionalidade de dimensionamento, também conhecido como normalização de bases de dados, assegura que funcionalidades com valores amplamente pagos são não dado excessiva avaliar na função objetivo. O código do dimensionamento da funcionalidade utiliza o [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) para dimensionar as funcionalidades para desvio de unidade. Esta é fornecida pelo MLlib para utilização em regressão linear com Stochastic gradação descida (SGD), um algoritmo popular para formação uma vasta gama de outros modelos como regularizadas regressões ou suporte vector máquinas (SVM) de aprendizagem automática.

>[AZURE.NOTE] Vamos ter encontrado o algoritmo de LinearRegressionWithSGD para ser sensível a funcionalidade de dimensionamento.

Eis o código para variáveis de escala para utilização com o algoritmo SGD linear regularized.

    # FEATURE SCALING

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.regression import LabeledPoint
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    
    # SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
    label = oneHotTRAINreg.map(lambda x: x.label)
    features = oneHotTRAINreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    label = oneHotTESTreg.map(lambda x: x.label)
    features = oneHotTESTreg.map(lambda x: x.features)
    scaler = StandardScaler(withMean=False, withStd=True).fit(features)
    dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
    oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tempo despendido para executar acima célula: 13.17 segundos


### <a name="cache-objects-in-memory"></a>Objetos de cache na memória

O tempo despendido para formação e testes dos algoritmos ML pode ser reduzido mediante a moldura de dados de entrada objetos utilizados para classificação, regressão e dimensionada funcionalidades de colocação em cache.

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.cache()
    indexedTESTbinary.cache()
    oneHotTRAINbinary.cache()
    oneHotTESTbinary.cache()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.cache()
    indexedTESTreg.cache()
    oneHotTRAINreg.cache()
    oneHotTESTreg.cache()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.cache()
    oneHotTESTregScaled.cache()
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:** 

Tempo despendido para executar acima célula: 0,15 segundos


## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Prever ou não uma sugestão é paga com os modelos de classificação binário

Esta secção mostra como utilizar três modelos para a tarefa de classificação binário de as previsões se ou não uma sugestão é paga para uma viagem de veículo. Os modelos apresentados são:

- Regularizado logística regressão 
- Modelo de aleatório floresta
- Árvores de aumentar a gradação de cor

Cada modelo de criação de secção de código é dividido passos: 

1. **Formação do modelo** de dados com um conjunto de parâmetro
2. **Avaliação de modelo** num conjunto de dados de teste com métricas
3. **Guardar modelo** blob para futuro consumo

### <a name="classification-using-logistic-regression"></a>Classificação através de regressão logística

O código nesta secção mostra como formar, avaliar e guardar um modelo de regressão logísticos com [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) prevê, com ou não uma sugestão é paga para uma viagem no pt veículo viagem e tarifa conjunto de dados.

**Preparar o modelo de regressão logística com CV e hyperparameter limpeza**

    # LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

    # GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
    from sklearn.metrics import roc_curve,auc
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    
    # CREATE MODEL WITH ONE SET OF PARAMETERS
    logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                                   regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                                   tolerance=0.0001, validateData=True, numClasses=2)
    
    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(logitModel.weights))
    print("Intercept: " + str(logitModel.intercept))
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RESULTADO:** 

Coeficientes: [0.0082065285375,-0.0223675576104,-0.0183812028036, - 3.48124578069e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1.00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Intercetar:-0.0111216486893

Tempo despendido para executar acima célula: 14.43 segundos

**Avaliar o modelo de classificação binário com métricas padrão**

    #EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # PREDICT ON TEST DATA WITH MODEL
    predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))
    
    # INSTANTIATE METRICS OBJECT
    metrics = BinaryClassificationMetrics(predictionAndLabels)

    # AREA UNDER PRECISION-RECALL CURVE
    print("Area under PR = %s" % metrics.areaUnderPR)

    # AREA UNDER ROC CURVE
    print("Area under ROC = %s" % metrics.areaUnderROC)
    metrics = MulticlassMetrics(predictionAndLabels)

    # OVERALL STATISTICS
    precision = metrics.precision()
    recall = metrics.recall()
    f1Score = metrics.fMeasure()
    print("Summary Stats")
    print("Precision = %s" % precision)
    print("Recall = %s" % recall)
    print("F1 Score = %s" % f1Score)


    ## SAVE MODEL WITH DATE-STAMP
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
    dirfilename = modelDir + logisticregressionfilename;
    logitModel.save(sc, dirfilename);
    
    # OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
    logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
    predictionAndLabelsDF = predictionAndLabels.toDF()
    predictionAndLabelsDF.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:** 

Área em PR = 0.985297691373

Área em ROC = 0.983714670256

Estatísticas de resumo

Precisão = 0.984304060189

Recuperar = 0.984304060189

F1 Pontuação = 0.984304060189

Tempo despendido para executar acima célula: 57.61 segundos

**Traçar a curva ROC.**

O *predictionAndLabelsDF* está registado como uma tabela, *tmp_results*, na célula anterior. *tmp_results* podem ser utilizados para fazer consultas e resultados de saída na moldura sqlResults de dados para o desenho. Eis o código.


    # QUERY RESULTS                              
    %%sql -q -o sqlResults
    SELECT * from tmp_results


Eis o código para efetuar previsões e Traçar a curva de ROC.

    # MAKE PREDICTIONS AND PLOT ROC-CURVE

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    # MAKE PREDICTIONS
    predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
    prob = predictions_pddf["probability"] 
    fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT ROC CURVE
    plt.figure(figsize=(5,5))
    plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
    plt.plot([0, 1], [0, 1], 'k--')
    plt.xlim([0.0, 1.0])
    plt.ylim([0.0, 1.05])
    plt.xlabel('False Positive Rate')
    plt.ylabel('True Positive Rate')
    plt.title('ROC Curve')
    plt.legend(loc="lower right")
    plt.show()
    

**RESULTADO:**

![Regressão logística ROC curve.png](./media/machine-learning-data-science-spark-data-exploration-modeling/logistic-regression-roc-curve.png)


### <a name="random-forest-classification"></a>Classificação aleatório floresta

O código nesta secção mostra como formar, avaliar e guardar um modelo de floresta aleatório prevê, com ou não uma sugestão é paga para uma viagem no pt veículo viagem e tarifa conjunto de dados.
    
    #PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.mllib.evaluation import MulticlassMetrics
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    # TRAIN RANDOMFOREST MODEL
    rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                           categoricalFeaturesInfo=categoricalFeaturesInfo,
                                           numTrees=25, featureSubsetStrategy="auto",
                                           impurity='gini', maxDepth=5, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRINT TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp;
    dirfilename = modelDir + rfclassificationfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Área em ROC = 0.985297691373

Tempo despendido para executar acima célula: 31.09 segundos


### <a name="gradient-boosting-trees-classification"></a>Classificação de árvores aumentar a gradação de cor

O código nesta secção mostra como formar, avaliar e guardar um modelo de árvores boosting com gradação de cor que prevê, com ou não uma sugestão é paga para uma viagem de viagem de veículo pt e conjunto de dados a importância a pagar.

    #PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    
    gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
    ## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
    #print('Learned classification GBT model:')
    #print(bgtModel.toDebugString())
    
    # PREDICT ON TEST DATA AND EVALUATE
    predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
    predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)
    
    # AREA UNDER ROC CURVE
    metrics = BinaryClassificationMetrics(predictionAndLabels)
    print("Area under ROC = %s" % metrics.areaUnderROC)
    
    # PERSIST MODEL IN A BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
    dirfilename = modelDir + btclassificationfilename;
    
    gbtModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RESULTADO:**

Área em ROC = 0.985297691373

Tempo despendido para executar acima célula: 19.76 segundos


## <a name="predict-tip-amounts-for-taxi-trips-with-regression-models"></a>Prever montantes de sugestão para viagens de veículo com os modelos de regressão

Esta secção mostra como utilizar três modelos para a tarefa de regressão de as previsões a quantidade da sugestão paga uma viagem de veículo com base em outras funcionalidades de sugestão. Os modelos apresentados são:

- Regularizado regressão linear
- Aleatória floresta
- Árvores de aumentar a gradação de cor

Estes modelos foram descritos na introdução. Cada modelo de criação de secção de código é dividido passos: 

1. **Formação do modelo** de dados com um conjunto de parâmetro
2. **Avaliação de modelo** num conjunto de dados de teste com métricas
3. **Guardar modelo** blob para futuro consumo

### <a name="linear-regression-with-sgd"></a>Regressão linear com SGD 

O código nesta secção mostra como utilizar funcionalidades com escala para formar uma regressão linear que utiliza descida gradação stochastic (SGD) para Otimização e como pontuação, avaliar e guarde o modelo de armazenamento de Blobs do Azure (WASB).

>[AZURE.TIP] No nossa experiência, pode haver problemas com a convergência dos modelos de LinearRegressionWithSGD e parâmetros tem de ser alteradas/otimizada cuidadosamente para obter um modelo de válida. Dimensionamento das variáveis significativamente ajuda com convergência. 


    #PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD LIBRARIES
    from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
    from pyspark.mllib.evaluation import RegressionMetrics
    from scipy import stats
    
    # USE SCALED FEATURES TO TRAIN MODEL
    linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

    # PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
    # NOTE: There are 20 coefficient terms for the 10 features, 
    #       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
    print("Coefficients: " + str(linearModel.weights))
    print("Intercept: " + str(linearModel.intercept))
    
    # SCORE ON SCALED TEST DATA-SET & EVALUATE
    predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
    testMetrics = RegressionMetrics(predictionAndLabels)
    
    # PRINT TEST METRICS
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = modelDir + linearregressionfilename;
    
    linearModel.save(sc, dirfilename)
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Coeficientes: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Interceptar: 0.853872718283

RMSE = 1.24190115863

R sqr = 0.608017146081

Tempo despendido para executar acima célula: 58.42 segundos


### <a name="random-forest-regression"></a>Regressão aleatório floresta

O código nesta secção mostra como formar, avaliar e guardar uma regressão de floresta aleatório prevê, com um montante de sugestão para os dados de viagem de veículo pt.


    #PREDICT TIP AMOUNTS USING RANDOM FOREST

    # RECORD START TIME
    timestart= datetime.datetime.now()

    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    from pyspark.mllib.util import MLUtils
    from pyspark.mllib.evaluation import RegressionMetrics
    
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                        numTrees=25, featureSubsetStrategy="auto",
                                        impurity='variance', maxDepth=10, maxBins=32)
    ## UN-COMMENT IF YOU WANT TO PRING TREES
    #print('Learned classification forest model:')
    #print(rfModel.toDebugString())
    
    ## PREDICT AND EVALUATE ON TEST DATA-SET
    predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)
    
    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp;
    dirfilename = modelDir + rfregressionfilename;
    
    rfModel.save(sc, dirfilename);
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

RMSE = 0.891209218139

R sqr = 0.759661334921

Tempo despendido para executar acima célula: 49.21 segundos


### <a name="gradient-boosting-trees-regression"></a>Regressão árvores de aumentar a gradação de cor

O código nesta secção mostra como formar, avaliar e guardar um modelo de árvores boosting com gradação de cor que prevê, com um montante de sugestão para os dados de viagem de veículo pt.

**Dar formação e avaliar**

    #PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

    # RECORD START TIME
    timestart= datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    from pyspark.mllib.util import MLUtils
    
    ## TRAIN MODEL
    categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
    gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                    numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)
    
    ## EVALUATE A TEST DATA-SET
    predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
    predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

    # TEST METRICS
    testMetrics = RegressionMetrics(predictionAndLabels)
    print("RMSE = %s" % testMetrics.rootMeanSquaredError)
    print("R-sqr = %s" % testMetrics.r2)

    # SAVE MODEL IN BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
    dirfilename = modelDir + btregressionfilename;
    gbtModel.save(sc, dirfilename)
    
    # CONVER RESULTS TO DF AND REGISER TEMP TABLE
    test_predictions = sqlContext.createDataFrame(predictionAndLabels)
    test_predictions.registerTempTable("tmp_results");
    
    # PRINT ELAPSED TIME
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

RMSE = 0.908473148639

R sqr = 0.753835096681

Tempo despendido para executar acima célula: 34.52 segundos

**Desenhar**

*tmp_results* está registado como uma tabela Hive a célula anterior. Resultados da tabela são o resultado na moldura de dados de *sqlResults* para desenhar. Eis o código

    # PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

    # SELECT RESULTS
    %%sql -q -o sqlResults
    SELECT * from tmp_results

Eis o código para desenhar os dados utilizando o servidor de Jupyter.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    import numpy as np

    # PLOT 
    ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
    plt.axis([-1, 20, -1, 20])
    plt.show(ax)
    

**RESULTADO:**

![Vs-previstos-sugestão-valores reais](./media/machine-learning-data-science-spark-data-exploration-modeling/actual-vs-predicted-tips.png)

    
## <a name="clean-up-objects-from-memory"></a>Limpar o objetos de memória

Utilizar `unpersist()` para eliminar objetos em cache na memória.
        
    # REMOVE ORIGINAL DFs
    taxi_df_train_cleaned.unpersist()
    taxi_df_train_with_newFeatures.unpersist()
    
    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTRAINbinary.unpersist()
    indexedTESTbinary.unpersist()
    oneHotTRAINbinary.unpersist()
    oneHotTESTbinary.unpersist()
    
    # FOR REGRESSION TRAINING AND TESTING
    indexedTRAINreg.unpersist()
    indexedTESTreg.unpersist()
    oneHotTRAINreg.unpersist()
    oneHotTESTreg.unpersist()
    
    # SCALED FEATURES
    oneHotTRAINregScaled.unpersist()
    oneHotTESTregScaled.unpersist()


## <a name="record-storage-locations-of-the-models-for-consumption-and-scoring"></a>Localizações de armazenamento de registo de modelos para consumo e pontuação

Consumir e pontuação um conjunto de dados independente descrito a [pontuação e modelos de aprendizagem máquina compilado motores de avaliar](machine-learning-data-science-spark-model-consumption.md) tópico, tem de copiar e colar estes nomes de ficheiro que contém os modelos de guardada criados aqui para o bloco de notas consumo Jupyter. Eis o código para imprimir os caminhos para ficheiros de modelo que precisa de aí.

    # MODEL FILE LOCATIONS FOR CONSUMPTION
    print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
    print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
    print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
    print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
    print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
    print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";


**SAÍDA**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 0317_03_23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 0317_05_21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 0317_04_11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 0317_06_08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 0317_04_36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 0317_06_51.737282"


## <a name="whats-next"></a>O que se segue?

Agora que criou modelos de classificação e de regressão com o MlLib motores, está pronto para aprender a pontuação e avaliar estes modelos. A exploração de dados avançada e bloco de notas de modelação mais aprofundadas dives para incluindo validação cruzada, hyper-o parâmetro de remoção e modelar avaliação. 

**Modelar consumo:** Para saber como pontuação e avaliar os modelos de classificação e regressão criados neste tópico, consulte o artigo [pontuação e modelos de aprendizagem máquina compilado motores de avaliar](machine-learning-data-science-spark-model-consumption.md).

**Publicação em validação e hyperparameter de remoção**: consulte o artigo [informações detalhadas de dados e a modelação com motores avançadas](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) no como modelos podem ser formação utilizando a limpeza de validação de cruz e hyper-o parâmetro



