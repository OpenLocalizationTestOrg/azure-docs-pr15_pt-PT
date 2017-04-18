<properties
    pageTitle="Dados ciência utilizar Scala e motores num Azure | Microsoft Azure"
    description="Como utilizar Scala para as tarefas de aprendizagem máquina controlados com os motores dimensionáveis MLlib e motores ML pacotes num cluster Azure HDInsight motores."  
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
    ms.date="08/01/2016"
    ms.author="bradsev;deguhath"/>


# <a name="data-science-using-scala-and-spark-on-azure"></a>Dados ciência utilizando Scala e motores no Azure

Este artigo mostra-lhe como utilizar Scala para as tarefas de aprendizagem máquina controlados com os motores dimensionáveis MLlib e motores ML pacotes num cluster Azure HDInsight motores. Orienta-o através as tarefas que constituem o [processo de ciências de dados](http://aka.ms/datascienceprocess): ingestão de dados e informações detalhadas, visualização, engenharia de funcionalidade, modelação e consumo de modelo. Os modelos no artigo incluem regressão linear e logístico, florestas aleatórias e árvores aumentado de gradação (GBTs), para além de duas tarefas comuns de aprendizagem máquina controlados:

- Problema de regressão: predição da quantia sugestão ($) para uma viagem de veículo
- Classificação binária: previsão de sugestão ou nenhuma sugestão (1/0) para uma viagem de veículo

O processo de modelação requer formação e avaliação num conjunto de dados de teste e métricas de precisão relevantes. Neste artigo, pode obter como armazenar estes modelos em armazenamento de Blobs do Azure e como pontuação e avaliar o respetivo desempenho aspeto do Office. Este artigo abrange também os tópicos mais avançados da como otimizar os modelos de utilizando a limpeza de validação de cruz e hyper-o parâmetro. Dados utilizados são um exemplo do 2013 pt veículo viagem e tarifa conjunto de dados disponível no GitHub.

[Scala](http://www.scala-lang.org/), um idioma com base na máquina de virtual Java, integra-se conceitos orientada para objectos e funcional idioma. É um idioma dimensionável adequadas para processamento distribuído na nuvem e que é executada no Azure motores clusters.

[Motores](http://spark.apache.org/) é um quadro de processamento de paralelamente abrir origem que suporta o processamento de na memória para aumentar o desempenho das aplicações de análise de dados grande. O motor de processamento de motores baseia-se para a velocidade, facilidade de utilização e a análise de sofisticadas. Capacidades de cálculo distribuído na memória do motores tornam uma boa escolha para algoritmos iterativos nos cálculos máquina de aprendizagem e o gráfico. O pacote de [spark.ml](http://spark.apache.org/docs/latest/ml-guide.html) fornece um conjunto de alto nível APIs incorporados na parte superior de dados molduras que podem ajudar a criar e sintonizar máquina prática aprendizagem tubagens uniforme. [MLlib](http://spark.apache.org/mllib/) é aprendizagem máquina dimensionáveis a biblioteca de motores, que dá capacidades de modelação a este ambiente distribuído.

[Motores de HDInsight](../hdinsight/hdinsight-apache-spark-overview.md) é a oferta alojado no Azure dos motores de abrir origem. Também inclui suporte para Jupyter Scala blocos de notas no cluster motores e pode executar consultas de interativas motores SQL para transformar, filtrar e visualizar dados armazenados num armazenamento de Blobs do Azure. Executar os fragmentos de código Scala neste artigo que fornecem as soluções e mostra as representações relevantes para visualizar os dados nos blocos de notas Jupyter instalados em clusters motores. Os passos de modelação nestes tópicos tem código que mostra como formar, avaliar, guardar e consumir cada tipo de modelo.

Os passos de configuração e código neste artigo são para Azure HDInsight 3.4 motores 1.6. No entanto, o código neste artigo e o [Bloco de notas do Scala Jupyter](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration%20Modeling%20and%20Scoring%20using%20Scala.ipynb) são genérico e deve trabalhar em qualquer cluster de motores. Os passos de configuração e gestão de cluster poderão ser ligeiramente diferentes a partir do que é apresentado neste artigo se não estiver a utilizar os motores de HDInsight.

> [AZURE.NOTE] Para um tópico mostra-lhe como utilizar Python em vez de Scala para concluir tarefas para um processo de ciências de dados de fim para fim, consulte o artigo [Ciência de dados utilizando motores no Azure HDInsight](machine-learning-data-science-spark-overview.md).


## <a name="prerequisites"></a>Pré-requisitos

-   Tem de ter uma subscrição do Azure. Se não já tiver uma, [obter uma versão de avaliação gratuita Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

-   Precisa de um cluster de Azure HDInsight 3.4 motores 1.6 para concluir os seguintes procedimentos. Para criar um cluster, consulte as instruções no artigo [Introdução: criar motores de Apache no Azure HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-spark-sql.md). Defina o tipo de cluster e a versão no menu de **Selecionar o tipo de Cluster** .

![Configuração do tipo de cluster HDInsight](./media/machine-learning-data-science-process-scala-walkthrough/spark-cluster-on-portal.png)


>[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Para obter uma descrição dos dados de viagem de veículo pt e instruções sobre como executar código a partir de um bloco de notas Jupyter no cluster motores, consulte as secções relevantes na [Descrição geral dos dados ciência utilizando motores no Azure HDInsight](machine-learning-data-science-spark-overview.md).  


## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Executar o código de Scala a partir de um bloco de notas Jupyter no cluster motores

Pode iniciar um bloco de notas Jupyter a partir do portal Azure. Localizar o cluster de motores no seu dashboard e, em seguida, clique na mesma para introduzir a página de gestão do seu cluster. Em seguida, clique em **Cluster Dashboards**e, em seguida, clique em **Jupyter bloco de notas** para abrir o bloco de notas associado ao cluster de motores.

![Dashboard de cluster e Jupyter blocos de notas](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-on-portal.png)

Também pode aceder a blocos de notas Jupyter na https://&lt;nome de cluster&gt;.azurehdinsight.net/jupyter. Substitua o *nome de cluster* com o nome do seu cluster. Tem a palavra-passe para a sua conta de administrador aceder os blocos de notas Jupyter.

![Aceda a Jupyter blocos de notas utilizando o nome do cluster](./media/machine-learning-data-science-process-scala-walkthrough/spark-jupyter-notebook.png)

Selecione **Scala** para ver um diretório que tem alguns exemplos de pré-embalados blocos de notas que utilizam a API PySpark. As informações detalhadas de modelação e pontuação utilizar Scala.ipynb bloco de notas que contém os exemplos de código para este conjunto de tópicos motores está disponível no [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).


Pode carregar o bloco de notas diretamente a partir do GitHub o servidor de Jupyter bloco de notas no seu cluster de motores. Na sua página base Jupyter, clique no botão **carregar** . No Explorador de ficheiros, cole o URL de (não processado conteúdo) GitHub do bloco de notas Scala e, em seguida, clique em **Abrir**. O bloco de notas Scala está disponível no seguinte URL:

[Exploration-Modeling-and-Scoring-using-Scala.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Configuração: Contextos motores predefinido e ramo magics motores bibliotecas e motores

### <a name="preset-spark-and-hive-contexts"></a>Contextos motores e Hive predefinidos

    # SET THE START TIME
    import java.util.Calendar
    val beginningTime = Calendar.getInstance().getTime()


Miolo motores que é fornecidos com Jupyter blocos de notas tem de predefinir contextos. Não é necessário definir explicitamente os motores ou contextos ramo antes de começar a trabalhar com a aplicação estão a desenvolver. Os contextos predefinidos são:

- `sc`para SparkContext
- `sqlContext`para HiveContext


### <a name="spark-magics"></a>Motores magics

O kernel motores fornece alguns predefinidas "magics", que são comandos especiais que pode ligar com `%%`. Dois estes comandos são utilizados os exemplos seguintes do código.

- `%%local`Especifica que o código modulares será executado localmente. O código tem de ser código Scala válido.
- `%%sql -o <variable name>`executa uma consulta de ramo contra `sqlContext`. Se o `-o` parâmetro é transmitido, o resultado da consulta persiste na `%%local` Scala contexto como fotograma de dados de motores.

Para obter mais informações sobre kernels para Jupyter blocos de notas e os respetivos predefinido "magics" que pode ligar com `%%` (por exemplo, `%%local`), consulte o artigo [Kernels disponíveis para Jupyter blocos de notas com clusters de HDInsight motores Linux no HDInsight](../hdinsight/hdinsight-apache-spark-jupyter-notebook-kernels.md).


### <a name="import-libraries"></a>Bibliotecas de importação

Importe os motores, MLlib e outras bibliotecas que terá utilizando o código seguinte.

    # IMPORT SPARK AND JAVA LIBRARIES
    import org.apache.spark.sql.SQLContext
    import org.apache.spark.sql.functions._
    import java.text.SimpleDateFormat
    import java.util.Calendar
    import sqlContext.implicits._
    import org.apache.spark.sql.Row

    # IMPORT SPARK SQL FUNCTIONS
    import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
    import org.apache.spark.sql.functions.rand

    # IMPORT SPARK ML FUNCTIONS
    import org.apache.spark.ml.Pipeline
    import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
    import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
    import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
    import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
    import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

    # IMPORT SPARK MLLIB FUNCTIONS
    import org.apache.spark.mllib.linalg.{Vector, Vectors}
    import org.apache.spark.mllib.util.MLUtils
    import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
    import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
    import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
    import org.apache.spark.mllib.tree.configuration.BoostingStrategy
    import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
    import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

    # SPECIFY SQLCONTEXT
    val sqlContext = new SQLContext(sc)


## <a name="data-ingestion"></a>Ingestão de dados

O primeiro passo no processo de ciências de dados é ingerir esta última os dados que pretende analisar. Trazer os dados de origens externas ou sistemas onde reside para o ambiente de informações detalhadas e modelação de dados. Neste artigo, os dados que ingerir esta última são um exemplo de 0,1% associados do veículo viagem e tarifa ficheiro (armazenado como um ficheiro. tsv). O ambiente de informações detalhadas e modelação de dados é motores. Esta secção contém o código para concluir a série de tarefas seguinte:

1. Definir caminhos de diretório para dados e modelo de armazenamento.
2. Leia o conjunto de dados de entrada (armazenados como um ficheiro. tsv).
3. Definir um esquema para os dados e limpar os dados.
4. Crie uma moldura limpo dados e em cache-lo na memória.
5. Registe-se os dados como uma tabela temporária SQLContext.
6. A tabela de consulta e importe os resultados numa moldura dados.


### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Definir caminhos de diretório para localizações de armazenamento no armazenamento de Blobs do Azure

Motores podem ler e escrever ao armazenamento de Blobs do Azure. Pode utilizar motores para processar qualquer um dos seus dados existentes e, em seguida, guarde os resultados novamente armazenamento de Blobs.

Para guardar ficheiros ou de modelos de armazenamento de BLOBs, precisa de especificar corretamente o caminho. Referenciar o contentor predefinido anexado ao cluster motores utilizando um caminho que começa com `wasb:///`. Referenciar outras localizações utilizando `wasb://`.

O código de exemplo seguinte especifica a localização dos dados de entrada serem lidos e o caminho para o armazenamento de Blobs do que está anexado ao cluster de motores onde o modelo será guardado.

    # SET PATHS TO DATA AND MODEL FILE LOCATIONS
    # INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
    val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
    val header = taxi_train_file.first;

    # SET THE MODEL STORAGE DIRECTORY PATH
    # NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
    val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";


### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importar dados, criar uma RDD e definir uma moldura de dados de acordo com o esquema

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
    val sqlContext = new SQLContext(sc)
    val taxi_schema = StructType(
        Array(
            StructField("medallion", StringType, true),
            StructField("hack_license", StringType, true),
            StructField("vendor_id", StringType, true),
            StructField("rate_code", DoubleType, true),
            StructField("store_and_fwd_flag", StringType, true),
            StructField("pickup_datetime", StringType, true),
            StructField("dropoff_datetime", StringType, true),
            StructField("pickup_hour", DoubleType, true),
            StructField("pickup_week", DoubleType, true),
            StructField("weekday", DoubleType, true),
            StructField("passenger_count", DoubleType, true),
            StructField("trip_time_in_secs", DoubleType, true),
            StructField("trip_distance", DoubleType, true),
            StructField("pickup_longitude", DoubleType, true),
            StructField("pickup_latitude", DoubleType, true),
            StructField("dropoff_longitude", DoubleType, true),
            StructField("dropoff_latitude", DoubleType, true),
            StructField("direct_distance", StringType, true),
            StructField("payment_type", StringType, true),
            StructField("fare_amount", DoubleType, true),
            StructField("surcharge", DoubleType, true),
            StructField("mta_tax", DoubleType, true),
            StructField("tip_amount", DoubleType, true),
            StructField("tolls_amount", DoubleType, true),
            StructField("total_amount", DoubleType, true),
            StructField("tipped", DoubleType, true),
            StructField("tip_class", DoubleType, true)
            )
        )

    # CAST VARIABLES ACCORDING TO THE SCHEMA
    val taxi_temp = (taxi_train_file.map(_.split("\t"))
                            .filter((r) => r(0) != "medallion")
                            .map(p => Row(p(0), p(1), p(2),
                                p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                                p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                                p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                                p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


    # CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
    val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

    val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
            .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
            .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
            .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
            .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
            .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
            .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
            .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
            .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

    # CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
    taxi_df_train_cleaned.cache()
    taxi_df_train_cleaned.count()

    # REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
    taxi_df_train_cleaned.registerTempTable("taxi_train")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tempo a ser executada a célula: 8 segundos.


### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Consultar a tabela e importar os resultados numa moldura de dados

Em seguida, a tabela para tarifa, passageiros e dados de sugestão; de consulta filtrar dados danificados e elevar; e imprima várias linhas.

    # QUERY THE DATA
    val sqlStatement = """
        SELECT fare_amount, passenger_count, tip_amount, tipped
        FROM taxi_train
        WHERE passenger_count > 0 AND passenger_count < 7
        AND fare_amount > 0 AND fare_amount < 200
        AND payment_type in ('CSH', 'CRD')
        AND tip_amount > 0 AND tip_amount < 25
    """
    val sqlResultsDF = sqlContext.sql(sqlStatement)

    # SHOW ONLY THE TOP THREE ROWS
    sqlResultsDF.show(3)

**Resultado:**

fare_amount|passenger_count|tip_amount|Inclinado
-----------|---------------|----------|------
       13.5|            1.0|       2.9|   1.0
       16,0|            2.0|       3.4.|   1.0
       10.5|            2.0|       1.0|   1.0


## <a name="data-exploration-and-visualization"></a>Informações detalhadas de dados e de visualização

Depois de trazer os dados para motores, o próximo passo no processo de ciências de dados é obter uma compreensão mais aprofundada de dados através de informações detalhadas e visualização. Nesta secção, examine os dados de veículo utilizando consultas SQL. Importe, em seguida, os resultados para um intervalo de dados para desenhar a variáveis-alvo e potenciais funcionalidades para inspeção visual utilizando a funcionalidade de visualização automática de Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Utilizar o local e mágico SQL para desenhar dados

Por predefinição, o resultado de qualquer fragmento de código que executar a partir de um bloco de notas Jupyter está disponível no contexto da sessão que é mantido em nós de trabalho. Se pretende guardar uma viagem para os nós de trabalho para cada cálculo e, se todos os dados que necessita para o cálculo esteja disponível localmente no nó do servidor Jupyter (que é o nó cabeça), pode utilizar o `%%local` mágico para executar o fragmento de código no servidor Jupyter.

- **Mágico SQL** (`%%sql`). O kernel HDInsight motores suporta inline fácil HiveQL consultas contra SQLContext. O (`-o VARIABLE_NAME`) argumento persistir o resultado da consulta SQL como fotograma de dados de Pandas no servidor Jupyter. Isto significa que estará disponível no modo local.
- `%%local`**mágico**. O `%%local` mágico é executado o código localmente no servidor Jupyter, o que é o nó cabeça do HDInsight cluster. Normalmente, pode utilizar `%%local` mágico em conjunto com o `%%sql` mágico com o `-o` parâmetro. O `-o` parâmetro seria persistirem o resultado da consulta SQL localmente e, em seguida, `%%local` mágico seria acionar o próximo conjunto de fragmento de código para executar o localmente contra o resultado das consultas SQL que é mantido localmente.

### <a name="query-the-data-by-using-sql"></a>Consultar os dados utilizando SQL
Esta consulta obtém as viagens de veículo por valor de tarifa, contar passageiros e valor de sugestão.

    # RUN THE SQL QUERY
    %%sql -q -o sqlResults
    SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25

O código seguinte, o `%%local` mágico cria uma moldura de dados locais, sqlResults. Pode utilizar sqlResults para desenhar ao utilizar o matplotlib.

> [AZURE.TIP] Mágico local é utilizado várias vezes neste artigo. Se o conjunto de dados for grande, fórum de exemplo para criar um pacote de dados que pode ajustar na memória local.

### <a name="plot-the-data"></a>Desenhar os dados

Pode desenhar utilizando o código de Python depois da moldura de dados no local contexto como fotograma de dados de Pandas.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
    sqlResults


 O kernel motores automaticamente apresenta o resultado de consultas SQL (HiveQL) depois de executar o código. Pode escolher entre vários tipos de visualizações:
 
- Tabela
- Gráfico circular
- Linha
- Área
- Barra

Eis o código para desenhar os dados:

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    import matplotlib.pyplot as plt
    %matplotlib inline

    # PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
    ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
    ax1.set_title('Tip amount distribution')
    ax1.set_xlabel('Tip Amount ($)')
    ax1.set_ylabel('Counts')
    plt.suptitle('')
    plt.show()

    # PLOT TIP BY PASSENGER COUNT
    ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
    ax2.set_title('Tip amount by Passenger count')
    ax2.set_xlabel('Passenger count')
    ax2.set_ylabel('Tip Amount ($)')
    plt.suptitle('')
    plt.show()

    # PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
    ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
    ax.set_title('Tip amount by Fare amount')
    ax.set_xlabel('Fare Amount ($)')
    ax.set_ylabel('Tip Amount ($)')
    plt.axis([-2, 80, -2, 20])
    plt.show()


**Resultado:**

![Sugestão quantidade histograma](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-histogram.png)

![Sugestão quantidade pela contagem passageiros](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Sugestão quantidade pelo montante de corrida](./media/machine-learning-data-science-process-scala-walkthrough/plot-tip-amount-by-fare-amount.png)


## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Criar funcionalidades e transformar funcionalidades e, em seguida, preparar os dados de entrada para funções de modelação

Para as funções de modelação de árvore de motores ML e MLlib, tem de preparar o destino e funcionalidades utilizando uma variedade de técnicas, tais como binning, indexação, quente um codificação e vectorization. Aqui estão os procedimentos para seguir nesta secção:

1. Crie uma nova funcionalidade ao **binning** horas para o tráfego tempo registos.
2. Aplica **a indexação e quente um codificação** às funcionalidades de categorias.
3. **Amostra e dividir o conjunto de dados** para frações formação e teste.
4. **Especificar variável de formação e funcionalidades**e, em seguida, criar ou não indexados quente um codificado formação e testar a entrada ponto com nome e são distribuídas conjuntos de dados (RDDs) ou pacotes de dados.
5. Automaticamente **categorizar e vectorize funcionalidades e destinos** para utilizar como entradas do tipo de modelos de aprendizagem automática.


### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Criar uma nova funcionalidade ao horas binning num tráfego tempo registos

Este código mostra-lhe como criar uma nova funcionalidade por binning horas para o tráfego tempo registos e como a moldura de dados resultante na memória em cache. Onde molduras RDDs e os dados são utilizadas repetidamente, colocação em cache oportunidades potenciais para os tempos de execução melhorados. Por conseguinte, irá cache RDDs e molduras de dados em várias fases nos seguintes procedimentos.

    # CREATE FOUR BUCKETS FOR TRAFFIC TIMES
    val sqlStatement = """
        SELECT *,
        CASE
         WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
         WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
         WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
         WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
        END as TrafficTimeBins
        FROM taxi_train
    """
    val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

    # CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
    taxi_df_train_with_newFeatures.cache()
    taxi_df_train_with_newFeatures.count()


### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexação e de quente um codificação de funcionalidades de categorias

A modelação e prever funções de MLlib necessitam de funcionalidades com dados de entrada categorias para ser indexadas ou codificado antes de utilizar. Esta secção mostra-lhe como indexar ou codificar funcionalidades categorias para entrada para as funções de modelação.

Tem de índice remissivo ou codificar os modelos de formas diferentes, consoante o modelo. Por exemplo, modelos de regressão linear e logísticos exigir quente um codificação. Por exemplo, uma funcionalidade com três categorias pode ser expandida por três colunas de funcionalidade. Cada coluna iria conter 0 ou 1, dependendo da categoria de uma observação. MLlib fornece a função [OneHotEncoder](http://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) para codificação de quente um. Este codificador mapas uma coluna de índices de etiqueta a uma coluna de vectores binários no máximo um único um valor. Com esta codificação algoritmos esperava funcionalidades valores numéricas, como regressão logística, podem ser aplicados às funcionalidades de categorias.

Aqui pode transforma apenas quatro variáveis para mostrar exemplos, que são cadeias de caracteres. Também pode indexar outras variáveis, como o dia da semana, representados por valores numéricos, como variáveis de categorias.

Para a indexação, utilizar `StringIndexer()`e para codificação de uma-quente, utilizar `OneHotEncoder()` funções a partir do MLlib. Eis o código para indexar e descodificar funcionalidades categorias:


    # CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # INDEX AND ENCODE VENDOR_ID
    val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
    val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
    val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
    val encoded1 = encoder.transform(indexed)

    # INDEX AND ENCODE RATE_CODE
    val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
    val indexed = stringIndexer.transform(encoded1)
    val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
    val encoded2 = encoder.transform(indexed)

    # INDEX AND ENCODE PAYMENT_TYPE
    val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
    val indexed = stringIndexer.transform(encoded2)
    val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
    val encoded3 = encoder.transform(indexed)

    # INDEX AND TRAFFIC TIME BINS
    val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
    val indexed = stringIndexer.transform(encoded3)
    val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
    val encodedFinal = encoder.transform(indexed)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tempo a ser executada a célula: 4 segundos.



### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Amostra e dividir o conjunto de dados para frações formação e teste

Este código cria uma sondagem dos dados (25%, neste exemplo). Apesar de amostragem não é necessária para deste exemplo devido ao tamanho do conjunto de dados, o artigo mostra-lhe como pode exemplo, de modo a que sabe como utilizá-lo para os seus próprios problemas quando for necessário. Quando são grandes amostras, isto pode poupar tempo significativo enquanto formar o modelos. Em seguida, divida a amostra de uma peça de formação (75%, neste exemplo) e uma peça de teste (25%, neste exemplo) para utilizar nas classificação e modelação de regressão.

Adicione um número aleatório (entre 0 e 1) para cada linha (numa coluna de "aleatório") que pode ser utilizada para selecionar validação cruzada dobras durante a formação.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # SPECIFY SAMPLING AND SPLITTING FRACTIONS
    val samplingFraction = 0.25;
    val trainingFraction = 0.75;
    val testingFraction = (1-trainingFraction);
    val seed = 1234;
    val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
    val sampledDFcount = encodedFinalSampledTmp.count().toInt

    val generateRandomDouble = udf(() => {
        scala.util.Random.nextDouble
    })

    # ADD A RANDOM NUMBER FOR CROSS-VALIDATION
    val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

    # SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
    # INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
    val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
    val trainData = splits(0)
    val testData = splits(1)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tempo a ser executada a célula: 2 segundos.


### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Especificar variável de formação e funcionalidades e, em seguida, criar ou não indexados quente um codificado formação e testes denominada molduras RDDs ponto ou dados de entrada

Esta secção contém código que mostra-lhe como indexar dados de texto de categorias como um tipo de dados de ponto com nome e descodificá-lo para utilizá-lo para formar e testar regressão logística MLlib e outros modelos de classificação. Objetos de ponto com nome são RDDs que estão formatadas de uma forma que é necessária como dados de entrada pela maioria dos algoritmos na MLlib de aprendizagem automática. Um [ponto de rótulo](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) é um local vector denso ou disperso, associados a uma etiqueta/resposta.

Este código, pode especificar a variável-alvo (dependentes) e as funcionalidades para utilizar para formar modelos. Em seguida, criar ou não indexados quente um codificado formação e testes denominada molduras RDDs ponto ou dados de entrada.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
    val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
    val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

    # SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
    val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
    val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

    # CREATE INDEXED LABELED POINT RDD OBJECTS
    val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
    val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

    # CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
    val indexedTESTbinaryDF = indexedTESTbinary.toDF()
    val indexedTRAINregDF = indexedTRAINreg.toDF()
    val indexedTESTregDF = indexedTESTreg.toDF()

    # CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
    val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
    val OneHotTRAIN = assemblerOneHot.transform(trainData)
    val OneHotTEST = assemblerOneHot.transform(testData)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tempo a ser executada a célula: 4 segundos.


### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Categorizar e vectorize funcionalidades e destinos para utilizar como entradas do tipo de modelos de aprendizagem automática automaticamente

Utilize motores ML para categorizar funcionalidades para utilizar nas funções de modelação de árvore e de destino. O código conclui duas tarefas:

-   Cria um alvo binário para classificação, atribuindo um valor de 0 ou 1 a cada ponto de dados entre 0 e 1 ao utilizar um valor limite de 0,5.
- Categoriza automaticamente funcionalidades. Se o número de valores numéricos distintos para qualquer funcionalidade for inferior a 32, essa funcionalidade estiver categorizada.

Eis o código para estas duas tarefas.

    # CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
    val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
    val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

    # CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
    # CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

    # TRAIN DATA
    val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
    val indexerModel = indexer.fit(indexedTRAINregDF)
    val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

    # TEST DATA
    val indexerModel = indexer.fit(indexedTESTbinaryDF)
    val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)



## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Modelo de classificação binário: prever se deve ser paga uma sugestão

Nesta secção, crie três tipos de modelos de classificação binário para prever ou não deve ser paga uma sugestão:

- Um **modelo de regressão logísticos** utilizando os motores ML `LogisticRegression()` função
- Um **modelo de classificação floresta aleatório** utilizando os motores ML `RandomForestClassifier()` função
- Uma **gradação de cor modelo de classificação árvore aumentar** utilizando o MLlib `GradientBoostedTrees()` função

### <a name="create-a-logistic-regression-model"></a>Criar um modelo de regressão logística

Em seguida, criar um modelo de regressão logísticos utilizando os motores ML `LogisticRegression()` função. Criar o modelo de criação de código numa série de passos:

1. Definir **comboio o modelo de** dados com um parâmetro.
2. **Avaliar o modelo** num conjunto de dados de teste com métricas.
3. **Guarde o modelo** no armazenamento de BLOBs para consumo futuro.
4. **O modelo de pontuação** relativamente a dados de teste.
5. **Desenhe os resultados** com recetor operativo curvas característica (ROC).

Eis o código para que estes procedimentos:

    # CREATE A LOGISTIC REGRESSION MODEL
    val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
    val lrModel = lr.fit(OneHotTRAIN)

    # PREDICT ON THE TEST DATA SET
    val predictions = lrModel.transform(OneHotTEST)

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)

    # SAVE THE MODEL
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LogisticRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

Carregar, pontuação e guarde os resultados.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
    val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON THE TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
    predictions.registerTempTable("testResults")

    # SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC RESULTS
    println("ROC on test data = " + ROC)


**Resultado:**

ROC nos dados de teste = 0.9827381497557599


Utilize Python no locais molduras de dados de Pandas para Traçar a curva ROC.


    # QUERY THE RESULTS
    %%sql -q -o sqlResults
    SELECT tipped, probability from testResults


    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    %matplotlib inline
    from sklearn.metrics import roc_curve,auc

    sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
    predictions_pddf = sqlResults[["tipped","probFloat"]]

    # PREDICT THE ROC CURVE
    # predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
    prob = predictions_pddf["probFloat"]
    fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
    roc_auc = auc(fpr, tpr)

    # PLOT THE ROC CURVE
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


**Resultado:**

![Sugestão ou sem ROC uma sugestão curva](./media/machine-learning-data-science-process-scala-walkthrough/plot-roc-curve-tip-or-not.png)


### <a name="create-a-random-forest-classification-model"></a>Criar um modelo de classificação aleatório floresta

Em seguida, criar um modelo de classificação floresta aleatório utilizando os motores ML `RandomForestClassifier()` funcionar e, em seguida, avalie o modelo de dados de teste.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE THE RANDOM FOREST CLASSIFIER MODEL
    val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

    # FIT THE MODEL
    val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
    val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

    # EVALUATE THE MODEL
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(predictions)
    println("F1 score on test data: " + Test_f1Score);

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
    val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
    val ROC = evaluator.evaluate(predictions)
    println("ROC on test data = " + ROC)


**Resultado:**

ROC nos dados de teste = 0.9847103571552683


### <a name="create-a-gbt-classification-model"></a>Criar um modelo de classificação GBT

Em seguida, criar um modelo de classificação GBT através do MLlib `GradientBoostedTrees()` funcionar e, em seguida, avalie o modelo de dados de teste.


    # TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE GBT CLASSIFICATION MODEL
    val boostingStrategy = BoostingStrategy.defaultParams("Classification")
    boostingStrategy.numIterations = 20
    boostingStrategy.treeStrategy.numClasses = 2
    boostingStrategy.treeStrategy.maxDepth = 5
    boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    # TRAIN THE MODEL
    val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

    # SAVE THE MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "GBT_Classification__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    gbtModel.save(sc, filename);

    # EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
    val labelAndPreds = indexedTESTbinary.map { point =>
      val prediction = gbtModel.predict(point.features)
      (point.label, prediction)
    }
    val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
    //println("Learned classification GBT model:\n" + gbtModel.toDebugString)
    println("Test Error = " + testErr)

    # USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
    val metrics = new MulticlassMetrics(labelAndPreds)
    println(s"Precision: ${metrics.precision}")
    println(s"Recall: ${metrics.recall}")
    println(s"F1 Score: ${metrics.fMeasure}")

    val metrics = new BinaryClassificationMetrics(labelAndPreds)
    println(s"Area under PR curve: ${metrics.areaUnderPR}")
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE ROC METRIC
    println(s"Area under ROC curve: ${metrics.areaUnderROC}")


**Resultado:**

Área abaixo ROC curva: 0.9846895479241554


## <a name="regression-model-predict-tip-amount"></a>Modelo de regressão: quantidade de sugestão de previsão

Nesta secção, pode cria dois tipos de modelos de regressão para prever a quantidade de sugestão:

- Um **modelo de regressão linear de regularizado** utilizando os motores ML `LinearRegression()` função. Irá guardar o modelo e avaliar o modelo de dados de teste.
- Um **modelo de regressão árvore aumentar com gradação de cores** , utilizando os motores ML `GBTRegressor()` função.


### <a name="create-a-regularized-linear-regression-model"></a>Criar um modelo de regressão linear regularizado

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
    val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

    # FIT THE MODEL BY USING DATA FRAMES
    val lrModel = lr.fit(OneHotTRAIN)
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
    val trainingSummary = lrModel.summary
    println(s"numIterations: ${trainingSummary.totalIterations}")
    println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
    trainingSummary.residuals.show()
    println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
    println(s"r2: ${trainingSummary.r2}")

    # SAVE THE MODEL IN AZURE BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "LinearRegression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    lrModel.save(filename);

    # PRINT THE COEFFICIENTS
    println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = lrModel.transform(OneHotTEST)

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tempo a ser executada a célula: 13 segundos.


    # LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
    val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
    println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

    # SCORE THE MODEL ON TEST DATA
    val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
    predictions.registerTempTable("testResults")

    # EVALUATE THE MODEL ON TEST DATA
    val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
    val r2 = evaluator.evaluate(predictions)
    println("R-sqr on test data = " + r2)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("R-sqr on test data = " + r2)


**Resultado:**

R-sqr nos dados de teste = 0.5960320470835743


Em seguida, os resultados dos testes de consulta como fotograma de dados e utilize AutoVizWidget e matplotlib para compreendê-lo.


    # RUN A SQL QUERY
    %%sql -q -o sqlResults
    select * from testResults

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER
    %%local

    # USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
    # CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
    sqlResults

O código cria uma moldura de dados locais a partir do resultado da consulta e desenha os dados. O `%%local` mágico cria uma moldura de dados locais, `sqlResults`, que pode utilizar para representar com matplotlib.

>[AZURE.NOTE] Este mágico motores é utilizado várias vezes neste artigo. Se a quantidade de dados for grande, deverá de exemplo para criar um pacote de dados que pode ajustar na memória local.

Crie representações utilizando Python matplotlib.

    # RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
    %%local
    sqlResults
    %matplotlib inline
    import numpy as np

    # PLOT THE RESULTS
    ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
    fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
    ax.set_title('Actual vs. Predicted Tip Amounts ($)')
    ax.set_xlabel("Actual")
    ax.set_ylabel("Predicted")
    #ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
    plt.axis([-1, 15, -1, 8])
    plt.show(ax)

**Resultado:**

![Dica quantidade: real vs. previstas](./media/machine-learning-data-science-process-scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)


### <a name="create-a-gbt-regression-model"></a>Criar um modelo de regressão GBT

Criar um modelo de regressão GBT utilizando os motores ML `GBTRegressor()` funcionar e, em seguida, avalie o modelo de dados de teste.

[Árvores aumentado com gradação de cores](http://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTs) são fatos de árvores de decisão. GBTs formar árvores de decisão iterativamente para minimizar uma função de perda. Pode utilizar GBTs para regressão e classificação. Pode gerir funcionalidades de categorias, não requer o dimensionamento da funcionalidade e pode capturar nonlinearities e interações de funcionalidade. Também pode utilizá-los numa definição multiclass classificação.


    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # TRAIN A GBT REGRESSION MODEL
    val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
    val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

    # MAKE PREDICTIONS
    val predictions = gbtModel.transform(indexedTESTwithCatFeat)

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(predictions)


    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    # PRINT THE RESULTS
    println("Test R-sqr is: " + Test_R2);


**Resultado:**

Teste R-sqr é: 0.7655383534596654



## <a name="advanced-modeling-utilities-for-optimization"></a>Utilitários de modelação avançada para Otimização

Nesta secção, utilize utilitários de formação de máquina que os programadores utilizam com frequência para optimização do modelo. Especificamente, o utilizador pode otimizar os modelos de aprendizagem máquina três formas diferentes utilizando a limpeza do parâmetro e validação cruzada:

-   Dividir os dados em conjuntos de comboio e validação, otimizar o modelo ao utilizar a limpeza de hyper-o parâmetro num conjunto de formação e avaliar num conjunto de validação (regressão linear)
-   Otimizar o modelo utilizando a validação de cruz e hyper-o parâmetro de remoção através CrossValidator função do motores ML (binária classificação)
-   Otimizar o modelo ao utilizar o código de validação de cruz e limpeza de parâmetro personalizado para utilizar qualquer função e parâmetro conjunto (regressão linear) de aprendizagem automática


**Validação cruzada** é uma técnica que avalia bem como um modelo de formação num conjunto de dados conhecido irá generalizar para prever as funcionalidades de conjuntos de dados no qual-não foi preparado. A ideia geral atrás esta técnica é que um modelo é formação num conjunto de dados de dados conhecidos e, em seguida, a precisão do seus previsões é testada contra um conjunto de dados independente. Uma implementação comum é dividir um conjunto de dados em *k*-dobras e, em seguida, formar o modelo de um modo round-robin em todas as mas um das dobras.

**Otimização Hyper-o parâmetro** é o problema de escolher um conjunto de hyper parâmetros para um algoritmo de formação, normalmente com o objectivo de optimizar uma medida de desempenho o algoritmo num conjunto de dados independente. Um hyper-o parâmetro é um valor que tem de especificar fora do procedimento de formação do modelo. Pressupostos sobre hyper-o parâmetro valores podem afetar a flexibilidade e a precisão do modelo. Árvores de decisão tem hyper-parâmetros, por exemplo, como a profundidade pretendida e o número de folhas na árvore do. Tem de definir um termo de sanções classificação para uma máquina de vetor suporte (SVM).

Uma forma comum para efetuar otimização hyper-o parâmetro é utilizar uma pesquisa de grelha, também denominada uma **varrer parâmetro**. Na pesquisa de grelha, uma pesquisa exaustiva é executada através dos valores de um subconjunto do espaço para um algoritmo de aprendizagem hyper-o parâmetro especificado. Validação cruzada pode fornecer uma métrica de desempenho para ordenar os resultados ideais produzidos pelo algoritmo de pesquisa de grelha. Se utilizar limpeza de hyper-o parâmetro de validação de cruz, pode ajudar a problemas de limite como overfitting um modelo de dados de formação. Desta forma, o modelo de mantém a capacidade para aplicar o conjunto de dados a partir do qual foram extraídos os dados de formação geral.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Otimizar um modelo de regressão linear com hyper-o parâmetro limpeza

Em seguida, dividir dados em conjuntos de comboio e validação, utilizar hyper-o parâmetro de remoção num conjunto de formação para otimizar o modelo e avaliar num conjunto de validação (regressão linear).

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # RENAME `tip_amount` AS A LABEL
    val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
    OneHotTRAINLabeled.cache()
    OneHotTESTLabeled.cache()

    # DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
    val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

    # DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
    val trainPct = 0.75
    val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = trainValidationSplit.fit(OneHotTRAINLabeled)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

    # COMPUTE TEST SET R2
    val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
    val Test_R2 = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");

    println("Test R-sqr is: " + Test_R2);


**Resultado:**

Teste R-sqr é: 0.6226484708501209


### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Otimizar o modelo de classificação binário utilizando a limpeza de validação de cruz e hyper-o parâmetro

Esta secção mostra-lhe como otimizar um modelo de classificação binário utilizando a limpeza de validação de cruz e hyper-o parâmetro. Esta opção utiliza os motores ML `CrossValidator` função.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
    val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
    indexedTRAINwithCatFeatBinTargetRF.cache()
    indexedTESTwithCatFeatBinTargetRF.cache()

    # DEFINE THE ESTIMATOR FUNCTION
    val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

    # DEFINE THE PARAMETER GRID
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

    # SPECIFY THE NUMBER OF FOLDS
    val numFolds = 3

    # DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
    val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

    # RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
    val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

    # MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
    val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

    # COMPUTE THE TEST F1 SCORE
    val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
    val Test_f1Score = evaluator.evaluate(testResults)

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


**Resultado:**

Tempo a ser executada a célula: 33 segundos.


### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Otimizar o modelo de regressão linear utilizando o código de validação de cruz e limpeza de parâmetro personalizado

Em seguida, otimizar o modelo utilizando código personalizado e identificar os parâmetros de modelo melhor ao utilizar o critério de precisão mais alto. Em seguida, crie o modelo de final, avaliar o modelo de dados de teste e guarde o modelo de armazenamento de Blobs. Por fim, carregar o modelo, pontuação dados de teste e avaliar precisão.

    # RECORD THE START TIME
    val starttime = Calendar.getInstance().getTime()

    # DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
    val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

    val nFolds = 3
    val numModels = paramGrid.size
    val numParamsinGrid = 2

    # SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
    val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

    var maxDepth = -1
    var numTrees = -1
    var param = ""
    var paramval = -1
    var validateLB = -1.0
    var validateUB = -1.0
    val h = 1.0 / nFolds;
    val RMSE  = Array.fill(numModels)(0.0)

    # CREATE K-FOLDS
    val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


    # LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
    for (i <- 0 to (nFolds-1)) {
        validateLB = i * h
        validateUB = (i + 1) * h
        val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
        val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
        val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
        validationLabPt.cache()
        trainCVLabPt.cache()

        for (nParamSets <- 0 to (numModels-1)) {
            for (nParams <- 0 to (numParamsinGrid-1)) {
                param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
                paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
                if (param == "maxDepth") {maxDepth = paramval}
                if (param == "numTrees") {numTrees = paramval}
            }
            val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=numTrees, maxDepth=maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)
            val labelAndPreds = validationLabPt.map { point =>
                                                     val prediction = rfModel.predict(point.features)
                                                     ( prediction, point.label )
                                                    }
            val validMetrics = new RegressionMetrics(labelAndPreds)
            val rmse = validMetrics.rootMeanSquaredError
            RMSE(nParamSets) += rmse
        }
        validationLabPt.unpersist();
        trainCVLabPt.unpersist();
    }
    val minRMSEindex = RMSE.indexOf(RMSE.min)

    # GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
    var best_maxDepth = -1
    var best_numTrees = -1
    for (nParams <- 0 to (numParamsinGrid-1)) {
        param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
        paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
        if (param == "maxDepth") {best_maxDepth = paramval}
        if (param == "numTrees") {best_numTrees = paramval}
    }

    # CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
    val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                      numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                      featureSubsetStrategy="auto",impurity="variance", maxBins=32)

    # SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
    val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
    val modelName = "BestCV_RF_Regression__"
    val filename = modelDir.concat(modelName).concat(datestamp)
    best_rfModel.save(sc, filename);

    # PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = best_rfModel.predict(point.features)
                                            ( prediction, point.label )
                                           }

    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2

    # GET THE TIME TO RUN THE CELL
    val endtime = Calendar.getInstance().getTime()
    val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
    println("Time taken to run the above cell: " + elapsedtime + " seconds.");


    # LOAD THE MODEL
    val savedRFModel = RandomForestModel.load(sc, filename)

    val labelAndPreds = indexedTESTreg.map { point =>
                                            val prediction = savedRFModel.predict(point.features)
                                            ( prediction, point.label )
                                           }
    # TEST THE MODEL
    val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
    val test_rsqr = new RegressionMetrics(labelAndPreds).r2


**Resultado:**

Tempo a ser executada a célula: 61 segundos.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Consumir modelos de aprendizagem compilado motores máquina automaticamente com Scala

Para obter uma descrição geral de tópicos guiá-lo as tarefas que compõem o processo de ciências de dados no Azure, consulte o artigo [O processo de ciências de dados de equipa](http://aka.ms/datascienceprocess).

[Tutoriais do processo de ciências de dados de equipa](data-science-process-walkthroughs.md) descreve outros tutoriais de ponto a ponto que demonstram os passos do processo de ciências de dados de equipa para cenários específicos. Os tutoriais ilustram também como combinar as ferramentas na nuvem e no local e serviços num fluxo de trabalho ou em curso para criar uma aplicação inteligente.

[Pontuação compilado motores máquina modelos de formação](machine-learning-data-science-spark-model-consumption.md) mostra-lhe como utilizar o código de Scala para carregar e pontuação novos conjuntos de dados com os modelos de aprendizagem máquina integrado motores e guardados numa armazenamento de Blobs do Azure automaticamente. Pode siga as instruções fornecidas aí e substituir simplesmente o código de Python com código Scala neste artigo para consumo automatizado.
