<properties
    pageTitle="Submeter tarefas motores remotamente utilizando Lívio | Microsoft Azure"
    description="Saiba como utilizar Lívio com HDInsight clusters para submeter tarefas de motores remotamente."
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
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="submit-spark-jobs-remotely-to-an-apache-spark-cluster-on-hdinsight-linux-using-livy"></a>Submeter motores tarefas remotamente a um cluster de motores de Apache no HDInsight Linux utilizando Lívio

Cluster de motores de Apache no Azure HDInsight inclui Lívio, uma interface REST para submeter remotamente a um cluster de motores de tarefas. Para obter documentação detalhada, consulte o artigo [Lívio](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server).

Pode utilizar Lívio para executar motores interativos conchas ou submeter as tarefas de lote a ser executados em motores. Este artigo fala sobre como utilizar Lívio para submeter lote tarefas. A sintaxe da abaixo utiliza Laço para efetuar chamadas de resto para o ponto final Lívio.

**Pré-requisitos:**

Tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de motores de Apache HDInsight Linux. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="submit-a-batch-job-the-cluster"></a>Submeter um cluster de processo

Antes de enviar um processo, terá de carregar a aplicação para caixa no armazenamento de cluster associado ao cluster. Pode utilizar [**AzCopy**](../storage/storage-use-azcopy.md), um utilitário de linha de comandos, para fazê-lo. Existem muitas outros clientes que pode utilizar para carregar dados. Pode encontrar mais informações sobre os mesmos no [carregar dados para trabalhos de Hadoop no HDInsight](hdinsight-upload-data.md).

    curl -k --user "<hdinsight user>:<user password>" -v -H <content-type> -X POST -d '{ "file":"<path to application jar>", "className":"<classname in jar>" }' 'https://<spark_cluster_name>.azurehdinsight.net/livy/batches'

**Exemplos**:

* Se o ficheiro para caixa se encontra o armazenamento de cluster (WASB)

        curl -k --user "admin:mypassword1!" -v -H 'Content-Type: application/json' -X POST -d '{ "file":"wasbs://mycontainer@mystorageaccount.blob.core.windows.net/data/SparkSimpleTest.jar", "className":"com.microsoft.spark.test.SimpleFile" }' "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se o pretender passar o nome do ficheiro para caixa e o NomeClasse como parte de um ficheiro de entrada (neste exemplo, txt)

        curl -k  --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

## <a name="get-information-on-batches-running-on-the-cluster"></a>Obtenha informações sobre lotes executadas no cluster

    curl -k --user "<hdinsight user>:<user password>" -v -X GET "https://<spark_cluster_name>.azurehdinsight.net/livy/batches"

**Exemplos**:

* Se pretender obter todas as secções executadas no cluster:

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

* Se pretender obter uma secção específica com um determinado batchId

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"


## <a name="delete-a-batch-job"></a>Eliminar uma tarefa batch

    curl -k --user "<hdinsight user>:<user password>" -v -X DELETE "https://<spark_cluster_name>.azurehdinsight.net/livy/batches/{batchId}"

**Exemplo**:

    curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/{batchId}"

## <a name="livy-and-high-availability"></a>Lívio e disponibilidade de alta

Lívio fornece alta disponibilidade para motores executadas no cluster de tarefas. Aqui estão algumas dos exemplos.

* Se o serviço de Lívio vai para baixo depois de ter submetido uma tarefa remotamente para um cluster de motores, a tarefa continua a ser executado em segundo plano. Quando estiver Lívio cópias de segurança,-repõe o estado da tarefa e relatórios-o novamente.

* Jupyter blocos de notas para HDInsight são recorrendo Lívio back-end. Se um bloco de notas está a ser executada uma tarefa de motores e o serviço de Lívio obtém reiniciado, o bloco de notas irão continuar a executar as células de código. 

## <a name="show-me-an-example"></a>Mostrar-me um exemplo

Nesta secção, podemos veja exemplos sobre como utilizar Lívio para apresentar um pedido de motores, monitorizar o progresso da aplicação e, em seguida, elimine a tarefa. A aplicação que utilizamos neste exemplo é um desenvolvidos no artigo [criar um programa autónomo Scala aplicação e executar num cluster de motores de HDInsight](hdinsight-apache-spark-create-standalone-application.md). Os passos abaixo partem do pressuposto do seguinte procedimento:

