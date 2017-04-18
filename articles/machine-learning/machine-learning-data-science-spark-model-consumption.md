<properties
    pageTitle="Modelos de aprendizagem máquina compilado motores de pontuação | Microsoft Azure"
    description="Como a pontuação aprendizagem modelos que tenham sido armazenados no armazenamento de Blobs do Azure (WASB)."
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

# <a name="score-spark-built-machine-learning-models"></a>Modelos de aprendizagem máquina compilado motores de pontuação 

[AZURE.INCLUDE [machine-learning-spark-modeling](../../includes/machine-learning-spark-modeling.md)]

Este tópico descreve como carregar máquina de formação (ML) modelos que foi criados utilizando motores MLlib e armazenadas no armazenamento de Blobs do Azure (WASB) e como pontuação-los com conjuntos de dados que também tenham sido armazenados no WASB. Mostra como os dados de entrada do processo previamente, transformar funcionalidades utilizando as funções de indexação e codificação no MLlib toolkit e como criar um objeto de dados de ponto com nome que pode ser utilizado como entrada para pontuação com os modelos de ML. Os modelos de utilizados para pontuação incluam regressão Linear, regressão logística, aleatório floresta modelos e modelos de árvore aumentar a gradação de cor.


## <a name="prerequisites"></a>Pré-requisitos

1. Precisa de uma conta do Azure e um cluster de necessidade de uma 1.6 de motores de 3.4 HDInsight HDInsight motores que para concluir este tutorial. Consulte o artigo [Descrição geral dos dados de ciência utilizando motores no Azure HDInsight](machine-learning-data-science-spark-overview.md) para obter instruções sobre como de cumprir estes requisitos. Esse tópico também contém uma descrição dos dados de pt 2013 veículo utilizados aqui e instruções sobre como executar código a partir de um bloco de notas Jupyter no cluster motores. **PySpark-machine-learning-data-science-spark-model-consumption.ipynb** bloco de notas que contém os exemplos de código neste tópico está disponível no [Github](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark).

2. Também terá de criar a modelos de modo a ser classificados aqui ao trabalhar através do tópico de [exploração de dados e a modelação com motores](machine-learning-data-science-spark-data-exploration-modeling.md) de aprendizagem automática.   


[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]
 

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Configuração: localizações de armazenamento, bibliotecas e o contexto de motores predefinido

Motores são capaz de leitura e escrita para um Azure armazenamento Blob (WASB). Para que qualquer um dos seus dados existentes armazenados aí podem ser processados utilizando motores e os resultados novamente armazenados no WASB.

Para guardar ficheiros de modelos de ou WASB, o caminho tem de ser especificado corretamente. O contentor predefinido anexado ao cluster motores pode ser referenciado utilizando um caminho que começa com: *"wasb / /"*. O código de exemplo seguinte especifica a localização dos dados serem lidos e o caminho para o diretório de armazenamento de modelo para o qual o resultado de modelo é guardado. 


### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Definir caminhos de diretório para localizações de armazenamento no WASB

Os modelos são guardados no: "wasb: / / / utilizador/remoteuser/NYCTaxi/modelos". Se este caminho não estiver definido corretamente, os modelos não são carregados para pontuação.

Os resultados visita tem sido guardados no: "wasb: / / / utilizador/remoteuser/NYCTaxi/ScoredResults". Se o caminho para a pasta estiver incorreto, os resultados não são guardados nessa pasta.   


>[AZURE.NOTE] As localizações de caminho do ficheiro poderá ser copiadas e coladas os marcadores de posição este código a partir dos resultados da última célula do bloco de notas **machine-learning-data-science-spark-data-exploration-modeling.ipynb** .   


Eis o código para definir caminhos de directório: 

    # LOCATION OF DATA TO BE SCORED (TEST DATA)
    taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";
    
    # SET THE MODEL STORAGE DIRECTORY PATH 
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
    
    # SET SCORDED RESULT DIRECTORY PATH
    # NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
    scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 
    
    # FILE LOCATIONS FOR THE MODELS TO BE SCORED
    logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
    linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
    randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
    randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
    BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
    BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

    # RECORD START TIME
    import datetime
    datetime.datetime.now()

**RESULTADO:**

