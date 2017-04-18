<properties
    pageTitle="Personalizar Clusters HDInsight utilizar ações de script | Microsoft Azure"
    description="Saiba como personalizar clusters HDInsight utilizando a ação de Script."
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

# <a name="customize-windows-based-hdinsight-clusters-using-script-action"></a>Personalizar clusters baseados no Windows HDInsight utilizando a ação de Script

**Acção de script** podem ser utilizados para invocar [scripts personalizados](hdinsight-hadoop-script-actions.md) durante o processo de criação para instalar o software adicional num cluster de computação.

As informações neste artigo são específicas a clusters HDInsight baseados no Windows. Para baseado em Linux clusters, consulte o artigo [clusters baseado em Personalizar Linux HDInsight utilizando a ação de Script](hdinsight-hadoop-customize-cluster-linux.md). 

HDInsight clusters podem ser personalizados numa variedade de outras formas, assim, tal como incluindo contas adicionais de armazenamento do Windows Azure, alterar o Hadoop ficheiros de configuração (core site.xml, ramo site.xml, etc.) ou adicionar partilhado bibliotecas (por exemplo, ramo, Oozie) para locais comuns no cluster. Estas personalizações podem ser feitas através do Azure PowerShell, o Azure HDInsight .NET SDK ou o Portal do Azure. Para obter mais informações, consulte o artigo [clusters de criar Hadoop HDInsight][hdinsight-provision-cluster].

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell-cli-and-dotnet-sdk.md)]

## <a name="script-action-in-the-cluster-creation-process"></a>Acção de script do processo de criação de cluster

Acção de script só é utilizada enquanto um clusters está a ser criada. O diagrama seguinte ilustra quando acção de Script é executada durante o processo de criação:

![Personalização de cluster HDInsight e as fases durante a criação de cluster][img-hdi-cluster-states]

Quando estiver a executar o script, cluster introduz o palco **ClusterCustomization** . Nesta fase, o script é executado da conta de administrador do sistema, paralelas em todos os nós especificado no cluster e fornece privilégios de administrador completo em nós.

> [AZURE.NOTE] Uma vez que tiver privilégios de administrador em nós de cluster durante a fase **ClusterCustomization** , pode utilizar o script para efetuar operações como parar e iniciar serviços, incluindo serviços relacionados com Hadoop. Por isso, como parte do script, tem de garantir que os serviços de Ambari e outros serviços relacionados com Hadoop estão a trabalhar antes do script estiver concluída. Estes serviços são necessários para com êxito verificar o estado de funcionamento e o estado do cluster enquanto está a ser criado-lo. Se alterar qualquer configuração num cluster de afeta estes serviços, tem de utilizar as funções de helper são fornecidas. Para mais informações sobre funções auxiliares, consulte o artigo [scripts de desenvolver ação de Script para HDInsight][hdinsight-write-script].

A saída e os registos de erros para o script são armazenados na conta de armazenamento predefinido que especificou para o cluster. Os registos são armazenados numa tabela com o nome **u < \cluster-name-fragment >< \time-stamp > setuplog**. Estes são os registos de agregação script executado em todos os nós (nó cabeça e nós de trabalho) no cluster.

Cada cluster pode aceitar a várias acções de script que são chamadas pela ordem em que estão especificados. Um script pode ser executou o nó cabeça, os nós de trabalho, ou ambas.

HDInsight fornece várias scripts para instalar os seguintes componentes em HDInsight clusters:

