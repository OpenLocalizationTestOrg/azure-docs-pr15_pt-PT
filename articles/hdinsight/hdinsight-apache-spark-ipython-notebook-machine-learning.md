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


# <a name="build-machine-learning-applications-to-run-on-apache-spark-clusters-on-hdinsight-linux"></a>Criar aplicações de formação do computador a executar num clusters de motores de Apache HDInsight Linux

Saiba como criar uma aplicação utilizando um cluster de motores de Apache no HDInsight de aprendizagem automática. Este artigo mostra como utilizar o bloco de notas Jupyter disponível com o cluster para criar e testar a nossa aplicação. A aplicação utiliza os dados de HVAC.csv de exemplo está disponíveis em todos os clusters por predefinição.

**Pré-requisitos:**

Tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de motores de Apache HDInsight Linux. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md). 

##<a name="data"></a>Mostrar-me os dados

Antes de Vamos começar a construir a aplicação, diga-nos compreenda a estrutura dos dados e o tipo de análise que vamos fazê-nos dados. 

Neste artigo, utilizamos o ficheiro de dados de **HVAC.csv** de exemplo que se encontra disponível na conta de armazenamento do Windows Azure que associada ao cluster HDInsight. Dentro da conta de armazenamento, o ficheiro está na **\HdiSamples\HdiSamples\SensorSampleData\hvac**. Transfira e abra o ficheiro CSV para obter um instantâneo dos dados.  

![Instantâneo de dados AVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.png "Instantâneo dos dados de AVAC")

Os dados mostram a temperatura de destino e a temperatura real de um edifício com sistemas AVAC instalados. Vamos assumir a coluna de **sistema** representa o ID do sistema e a coluna **SystemAge** representa o número de anos que o sistema de AVAC tenha sido num local no edifício.

Utilizamos estes dados para prever se um edifício estará os valores que atingem ou de acordo com base na temperatura de destino, atribuída um ID do sistema e a idade de sistema.

##<a name="app"></a>Escrever uma aplicação de formação de máquina utilizando motores MLlib

Esta aplicação utilizamos uma tubagem motores ML para efetuar uma classificação de documento. No pipeline de podemos dividir o documento em palavras, converter as palavras em por vetor funcionalidade numérica e finalmente criar um modelo de previsão utilizando as etiquetas e vectores de funcionalidade. Execute os passos seguintes para criar a aplicação.

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do startboard, clique no mosaico do seu cluster de motores (se afixados-lo para a startboard). Também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Pá de cluster motores, clique em **Cluster Dashboard**e, em seguida, clique em **Jupyter bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Jupyter para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas. Clique em **Novo**e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de notas Jupyter")

3. Um novo bloco de notas é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de notas")

3. Uma vez que criou um bloco de notas utilizando o kernel PySpark, não necessita de criar qualquer contextos explicitamente. Os contextos motores e ramo serão criados automaticamente para si quando executa a primeira célula de código. Pode começar por importar os tipos de que são necessários para este cenário. Cole o seguinte fragmento numa célula vazia e, em seguida, prima **SHIFT + ENTER**. 

        from pyspark.ml import Pipeline
        from pyspark.ml.classification import LogisticRegression
        from pyspark.ml.feature import HashingTF, Tokenizer
        from pyspark.sql import Row
        
        import os
        import sys
        from pyspark.sql.types import *
        
        from pyspark.mllib.classification import LogisticRegressionWithSGD
        from pyspark.mllib.regression import LabeledPoint
        from numpy import array
        
        
     
4. Tem agora carregar os dados (hvac.csv), analisá-la e utilizá-la para formar o modelo. Para que isto, pode definir uma função que verifica se a temperatura real do edifício é maior do que a temperatura de destino. Se a temperatura real for maior, edifício é quente, representada pelo valor **1.0**. Se a temperatura real for menor, edifício é frio, representada pelo valor **0,0**. 

    Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

        
        # List the structure of data for better understanding. Becuase the data will be
        # loaded as an array, this structure makes it easy to understand what each element
        # in the array corresponds to

        # 0 Date
        # 1 Time
        # 2 TargetTemp
        # 3 ActualTemp
        # 4 System
        # 5 SystemAge
        # 6 BuildingID

        LabeledDocument = Row("BuildingID", "SystemInfo", "label")

        # Define a function that parses the raw CSV file and returns an object of type LabeledDocument
        
        def parseDocument(line):
            values = [str(x) for x in line.split(',')]
            if (values[3] > values[2]):
                hot = 1.0
            else:
                hot = 0.0        
    
            textValue = str(values[4]) + " " + str(values[5])
    
            return LabeledDocument((values[6]), textValue, hot)

        # Load the raw HVAC.csv file, parse it using the function
        data = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

        documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
        training = documents.toDF()