DateTime.DateTime (2016, 4, 25, 23, 56, 19, 229403)


### <a name="import-libraries"></a>Bibliotecas de importação

Definir o contexto de motores e importar necessárias bibliotecas com o seguinte código

    #IMPORT LIBRARIES
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

Miolo PySpark que é fornecidos com Jupyter blocos de notas têm um contexto predefinido. Por isso, não é necessário definir os motores ou contextos ramo explicitamente antes de começar a trabalhar com a aplicação estão a desenvolver. Estes estão disponíveis para si, por predefinição. Estes contextos são:

- SC - para motores 
- sqlContext - para ramo

O kernel PySpark fornece alguns predefinidas "magics", que são comandos especiais que pode ligar com %. Existem duas essas comandos que são utilizados nestes exemplos de código.

- **% local** Especificar que o código nas linhas subsequentes é executado localmente. Código tem de ser código Python válido.
- **% + o sql<variable name>** 
- Executa uma consulta de ramo contra a sqlContext. Se o parâmetro + o lhe é transmitido, o resultado da consulta persiste na % local contexto Python como um dataframe Pandas.
 

Para obter mais informações sobre kernels para Jupyter blocos de notas e o predefinido "magics" que fornecer, consulte o artigo [Kernels disponíveis para Jupyter blocos de notas com clusters de HDInsight motores Linux no HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Ingerir esta última dados e crie uma moldura limpo dados

Esta secção contém o código de uma série de tarefas necessárias para ingerir esta última os dados a ser classificados. Ler uma amostra de 0,1% associados do veículo viagem e tarifa ficheiro (armazenado como um ficheiro. tsv), formatar os dados e, em seguida, cria uma moldura de dados simples.

Os ficheiros de viagem ou tarifas de veículo foram associados com base em do procedimento previsto na: [o processo de ciências de dados de equipa em ação: utilizar clusters de HDInsight Hadoop](machine-learning-data-science-process-hive-walkthrough.md) tópico.

    # INGEST DATA AND CREATE A CLEANED DATA FRAME

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT FILE FROM PUBLIC BLOB
    taxi_test_file = sc.textFile(taxi_test_file_loc)
    
    # GET SCHEMA OF THE FILE FROM HEADER
    taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)
    
    # PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
    taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
            .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                            float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                            float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))
        
    # GET SCHEMA OF THE FILE FROM HEADER
    schema_string = taxi_test_file.first()
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
    
    # CREATE DATA FRAME
    taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)
    
    # CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
    taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
        .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
        .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
        .drop('direct_distance').drop('surcharge')\
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_cleaned.cache()
    taxi_df_test_cleaned.count()
    
    # REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
    taxi_df_test_cleaned.registerTempTable("taxi_test")
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tempo despendido para executar acima célula: 46.37 segundos


## <a name="prepare-data-for-scoring-in-spark"></a>Preparar dados para motores de pontuação 

Esta secção mostra como índice, codificar e dimensionar funcionalidades categorias para prepará-las para utilização em algoritmos MLlib controlado formação para a classificação e regressão.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funcionalidade transformação: indexar e descodificar funcionalidades categorias para introduzidos modelos para de pontuação 

Esta secção mostra como indexar categorias dados utilizando uma `StringIndexer` e descodificar funcionalidades com `OneHotEncoder` para os modelos de entrada.