Nome | Script
----- | -----
**Instalar motores** | https://hdiconfigactions.blob.Core.Windows.NET/sparkconfigactionv03/spark-Installer-v03.ps1. Consulte o artigo [instalar e utilizar gerar sobre clusters de HDInsight][hdinsight-install-spark].
**Instalar R** | https://hdiconfigactions.blob.Core.Windows.NET/rconfigactionv02/r-Installer-v02.ps1. Consulte [instalação e utilização R sobre clusters de HDInsight][hdinsight-install-r].
**Instalar Solr** | https://hdiconfigactions.blob.Core.Windows.NET/solrconfigactionv01/solr-Installer-v01.ps1. Consulte o artigo [instalar e utilizar clusters de Solr no HDInsight](hdinsight-hadoop-solr-install.md).
- **Instalar Giraph** | https://hdiconfigactions.blob.Core.Windows.NET/giraphconfigactionv01/giraph-Installer-v01.ps1. Consulte o artigo [instalar e utilizar clusters de Giraph no HDInsight](hdinsight-hadoop-giraph-install.md).
| **Carregar previamente ramo de bibliotecas** | https://hdiconfigactions.blob.Core.Windows.NET/setupcustomhivelibsv01/Setup-customhivelibs-v01.ps1. Consulte o artigo [Adicionar Hive bibliotecas em HDInsight clusters](hdinsight-hadoop-add-hive-libraries.md) |


## <a name="call-scripts-using-the-azure-portal"></a>Scripts de chamada utilizando o Portal do Azure

**A partir do Portal do Azure**

