<properties 
    pageTitle="Utilizar bibliotecas personalizadas com um cluster de motores de HDInsight para analisar os registos de Web site | Microsoft Azure" 
    description="Utilizar bibliotecas personalizadas com um cluster de motores de HDInsight para analisar os registos de Web site" 
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

# <a name="analyze-website-logs-using-a-custom-library-with-apache-spark-cluster-on-hdinsight-linux"></a>Analisar os registos de Web site utilizando uma biblioteca personalizada com cluster de motores de Apache HDInsight Linux

Este bloco de notas demonstra como analisar os dados do registo através de uma biblioteca personalizada com motores no HDInsight. A biblioteca personalizada que utilizamos é uma biblioteca de Python denominada **iislogparser.py**.

> [AZURE.TIP] Neste tutorial também está disponível como um bloco de notas Jupyter num cluster de motores (Linux) que criar no HDInsight. A experiência de bloco de notas permite-lhe executar os fragmentos Python a partir do bloco de notas. Para executar o tutorial a partir de um bloco de notas, crie um cluster de motores, iniciar um bloco de notas Jupyter (`https://CLUSTERNAME.azurehdinsight.net/jupyter`), e, em seguida, execute o bloco de notas **Analisar os registos com motores utilizando um library.ipynb personalizado** na pasta **PySpark** .

**Pré-requisitos:**

Tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de motores de Apache HDInsight Linux. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="save-raw-data-as-an-rdd"></a>Guardar os dados não processados como um RDD

Nesta secção, utilizamos o bloco de notas [Jupyter](https://jupyter.org) associado a um cluster de motores de Apache HDInsight para executar tarefas que processam os dados de exemplo não processados e guardá-lo como uma tabela Hive. Os dados de exemplo serão um ficheiro. csv (hvac.csv) disponível em todos os clusters por predefinição.

Assim que os seus dados são guardados como uma tabela Hive, na secção seguinte podemos irá ligar à tabela Hive utilizando ferramentas de BI como o Power BI e o quadro.

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do startboard, clique no mosaico do seu cluster de motores (se afixados-lo para a startboard). Também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Pá de cluster motores, clique em **Cluster Dashboard**e, em seguida, clique em **Jupyter bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Jupyter para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas. Clique em **Novo**e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de notas Jupyter")

3. Um novo bloco de notas é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de notas")

4. Uma vez que criou um bloco de notas utilizando o kernel PySpark, não necessita de criar qualquer contextos explicitamente. Os contextos motores e ramo serão criados automaticamente para si quando executa a primeira célula de código. Pode começar por importar os tipos de que são necessários para este cenário. Cole o seguinte fragmento numa célula vazia e, em seguida, prima **SHIFT + ENTER**.


        from pyspark.sql import Row
        from pyspark.sql.types import *


5. Crie um RDD utilizando os dados de registo de exemplo já disponíveis no cluster. Pode aceder aos dados na conta de armazenamento predefinido associado ao cluster no **\HdiSamples\HdiSamples\WebsiteLogSampleData\SampleLog\909f2b.log**.


        logs = sc.textFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b.log')


6. Obter um registo de exemplo definido para verificar se o passo anterior foi concluído com êxito.

        logs.take(5)

    Deverá visualizar um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [u'#Software: Microsoft Internet Information Services 8.0',
         u'#Fields: date time s-sitename cs-method cs-uri-stem cs-uri-query s-port cs-username c-ip cs(User-Agent) cs(Cookie) cs(Referer) cs-host sc-status sc-substatus sc-win32-status sc-bytes cs-bytes time-taken',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32',
         u'2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step4.png X-ARR-LOG-ID=4bea5b3d-8ac9-46c9-9b8c-ec3e9500cbea 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 72177 871 47']

## <a name="analyze-log-data-using-a-custom-python-library"></a>Analisar os dados do registo através de uma biblioteca de Python personalizada

7. Em saída, as primeiras linhas de algumas incluem as informações de cabeçalho e o esquema descrito no cabeçalho dessa corresponde a cada linha restante. Ao analisar esses registos poderão ser complicado. Por isso, utilizamos uma biblioteca de Python (**iislogparser.py**) personalizada que considera que essas registos muito mais fácil de análise. Por predefinição, esta biblioteca está incluída no seu cluster de motores HDInsight na **/HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py**.

    No entanto, esta biblioteca não está no `PYTHONPATH` para podemos não pode utilizá-lo ao utilizar uma declaração de importação como `import iislogparser`. Para utilizar esta biblioteca, podemos tem distribuí-la para todos os nós de trabalho. Execute o seguinte fragmento.


        sc.addPyFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/iislogparser.py')


9. `iislogparser`Fornece uma função `parse_log_line` que devolve `None` se uma linha de registo é uma linha de cabeçalho e devolve uma instância da `LogLine` classe se encontra uma linha de registo. Utilizar o `LogLine` escolares para extrair apenas as linhas de registo do RDD:

        def parse_line(l):
            import iislogparser
            return iislogparser.parse_log_line(l)
        logLines = logs.map(parse_line).filter(lambda p: p is not None).cache()


10. Obter algumas extraídas linhas de registo para confirmar que o passo foi concluído com êxito.

        logLines.take(2)

    O resultado deverá ser semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step2.png X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 53175 871 46,
        2014-01-01 02:01:09 SAMPLEWEBSITE GET /blogposts/mvc4/step3.png X-ARR-LOG-ID=9eace870-2f49-4efd-b204-0d170da46b4a 80 - 1.54.23.196 Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36 - http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx www.sample.com 200 0 0 51237 871 32]