O [StringIndexer](http://spark.apache.org/docs/latest/ml-features.html#stringindexer) codifica uma coluna de cadeia de etiquetas para uma coluna de índices de etiqueta. Os índices são ordenados por frequência de etiqueta. 

O [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mapas uma coluna de índices de etiqueta a uma coluna de vectores binários, no máximo um único um valor. Esta codificação permite algoritmos esperava funcionalidades avaliadas contínuas, como regressão logística, para serem aplicados a funcionalidades de categorias.
    
    #INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # LOAD PYSPARK LIBRARIES
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer
    
    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_test 
    """
    taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)
    
    # CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
    taxi_df_test_with_newFeatures.cache()
    taxi_df_test_with_newFeatures.count()
    
    # INDEX AND ONE-HOT ENCODING
    stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
    model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
    indexed = model.transform(taxi_df_test_with_newFeatures)
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
    
    # INDEX AND ENCODE TRAFFIC TIME BINS
    stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
    model = stringIndexer.fit(encoded3)
    indexed = model.transform(encoded3)
    encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
    encodedFinal = encoder.transform(indexed)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tempo despendido para executar acima célula: 5.37 segundos


### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Criar RDD objetos com matrizes funcionalidade introduções para modelos

Esta secção contém código que mostra como indexar dados de texto de categorias como um objeto RDD e quente um codificar para que possam ser utilizado para formar e testar regressão logística MLlib e modelos de árvore. Os dados indexados são armazenados numa objetos [e são distribuídos conjunto de dados (RDD)](http://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . Estes são à produção básica no motores. Um objeto RDD representa uma coleção de imutáveis, com partições dos elementos que pode ser operado em paralelo com motores.

Também contém código que mostra como dimensionar os dados com a `StandardScalar` fornecida pela MLlib para utilização em regressão linear com Stochastic gradação descida (SGD), um algoritmo popular para uma vasta gama de modelos de aprendizagem máquina de formação. O [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) é utilizada para dimensionar as funcionalidades para desvio de unidade. Funcionalidade de dimensionamento, também conhecido como normalização de bases de dados, assegura que funcionalidades com valores amplamente pagos são não dado excessiva avaliar na função objetivo. 


    # CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

    # RECORD START TIME
    timestart = datetime.datetime.now()

    # IMPORT LIBRARIES
    from pyspark.mllib.linalg import Vectors
    from pyspark.mllib.feature import StandardScaler, StandardScalerModel
    from pyspark.mllib.util import MLUtils
    from numpy import array
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingBinary(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC RERESSION MODELS
    def parseRowOneHotBinary(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features
    
    # ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
    def parseRowIndexingRegression(line):
        features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                             line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                             line.trip_distance, line.fare_amount])
        return  features
    
    # INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
    def parseRowOneHotRegression(line):
        features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                            line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                            line.vendorVec.toArray(), line.rateVec.toArray(), 
                                            line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
        return  features

    # FOR BINARY CLASSIFICATION TRAINING AND TESTING
    indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
    oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)
    
    # FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
    indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
    oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)
    
    # SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
    scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
    oneHotTESTregScaled = scaler.transform(oneHotTESTreg)
    
    # CACHE RDDS IN MEMORY
    indexedTESTbinary.cache();
    oneHotTESTbinary.cache();
    indexedTESTreg.cache();
    oneHotTESTreg.cache();
    oneHotTESTregScaled.cache();
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 

**RESULTADO:**

Tempo despendido para executar acima célula: 11.72 segundos


## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Pontuação com o modelo de regressão logísticos e guardar o resultado de blob

O código nesta secção mostra como carregar um modelo de regressão logísticos que tenha sido guardado no armazenamento de Blobs do Azure e utilizá-la para prever ou não uma sugestão é paga numa viagem de veículo, pontuação-lo com métricas de classificação padrão e, em seguida, guardar e desenhar os resultados ao armazenamento blob. Os resultados visita são armazenados em objectos RDD. 


    # SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    # IMPORT LIBRARIES
    from pyspark.mllib.classification import LogisticRegressionModel
    
    ## LOAD SAVED MODEL
    savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
    predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))
    
    ## SAVE SCORED RESULTS (RDD) TO BLOB
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
    dirfilename = scoredResultDir + logisticregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:**

Tempo despendido para executar acima célula: 19.22 segundos


## <a name="score-a-linear-regression-model"></a>Pontuação um modelo de regressão Linear

[LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) é utilizada para formar um modelo de regressão linear utilizando Stochastic gradação descida (SGD) para otimização para prever a quantidade de sugestão paga. 

O código nesta secção mostra como carregar um modelo de regressão Linear a partir do armazenamento de Blobs do Azure, pontuação utilizando variáveis com escala e, em seguida, guarde os resultados novamente para o blob.

    #SCORE LINEAR REGRESSION MODEL

    # RECORD START TIME
    timestart = datetime.datetime.now()
    
    #LOAD LIBRARIES
    from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel
    
    # LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
    savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
    predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
    dirfilename = scoredResultDir + linearregressionfilename;
    predictions.saveAsTextFile(dirfilename)
    
    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 


**RESULTADO:**

Tempo despendido para executar acima célula: 16.63 segundos


## <a name="score-classification-and-regression-random-forest-models"></a>Classificação e regressão aleatório floresta os modelos de pontuação

O código nesta secção mostra como carregar a classificação guardada e regressão aleatório floresta modelos guardado no armazenamento de Blobs do Azure, pontuação seu desempenho com classificador padrão e medidas de regressão e, em seguida, guarde os resultados novamente ao armazenamento blob do.

[Aleatório florestas](http://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) são fatos de árvores de decisão.  Que combinam muitos árvores de decisão para reduzir o risco de overfitting. Aleatórias florestas podem processar funcionalidades categorias, expandir para a definição de classificação multiclass, não requer funcionalidade dimensionamento e podem capturar não modo e funcionalidade interações. Aleatórias florestas são um da modelos de classificação e regressão de aprendizagem automática mais bem sucedida.

[spark.mllib](http://spark.apache.org/mllib/) suporta florestas aleatórias para classificação binária e multiclass e para regressão, utilizar funcionalidades de contínuas e categorias. 

    # SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES 
    from pyspark.mllib.tree import RandomForest, RandomForestModel
    
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
    savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + rfregressionfilename;
    predictions.saveAsTextFile(dirfilename)

    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds";

**RESULTADO:**

Tempo despendido para executar acima célula: 31.07 segundos


## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Classificação e regressão gradação aumentar a árvore de modelos de pontuação

O código nesta secção mostra como carregar classificação de regressão com gradação de cor aumentar a árvore de modelos e a partir do armazenamento de Blobs do Azure, pontuação seu desempenho com classificador padrão e medidas de regressão e, em seguida, guarde os resultados novamente ao armazenamento blob. 

**spark.mllib** suporta GBTs de classificação binária e de regressão, utilizar funcionalidades de contínuas e categorias. 

[Árvores de aumentar a gradação de cor](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são fatos de árvores de decisão. GBTs formar árvores de decisão iterativamente para minimizar uma função de perda. GBTs pode processar funcionalidades categorias, não requer funcionalidade dimensionamento e podem capturar não modo e funcionalidade interações. Também pode ser utilizados numa definição multiclass classificação.


    # SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

    # RECORD START TIME
    timestart = datetime.datetime.now()

    #IMPORT MLLIB LIBRARIES
    from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
    
    # CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    #LOAD AND SCORE THE MODEL
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
    predictions = savedModel.predict(indexedTESTbinary)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btclassificationfilename;
    predictions.saveAsTextFile(dirfilename)
    

    # REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

    # LOAD AND SCORE MODEL 
    savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
    predictions = savedModel.predict(indexedTESTreg)
    
    # SAVE RESULTS
    datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
    btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
    dirfilename = scoredResultDir + btregressionfilename;
    predictions.saveAsTextFile(dirfilename)


    # PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
    timeend = datetime.datetime.now()
    timedelta = round((timeend-timestart).total_seconds(), 2) 
    print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
    
**RESULTADO:**

Tempo despendido para executar acima célula: 14.6 segundos


## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Limpar o objetos de memória e imprimir localizações dos ficheiros de visita

    # UNPERSIST OBJECTS CACHED IN MEMORY
    taxi_df_test_cleaned.unpersist()
    indexedTESTbinary.unpersist();
    oneHotTESTbinary.unpersist();
    indexedTESTreg.unpersist();
    oneHotTESTreg.unpersist();
    oneHotTESTregScaled.unpersist();


    # PRINT OUT PATH TO SCORED OUTPUT FILES
    print "logisticRegFileLoc: " + logisticregressionfilename;
    print "linearRegFileLoc: " + linearregressionfilename;
    print "randomForestClassificationFileLoc: " + rfclassificationfilename;
    print "randomForestRegFileLoc: " + rfregressionfilename;
    print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
    print "BoostedTreeRegressionFileLoc: " + btregressionfilename;


**RESULTADO:**

logisticRegFileLoc: 05-LogisticRegressionWithLBFGS_2016-0317_22_38.953814.txt

linearRegFileLoc: 05-LinearRegressionWithSGD_2016-0317_22_58.878949

randomForestClassificationFileLoc: 05-RandomForestClassification_2016-0317_23_15.939247.txt

randomForestRegFileLoc: 05-RandomForestRegression_2016-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt



## <a name="consume-spark-models-through-a-web-interface"></a>Consumir motores modelos através de uma interface de web

Motores fornecem um mecanismos para submeter remotamente consultas interativas através de uma interface resto ou trabalhos em lotes com um componente chamado Lívio. Lívio está ativado por predefinição no seu cluster de motores de HDInsight. Para obter mais informações sobre Lívio consulte o artigo: [trabalhos de submeter motores remotamente utilizando Lívio](../hdinsight/hdinsight-apache-spark-livy-rest-interface.md). 

Pode utilizar Lívio para remotamente submeter uma tarefa que batch pontuações de um ficheiro que está armazenado num BLOBs do Azure e, em seguida, escreve os resultados para outra blob. Para fazer isto, carregar o script Python partir  
[Github](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) para o blob do cluster motores. Pode utilizar uma ferramenta de como o **Explorador de armazenamento do Microsoft Azure** ou **AzCopy** para copiar o script para o blob cluster. No nosso caso podemos carregado o script para ***wasb:///example/python/ConsumeGBNYCReg.py***.   


>[AZURE.NOTE] As teclas de acesso precisa de pode ser encontrado no portal de para a conta de armazenamento associado ao cluster de motores. 


Depois de carregado para esta localização, este script é executado dentro do cluster de motores num contexto distribuído. Carrega o modelo de e executar previsões em ficheiros de entrada baseados no modelo.  

Pode invocar este script remotamente ao efetuar um pedido HTTPS/resto simple no Lívio.  Eis um comando de Laço para construir o pedido de HTTP para invocar o script Python remotamente. Substitua os valores adequados para o seu cluster de motores CLUSTERLOGIN, CLUSTERPASSWORD, nome de cluster.


    # CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

    curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches

Pode utilizar qualquer idioma no sistema remoto para invocar a tarefa de motores através de Lívio ao efetuar uma chamada HTTPS simple com autenticação básica.   


>[AZURE.NOTE] Seria conveniente utilizar a biblioteca de pedidos de Python quando fazer esta chamada HTTP, mas não está atualmente instalado por predefinição no Azure funções. Para que mais antigas bibliotecas HTTP são utilizadas em vez disso.   


Eis o código de Python para a chamada HTTP:

    #MAKE AN HTTPS CALL ON LIVY. 

    import os

    # OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILBLE BY DEFAULT
    import httplib, urllib, base64
    
    # REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
    host = '<spark cluster name>.azurehdinsight.net:443'
    username='<username>'
    password='<password>'
    
    #AUTHORIZATION
    conn = httplib.HTTPSConnection(host)
    auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
    headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}
    
    # SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
    # IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
    r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
    response = conn.getresponse().read()
    print(response)
    conn.close()


Também pode adicionar este código Python para [Azure funções](https://azure.microsoft.com/documentation/services/functions/) para acionar uma submissão motores de tarefa que as pontuações de um blob com base em vários eventos como um temporizador, criação ou a actualização de um blob. 

Se preferir uma experiência do cliente gratuita código, utilize as [Aplicações do Azure lógica](https://azure.microsoft.com/documentation/services/app-service/logic/) para invocar o lote de motores de pontuação, que define uma ação de HTTP no **Estruturador de fluxos de lógica de aplicações** e definindo respetivos parâmetros. 

- A partir do Azure portal, crie uma nova aplicação de lógica ao selecionar **+ Novo** -> **Web + Mobile** -> **Lógica de aplicação**. 
- Para ativar o **Estruturador de fluxos de lógica de aplicações**, introduza o nome da aplicação de lógica e plano de serviço de aplicação.
- Selecione uma ação de HTTP e introduza os parâmetros de apresentado na figura seguinte:

![](./media/machine-learning-data-science-spark-model-consumption/spark-logica-app-client.png)


## <a name="whats-next"></a>O que se segue? 

**Publicação em validação e hyperparameter de remoção**: consulte o artigo [informações detalhadas de dados e a modelação com motores avançadas](machine-learning-data-science-spark-advanced-data-exploration-modeling.md) no como modelos podem ser formação utilizando a limpeza de validação de cruz e hyper-o parâmetro.
