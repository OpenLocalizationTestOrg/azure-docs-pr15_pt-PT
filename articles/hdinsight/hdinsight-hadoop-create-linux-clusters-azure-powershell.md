<properties
    pageTitle="Criar clusters Hadoop, HBase, tempestade ou motores no Linux no HDInsight através do Azure PowerShell | Microsoft Azure"
    description="Saiba como criar clusters Hadoop, HBase, tempestade ou motores de Linux para HDInsight por através do Azure PowerShell."
    services="hdinsight"
    documentationCenter=""
    authors="nitinme"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/05/2016"
    ms.author="nitinme"/>

# <a name="create-linux-based-clusters-in-hdinsight-by-using-azure-powershell"></a>Criar clusters baseados em Linux HDInsight através do PowerShell do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Azure PowerShell é um ambiente poderoso de scripts que pode utilizar para controlar e automatizar a implementação e gestão dos seus das cargas de trabalho no Microsoft Azure. Este artigo fornece mais informações sobre como criar um cluster de baseado em Linux HDInsight utilizando o Azure PowerShell. Também inclui um script de exemplo.

> [AZURE.NOTE] Azure PowerShell só está disponível em clientes do Windows. Se estiver a utilizar um cliente Linux, Unix ou Mac OS X, consulte o artigo [criar um cluster de baseado em Linux HDInsight utilizando o clip do Azure](hdinsight-hadoop-create-linux-clusters-azure-cli.md) para obter informações sobre como utilizar o clip do Azure para criar um cluster.

## <a name="prerequisites"></a>Pré-requisitos
Tem de ter o seguinte antes de iniciar este procedimento:

