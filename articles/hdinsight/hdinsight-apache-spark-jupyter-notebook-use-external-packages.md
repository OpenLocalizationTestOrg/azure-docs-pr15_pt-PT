<properties 
    pageTitle="Utilizar os pacotes externos com Jupyter blocos de notas em motores de Apache clusters no HDInsight | Azure"
    description="Instruções passo a passo sobre como configurar Jupyter blocos de notas disponíveis com os motores de HDInsight clusters para utilizar os pacotes de motores externos." 
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


# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Utilizar os pacotes externos com Jupyter blocos de notas em motores de Apache clusters no HDInsight Linux

>[AZURE.NOTE] Este artigo é aplicável às motores 1.5.2 no HDInsight 3.3 e motores 1.6.2 no HDInsight 3.4. 

Saiba como configurar um bloco de notas Jupyter num cluster de motores de Apache no HDInsight (Linux) para utilizar externos, pacotes contribuiu com da Comunidade, que não estão incluídos out of box no cluster. 

Pode procurar o [repositório de Maven](http://search.maven.org/) para a lista completa de pacotes que estão disponíveis. Também pode obter uma lista de pacotes disponíveis a partir de outras origens. Por exemplo, uma lista completa de pacotes contribuiu com Comunidade está disponível em [Motores pacotes](http://spark-packages.org/).

Este artigo vai aprender a utilizar o pacote de [motores csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de notas Jupyter.

##<a name="prerequisites"></a>Pré-requisitos

Tem de ter o seguinte procedimento:

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Um cluster de motores de Apache HDInsight Linux. Para obter instruções, consulte o artigo [Criar motores de Apache clusters no Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Utilizar os pacotes externos com Jupyter blocos de notas 

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do startboard, clique no mosaico do seu cluster de motores (se afixados-lo para a startboard). Também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Pá de cluster motores, clique em **Ligações rápidas**e, em seguida, a partir de pá **Cluster de Dashboard** , clique em **Jupyter bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Jupyter para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas. Clique em **Novo**e, em seguida, clique em **motores**.

    ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de notas Jupyter")

3. Um novo bloco de notas é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de notas")

4. Irá utilizar o `%%configure` mágico para configurar o bloco de notas para utilizar um pacote de externo. Nos blocos de notas que utilizam pacotes externos, certifique-se de ligar para o `%%configure` mágico na primeira célula código. Este procedimento assegura que o kernel está configurado para utilizar o pacote de antes da sessão é iniciada.

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    >[AZURE.IMPORTANT] Caso se esqueça de configurar o kernel na primeira célula, pode utilizar o `%%configure` com o `-f` o parâmetro, mas que irá reinicie a sessão e progresso de todas as serão perdido.

5. No fragmento de acima, `packages` espera de uma lista das coordenadas maven repositório Central Maven. Neste fragmento `com.databricks:spark-csv_2.10:1.4.0` é a coordenada maven para **motores csv** pacote. Eis como construir as coordenadas para um pacote.

    um. Localize o pacote no repositório de Maven. Para este tutorial, utilizamos [motores csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
    
    b. A partir do repositório reunir os valores de **ID de grupo**, **ArtifactId**e **versão**.

    ![Pacotes de externos utilização Jupyter bloco de notas] (./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Pacotes de externos utilização Jupyter bloco de notas")

    c. Concatenar os três valores separados por dois pontos (****:).

        com.databricks:spark-csv_2.10:1.4.0

6. Executar a célula de código com o `%%configure` mágico. Isto irá configurar a sessão Lívio subjacente para utilizar o pacote que forneceu. Nas células subsequentes no bloco de notas, agora pode usar o pacote, conforme apresentado abaixo.

        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

7. Em seguida, pode executar fragmentos, tal como mostrado abaixo, para ver os dados a partir do dataframe que criou no passo anterior.

        df.show()

        df.select("Time").count()


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

* [Utilizar blocos de notas Zeppelin com um cluster de motores num HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)

* [Kernels disponíveis para o bloco de notas Jupyter num cluster de motores para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)

* [Instalar Jupyter no seu computador e ligar a um cluster de motores de HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Adicionar e utilizar recursos

* [Gerir os recursos para o cluster de motores de Apache no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)

* [Controlar e depuração tarefas em execução num cluster de motores de Apache HDInsight](hdinsight-apache-spark-job-debugging.md)
