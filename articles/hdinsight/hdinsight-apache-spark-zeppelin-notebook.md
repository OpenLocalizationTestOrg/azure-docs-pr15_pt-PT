<properties 
    pageTitle="Utilizar blocos de notas Zeppelin com cluster de motores no HDInsight Linux | Microsoft Azure" 
    description="Instruções passo a passo sobre como utilizar blocos de notas Zeppelin com clusters de motores no HDInsight Linux." 
    services="hdinsight" 
    documentationCenter="" 
    authors="nitinme" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="hdinsight" 
    ms.workload="big-data" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/05/2016" 
    ms.author="nitinme"/>


# <a name="use-zeppelin-notebooks-with-apache-spark-cluster-on-hdinsight-linux"></a>Utilizar blocos de notas Zeppelin com cluster de motores de Apache no HDInsight Linux

Motores de HDInsight clusters incluem blocos de notas de Zeppelin que pode utilizar para executar motores tarefas. Neste artigo, saiba como utilizar o bloco de notas Zeppelin num HDInsight cluster.


**Pré-requisitos:**

* Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

* Um cluster de motores de Apache. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="launch-a-zeppelin-notebook"></a>Iniciar um bloco de notas Zeppelin

1. Pá de cluster motores, clique em **Cluster Dashboard**e, em seguida, clique em **Zeppelin bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Zeppelin para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/zeppelin`

2. Crie um novo bloco de notas. A partir do painel de cabeçalho, clique em **Bloco de notas**e, em seguida, clique em **Criar nova nota**.

    ![Criar um novo bloco de notas Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.createnewnote.png "Criar um novo bloco de notas Zeppelin")

    Introduza um nome para o bloco de notas e, em seguida, clique em **Criar nota**.

3. Além disso, certifique-se do que cabeçalho do bloco de notas mostra um estado ligado. Este é representada por um ponto verde no canto superior direito.

    ![Estado do bloco de notas Zeppelin] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.newnote.connected.png "Estado do bloco de notas Zeppelin")

4. Carregar os dados de exemplo para uma tabela temporária. Quando cria um cluster de motores no HDInsight, o ficheiro de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associado em **\HdiSamples\SensorSampleData\hvac**.

    No parágrafo vazio que é criado por predefinição no novo bloco de notas, cole o seguinte fragmento.

        %livy.spark
        //The above magic instructs Zeppelin to use the Livy Scala interpreter

        // Create an RDD using the default Spark context, sc
        val hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        // Define a schema
        case class Hvac(date: String, time: String, targettemp: Integer, actualtemp: Integer, buildingID: String)
        
        // Map the values in the .csv file to the schema
        val hvac = hvacText.map(s => s.split(",")).filter(s => s(0) != "Date").map(
            s => Hvac(s(0), 
                    s(1),
                    s(2).toInt,
                    s(3).toInt,
                    s(6)
            )
        ).toDF()
        
        // Register as a temporary table called "hvac"
        hvac.registerTempTable("hvac")
        
    Prima **SHIFT + ENTER** ou clique no botão **Reproduzir** para o parágrafo executar o fragmento. O estado no canto direito do parágrafo deve progresso a partir do pronto, pendente, a trabalhar RAPIDAMENTE para terminado. O resultado é apresentado na parte inferior do mesmo parágrafo. A captura de ecrã tem o seguinte aspeto:

    ![Criar uma tabela a partir de dados não processados temporária] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.loaddDataintotable.png "Criar uma tabela a partir de dados não processados temporária")

    Também pode fornecer um título para cada parágrafo. A partir do canto direito, clique no ícone **Definições** e, em seguida, clique em **Mostrar título**.

5. Pode agora executar instruções SQL de motores na tabela **AVAC** . Cole a seguinte consulta um novo parágrafo. A consulta obtém o ID do edifício e a diferença entre o destino e temperaturas reais para cada criar numa determinada data. Prima **SHIFT + ENTER**.

        %sql
        select buildingID, (targettemp - actualtemp) as temp_diff, date from hvac where date = "6/1/13" 

    A instrução de **sql de %** no início indica o bloco de notas para utilizar o intérprete Lívio Scala.

    A seguinte captura de ecrã mostra o resultado.

    ![Executar uma instrução de SQL motores utilizando o bloco de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery1.png "Executar uma instrução de SQL motores utilizando o bloco de notas")

     Para alternar entre diferentes representações para o mesmo resultado, clique em Opções de visualização (realçadas no retângulo). Clique em **Definições** para escolher que consitutes a chave e os valores no resultado. A captura de ecrã acima utiliza **buildingID** como a tecla e a média dos **temp_diff** como o valor.

    
6. Também pode executar declarações de SQL motores utilizar variáveis de consulta. O fragmento de seguinte mostra como definir uma variável, **Temp**, na consulta com os valores possíveis que pretende de consulta com. Quando executar a consulta pela primeira vez, é preenchida automaticamente uma seta de lista pendente com os valores especificados para a variável.

        %sql
        select buildingID, date, targettemp, (targettemp - actualtemp) as temp_diff from hvac where targettemp > "${Temp = 65,65|75|85}" 

    Cole este fragmento de um parágrafo novo e prima **SHIFT + ENTER**. A seguinte captura de ecrã mostra o resultado.

    ![Executar uma instrução de SQL motores utilizando o bloco de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.note.sparksqlquery2.png "Executar uma instrução de SQL motores utilizando o bloco de notas")

    Para consultas subsequentes, pode selecionar um novo valor a partir do menu pendente e execute novamente a consulta. Clique em **Definições** para escolher que consitutes a chave e os valores no resultado. A captura de ecrã acima utiliza **buildingID** como chave, a média dos **temp_diff** como o valor e **targettemp** como o grupo.

7. Reinicie o intérprete Lívio para sair da aplicação. Para fazê-lo, abrir as definições de interpretação ao clicar em com sessão iniciada no nome de utilizador a partir do canto superior direito e, em seguida, clique em **interpretação**.

    ![Iniciação interpretação] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Ramo de registo de saída")

2. Desloque-se para as definições de interpretação Lívio e, em seguida, clique em **reiniciar**.

    ![Reinicie o intepreter Lívio] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie o intepreter Zeppelin")

## <a name="how-do-i-use-external-packages-with-the-notebook"></a>Como utilizo pacotes externos com o bloco de notas?

Pode configurar o bloco de notas Zeppelin num cluster de motores de Apache no HDInsight (Linux) para utilizar os pacotes externos, contribuiu com da Comunidade, que não estão incluído out-de-de-box no cluster. Pode procurar o [repositório de Maven](http://search.maven.org/) para a lista completa de pacotes que estão disponíveis. Também pode obter uma lista de pacotes disponíveis a partir de outras origens. Por exemplo, uma lista completa de pacotes contribuiu com Comunidade está disponível em [Motores pacotes](http://spark-packages.org/).

Neste artigo verá como utilizar o pacote de [motores csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de notas Jupyter.

1. Abra as definições de interpretação. A partir do canto superior direito, clique em com sessão iniciada no nome de utilizador e, em seguida, clique em **interpretação**.

    ![Iniciação interpretação] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Ramo de registo de saída")

2. Desloque-se para as definições de interpretação Lívio e, em seguida, clique em **Editar**.

    ![Alterar as definições de interpretação] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-1.png "Alterar as definições de interpretação")

3. Adicionar uma nova chave, denominada **livy.spark.jars.packages** e configurar o seu valor no formato `group:id:version`. Por isso, se pretender utilizar o pacote de [motores csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) , tem de definir o valor da chave para `com.databricks:spark-csv_2.10:1.4.0`.

    ![Alterar as definições de interpretação] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-use-external-package-2.png "Alterar as definições de interpretação")

    Clique em **Guardar** e, em seguida, reinicie o intérprete Lívio.

4. **Sugestão**: Se pretender que compreender como chegar o valor da chave de introduzido acima, eis como.

    um. Localize o pacote no repositório de Maven. Para este tutorial, utilizámos [motores csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. A partir do repositório reunir os valores de **ID de grupo**, **ArtifactId**e **versão**.

    ![Pacotes de externos utilização Jupyter bloco de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/use-external-packages-with-jupyter.png "Pacotes de externos utilização Jupyter bloco de notas")

    c. Concatenar os três valores separados por dois pontos (****:).

        com.databricks:spark-csv_2.10:1.4.0

## <a name="where-are-the-zeppelin-notebooks-saved"></a>Onde são guardados os blocos de notas Zeppelin?

Os blocos de notas Zeppelin são guardados a headnodes cluster. Por isso, se eliminar cluster, os blocos de notas também serão eliminados. Se quiser manter os blocos de notas para utilizar posteriormente no outros clusters, tem de exportá-los depois de ter terminado a executar as tarefas. Para exportar um bloco de notas, clique no ícone **de exportação** , conforme mostrado na imagem abaixo.

![Transferir o bloco de notas] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-download-notebook.png "Transferir o bloco de notas")

Este procedimento guarda o bloco de notas como um ficheiro JSON na sua localização de transferência.

## <a name="livy-session-management"></a>Gestão de sessões Lívio

Quando executa o primeiro parágrafo de código no seu bloco de notas Zeppelin, é criada uma nova sessão Lívio no seu cluster de motores de HDInsight. Esta sessão é partilhada em todos os blocos de notas de Zeppelin que crie posteriormente. Se, por alguma razão Lívio sessão está eliminados (reinicie cluster, etc.), não conseguir executar tarefas a partir do bloco de notas Zeppelin.

Neste caso, deve efetuar os seguintes passos antes de poder iniciar a executar tarefas a partir de um bloco de notas Zeppelin. 

1. Reinicie o intérprete Lívio do bloco de notas Zeppelin. Para fazê-lo, abrir as definições de interpretação ao clicar em com sessão iniciada no nome de utilizador a partir do canto superior direito e, em seguida, clique em **interpretação**.

    ![Iniciação interpretação] (./media/hdinsight-apache-spark-zeppelin-notebook/zeppelin-launch-interpreter.png "Ramo de registo de saída")

2. Desloque-se para as definições de interpretação Lívio e, em seguida, clique em **reiniciar**.

    ![Reinicie o intepreter Lívio] (./media/hdinsight-apache-spark-zeppelin-notebook/hdispark.zeppelin.restart.interpreter.png "Reinicie o intepreter Zeppelin")

3. Execute uma célula de código a partir de um bloco de notas Zeppelin existente. Esta ação cria uma nova sessão Lívio no HDInsight cluster.

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

* [Executar tarefas remotamente num cluster de motores utilizando Lívio](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões

* [Utilize o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para criar e submeter motores Scala aplicações](hdinsight-apache-spark-intellij-tool-plugin.md)

* [Utilizar o plug-in do HDInsight ferramentas para obter IntelliJ IDEIA para depurar as aplicações de motores remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)

* [Kernels disponíveis para o bloco de notas Jupyter num cluster de motores para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Utilizar os pacotes externos com Jupyter blocos de notas](hdinsight-apache-spark-jupyter-notebook-use-external-packages.md)

* [Instalar Jupyter no seu computador e ligar a um cluster de motores de HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)


[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md 







