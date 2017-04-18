<properties
    pageTitle="Criar um cluster de motores HDInsight Linux e utilizar SQL motores de Jupyter para análise interactiva | Microsoft Azure"
    description="Instruções passo a passo sobre como criar rapidamente uma motores Apache in HDInsight cluster e, em seguida, utilizam SQL motores de Jupyter blocos de notas para executar consultas de interactivas."
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
    ms.topic="get-started-article"
    ms.date="10/28/2016"
    ms.author="nitinme"/>


# <a name="get-started-create-apache-spark-cluster-on-hdinsight-linux-and-run-interactive-queries-using-spark-sql"></a>Introdução: criar Apache motores cluster HDInsight Linux e executar consultas de interativas utilizando motores SQL

Saiba como criar um cluster de motores de Apache no HDInsight e, em seguida, utilizar [Jupyter](https://jupyter.org) bloco de notas para executar consultas SQL motores de interativas no cluster motores.

   ![Começar a utilizar o Apache motores no HDInsight] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.getstartedflow.png  "Começar a utilizar o Apache motores tutorial HDInsight. Passos ilustrados: criar uma conta de armazenamento criar um cluster; executar instruções SQL de motores")

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Pré-requisitos

- **Azure uma subscrição**. Antes de começar este tutorial, tem de ter uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Cliente da Shell de um seguro (SSH)**: Linux, Unix e OS X sistemas fornecidos um SSH cliente através de `ssh` comando. Para sistemas Windows, recomendamos que [betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).
    
