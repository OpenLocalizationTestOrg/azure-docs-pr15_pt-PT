<properties
   pageTitle="Criar clusters baseados no Windows Hadoop no HDInsight através do Azure PowerShell | Microsoft Azure"
    description="Saiba como criar clusters para Azure HDInsight utilizando o Azure PowerShell."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-powershell"></a>Criar clusters baseados no Windows Hadoop no HDInsight através do PowerShell do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar clusters HDInsight através do Azure PowerShell. Azure PowerShell é um módulo que fornece cmdlets para gerir Azure com o Windows PowerShell. Para outras criação do cluster ferramentas e funcionalidades, clique em Selecionar o separador na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).


##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Antes de começar as instruções neste artigo, tem de ter o seguinte procedimento:

- Subscrição do Azure. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Azure PowerShell.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Criar clusters
Azure PowerShell é um ambiente poderoso de scripts que pode utilizar para controlar e automatizar a implementação e gestão dos seus das cargas de trabalho no Azure. Esta secção fornece instruções sobre como criar um cluster de HDInsight utilizando o Azure PowerShell. Para obter informações sobre como configurar uma estação de trabalho para executar os cmdlets do Windows PowerShell do HDInsight, consulte o artigo [instalar e configurar o Azure PowerShell](../powershell-install-configure.md). Para obter mais informações sobre como utilizar o Azure PowerShell com HDInsight, consulte [administrar HDInsight através do PowerShell](hdinsight-administer-use-powershell.md). Para a lista dos cmdlets HDInsight Windows PowerShell, consulte o artigo [referência do cmdlet HDInsight](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Os procedimentos seguintes são necessários para criar um cluster de HDInsight com Azure PowerShell:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<Enter an Alias>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    #endregion

    ###########################################
    # Service names and varialbes
    ###########################################
    #region - service names
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

    $resourceGroupName = $namePrefix + "rg"
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName

    $location = "East US 2"
    $clusterSizeInNodes = 1
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ###########################################
    # Connect to Azure
    ###########################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ###########################################
    # Create the resource group
    ###########################################
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location

    ###########################################
    # Preapre default storage account and container
    ###########################################
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Type Standard_GRS `
        -Location $location

    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $hdinsightClusterName -Context $defaultStorageContext 

    ###########################################
    # Create the cluster
    ###########################################

    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes $clusterSizeInNodes `
        -ClusterType Hadoop `
        -OSType Windows `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $hdinsightClusterName 

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName 

## <a name="create-clusters-using-arm-template"></a>Criar clusters de utilizar o modelo de processador

Pode utilizar o Azure PowerShell para implementar um modelo de processador que cria um cluster de HDInsight.  Consulte o artigo [ligar a modelos através do Azure PowerShell](hdinsight-hadoop-create-windows-clusters-arm-templates.md#call-templates-using-powershell).

##<a name="customize-clusters"></a>Personalizar clusters

- Consulte o artigo [Personalizar HDInsight clusters utilizando o arranque](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
- Consulte [clusters baseados no Windows personalizar HDInsight utilizando a ação de Script](hdinsight-hadoop-customize-cluster.md#call-scripts-using-azure-powershell).


##<a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu várias formas para criar um cluster de HDInsight. Para saber mais, consulte os artigos seguintes:

* [Introdução ao Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) - Saiba como começar a trabalhar com o seu cluster HDInsight
* [Submeter Hadoop através de programação de tarefas](hdinsight-submit-hadoop-jobs-programmatically.md) - Saiba como através de programação submeter tarefas para HDInsight
* [Gerir Hadoop clusters no HDInsight através do PowerShell](hdinsight-administer-use-powershell.md) - obter informações sobre como trabalhar com HDInsight utilizando o PowerShell do Azure
* [Documentação do Azure HDInsight SDK]  [ hdinsight-sdk-documentation] -descobrir o SDK HDInsight




[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/library/mt146778(v=sql.120).aspx
