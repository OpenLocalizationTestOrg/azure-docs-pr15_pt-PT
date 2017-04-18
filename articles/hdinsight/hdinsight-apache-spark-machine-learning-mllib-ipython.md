<properties 
    pageTitle="Utilizar Apache motores para criar aplicações de formação de máquina no HDInsight | Microsoft Azure" 
    description="Instruções passo a passo sobre como utilizar blocos de notas com Apache motores para criar aplicações de aprendizagem automática" 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"
    tags="azure-portal"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="machine-learning-predictive-analysis-on-food-inspection-data-using-mllib-with-apache-spark-cluster-on-hdinsight-linux"></a>Formação de máquina: análise de previsão no inspeção de alimentação dados utilizando MLlib com motores de Apache cluster no HDInsight Linux

> [AZURE.TIP] Neste tutorial também está disponível como um bloco de notas Jupyter num cluster de motores (Linux) que criar no HDInsight. A experiência de bloco de notas permite-lhe executar os fragmentos Python a partir do bloco de notas. Para executar o tutorial a partir de um bloco de notas, crie um cluster de motores, iniciar um bloco de notas Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), e, em seguida, execute o bloco de notas **motores máquina aprender - nos dados da inspeção de alimentação de análise de previsão com MLLib.ipynb** na pasta **Python** .


Este artigo demonstra como utilizar **MLLib**, incorporada aprendizagem automática do motores bibliotecas, para efetuar uma análise de aspeto do Office simple num conjunto de dados aberto. MLLib é uma biblioteca de motores de core que fornece um número de utilitários são úteis para tarefas de formação de máquina, incluindo utilitários são adequados para:

* Classificação

* Regressão

* Clusters

* Modelação de tópico

* Análise de componente principal (PCA) e decomposição valor no singular (DVS)

* Hipótese de testes e calcular estatísticas de exemplo

Este artigo apresenta uma abordagem simples para *classificação* através de regressão logística.

## <a name="what-are-classification-and-logistic-regression"></a>O que são classificação e regressão logística?

*Classificação*, uma tarefa, de aprendizagem automática muito comum é o processo de ordenar os dados de entrada em categorias. É a tarefa de um algoritmo de classificação descobrir como atribuir "etiquetas" para dados que fornece de entrada. Por exemplo, poderia pensar do algoritmo de aprendizagem máquina que aceita cotações informações como entrada e divide as ações em duas categorias: ações que deve vende e unidades populacionais qual deve a manter.