- **Teclas de Shell seguro (SSH) (opcionais)**: pode proteger a conta SSH utilizada para ligar ao cluster utilizando uma palavra-passe ou uma chave pública. Utilizar uma palavra-passe obtém a começar rapidamente e deve utilizar esta opção se pretender rapidamente um cluster de criar e executar algumas tarefas de teste. Utilizar uma chave é mais segura, no entanto, é necessário configuração adicionais. Poderá pretender utilizar esta abordagem para criar um cluster de produção. Neste artigo, utilizamos a abordagem de palavra-passe. Para obter instruções sobre como criar e utilizar teclas SSH com HDInsight, consulte os artigos seguintes:

    -  A partir de um Linux computador - [Utilizar SSH com baseado em Linux HDInsight (Hadoop) Linux, Unix, ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
    
    -  A partir de um computador Windows - [Utilizar SSH com baseado em Linux HDInsight (Hadoop) a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

>[AZURE.NOTE] Este artigo utiliza um modelo de Gestor de recursos Azure para criar um cluster de motores que utiliza o [Azure armazenamento de Blobs como o armazenamento de cluster](hdinsight-hadoop-use-blob-storage.md). Também pode criar um cluster de motores que utiliza o [Azure dados Lake loja](../data-lake-store/data-lake-store-overview.md) como um armazenamento adicional, para além do Azure armazenamento de Blobs como o armazenamento de predefinido. Para obter instruções, consulte o artigo [criar um cluster de HDInsight com dados Lake arquivo](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-spark-cluster"></a>Criar motores cluster

Nesta secção, cria um cluster de versão 3.4 HDInsight (motores versão 1.6.1) utilizando um modelo de Gestor de recursos Azure. Para obter informações sobre HDInsight versões e os respetivos SLA, consulte o artigo [o controlo de versões do HDInsight componente](hdinsight-component-versioning.md). Para outros métodos de criação de cluster, consulte o artigo [Criar HDInsight clusters](hdinsight-hadoop-provision-linux-clusters.md).

1. Clique na imagem seguinte para abrir o modelo no Portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-spark-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo está localizado no contentor de BLOBs público, *https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-spark-cluster-in-hdinsight.json*. 
   
2. A partir do pá parâmetros, introduza o seguinte:

    - **Nome de cluster**: introduza um nome para o cluster de Hadoop que irá criar.
    - **Nome de início de sessão do cluster e palavra-passe**: O nome de início de sessão predefinido é administrador.
    - **SSH nome de utilizador e palavra-passe**.
    
    Fórum anote estes valores.  Terá-las mais tarde no tutorial.

    > [AZURE.NOTE] SSH é utilizada para aceder ao cluster de HDInsight utilizando uma linha de comandos remotamente. O nome de utilizador e palavra-passe que utiliza aqui é utilizado quando se liga ao cluster através de SSH. Além disso, o nome de utilizador SSH tem de ser exclusivo, à medida que cria uma conta de utilizador em todos os nós de cluster de HDInsight. A seguinte é alguns dos nomes de conta reservados para ser utilizado pelos serviços num cluster e não pode ser utilizado como o nome de utilizador SSH:
    >
    > raiz, hdiuser, tempestade, hbase, ubuntu, zookeeper, hdfs, fio, mapred, hbase, ramo, oozie, falcon, sqoop, admin, tez, hcat, hdinsight zookeeper.

    > Para obter mais informações sobre como utilizar SSH com HDInsight, consulte um dos seguintes artigos:

    > * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    > * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3. clique em **OK** para guardar os parâmetros.

4. pá **implementação personalizada** , clique em caixa de lista pendente do **grupo de recursos** e, em seguida, clique em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contentor que agrupa cluster, a conta de armazenamento dependentes e outro recurso ligado.

5. clique **termos Legal**e, em seguida, clique em **Criar**.

6. clique em **Criar**. Irá ver um novo mosaico intitulado Submitting implementação para implementação do modelo. Bastam sobre cerca de 20 minutos para criar cluster e a base de dados SQL.



## <a name="run-spark-sql-queries-using-a-jupyter-notebook"></a>Executar consultas de SQL motores através de um bloco de notas Jupyter

Nesta secção, utilize Jupyter bloco de notas para executar consultas de SQL motores contra cluster motores. Motores de HDInsight clusters fornecem duas kernels que pode utilizar com o bloco de notas Jupyter. Estas são:

* **PySpark** (para aplicações escritas Python)
* **Motores** (para aplicações escritas Scala)

Neste artigo, irá utilizar o kernel PySpark. No artigo [Kernels disponíveis no Jupyter blocos de notas com os motores HDInsight clusters](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels) , leia detalhes sobre os benefícios da utilização do kernel PySpark. No entanto, algumas das principais vantagens de utilizar o kernel PySpark são:

* Não necessita de definir os contextos para motores e ramo. Estes são automaticamente definidas por si.
* Pode utilizar magics de célula, tal como `%%sql`, para executar diretamente as consultas SQL ou ramo, sem qualquer fragmentos de código anterior.
* A exportação para as consultas SQL ou ramo automaticamente visualizar os.

### <a name="create-jupyter-notebook-with-pyspark-kernel"></a>Criar Jupyter bloco de notas com PySpark kernel 

1. A partir do [Portal do Azure](https://portal.azure.com/), a partir do startboard, clique no mosaico do seu cluster de motores (se afixados-lo para a startboard). Também pode navegar para o seu cluster em **Procurar tudo** > **HDInsight Clusters**.   

2. Pá de cluster motores, clique em **Cluster Dashboard**e, em seguida, clique em **Jupyter bloco de notas**. Se lhe for pedido, introduza as credenciais de administrador para o cluster.

    > [AZURE.NOTE] Também pode atingir o bloco de notas Jupyter para o seu cluster abrindo o seguinte URL no seu browser. Substitua o __nome de cluster__ com o nome do seu cluster:
    >
    > `https://CLUSTERNAME.azurehdinsight.net/jupyter`

2. Crie um novo bloco de notas. Clique em **Novo**e, em seguida, clique em **PySpark**.

    ![Criar um novo bloco de notas Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.createnotebook.png "Criar um novo bloco de notas Jupyter")

3. Um novo bloco de notas é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de notas na parte superior e introduza um nome amigável.

    ![Fornecer um nome para o bloco de notas] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.note.jupyter.notebook.name.png "Fornecer um nome para o bloco de notas")

4. Uma vez que criou um bloco de notas utilizando o kernel PySpark, não necessita de criar qualquer contextos explicitamente. Os contextos motores e ramo serão criados automaticamente para si quando executa a primeira célula de código. Pode começar por importar os tipos de necessários para este cenário. Para fazê-lo, cole o seguinte fragmento de código numa célula e prima **SHIFT + ENTER**.

        from pyspark.sql.types import *
        
    Sempre que executar uma tarefa no Jupyter, o título de janela de browser web irá mostrar um estado **(ocupado)** juntamente com o título do bloco de notas. Também irá ver um círculo sólido ao lado do texto **PySpark** no canto superior direito. Depois da tarefa está concluída, isto irá alterar para um círculo.

     ![Estado de uma tarefa de bloco de notas Jupyter] (./media/hdinsight-apache-spark-jupyter-spark-sql/hdispark.jupyter.job.status.png "Estado de uma tarefa de bloco de notas Jupyter")

4. Carregar os dados de exemplo para uma tabela temporária. Quando cria um cluster de motores no HDInsight, o ficheiro de dados de exemplo, **hvac.csv**, é copiado para a conta de armazenamento associado em **\HdiSamples\HdiSamples\SensorSampleData\hvac**.

    Numa célula vazia, cole o seguinte exemplo de código e prima **SHIFT + ENTER**. Este exemplo de código regista os dados numa tabela temporário denominado **AVAC**.

        # Load the data
        hvacText = sc.textFile("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
        
        # Create the schema
        hvacSchema = StructType([StructField("date", StringType(), False),StructField("time", StringType(), False),StructField("targettemp", IntegerType(), False),StructField("actualtemp", IntegerType(), False),StructField("buildingID", StringType(), False)])
        
        # Parse the data in hvacText
        hvac = hvacText.map(lambda s: s.split(",")).filter(lambda s: s[0] != "Date").map(lambda s:(str(s[0]), str(s[1]), int(s[2]), int(s[3]), str(s[6]) ))
        
        # Create a data frame
        hvacdf = sqlContext.createDataFrame(hvac,hvacSchema)
        
        # Register the data fram as a table to run queries against
        hvacdf.registerTempTable("hvac")

5. Uma vez que estiver a utilizar um kernel PySpark, agora, pode diretamente executar uma consulta SQL na tabela temporária **AVAC** que acabou de criar utilizando o `%%sql` mágico. Para mais informações sobre o `%%sql` mágico, bem como outras magics disponíveis com PySpark kernel, consulte o artigo [Kernels disponíveis no Jupyter blocos de notas com clusters de motores HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md#why-should-i-use-the-new-kernels).
        
        %%sql
        SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"

5. Assim que a tarefa é concluída com êxito, o resultado da tabela seguinte é apresentado por predefinição.

    ![Resultado da tabela de resultado da consulta] (./media/hdinsight-apache-spark-jupyter-spark-sql/tabular.output.png "Resultado da tabela de resultado da consulta")

    Também pode ver os resultados na também outras visualizações. Por exemplo, um gráfico de área para o mesmo resultado terá um aspeto semelhante ao seguinte.

    ![Gráfico de área de resultado da consulta] (./media/hdinsight-apache-spark-jupyter-spark-sql/area.output.png "Gráfico de área de resultado da consulta")


6. Depois de terminar a execução da aplicação, deverá encerramento o bloco de notas para libertar recursos. Para fazê-lo, a partir do menu **ficheiro** no bloco de notas, clique em **Fechar e parar**. Este irá encerramento e feche o bloco de notas.

##<a name="delete-the-cluster"></a>Eliminar o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


## <a name="see-also"></a>Consulte também


* [Descrição geral: Apache motores no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários

* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)

* [Motores com máquina formação: utilizar motores no HDInsight para analisar temperatura edifício utilizando dados de AVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)

* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)

* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)

* [Análise de registo de Web site utilizando motores no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

* [Aplicação conhecimentos aprofundados telemetria análise de dados utilizando motores no HDInsight](hdinsight-spark-analyze-application-insight-logs.md)

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

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage-create-storage-account.md