11. O `LogLine` classe, por sua vez, tem alguns métodos úteis, tal como `is_error()`, que devolve se uma entrada de registo tem um código de erro. Utilize esta opção para calcular o número de erros nas linhas de registo extraídas e, em seguida, inicie sessão todos os erros de um ficheiro diferente.

        errors = logLines.filter(lambda p: p.is_error())
        numLines = logLines.count()
        numErrors = errors.count()
        print 'There are', numErrors, 'errors and', numLines, 'log entries'
        errors.map(lambda p: str(p)).saveAsTextFile('wasbs:///HdiSamples/HdiSamples/WebsiteLogSampleData/SampleLog/909f2b-2.log')

    Deverá visualizar um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        There are 30 errors and 646 log entries

12. Também pode utilizar **Matplotlib** para construir uma visualização dos dados. Por exemplo, se pretender identificar o problema de pedidos de executar muito tempo, poderá pretender encontrar os ficheiros que demoram mais tempo para servir de em média.
O fragmento de abaixo obtém os recursos de 25 superiores que tirou com a maioria dos tempo para servir um pedido.

        def avgTimeTakenByKey(rdd):
            return rdd.combineByKey(lambda line: (line.time_taken, 1),
                                    lambda x, line: (x[0] + line.time_taken, x[1] + 1),
                                    lambda x, y: (x[0] + y[0], x[1] + y[1]))\
                      .map(lambda x: (x[0], float(x[1][0]) / float(x[1][1])))
            
        avgTimeTakenByKey(logLines.map(lambda p: (p.cs_uri_stem, p))).top(25, lambda x: x[1])

    Deverá visualizar um resultado semelhante ao seguinte:

        # -----------------
        # THIS IS AN OUTPUT
        # -----------------

        [(u'/blogposts/mvc4/step13.png', 197.5),
         (u'/blogposts/mvc2/step10.jpg', 179.5),
         (u'/blogposts/extractusercontrol/step5.png', 170.0),
         (u'/blogposts/mvc4/step8.png', 159.0),
         (u'/blogposts/mvcrouting/step22.jpg', 155.0),
         (u'/blogposts/mvcrouting/step3.jpg', 152.0),
         (u'/blogposts/linqsproc1/step16.jpg', 138.75),
         (u'/blogposts/linqsproc1/step26.jpg', 137.33333333333334),
         (u'/blogposts/vs2008javascript/step10.jpg', 127.0),
         (u'/blogposts/nested/step2.jpg', 126.0),
         (u'/blogposts/adminpack/step1.png', 124.0),
         (u'/BlogPosts/datalistpaging/step2.png', 118.0),
         (u'/blogposts/mvc4/step35.png', 117.0),
         (u'/blogposts/mvcrouting/step2.jpg', 116.5),
         (u'/blogposts/aboutme/basketball.jpg', 109.0),
         (u'/blogposts/anonymoustypes/step11.jpg', 109.0),
         (u'/blogposts/mvc4/step12.png', 106.0),
         (u'/blogposts/linq8/step0.jpg', 105.5),
         (u'/blogposts/mvc2/step18.jpg', 104.0),
         (u'/blogposts/mvc2/step11.jpg', 104.0),
         (u'/blogposts/mvcrouting/step1.jpg', 104.0),
         (u'/blogposts/extractusercontrol/step1.png', 103.0),
         (u'/blogposts/sqlvideos/sqlvideos.jpg', 102.0),
         (u'/blogposts/mvcrouting/step21.jpg', 101.0),
         (u'/blogposts/mvc4/step1.png', 98.0)]


