<properties
    pageTitle="Utilizar ramo interativo no HDInsight | Microsoft Azure"
    description="Saiba como utilizar interativos Hive (ramo de LLAP) no HDInsight."
    keywords=""
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/27/2016"
    ms.author="jgao"/>


# <a name="use-interactive-hive-in-hdinsight-preview"></a>Utilizar ramo interativo no HDInsight (pré-visualização)

Interactivo Hive (também conhecido como [Direto longas e processo]( https://cwiki.apache.org/confluence/display/Hive/LLAP)) é um novo [tipo de cluster]( hdinsight-hadoop-provision-linux-clusters.md#cluster-types)de HDInsight.  Ramo interativo permite na memória, cache que torna ramo consultas muito mais interativos e mais rápido. Esta nova funcionalidade torna HDInsight um da parte do mundo a maior parte dos performant, flexível, e abrir Big Data solução na nuvem na memória coloca em cache (utilizando ramo e motores) e avançadas analytics através da integração abrangente com os serviços de R. 

Cluster Hive interativos é a diferença entre o cluster Hadoop. Apenas contém o serviço de ramo. 

> [AZURE.NOTE] MapReduce, porco, Sqoop, Oozie e outros serviços serão removidos a partir deste tipo de cluster mais rapidamente.
O serviço de ramo no cluster Hive interativos só está acessível através da vista Ambari ramo, Beeline e Hive ODBC. -Não pode ser acedido através do ramo consola, Templeton, clip do Azure e Azure PowerShell. 


 


## <a name="create-an-interactive-hive-cluster"></a>Criar um cluster Hive interativos

Interativo ramo cluster só é suportada em clusters baseado em Linux. Para obter informações sobre a criação de HDInsight clusters, consulte o artigo [clusters de baseado em criar Linux Hadoop HDInsight](hdinsight-hadoop-provision-linux-clusters.md).


## <a name="execute-hive-from-interactive-hive"></a>Executar ramo do ramo interativo

Existem diferentes opções de como pode executar consultas de ramo:

- Executar ramo utilizando a vista de Ambari ramo

    Para obter informações sobre como utilizar a vista Hive, consulte o artigo [utilizar a vista Hive com Hadoop no HDInsight]( hdinsight-hadoop-use-hive-ambari-view.md).

- Executar ramo utilizando Beeline

    Para obter informações sobre como utilizar Beeline em HDInsight, consulte o artigo [Utilizar Hive com Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md).

    Utilize Beeline a partir do headnode ou um nó limite branco.  É recomendado com Beeline a partir de um nó limite branco.  Para obter informações sobre como criar um cluster de HDInsight com um edgenode vazia, consulte o artigo [nós de limite branco utilização no HDInsight](hdinsight-apps-use-edge-node.md).

- Executar ramo utilizando Hive ODBC

    Para obter informações sobre como utilizar Hive ODBC, consulte o artigo [Ligar o Excel ao Hadoop com o controlador de ODBC Hive da Microsoft](hdinsight-connect-excel-hive-odbc-driver.md).

**Para localizar a cadeia de ligação JDBC:**

1.  Inicie sessão Ambari utilizando o seguinte URL: https://<ClusterName>. AzureHDInsight.net.
2.  Clique em **Hive** no menu à esquerda.
3.  Clique no ícone realçado para copiar o URL:

    ![HDInsight Hadoop ramo interativo LLAP JDBC](./media/hdinsight-hadoop-use-interactive-hive/hdinsight-hadoop-use-interactive-hive-jdbc.png)

## <a name="see-also"></a>Consulte também
-   [Clusters de baseado em criar Linux Hadoop HDInsight](hdinsight-hadoop-provision-linux-clusters.md): Saiba como criar clusters Hive interativo no HDInsight.
-   [Utilizar Hive com Hadoop no HDInsight com Beeline](hdinsight-hadoop-use-hive-beeline.md): Saiba como utilizar Beeline para submeter ramo de consultas.
-   [Ligar o Excel ao Hadoop com o controlador de ODBC Hive da Microsoft](hdinsight-connect-excel-hive-odbc-driver.md): Saiba como ligar o Excel para ramo.