Regressão logística é o algoritmo de que utiliza para a classificação. Regressão logística do motores API é útil para *classificação binária*ou classificar dados de entrada para um dos dois grupos. Para mais informações sobre as regressões logísticos, consulte o artigo [Wikipédia](https://en.wikipedia.org/wiki/Logistic_regression).

Em resumo, o processo de regressão logística gera uma *função logística* que podem ser utilizados para prever a probabilidade que uma entrada vector pertence um grupo ou a outra.  

## <a name="what-are-we-trying-to-accomplish-in-this-article"></a>O que está estamos a tentar efetuar neste artigo?

Irá utilizar motores para efetuar algumas análise de aspeto do Office nos dados da inspeção de alimentação (**Food_Inspections1.csv**) foram adquiridos através do [portal de dados cidade de Chicago](https://data.cityofchicago.org/). Este conjunto de dados contém informações sobre os controlos de alimentação que foram realizados em Chicago, incluindo informações sobre cada estabelecimento de alimentação foi seja inspecionado, violações que foram encontradas (se existir) e os resultados do controlo. O ficheiro de dados CSV já está disponível na conta de armazenamento associada ao cluster no **/HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv**.

Nos passos abaixo, desenvolver um modelo para ver o que demora para passar ou falhar um controlo de alimentação. 

## <a name="start-building-a-machine-learning-application-using-spark-mllib"></a>Começar a construir uma aplicação de formação de máquina utilizando motores MLlib

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do startboard, clique no mosaico do seu cluster de motores (se afixados-lo para a startboard). Também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Pá de cluster motores, clique em **Cluster Dashboard**e, em seguida, clique em **Jupyter bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Jupyter para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas. Clique em **Novo**e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de notas Jupyter")

3. Um novo bloco de notas é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de notas")

3. Uma vez que criou um bloco de notas utilizando o kernel PySpark, não necessita de criar qualquer contextos explicitamente. Os contextos motores e ramo serão criados automaticamente para si quando executa a primeira célula de código. Pode começar a construir a sua aplicação de aprendizagem importando os tipos de necessários para este cenário de automática. Para fazê-lo, coloque o cursor na célula e prima **SHIFT + ENTER**.


        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        from pyspark.sql.functions import UserDefinedFunction
        from pyspark.sql.types import *

## <a name="construct-an-input-dataframe"></a>Construir uma entrada dataframe

Pode utilizamos `sqlContext` para desempenhar transformações em dados estruturados. É a primeira tarefa carregar os dados de exemplo ((**Food_Inspections1.csv**)) para um motores SQL *dataframe*. 

1. Uma vez que os dados não processados estão num formato CSV, precisamos de utilizar o contexto de motores de separar cada linha do ficheiro na memória como texto não estruturado; em seguida, utilize biblioteca CSV do Python para analisar individualmente cada linha. 


        def csvParse(s):
            import csv
            from StringIO import StringIO
            sio = StringIO(s)
            value = csv.reader(sio).next()
            sio.close()
            return value
        
        inspections = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections1.csv')\
                        .map(csvParse)


2. Temos agora o ficheiro CSV como um RDD. Obter diga-numa linha de RDD para compreender o esquema dos dados.


        inspections.take(1)


    Deverá visualizar um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [['413707',
          'LUNA PARK INC',
          'LUNA PARK  DAY CARE',
          '2049789',
          "Children's Services Facility",
          'Risk 1 (High)',
          '3250 W FOSTER AVE ',
          'CHICAGO',
          'IL',
          '60625',
          '09/21/2010',
          'License-Task Force',
          'Fail',
          '24. DISH WASHING FACILITIES: PROPERLY DESIGNED, CONSTRUCTED, MAINTAINED, INSTALLED, LOCATED AND OPERATED - Comments: All dishwashing machines must be of a type that complies with all requirements of the plumbing section of the Municipal Code of Chicago and Rules and Regulation of the Board of Health. OBSEVERD THE 3 COMPARTMENT SINK BACKING UP INTO THE 1ST AND 2ND COMPARTMENT WITH CLEAR WATER AND SLOWLY DRAINING OUT. INST NEED HAVE IT REPAIR. CITATION ISSUED, SERIOUS VIOLATION 7-38-030 H000062369-10 COURT DATE 10-28-10 TIME 1 P.M. ROOM 107 400 W. SURPERIOR. | 36. LIGHTING: REQUIRED MINIMUM FOOT-CANDLES OF LIGHT PROVIDED, FIXTURES SHIELDED - Comments: Shielding to protect against broken glass falling into food shall be provided for all artificial lighting sources in preparation, service, and display facilities. LIGHT SHIELD ARE MISSING UNDER HOOD OF  COOKING EQUIPMENT AND NEED TO REPLACE LIGHT UNDER UNIT. 4 LIGHTS ARE OUT IN THE REAR CHILDREN AREA,IN THE KINDERGARDEN CLASS ROOM. 2 LIGHT ARE OUT EAST REAR, LIGHT FRONT WEST ROOM. NEED TO REPLACE ALL LIGHT THAT ARE NOT WORKING. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned. MISSING CEILING TILES WITH STAINS IN WEST,EAST, IN FRONT AREA WEST, AND BY THE 15MOS AREA. NEED TO BE REPLACED. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair. SPLASH GUARDED ARE NEEDED BY THE EXPOSED HAND SINK IN THE KITCHEN AREA | 34. FLOORS: CONSTRUCTED PER CODE, CLEANED, GOOD REPAIR, COVING INSTALLED, DUST-LESS CLEANING METHODS USED - Comments: The floors shall be constructed per code, be smooth and easily cleaned, and be kept clean and in good repair. INST NEED TO ELEVATE ALL FOOD ITEMS 6INCH OFF THE FLOOR 6 INCH AWAY FORM WALL.  ',
          '41.97583445690982',
          '-87.7107455232781',
          '(41.97583445690982, -87.7107455232781)']]


3. O resultado acima dá-numa ideia do esquema do ficheiro de entrada o ficheiro inclui o nome de cada estabelecimento, o tipo de estabelecimento, o endereço, os dados de controlos e a localização, entre outras coisas. Ao selecionar colunas alguns que será útil para a nossa análise aspeto do Office e agrupar os resultados como um dataframe que utilizamos, em seguida, para criar uma tabela temporária.


        schema = StructType([
        StructField("id", IntegerType(), False), 
        StructField("name", StringType(), False), 
        StructField("results", StringType(), False), 
        StructField("violations", StringType(), True)])

        df = sqlContext.createDataFrame(inspections.map(lambda l: (int(l[0]), l[1], l[12], l[13])) , schema)
        df.registerTempTable('CountResults')

4. Agora, temos uma *dataframe*, `df` no qual podemos pode executar a nossa análise. Também temos uma tabela temporária **CountResults**de chamadas. Vamos incluiu 4 colunas de interesse na dataframe: **id**, **nome**, **os resultados**e **violações**. 
    
    Vamos obter uma pequena amostra dos dados:

        df.show(5)

    Deverá visualizar um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        +------+--------------------+-------+--------------------+
        |    id|                name|results|          violations|
        +------+--------------------+-------+--------------------+
        |413707|       LUNA PARK INC|   Fail|24. DISH WASHING ...|
        |391234|       CAFE SELMARIE|   Fail|2. FACILITIES TO ...|
        |413751|          MANCHU WOK|   Pass|33. FOOD AND NON-...|
        |413708|BENCHMARK HOSPITA...|   Pass|                    |
        |413722|           JJ BURGER|   Pass|                    |
        +------+--------------------+-------+--------------------+

## <a name="understand-the-data"></a>Compreender os dados

1. Vamos começar ter uma ideia do que contém os nossos conjunto de dados. Por exemplo, o que são os diferentes valores na coluna **resultados** ?


        df.select('results').distinct().show()

    
    Deverá visualizar um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        +--------------------+
        |             results|
        +--------------------+
        |                Fail|
        |Business Not Located|
        |                Pass|
        |  Pass w/ Conditions|
        |     Out of Business|
        +--------------------+
    
2. Uma visualização rápida pode Ajude-nos motivo sobre a distribuição destes resultados. Temos já os dados numa tabela temporário **CountResults**. Pode executar a seguinte consulta SQL contra a tabela para obter uma melhor compreensão da forma como os resultados são distribuídos.

        %%sql -o countResultsdf
        SELECT results, COUNT(results) AS cnt FROM CountResults GROUP BY results

    O `%%sql` mágico seguido `-o countResultsdf` assegura que o resultado da consulta é mantido localmente no servidor Jupyter (normalmente a headnode do cluster). O resultado é mantido como um dataframe [Pandas](http://pandas.pydata.org/) com o nome especificado **countResultsdf**.
    
    Deverá visualizar um resultado semelhante ao seguinte:
    
    ![Resultado da consulta SQL] (./media/hdinsight-apache-spark-machine-learning-mllib-ipython/query.output.png "Resultado da consulta SQL")

    Para mais informações sobre o `%%sql` mágico, bem como outras magics disponíveis com PySpark kernel, consulte o artigo [Kernels disponíveis no Jupyter blocos de notas com clusters de motores HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

3. Também pode utilizar Matplotlib, uma biblioteca utilizada para construir a visualização dos dados, para criar um desenho. Uma vez que o desenho tem de ser criado a partir do dataframe localmente persistentes **countResultsdf** , o fragmento de código têm de começar com o `%%local` mágico. Este procedimento assegura que o código for executado localmente no servidor Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        
        labels = countResultsdf['results']
        sizes = countResultsdf['cnt']
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')

    Deverá visualizar um resultado semelhante ao seguinte:

    ![Resultado de resultado](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_13_1.png)


4. Pode ver se há 5 distintos os resultados que pode ter um controlo:
    
    * Empresas não localizada 
    * Falhar
    * Pass
    * Suporte técnico da Microsoft com condições, e
    * Terminar empresas 

    Diga-nos desenvolva um modelo que pode adivinhar o resultado de um controlo de alimentação, tendo em conta as violações. Uma vez que regressão logística é um método de classificação binário, faz sentido para agrupar os nossos dados em duas categorias: **falhar** e **passam**. Um "passar c / condições" ainda é uma fase, para quando podemos formar o modelo, podemos iremos considerá dois resultados equivalente. Dados com os resultados ("Empresas não localizado", "fora do negócio") não são úteis para que recomendamos irá removê-los a partir de nosso conjunto de formação. Deve ser importa uma vez que estas duas categorias compõem uma percentagem muito pequena dos resultados mesmo assim.

5. Diga-nos prosseguir e converta a nossa dataframe existente (`df`) para uma nova dataframe onde cada inspeção é representada como um par de violações de etiqueta. No nosso caso, uma etiqueta de `0.0` representa uma falha de, uma etiqueta de `1.0` representa um sucesso e uma etiqueta de `-1.0` representa alguns resultados para além dessas duas. Vamos irá filtrar esses outros resultados quando computação a moldura de dados nova.


        def labelForResults(s):
            if s == 'Fail':
                return 0.0
            elif s == 'Pass w/ Conditions' or s == 'Pass':
                return 1.0
            else:
                return -1.0
        label = UserDefinedFunction(labelForResults, DoubleType())
        labeledData = df.select(label(df.results).alias('label'), df.violations).where('label >= 0')


    Vamos obter uma linha dos dados com nome para ver o aspeto.


        labeledData.take(1)


    Deverá visualizar um resultado semelhante ao seguinte:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        [Row(label=0.0, violations=u"41. PREMISES MAINTAINED FREE OF LITTER, UNNECESSARY ARTICLES, CLEANING  EQUIPMENT PROPERLY STORED - Comments: All parts of the food establishment and all parts of the property used in connection with the operation of the establishment shall be kept neat and clean and should not produce any offensive odors.  REMOVE MATTRESS FROM SMALL DUMPSTER. | 35. WALLS, CEILINGS, ATTACHED EQUIPMENT CONSTRUCTED PER CODE: GOOD REPAIR, SURFACES CLEAN AND DUST-LESS CLEANING METHODS - Comments: The walls and ceilings shall be in good repair and easily cleaned.  REPAIR MISALIGNED DOORS AND DOOR NEAR ELEVATOR.  DETAIL CLEAN BLACK MOLD LIKE SUBSTANCE FROM WALLS BY BOTH DISH MACHINES.  REPAIR OR REMOVE BASEBOARD UNDER DISH MACHINE (LEFT REAR KITCHEN). SEAL ALL GAPS.  REPLACE MILK CRATES USED IN WALK IN COOLERS AND STORAGE AREAS WITH PROPER SHELVING AT LEAST 6' OFF THE FLOOR.  | 38. VENTILATION: ROOMS AND EQUIPMENT VENTED AS REQUIRED: PLUMBING: INSTALLED AND MAINTAINED - Comments: The flow of air discharged from kitchen fans shall always be through a duct to a point above the roofline.  REPAIR BROKEN VENTILATION IN MEN'S AND WOMEN'S WASHROOMS NEXT TO DINING AREA. | 32. FOOD AND NON-FOOD CONTACT SURFACES PROPERLY DESIGNED, CONSTRUCTED AND MAINTAINED - Comments: All food and non-food contact equipment and utensils shall be smooth, easily cleanable, and durable, and shall be in good repair.  REPAIR DAMAGED PLUG ON LEFT SIDE OF 2 COMPARTMENT SINK.  REPAIR SELF CLOSER ON BOTTOM LEFT DOOR OF 4 DOOR PREP UNIT NEXT TO OFFICE.")]


## <a name="create-a-logistic-regression-model-from-the-input-dataframe"></a>Criar um modelo de regressão logísticos a partir do dataframe entrada

Os nossos tarefa final é converter os dados com nome para um formato que pode ser analisado por regressão logística. A entrada de um algoritmo de regressão logística deve ser um conjunto de *pares de vetor da funcionalidade de etiqueta*, onde o "vector funcionalidade" é um vector dos números que representa o ponto de entrada de alguma forma. Por isso, é necessário uma forma para converter a coluna de "violações", que é semiestruturados e contém muitas comentários no texto de livre, para uma matriz de números reais que uma máquina facilmente possa compreender. 

É uma máquina padrão abordagem para processamento de linguagem natural de formação atribuir a cada palavra distinta "index" e, em seguida, passar por vetor à máquina algoritmo de formação, assim que o valor de cada índice contém a frequência relativa de que o word na cadeia de texto. 

MLLib fornece uma forma fácil de efetuar esta operação. Primeiro, vamos irá "tokenize" cada cadeia violações para obter as palavras individuais numa cadeia de cada e, em seguida, vamos utilizar um `HashingTF` para converter cada conjunto de tokens de vetor uma funcionalidade que, em seguida, pode ser passado para o algoritmo de regressão logística para construir um modelo. Vamos irá realizar todos estes passos em sequência, utilizando uma "tubagem".


    tokenizer = Tokenizer(inputCol="violations", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    
    model = pipeline.fit(labeledData)


## <a name="evaluate-the-model-on-a-separate-test-dataset"></a>Avaliar o modelo de um conjunto de dados de teste separada

Vamos pode utilizar o modelo de criámos anterior para *prever* que os resultados de controlos novos será, com base em violações que foram observadas. Vamos formação este modelo num conjunto de dados **Food_Inspections1.csv**. Diga-nos, utilize um segundo conjunto de dados, **Food_Inspections2.csv**, para *avaliar* a intensidade neste modelo em novos dados. Este segundo conjunto de dados (**Food_Inspections2.csv**) já devem estar no contentor de armazenamento predefinido associado ao cluster.

1. O fragmento de abaixo cria um novo dataframe, **predictionsDf** que contém a previsão gerado pelo modelo. O fragmento de também cria uma tabela temporária **que previsões** com base na dataframe.


        testData = sc.textFile('wasbs:///HdiSamples/HdiSamples/FoodInspectionData/Food_Inspections2.csv')\
                 .map(csvParse) \
                 .map(lambda l: (int(l[0]), l[1], l[12], l[13]))
        testDf = sqlContext.createDataFrame(testData, schema).where("results = 'Fail' OR results = 'Pass' OR results = 'Pass w/ Conditions'")
        predictionsDf = model.transform(testDf)
        predictionsDf.registerTempTable('Predictions')
        predictionsDf.columns


    Deverá visualizar um resultado semelhante ao seguinte:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
        
        ['id',
         'name',
         'results',
         'violations',
         'words',
         'features',
         'rawPrediction',
         'probability',
         'prediction']

2. Veja um das previsões. Execute este fragmento:

        predictionsDf.take(1)

    Irá ver a previsão para a primeira entrada no conjunto de dados de teste.

3. O `model.transform()` método serão aplicadas a transformação mesmo para todos os novos dados com o mesmo esquema e chegar a uma previsão de como classificar os dados. Vamos pode efetuar algumas estatísticas simples para obter uma ideia de como precisos nossos previsões foram:


        numSuccesses = predictionsDf.where("""(prediction = 0 AND results = 'Fail') OR 
                                              (prediction = 1 AND (results = 'Pass' OR 
                                                                   results = 'Pass w/ Conditions'))""").count()
        numInspections = predictionsDf.count()
        
        print "There were", numInspections, "inspections and there were", numSuccesses, "successful predictions"
        print "This is a", str((float(numSuccesses) / float(numInspections)) * 100) + "%", "success rate"

    O resultado tem o seguinte aspeto:
    
        # -----------------
        # THIS IS AN OUTPUT
        # -----------------
    
        There were 9315 inspections and there were 8087 successful predictions
        This is a 86.8169618894% success rate


    Utilizar uma regressão logística com motores dá-num modelo de precisão de relação entre as descrições de violações em inglês e se a um determinado negócio seria passar ou falhar um controlo de alimentação. 

## <a name="create-a-visual-representation-of-the-prediction"></a>Criar uma representação visual de previsão

Vamos pode agora construir uma visualização final para nos ajudar motivo sobre os resultados deste teste. 

1. Vamos começar por extrair a previsões diferentes e os resultados da tabela temporária **previsões** criada anteriormente. Consultas que se seguem separam o resultado como *true_positive*, *false_positive*, *true_negative*e *false_negative*. Em consultas abaixo, vamos desativar visualização utilizando `-q` e também guardar a saída (utilizando `-o`) como dataframes que podem ser utilizados, em seguida, com a `%%local` mágico. 

        %%sql -q -o true_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND results = 'Fail'

        %%sql -q -o false_positive
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 0 AND (results = 'Pass' OR results = 'Pass w/ Conditions')

        %%sql -q -o true_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND results = 'Fail'

        %%sql -q -o false_negative
        SELECT count(*) AS cnt FROM Predictions WHERE prediction = 1 AND (results = 'Pass' OR results = 'Pass w/ Conditions') 

2. Por fim, utilize o fragmento que se segue para gerar o desenho utilizando **Matplotlib**.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        labels = ['True positive', 'False positive', 'True negative', 'False negative']
        sizes = [true_positive['cnt'], false_positive['cnt'], false_negative['cnt'], true_negative['cnt']]
        colors = ['turquoise', 'seagreen', 'mediumslateblue', 'palegreen', 'coral']
        plt.pie(sizes, labels=labels, autopct='%1.1f%%', colors=colors)
        plt.axis('equal')
    
    Deverá ver o resultado seguinte.
    
    ![Resultado da previsão](./media/hdinsight-apache-spark-machine-learning-mllib-ipython/output_26_1.png)


    Neste gráfico, um resultado "positivo" refere-se para o controlo de alimentação falha, enquanto um resultado negativo que se refere a um controlo passado.

## <a name="shut-down-the-notebook"></a>Encerrar o bloco de notas

Depois de terminar a execução da aplicação, deverá encerramento o bloco de notas para libertar recursos. Para fazê-lo, a partir do menu **ficheiro** no bloco de notas, clique em **Fechar e parar**. Este irá encerramento e feche o bloco de notas.


## <a name="seealso"></a>Consulte também


* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para analisar temperatura edifício utilizando dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de registo de Web site utilizando motores no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar as aplicações

* [Criar uma aplicação autónoma Scala](hdinsight-apache-spark-create-standalone-application.md)

* [Executar tarefas remotamente num cluster de motores utilizando Lívio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para criar e submeter motores Scala aplicações](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizar o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para depurar as aplicações de motores remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Utilizar blocos de notas Zeppelin com um cluster de motores num HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de notas Jupyter num cluster de motores para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizar os pacotes externos com Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e ligar a um cluster de motores de HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)
