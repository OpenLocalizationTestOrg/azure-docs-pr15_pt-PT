<properties
    pageTitle="Gerir clusters Hadoop no HDInsight com o PowerShell | Microsoft Azure"
    description="Saiba como desempenhar tarefas administrativas para clusters Hadoop no HDInsight através do Azure PowerShell."
    services="hdinsight"
    editor="cgronlun"
    manager="jhubbard"
    tags="azure-portal"
    authors="mumian"
    documentationCenter=""/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/10/2016"
    ms.author="jgao"/>

# <a name="manage-hadoop-clusters-in-hdinsight-by-using-azure-powershell"></a>Gerir clusters de Hadoop HDInsight utilizando o PowerShell do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell é um ambiente poderoso de scripts que pode utilizar para controlar e automatizar a implementação e gestão dos seus das cargas de trabalho no Azure. Este artigo vai aprender a gerir clusters de Hadoop no Azure HDInsight utilizando uma consola Azure PowerShell local através da utilização do Windows PowerShell. Para a lista dos cmdlets do HDInsight PowerShell, consulte o artigo [referência do cmdlet HDInsight][hdinsight-powershell-reference].



**Pré-requisitos**

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

##<a name="install-azure-powershell"></a>Instalar o Azure PowerShell

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

Se tiver instalado o Azure PowerShell versão 0,9 x, tem de desinstalá-lo antes de instalar uma versão mais recente.

Para verificar a versão do PowerShell instalado:

    Get-Module *azure*
    
Para desinstalar a versão mais antiga, execute programas e funcionalidades no painel de controlo. 


##<a name="create-clusters"></a>Criar clusters

Consulte o artigo [baseado em criar Linux clusters de HDInsight através do PowerShell do Azure](hdinsight-hadoop-create-linux-clusters-azure-powershell.md)

##<a name="list-clusters"></a>Clusters de lista
Utilize o seguinte comando para listar todos os clusters na subscrição atual:

    Get-AzureRmHDInsightCluster

##<a name="show-cluster"></a>Mostrar cluster

Utilize o seguinte comando para mostrar detalhes de um cluster específico a subscrição atual:

    Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##<a name="delete-clusters"></a>Eliminar clusters

Utilize o seguinte comando para eliminar um cluster de:

    Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

Também pode eliminar um cluster ao remover o grupo de recursos que contém o cluster. Tenha em atenção, este procedimento irá eliminar todos os recursos no grupo incluindo a conta de armazenamento predefinida.

    Remove-AzureRmResourceGroup -Name <Resource Group Name>
            
##<a name="scale-clusters"></a>Clusters de escala
Cluster dimensionamento funcionalidade permite-lhe alterar o número de nós trabalhador utilizada por um cluster de que está em execução no Azure HDInsight sem ter de voltar a criar o cluster.