5. Configurar o pipeline de formação de máquina motores é constituído por três fases: atomizador, hashingTF e lr. Para obter mais informações sobre o que é uma tubagem e como funciona consulte <a href="http://spark.apache.org/docs/latest/ml-guide.html#how-it-works" target="_blank">máquina motores em curso de formação</a>.

    Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

        tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
        hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
        lr = LogisticRegression(maxIter=10, regParam=0.01)
        pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

6. Ajuste a tubagem para o documento de formação. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

        model = pipeline.fit(training)

7. Verifique se o documento de formação para o ponto de verificação o progresso com a aplicação. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

        training.show()

    Isto deve dar o resultado semelhante ao seguinte:

        +----------+----------+-----+
        |BuildingID|SystemInfo|label|
        +----------+----------+-----+
        |         4|     13 20|  0.0|
        |        17|      3 20|  0.0|
        |        18|     17 20|  1.0|
        |        15|      2 23|  0.0|
        |         3|      16 9|  1.0|
        |         4|     13 28|  0.0|
        |         2|     12 24|  0.0|
        |        16|     20 26|  1.0|
        |         9|      16 9|  1.0|
        |        12|       6 5|  0.0|
        |        15|     10 17|  1.0|
        |         7|      2 11|  0.0|
        |        15|      14 2|  1.0|
        |         6|       3 2|  0.0|
        |        20|     19 22|  0.0|
        |         8|     19 11|  0.0|
        |         6|      15 7|  0.0|
        |        13|      12 5|  0.0|
        |         4|      8 22|  0.0|
        |         7|      17 5|  0.0|
        +----------+----------+-----+


    Voltar atrás e verifique se o resultado da observou ficheiro CSV. Por exemplo, a primeira linha do ficheiro CSV tem estes dados:

    ![Instantâneo de dados AVAC] (./media/hdinsight-apache-spark-ipython-notebook-machine-learning/hdispark.ml.show.data.first.row.png "Instantâneo dos dados de AVAC")

    Repare como a temperatura real é menor que a temperatura de destino a sugerir que o edifício esteja frio. Por conseguinte, em saída de formação, o valor de **etiqueta** na primeira linha é **0,0**, que significa que não é quente edifício.

8.  Prepare um conjunto de dados para executar o modelo de formação contra. Para fazê-lo, podemos seria passar num ID do sistema e idade do sistema (assinalado como **SystemInfo** no resultado da formação) e o modelo de seria prever se edifício com esse ID do sistema e idade sistema seria valores que atingem (representada pelo 1.0) ou mais fresca (representada por 0,0).

    Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.
        
        # SystemInfo here is a combination of system ID followed by system age
        Document = Row("id", "SystemInfo")
        test = sc.parallelize([(1L, "20 25"),
                      (2L, "4 15"),
                      (3L, "16 9"),
                      (4L, "9 22"),
                      (5L, "17 10"),
                      (6L, "7 22")]) \
            .map(lambda x: Document(*x)).toDF() 

9. Por fim, efetue previsões nos dados de teste. Cole o seguinte fragmento numa célula vazia e prima **SHIFT + ENTER**.

        # Make predictions on test documents and print columns of interest
        prediction = model.transform(test)
        selected = prediction.select("SystemInfo", "prediction", "probability")
        for row in selected.collect():
            print row

10. Deverá visualizar um resultado semelhante ao seguinte:

        Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
        Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
        Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
        Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
        Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
        Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))

    A partir da primeira linha na previsão, pode ver que para um sistema de AVAC com 20 de ID e 25 anos de idade sistema, edifício será quente (**predição = 1.0**). O primeiro valor para DenseVector (0.49999) corresponde ao previsão 0,0 e do segundo valor (0.5001) corresponde a previsão 1.0. Em saída, apesar do segundo valor é apenas marginalmente superior, o modelo mostra **predição = 1.0**.

11. Depois de terminar a execução da aplicação, deverá encerramento o bloco de notas para libertar recursos. Para fazê-lo, a partir do menu **ficheiro** no bloco de notas, clique em **Fechar e parar**. Este irá encerramento e feche o bloco de notas.
           

##<a name="anaconda"></a>Utilizar Anaconda scikit-saiba biblioteca para máquina aprendizagem

Motores de Apache clusters no HDInsight incluem bibliotecas Anaconda. Isto também inclui a **scikit-saiba** biblioteca para a aprendizagem máquina. A biblioteca também inclui vários conjuntos de dados que pode utilizar para criar aplicações de exemplo diretamente a partir de um bloco de notas Jupyter. Para obter exemplos sobre como utilizar o scikit-saiba biblioteca, consulte o artigo [http://scikit-learn.org/stable/auto_examples/index.html](http://scikit-learn.org/stable/auto_examples/index.html).

##<a name="seealso"></a>Consulte também

* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

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


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[hdinsight-weblogs-sample]: hdinsight-hive-analyze-website-log.md
[hdinsight-sensor-data-sample]: hdinsight-hive-analyze-sensor-data.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