- Uma subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- Azure PowerShell.
    Para obter mais informações sobre como utilizar o Azure PowerShell com HDInsight, consulte [administrar HDInsight através do PowerShell](hdinsight-administer-use-powershell.md). Para a lista de cmdlets do Windows PowerShell do HDInsight, consulte o artigo [referência do cmdlet HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Para criar um cluster de HDInsight utilizando o Azure PowerShell, tem de concluir os seguintes procedimentos:

- Criar um grupo de recursos Azure
- Criar uma conta de armazenamento do Windows Azure
- Criar um contentor de Blobs do Azure
- Criar um cluster de HDInsight

Os dois parâmetros mais importantes que têm de ser definidas para criar Linux clusters são aqueles que especifique o tipo de sistema operativo e os detalhes do utilizador SSH:

- Certifique-se de que especificar o parâmetro **- OSType** como **Linux**.
- Para utilizar SSH para sessões de remoto em clusters, pode especificar a palavra-passe de utilizador do SSH ou a chave pública SSH. Se especificar a palavra-passe de utilizador do SSH e a chave pública SSH, a tecla será ignorada. Se pretender utilizar a chave SSH para sessões de remoto, tem de especificar uma em branco SSH palavra-passe quando lhe for pedido para um. Para mais informações sobre como utilizar SSH com HDInsight, consulte um dos seguintes artigos:

    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
    * [Utilizar SSH com baseado em Linux Hadoop no HDInsight a partir do Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

O script seguinte demonstra como criar um novo cluster:

    $token ="<SpecifyAnUniqueString>"

    $resourceGroupName = $token + "rg"      # Provide a Resource Group name
    $clusterName = $token
    $defaultStorageAccountName = $token + "store"   # Provide a Storage account name
    $defaultStorageContainerName = $token + "container"
    $location = "East US 2"     # Change the location if needed
    $clusterNodes = 1           # The number of nodes in the HDInsight cluster

    # Sign in to Azure
    Login-AzureRmAccount

    # Select the subscription to use if you have multiple subscriptions
    #$subscriptionID = "<SubscriptionName>"        # Provide your Subscription Name
    #Select-AzureRmSubscription -SubscriptionId $subscriptionID

    # Create an Azure Resource Group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    # Create an Azure Storage account and container used as the default storage
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -StorageAccountName $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $defaultStorageAccountName -ResourceGroupName $resourceGroupName)[0].Key1
    $destContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer -Name $defaultStorageContainerName -Context $destContext

    # Create an HDInsight cluster
    $credentials = Get-Credential -Message "Enter Cluster user credentials" -UserName "admin"
    $sshCredentials = Get-Credential -Message "Enter SSH user credentials"

    # The location of the HDInsight cluster must be in the same data center as the Storage account.
    $location = Get-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $defaultStorageAccountName | %{$_.Location}

    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials

Os valores que especificar para **$clusterCredentials** são utilizados para criar a conta de utilizador para o cluster Hadoop. Utilize esta conta para ligar ao cluster.

Os valores que especificar para **$sshCredentials** são utilizados para criar o utilizador SSH para o cluster. Utilize esta conta para iniciar uma sessão remota do SSH no cluster e executar tarefas.

> [AZURE.IMPORTANT] Neste script, tem de especificar o número de nós de trabalho que será no cluster. Se planeia utilizar mais do que 32 nós de trabalho (seja na criação de cluster ou por dimensionamento cluster após a criação), também tem de especificar um tamanho de cabeça nó com, pelo menos, 8 núcleos e 14 GB de RAM.
>
> Para mais informações sobre tamanhos de nó e custos associados, consulte o artigo [HDInsight preços](https://azure.microsoft.com/pricing/details/hdinsight/).

Pode demorar até 20 minutos para criar um cluster.

O exemplo seguinte demonstra como adicionar uma conta de armazenamento adicional:

    # Create another storage account used as additional storage account
    $additionalStorageAccountName = $token + "store2"
    New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $additionalStorageAccountName -Location $location -Type Standard_LRS
    $additionalStorageAccountKey = (Get-AzureRmStorageAccountKey -Name $additionalStorageAccountName -ResourceGroupName $resourceGroupName)[0].Value

    $config = New-AzureRmHDInsightClusterConfig
    Add-AzureRmHDInsightStorage -Config $config -StorageAccountName "$additionalStorageAccountName.blob.core.windows.net" -StorageAccountKey $additionalStorageAccountKey

    # Create a new HDInsight cluster
    New-AzureRmHDInsightCluster `
        -ClusterName $clusterName `
        -ResourceGroupName $resourceGroupName `
        -HttpCredential $credentials `
        -Location $location `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultStorageContainerName  `
        -ClusterSizeInNodes $clusterNodes `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.4" `
        -SshCredential $sshCredentials `
        -Config $config

## <a name="customize-clusters"></a>Personalizar clusters

- Consulte o artigo [Personalizar HDInsight clusters utilizando o arranque](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [clusters baseados no Windows personalizar HDInsight utilizando a ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).

## <a name="delete-the-cluster"></a>Eliminar o cluster

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Próximos passos

Agora que criou com êxito um cluster de HDInsight, utilize os seguintes recursos para saber como trabalhar com o seu cluster.

### <a name="hadoop-clusters"></a>Hadoop clusters

* [Utilizar ramo com HDInsight](hdinsight-use-hive.md)
* [Utilizar porco com HDInsight](hdinsight-use-pig.md)
* [Utilizar MapReduce com HDInsight](hdinsight-use-mapreduce.md)

### <a name="hbase-clusters"></a>HBase clusters

* [Introdução ao HBase no HDInsight](hdinsight-hbase-tutorial-get-started-linux.md)
* [Desenvolver aplicações de Java para HBase no HDInsight](hdinsight-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Clusters tempestade

* [Desenvolver topologias Java para tempestade no HDInsight](hdinsight-storm-develop-java-topology.md)
* [Utilizar componentes de Python tempestade no HDInsight](hdinsight-storm-develop-python-topology.md)
* [Implementar e monitorizar topologias com tempestade no HDInsight](hdinsight-storm-deploy-monitor-topology-linux.md)

### <a name="spark-clusters"></a>Motores clusters

* [Criar uma aplicação autónoma Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar tarefas remotamente num cluster de motores utilizando Lívio](hdinsight-apache-spark-livy-rest-interface.md)
* [Motores com BI: efetuar uma análise de dados interativos utilizando motores no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Motores com máquina formação: utilizar motores no HDInsight para prever resultados da inspeção de alimentação](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Transmissão de motores: Motores de utilização no HDInsight para criar aplicações de transmissão em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