13. Também pode apresentar estas informações sob a forma de desenho. Como primeiro passo para criar um desenho, diga-em primeiro lugar, crie uma tabela de temporária **AverageTime**. A tabela agrupa os registos por tempo para ver se havia qualquer picos latência invulgares em qualquer altura específica.

        avgTimeTakenByMinute = avgTimeTakenByKey(logLines.map(lambda p: (p.datetime.minute, p))).sortByKey()
        schema = StructType([StructField('Minutes', IntegerType(), True),
                             StructField('Time', FloatType(), True)])
                             
        avgTimeTakenByMinuteDF = sqlContext.createDataFrame(avgTimeTakenByMinute, schema)
        avgTimeTakenByMinuteDF.registerTempTable('AverageTime')

14. Em seguida, pode executar a consulta SQL seguinte para obter todos os registos na tabela **AverageTime** .

        %%sql -o averagetime
        SELECT * FROM AverageTime

    O `%%sql` mágico seguido `-o averagetime` assegura que o resultado da consulta é mantido localmente no servidor Jupyter (normalmente a headnode do cluster). O resultado é mantido como um dataframe [Pandas](http://pandas.pydata.org/) com o nome especificado **averagetime**.

    Deverá visualizar um resultado semelhante ao seguinte:

    ![Resultado da consulta SQL] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/sql.output.png "Resultado da consulta SQL")

    Para mais informações sobre o `%%sql` mágico, bem como outras magics disponíveis com PySpark kernel, consulte o artigo [Kernels disponíveis no Jupyter blocos de notas com clusters de motores HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).

15. Agora pode utilizar Matplotlib, uma biblioteca utilizada para construir a visualização dos dados, para criar um desenho. Uma vez que o desenho tem de ser criado a partir do dataframe localmente persistentes **averagetime** , o fragmento de código têm de começar com o `%%local` mágico. Este procedimento assegura que o código for executado localmente no servidor Jupyter.

        %%local
        %matplotlib inline
        import matplotlib.pyplot as plt
        
        plt.plot(averagetime['Minutes'], averagetime['Time'], marker='o', linestyle='--')
        plt.xlabel('Time (min)')
        plt.ylabel('Average time taken for request (ms)')

    Deverá visualizar um resultado semelhante ao seguinte:

    ![Matplotlib saída] (./media/hdinsight-apache-spark-custom-library-website-log-analysis/hdi-apache-spark-web-log-analysis-plot.png "Matplotlib saída")

16. Depois de terminar a execução da aplicação, deverá encerramento o bloco de notas para libertar recursos. Para fazê-lo, a partir do menu **ficheiro** no bloco de notas, clique em **Fechar e parar**. Este irá encerramento e feche o bloco de notas.
    

## <a name="seealso"></a>Consulte também


* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para analisar temperatura edifício utilizando dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

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