1. Começar a criar um cluster, tal como descrito em [clusters de criar Hadoop HDInsight](hdinsight-provision-clusters.md#portal).
2. Em configuração opcional, para pá **Script ações** , clique em **Adicionar ação de script** para fornecer detalhes sobre a ação de script, conforme apresentado abaixo:

    ![Acção de Script de utilização para personalizar um cluster de] (./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Acção de Script de utilização para personalizar um cluster de")

    <table border='1'>
        <tr><th>Propriedade</th><th>Valor</th></tr>
        <tr><td>Nome</td>
            <td>Especifique um nome para a ação de script.</td></tr>
        <tr><td>Script URI</td>
            <td>Especifique o URI para o script que é chamado para personalizar o cluster. s</td></tr>
        <tr><td>Cabeça/trabalho</td>
            <td>Especifique os nós (**cabeça** ou **trabalhador**) no qual o script de personalização é executado. </b>.
        <tr><td>Parâmetros</td>
            <td>Especifique os parâmetros, se necessário pelo script.</td></tr>
    </table>

    Prima ENTER para adicionar mais do que uma ação de script para instalar vários componentes no cluster.

3. Clique em **Selecionar** para a configuração de ação de script de guardar e continuar com a criação de cluster.

## <a name="call-scripts-using-azure-powershell"></a>Scripts de chamada através do PowerShell do Azure

Este seguinte script do PowerShell demonstra como instalar motores num cluster de HDInsight baseados no Windows.  

    # Provide values for these variables
    $subscriptionID = "<Azure Suscription ID>" # After "Login-AzureRmAccount", use "Get-AzureRmSubscription" to list IDs.

    $nameToken = "<Enter A Name Token>"  # The token is use to create Azure service names.
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    
    $resourceGroupName = $namePrefix + "rg"
    $location = "EAST US 2" # used for creating resource group, storage account, and HDInsight cluster.
    
    $hdinsightClusterName = $namePrefix + "spark"
    $httpUserName = "admin"
    $httpPassword = "<Enter a Password>"
    
    $defaultStorageAccountName = "$namePrefix" + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    
    #############################################################
    # Connect to Azure
    #############################################################
    
    Try{
        Get-AzureRmSubscription
    }
    Catch{
        Login-AzureRmAccount
    }
    Select-AzureRmSubscription -SubscriptionId $subscriptionID
    
    #############################################################
    # Prepare the dependent components
    #############################################################
    
    # Create resource group
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    
    # Create storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $storageAccountKey  
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext
    
    #############################################################
    # Create cluster with ApacheSpark
    #############################################################
    
    # Specify the configuration options
    $config = New-AzureRmHDInsightClusterConfig `
                -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
                -DefaultStorageAccountKey $defaultStorageAccountKey 
                
    
    # Add a script action to the cluster configuration
    $config = Add-AzureRmHDInsightScriptAction `
                -Config $config `
                -Name "Install Spark" `
                -NodeType HeadNode `
                -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1 `
    
    # Start creating a cluster with Spark installed
    New-AzureRmHDInsightCluster `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -Location $location `
            -ClusterSizeInNodes 2 `
            -ClusterType Hadoop `
            -OSType Windows `
            -DefaultStorageContainer $defaultBlobContainerName `
            -Config $config


Para instalar outro software, terá de substituir o ficheiro de script no script:


Quando lhe for pedido, introduza as credenciais para o cluster. Pode demorar alguns minutos antes do cluster é criado.

## <a name="call-scripts-using-net-sdk"></a>Scripts de chamada utilizando o .NET SDK 

O exemplo seguinte demonstra como instalar motores num cluster de HDInsight baseados no Windows. Para instalar outro software, terá de substituir o ficheiro de script no código.

**Para criar um cluster de HDInsight com motores** 

1. Crie uma aplicação de consola do c# no Visual Studio.
2. A partir da consola do Gestor de pacotes Nuget, execute o seguinte comando.

        Install-Package Microsoft.Rest.ClientRuntime.Azure.Authentication -Pre
        Install-Package Microsoft.Azure.Management.ResourceManager -Pre
        Install-Package Microsoft.Azure.Management.HDInsight

2. Utilize o seguinte utilizando as instruções no ficheiro Program.cs:

        using System;
        using System.Security;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Models;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;

3. Coloque o código de aula com o seguinte:

        private static HDInsightManagementClient _hdiManagementClient;

        // Replace with your AAD tenant ID if necessary
        private const string TenantId = UserTokenProvider.CommonTenantId; 
        private const string SubscriptionId = "<Your Azure Subscription ID>";
        // This is the GUID for the PowerShell client. Used for interactive logins in this example.
        private const string ClientId = "1950a258-227b-4e31-a9cf-717495945fc2";
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumWorkerNodes = 2;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const string NewClusterType = "Hadoop";
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            // Authenticate and get a token
            var authToken = Authenticate(TenantId, ClientId, SubscriptionId);
            // Flag subscription for HDInsight, if it isn't already.
            EnableHDInsight(authToken);
            // Get an HDInsight management client
            _hdiManagementClient = new HDInsightManagementClient(authToken);

            CreateCluster();
        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumWorkerNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }

        /// <summary>
        /// Authenticate to an Azure subscription and retrieve an authentication token
        /// </summary>
        /// <param name="TenantId">The AAD tenant ID</param>
        /// <param name="ClientId">The AAD client ID</param>
        /// <param name="SubscriptionId">The Azure subscription ID</param>
        /// <returns></returns>
        static TokenCloudCredentials Authenticate(string TenantId, string ClientId, string SubscriptionId)
        {
            var authContext = new AuthenticationContext("https://login.microsoftonline.com/" + TenantId);
            var tokenAuthResult = authContext.AcquireToken("https://management.core.windows.net/", 
                ClientId, 
                new Uri("urn:ietf:wg:oauth:2.0:oob"), 
                PromptBehavior.Always, 
                UserIdentifier.AnyUser);
            return new TokenCloudCredentials(SubscriptionId, tokenAuthResult.AccessToken);
        }
        /// <summary>
        /// Marks your subscription as one that can use HDInsight, if it has not already been marked as such.
        /// </summary>
        /// <remarks>This is essentially a one-time action; if you have already done something with HDInsight
        /// on your subscription, then this isn't needed at all and will do nothing.</remarks>
        /// <param name="authToken">An authentication token for your Azure subscription</param>
        static void EnableHDInsight(TokenCloudCredentials authToken)
        {
            // Create a client for the Resource manager and set the subscription ID
            var resourceManagementClient = new ResourceManagementClient(new TokenCredentials(authToken.Token));
            resourceManagementClient.SubscriptionId = SubscriptionId;
            // Register the HDInsight provider
            var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");
        }


4. Prima **F5** para executar a aplicação.


## <a name="support-for-open-source-software-used-on-hdinsight-clusters"></a>Suporte para o software de abrir origem utilizado em HDInsight clusters
O serviço do Microsoft Azure HDInsight é uma plataforma flexível que permite-lhe criar aplicações de grande dados na nuvem utilizando um ecossistema de tecnologias de abrir origem à volta Hadoop. Microsoft Azure fornece um nível de suporte geral para abrir origem tecnologias, conforme descrito na secção **Âmbito de suporte** do <a href="http://azure.microsoft.com/support/faq/" target="_blank">Web site do Azure suporte FAQ</a>. O serviço de HDInsight fornece um nível adicional de suporte para alguns dos componentes, conforme descrito abaixo.

Existem dois tipos de componentes de abrir origem que estão disponíveis no serviço HDInsight:

- **Componentes incorporados** - estes componentes pré-instalado em HDInsight clusters e fornecer-lhe funcionalidades principais do cluster. Por exemplo, FIO ResourceManager, o idioma de consulta ramo (HiveQL) e a biblioteca Mahout pertencem a esta categoria. Uma lista completa de componentes de cluster está disponível no [quais são as novidades nas versões cluster Hadoop fornecidas pela HDInsight?](hdinsight-component-versioning.md) </a>.
- **Componentes de personalizada** -, como um utilizador do cluster, pode instalar ou utilizar na sua carga de trabalho qualquer componente criado por si ou disponível na Comunidade.

Componentes incorporados são totalmente suportados e o Microsoft Support irá ajudar a identificar e resolver problemas relacionados com a estes componentes.

> [AZURE.WARNING] Componentes fornecidos com o cluster HDInsight são totalmente suportadas e o Microsoft Support irá ajudar a identificar e resolver problemas relacionados com a estes componentes.
>
> Componentes personalizados recebem faremos suporte para o ajudar a resolver ainda mais o problema. Isto poderá resultar em resolver o problema ou pedir-lhe para participar canais disponíveis para as tecnologias de abrir origem onde se encontram conhecimentos abrangente para essa tecnologia. Por exemplo, existem muitos sites de Comunidade que podem ser utilizados, tal como: [Fórum MSDN para HDInsight](https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Também projetos Apache tem sites de projeto no [http://apache.org](http://apache.org), por exemplo: [Hadoop](http://hadoop.apache.org/), [motores](http://spark.apache.org/).

O serviço de HDInsight fornece várias formas de utilizar componentes personalizados. Independentemente de como um componente é utilizado ou instalado no cluster, aplica-se ao mesmo nível de suporte. Segue-se uma lista das formas mais comuns que componentes personalizados podem ser utilizados em HDInsight clusters:

1. Submissão de tarefa - Hadoop ou outros tipos de tarefas que pode executar ou utilizam componentes personalizados pode ser apresentado ao cluster.
2. Personalização de cluster - durante a criação de cluster, pode especificar definições adicionais e componentes personalizados que serão instaladas em nós de cluster.
3. Amostras - para populares componentes personalizados, Microsoft e outras pessoas podem fornecem exemplos de como estes componentes podem ser utilizados em HDInsight clusters. Estes exemplos são fornecidos sem suporte.

## <a name="develop-script-action-scripts"></a>Desenvolver scripts de acção de Script

Consulte o artigo [scripts de desenvolver ação de Script para HDInsight][hdinsight-write-script].


## <a name="see-also"></a>Consulte também

- [Criar Hadoop clusters no HDInsight] [ hdinsight-provision-cluster] fornece instruções sobre como criar um cluster de HDInsight ao utilizar outras opções personalizadas.
- [Desenvolver scripts de acção de Script para HDInsight][hdinsight-write-script]
- [Instalar e utilizar motores no HDInsight clusters][hdinsight-install-spark]
- [Instalar e utilizar R no HDInsight clusters][hdinsight-install-r]
- [Instalar e utilizar clusters de Solr no HDInsight](hdinsight-hadoop-solr-install.md).
- [Instalar e utilizar clusters de Giraph no HDInsight](hdinsight-hadoop-giraph-install.md).

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Fases durante a criação de cluster"