>[AZURE.NOTE] Apenas clusters com HDInsight versão 3.1.3 ou superior são suportadas. Se tem a certeza da versão do seu cluster, pode verificar a página de propriedades.  Consulte [clusters de lista e a apresentação](hdinsight-administer-use-portal-linux.md#list-and-show-clusters).

O impacto de alterar o número de nós de dados para cada tipo de cluster suportado pelo HDInsight:

- Hadoop

    Forma totalmente integrada pode aumentar o número de nós de trabalho num cluster Hadoop que está a ser executado sem que afetam quaisquer tarefas pendentes ou em execução. Também podem ser apresentadas novas tarefas enquanto a operação está em curso. Falhas numa operação de dimensionamento correctamente são processadas para que o cluster sempre fica num estado funcional.

    Quando um cluster de Hadoop está dimensionado para baixo, reduzindo o número de nós de dados, alguns dos serviços no cluster reiniciados. Isto faz com que todas as em execução e tarefas pendentes falha após a conclusão da operação de dimensionamento. No entanto, pode, submeter as tarefas de uma vez concluída a operação.

- HBase

    Forma totalmente integrada pode adicionar ou remover nós ao seu cluster HBase enquanto estiver em execução. Os servidores regionais são automaticamente distribuídos dentro de alguns minutos de concluir a operação de dimensionamento. No entanto, pode também manualmente equilibrar os servidores regionais ao iniciar sessão no headnode de cluster e executando os seguintes comandos a partir de uma janela de linha de comandos:

        >pushd %HBASE_HOME%\bin
        >hbase shell
        >balancer

- Tempestade

    Forma totalmente integrada pode adicionar ou remover nós de dados para o seu cluster tempestade enquanto estiver em execução. Mas após uma conclusão com êxito da operação de dimensionamento, terá de redistribuir da topologia.

    Rebalanceamento pode ser feito de duas maneiras:

    * Web tempestade IU
    * Ferramenta de interface de comandos (CLI)

    Consulte a [documentação Apache tempestade](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obter mais detalhes.

    Web tempestade IU está disponível no HDInsight cluster:

    ![Rebalance de escala de tempestade HDInsight](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

    Eis um exemplo como utilizar o comando clip para redistribuir a topologia de tempestade:

        ## Reconfigure the topology "mytopology" to use 5 worker processes,
        ## the spout "blue-spout" to use 3 executors, and
        ## the bolt "yellow-bolt" to use 10 executors

        $ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Para alterar o tamanho do cluster Hadoop utilizando o Azure PowerShell, execute o seguinte comando a partir de um computador cliente:

    Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
    

##<a name="grantrevoke-access"></a>Conceder/revogar o acesso

HDInsight clusters tem os seguintes serviços web HTTP (todos estes serviços de tem os pontos finais RESTful):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Por predefinição, estes serviços são concedidos de acesso. Pode revogar/conceder acesso. Para revogar:

    Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Para conceder:

    $clusterName = "<HDInsight Cluster Name>"

    # Credential option 1
    $hadoopUserName = "admin"
    $hadoopUserPassword = "<Enter the Password>"
    $hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
    $credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

    # Credential option 2
    #$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
    
    Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE] Ao conceder/revogar o acesso, irá repor cluster nome de utilizador e palavra-passe.

Também pode ser feito através do Portal. Consulte [Administrar HDInsight utilizando o portal do Azure][hdinsight-admin-portal].

##<a name="update-http-user-credentials"></a>Atualizar as credenciais de utilizador HTTP

É o mesmo procedimento como [HTTP conceder/revogar o acesso](#grant/revoke-access). Se o cluster tiver sido concedido acesso HTTP, tem de revogá-la pela primeira vez.  E, em seguida, conceder o acesso com as credenciais de utilizador do novos HTTP.


##<a name="find-the-default-storage-account"></a>Localizar a conta de armazenamento predefinida

O seguinte script do Powershell demonstra como obter o nome da conta predefinida armazenamento e a chave de conta de armazenamento predefinido para um cluster de.

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup
    $defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
    $defaultBlobContainerName = $cluster.DefaultStorageContainer
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##<a name="find-the-resource-group"></a>Localizar o grupo de recursos

No modo de Gestor de recursos, cada cluster HDInsight pertence a um grupo de recursos Azure.  Para localizar o grupo de recursos:

    $clusterName = "<HDInsight Cluster Name>"
    
    $cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $resourceGroupName = $cluster.ResourceGroup


##<a name="submit-jobs"></a>Submeter tarefas

**Para submeter MapReduce tarefas**

Consulte o artigo [Executar Hadoop MapReduce amostras no HDInsight baseados no Windows](hdinsight-run-samples.md).

**Para submeter ramo de tarefas** 

Consulte o artigo [Executar Hive consultas através do PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**Para submeter porco tarefas**

Consulte o artigo [porco executar tarefas através do PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**Para submeter Sqoop tarefas**

Consulte o artigo [utilizar Sqoop com HDInsight](hdinsight-use-sqoop.md).

**Para submeter Oozie tarefas**

Consulte o artigo [Utilizar Oozie com Hadoop definir e executar um fluxo de trabalho HDInsight](hdinsight-use-oozie.md).

##<a name="upload-data-to-azure-blob-storage"></a>Carregar dados para o armazenamento de Blobs do Azure
Consulte o artigo [Transferir dados do HDInsight][hdinsight-upload-data].


## <a name="see-also"></a>Consulte também
* [Documentação de referência do cmdlet HDInsight][hdinsight-powershell-reference]
* [Administrar HDInsight, utilizando o portal do Azure][hdinsight-admin-portal]
* [Administrar HDInsight utilizando uma interface da linha de comandos][hdinsight-admin-cli]
* [Criar HDInsight clusters][hdinsight-provision]
* [Carregar dados ao HDInsight][hdinsight-upload-data]
* [Submeter tarefas de Hadoop através de programação][hdinsight-submit-jobs]
* [Introdução ao Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: powershell-install-configure.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png