* Já tiver copiado sobre a para aplicação caixa para a conta de armazenamento associada ao cluster.
* Tiver Laço instalado no computador onde está a tentar estes passos.

Execute os passos seguintes.

1. Diga-em primeiro lugar certifique-se de que Lívio está em execução no cluster. Vamos pode fazê-lo ao obter uma lista de executar o lotes. Se esta for a primeira vez que estiver a executar uma tarefa utilizando Lívio, isto deve devolver zero.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Deve obter um resultado semelhante ao seguinte:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:47:53 GMT
        < Content-Length: 34
        <
        {"from":0,"total":0,"sessions":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Repare como a última linha no resultado diz **total: 0**, o que sugere sem secções em execução.

2. Diga-nos agora submeta um processo. O fragmento de abaixo utiliza um ficheiro de entrada (txt) para transmitir o nome para caixa e o nome de classe como parâmetros. Este é a abordagem recomendada se estiver a executar estes passos de um computador Windows.

        curl -k --user "admin:mypassword1!" -v -H "Content-Type: application/json" -X POST --data @C:\Temp\input.txt "https://mysparkcluster.azurehdinsight.net/livy/batches"

    Os parâmetros no ficheiro **txt** são definidos da seguinte forma:

        { "file":"wasbs:///example/jars/SparkSimpleApp.jar", "className":"com.microsoft.spark.example.WasbIOTest" }

    Deverá visualizar um resultado semelhante ao seguinte:

        < HTTP/1.1 201 Created
        < Content-Type: application/json; charset=UTF-8
        < Location: /0
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:51:30 GMT
        < Content-Length: 36
        <
        {"id":0,"state":"starting","log":[]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    Repare como a última linha da saída diz **Estado: Iniciar**. Também indica, **id: 0**. Este é o ID do lote.

3. Agora pode obter o estado deste lote específica utilizando o ID lote.

        curl -k --user "admin:mypassword1!" -v -X GET "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Deverá visualizar um resultado semelhante ao seguinte:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Fri, 20 Nov 2015 23:54:42 GMT
        < Content-Length: 509
        <
        {"id":0,"state":"success","log":["\t diagnostics: N/A","\t ApplicationMaster host: 10.0.0.4","\t ApplicationMaster RPC port: 0","\t queue: default","\t start time: 1448063505350","\t final status: SUCCEEDED","\t tracking URL: http://hn0-myspar.lpel1gnnvxne3gwzqkfq5u5uzh.jx.internal.cloudapp.net:8088/proxy/application_1447984474852_0002/","\t user: root","15/11/20 23:52:47 INFO Utils: Shutdown hook called","15/11/20 23:52:47 INFO Utils: Deleting directory /tmp/spark-b72cd2bf-280b-4c57-8ceb-9e3e69ac7d0c"]}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    O resultado mostra agora **Estado: sucesso**, que sugere que a tarefa foi concluída com êxito.

4. Se pretender, pode eliminar o lote agora.

        curl -k --user "admin:mypassword1!" -v -X DELETE "https://mysparkcluster.azurehdinsight.net/livy/batches/0"

    Deverá visualizar um resultado semelhante ao seguinte:

        < HTTP/1.1 200 OK
        < Content-Type: application/json; charset=UTF-8
        < Server: Microsoft-IIS/8.5
        < X-Powered-By: ARR/2.5
        < X-Powered-By: ASP.NET
        < Date: Sat, 21 Nov 2015 18:51:54 GMT
        < Content-Length: 17
        <
        {"msg":"deleted"}* Connection #0 to host mysparkcluster.azurehdinsight.net left intact

    A última linha da saída mostra que o lote foi eliminado com êxito. Se eliminar uma tarefa enquanto estiver em execução, essencialmente-lo irá eliminar a tarefa. Se eliminar uma tarefa que foi concluída com êxito ou caso contrário, elimina as informações da tarefa completamente.

## <a name="seealso"></a>Consulte também


* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para analisar temperatura edifício utilizando dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de registo de Web site utilizando motores no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar as aplicações

* [Criar uma aplicação autónoma Scala](hdinsight-apache-spark-create-standalone-application.md)

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
