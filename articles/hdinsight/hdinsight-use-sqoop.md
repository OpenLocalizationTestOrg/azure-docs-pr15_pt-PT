<properties
    pageTitle="Utilizar Hadoop Sqoop no HDInsight | Microsoft Azure"
    description="Saiba como utilizar o Azure PowerShell a partir de uma estação de trabalho para executar Sqoop importar e exportar entre um cluster de Hadoop e uma base de dados do Azure SQL."
    editor="cgronlun"
    manager="jhubbard"
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/02/2016"
    ms.author="jgao"/>

#<a name="use-sqoop-with-hadoop-in-hdinsight"></a>Utilizar Sqoop com Hadoop no HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar Sqoop no HDInsight para importar e exportar entre HDInsight cluster e base de dados Azure SQL ou base de dados do SQL Server.

Apesar de Hadoop é uma escolha natural para processamento dados semistructured e não estruturados, como registos de início e de ficheiros, também poderá uma necessidade de dados estruturados que estão armazenados no bases de dados relacionais do processo.

[Sqoop] [ sqoop-user-guide-1.4.4] é uma ferramenta concebida para transferir dados entre Hadoop clusters e bases de dados relacionais. Pode utilizá-lo para importar dados a partir de um sistema de gestão de base de dados relacional (RDBMS), tais como o SQL Server, MySQL ou Oracle para o sistema de ficheiros distribuído de Hadoop (HDFS), transformar os dados no Hadoop com MapReduce ou ramo e, em seguida, exporte os dados novamente para uma RDBMS. Neste tutorial, estiver a utilizar uma base de dados do SQL Server para a base de dados relacional.

Para versões de Sqoop que são suportadas em HDInsight clusters, consulte o artigo [quais são as novidades nas versões cluster fornecidas pela HDInsight?] [hdinsight-versions].

##<a name="understand-the-scenario"></a>Compreender o cenário

HDInsight cluster vem com alguns dados de exemplo. Irá utilizar os exemplos de duas seguintes:

- Um ficheiro de registo log4j, localizado no */example/data/sample.log*. Registos que se seguem são extraídos do ficheiro:

        2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
        2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
        2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
        ...

- Uma tabela Hive com o nome *hivesampletable*, que referencia o ficheiro de dados que se encontra no */hive/warehouse/hivesampletable*. A tabela contém alguns dados de dispositivo móvel. 

  	| Campo | Tipo de dados |
  	| ----- | --------- |
  	| ClientID | cadeia |
  	| querytime | cadeia |
  	| mercado | cadeia |
  	| deviceplatform | cadeia |
  	| devicemake | cadeia |
  	| devicemodel | cadeia |
  	| Estado | cadeia |
  	| país/região | cadeia |
  	| querydwelltime | dupla |
  	| ID de sessão | bigint |
  	| sessionpagevieworder | bigint |

Que pela primeira vez exportar *sample.log* e *hivesampletable* para a base de dados do Azure SQL ou para SQL Server e, em seguida, importe a tabela que contém os dados do dispositivo móvel para o HDInsight utilizando o caminho seguinte:

    /tutorials/usesqoop/importeddata

## <a name="create-cluster-and-sql-database"></a>Criar cluster e base de dados SQL

Esta secção mostra-lhe como criar um cluster e os esquemas de base de dados SQL para executar o tutorial utilizando o portal do Azure e de um modelo de Gestor de recursos do Azure.  Se preferir utilizar o Azure PowerShell, consulte o artigo [anexo A](#appendix-a---a-powershell-sample).

1. Clique na imagem seguinte para abrir um modelo de Gestor de recursos no Portal do Azure.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    O modelo de Gestor de recursos está localizado no contentor de BLOBs público, *https://hditutorialdata.blob.core.windows.net/usesqoop/create-linux-based-hadoop-cluster-in-hdinsight-and-sql-database.json*. 
    
    O modelo de Gestor de recursos chama um pacote de bacpac para implementar os esquemas de tabela para a base de dados SQL.  O pacote de bacpac também está localizado no contentor de BLOBs público, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Se quiser utilizar um contentor privado para os ficheiros de bacpac, utilize os seguintes valores no modelo:
    
        "storageKeyType": "Primary",
        "storageKey": "<TheAzureStorageAccountKey>",
    
2. A partir do pá parâmetros, introduza o seguinte:

    - **Nome de cluster**: introduza um nome para o cluster de Hadoop que irá criar.
    - **Nome de início de sessão do cluster e palavra-passe**: O nome de início de sessão predefinido é administrador.
    - **SSH nome de utilizador e palavra-passe**.
    - **Nome de início de sessão do servidor de base de dados do SQL e palavra-passe**.

    Os seguintes valores são codificada na secção variáveis:
    
  	|Nome de conta de armazenamento predefinido|<CluterName>armazenar|
  	|----------------------------|-----------------|
  	|Nome de servidor de base de dados SQL Azure|<ClusterName>DBSERVER|
  	|Nome de base de dados SQL Azure|<ClusterName>BD|
    
    Fórum anote estes valores.  Terá-las mais tarde no tutorial.
    
3. clique em **OK** para guardar os parâmetros.

4. pá **implementação personalizada** , clique em caixa de lista pendente do **grupo de recursos** e, em seguida, clique em **Novo** para criar um novo grupo de recursos. O grupo de recursos é um contentor que agrupa cluster, a conta de armazenamento dependentes e outro recurso ligado.

5. clique **termos Legal**e, em seguida, clique em **Criar**.

6. clique em **Criar**. Irá ver um novo mosaico intitulado Submitting implementação para implementação do modelo. Bastam sobre cerca de 20 minutos para criar cluster e a base de dados SQL.

Se optar por utilizar a base de dados Azure SQL existente ou Microsoft SQL Server

- **Base de dados Azure SQL**: tem de configurar uma regra de firewall para o servidor de base de dados do Azure SQL permitir o acesso a partir do seu estação de trabalho. Para obter instruções sobre como criar um Azure SQL de base de dados e configurar a firewall, consulte o artigo [começar a utilizar a base de dados Azure SQL][sqldatabase-get-started]. 

    > [AZURE.NOTE] Por predefinição, uma base de dados do Azure SQL permite ligações a partir do Azure serviços, como o Azure HDInsight. Se esta definição de firewall estiver desactivada, tem de o ativada a partir do portal Azure. Para obter instruções sobre como criar uma base de dados do Azure SQL e configurar regras de firewall, consulte o artigo [criar e configurar a base de dados do SQL][sqldatabase-create-configue].

- **SQL Server**: se o seu cluster HDInsight está na mesma rede virtual no Azure como SQL Server, pode utilizar os passos neste artigo para importar e exportar dados para uma base de dados do SQL Server.

    > [AZURE.NOTE] HDInsight suporta apenas baseadas na localização redes virtuais e -atualmente não funciona com redes virtuais com base no grupo afinidade.

    * Para criar e configurar uma rede virtual, consulte o artigo [criar uma rede virtual através do portal Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).

        * Quando estiver a utilizar o SQL Server no seu centro de dados, tem de configurar a rede virtual como *site para o site* ou *site de ponto*.

            > [AZURE.NOTE] Para redes virtuais **ponto-para-site** , SQL Server tem de executar o cliente VPN aplicação de configuração, o que está disponível a partir do **Dashboard** da configuração de rede virtual Azure.

        * Quando estiver a utilizar o SQL Server uma máquina virtual Azure, qualquer configuração de rede virtual pode ser utilizada se a máquina virtual alojamento do SQL Server é um membro da mesma rede virtual como HDInsight.

    * Para criar um cluster de HDInsight numa rede virtual, consulte [Criar Hadoop clusters no HDInsight utilizando as opções personalizadas](hdinsight-provision-clusters.md)

    > [AZURE.NOTE] SQL Server também têm de permitir autenticação. Tem de utilizar um início de sessão do SQL Server para concluir os passos neste artigo.


## <a name="run-sqoop-jobs"></a>Executar tarefas Sqoop

HDInsight pode executar tarefas Sqoop utilizando uma variedade de métodos. Utilize a tabela seguinte para decidir qual o método é ideal para si, em seguida, siga a hiperligação para obter instruções sobre.

| **Utilize esta opção** se pretender que...                                   | … .an **interativos** shell | ... processamento **batch** | … .with este **sistema operativo de cluster** | … .from este **sistema operativo do cliente** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-use-sqoop-mac-linux.md)                        |              ✔              |            ✔            | Linux                                     | Windows, Mac OS X, Unix ou Linux        |
| [.NET SDK para Hadoop](hdinsight-hadoop-use-sqoop-dotnet-sdk.md) |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows (por agora)                        |
| [Azure PowerShell](hdinsight-hadoop-use-sqoop-powershell.md)  |           &nbsp;            |            ✔            | Linux ou o Windows                          | Windows                                  |

##<a name="limitations"></a>Limitações

* Em volume export - baseado em com Linux HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou base de dados do SQL Azure atualmente não suporta insere em volume.

* De lotes - com baseado em Linux HDInsight, ao utilizar o `-batch` mudar quando efetuar inserções, Sqoop irá efetuar várias insere em vez de lotes as operações de inserir.

##<a name="next-steps"></a>Próximos passos

Agora que tem aprendeu como utilizar Sqoop. Para obter mais informações, consulte o artigo:

- [Utilizar ramo com HDInsight](hdinsight-use-hive.md)
- [Utilizar porco com HDInsight](hdinsight-use-pig.md)
- [Utilizar Oozie com HDInsight][hdinsight-use-oozie]: utilizar Sqoop ação num fluxo de trabalho Oozie.
- [Analisar os dados de atrasos em voos utilizando HDInsight][hdinsight-analyze-flight-data]: utilizar Hive para analisar voo atrasar dados e, em seguida, utilize Sqoop para exportar dados para uma base de dados do Azure SQL.
- [Carregar dados ao HDInsight][hdinsight-upload-data]: encontrar outros métodos para carregar os dados ao armazenamento Blob HDInsight/Azure.


## <a name="appendix-a---a-powershell-sample"></a>Anexo A - uma amostra de PowerShell

O exemplo PowerShell executa os seguintes passos:

1. Ligar ao Azure.
2. Crie um grupo de recursos Azure. Para obter mais informações, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md)
3. Crie um servidor de base de dados do Azure SQL, uma base de dados do Azure SQL e duas tabelas. 

    Se utiliza o SQL Server em vez disso, utilize as seguintes instruções para criar as tabelas:
    
        CREATE TABLE [dbo].[log4jlogs](
         [t1] [nvarchar](50),
         [t2] [nvarchar](50),
         [t3] [nvarchar](50),
         [t4] [nvarchar](50),
         [t5] [nvarchar](50),
         [t6] [nvarchar](50),
         [t7] [nvarchar](50))

        CREATE TABLE [dbo].[mobiledata](
         [clientid] [nvarchar](50),
         [querytime] [nvarchar](50),
         [market] [nvarchar](50),
         [deviceplatform] [nvarchar](50),
         [devicemake] [nvarchar](50),
         [devicemodel] [nvarchar](50),
         [state] [nvarchar](50),
         [country] [nvarchar](50),
         [querydwelltime] [float],
         [sessionid] [bigint],
         [sessionpagevieworder][bigint])

    A forma mais fácil para examinar a base de dados e tabelas é utilizar o Visual Studio. O servidor de base de dados e a base de dados podem ser analisadas através do portal Azure.

4. Crie um cluster de HDInsight.

    Para examinar cluster, pode utilizar o Azure portal ou Azure PowerShell.

5. Processa previamente o ficheiro de dados de origem.

    Neste tutorial, irá exportar um ficheiro de registo de log4j (um ficheiro delimitado) e uma tabela Hive para uma base de dados do Azure SQL. O ficheiro delimitado chama */example/data/sample.log*. Anteriormente no tutorial, viu alguns exemplos de log4j registos. No ficheiro de registo, existem algumas linhas em branco e algumas linhas semelhantes às seguintes:
    
        java.lang.Exception: 2012-02-03 20:11:35 SampleClass2 [FATAL] unrecoverable system problem at id 609774657
            at com.osa.mocklogger.MockLogger$2.run(MockLogger.java:83)
    
    Este é fino para outros exemplos que utilizam estes dados, mas recomendamos tem de remover estes exceções antes podemos pode importar para a base de dados Azure SQL ou do SQL Server. Exportar Sqoop falhará se existir uma cadeia vazia ou uma linha com uma inferior número de elementos de que o número de campos definidos na tabela de base de dados Azure SQL. A tabela log4jlogs tem 7 campos de tipo de cadeia.

    Este procedimento cria um novo ficheiro no cluster: tutorials/usesqoop/data/sample.log. Para examinar o ficheiro de dados modificados, pode utilizar o portal do Azure, uma ferramenta de Explorador de armazenamento do Windows Azure ou Azure PowerShell. [Introdução ao HDInsight] [ hdinsight-get-started] tem uma amostra de código para utilizar o Azure PowerShell para transferir um ficheiro e apresentar o conteúdo do ficheiro.

6. Exporte um ficheiro de dados para a base de dados do Azure SQL.

    O ficheiro de origem for tutorials/usesqoop/data/sample.log. A tabela onde os dados são exportados para chama log4jlogs.
    
    > [AZURE.NOTE] Diferente de informações da cadeia de ligação, os passos nesta secção deverão funcionar para uma base de dados do Azure SQL ou para o SQL Server. Estes passos foram testados utilizando a seguinte configuração:
    >
    > * **Configuração do site do ponto de rede virtual Azure**: uma rede virtual ligado HDInsight cluster ao SQL Server num centro de dados privado. Para mais informações, consulte [Configurar uma VPN ponto-para-Site no Portal de gestão](../vpn-gateway/vpn-gateway-point-to-site-create.md) .
    > * **Azure HDInsight 3.1**: consulte o artigo [Criar Hadoop clusters no HDInsight utilizando as opções personalizadas](hdinsight-provision-clusters.md) para obter informações sobre a criação de um cluster numa rede virtual.
    > * **SQL Server 2014**: configurado para permitir a autenticação e a executar o cliente VPN o pacote de configuração para estabelecer uma ligação segura à rede virtual.

7. Exporte uma tabela Hive para a base de dados do Azure SQL.

8. Importe a tabela mobiledata ao HDInsight cluster.

    Para examinar o ficheiro de dados modificados, pode utilizar o portal do Azure, uma ferramenta de Explorador de armazenamento do Windows Azure ou Azure PowerShell.  [Introdução ao HDInsight] [ hdinsight-get-started] tem uma amostra de código sobre como utilizar o Azure PowerShell para transferir um ficheiro e apresentar o conteúdo do ficheiro.


### <a name="the-powershell-sample"></a>O exemplo PowerShell

    # Prepare an Azure SQL database to be used by the Sqoop tutorial
    
    #region - provide the following values
    
    $subscriptionID = "<Enter your Azure Subscription ID>"
    
    $sqlDatabaseLogin = "<Enter a SQL Database Login name>" #SQL Database server login
    $sqlDatabasePassword = "<Enter a Password>"
    
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<Enter a Password>"
    
    # used for creating Azure service names
    $nameToken = "<Enter an alias>" 
    $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")
    #endregion
    
    #region - variables
    
    # Resource group variables
    $resourceGroupName = $namePrefix + "rg"
    $location = "East US 2" # used by all Azure services defined in this tutorial
    
    # SQL database varialbes
    $sqlDatabaseServerName = $namePrefix + "sqldbserver"
    $sqlDatabaseName = $namePrefix + "sqldb"
    $sqlDatabaseConnectionString = "Data Source=$sqlDatabaseServerName.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
    $sqlDatabaseMaxSizeGB = 10
    
    # Used for retrieving external IP address and creating firewall rules
    $ipAddressRestService = "http://bot.whatismyipaddress.com"
    $fireWallRuleName = "UseSqoop"
    
    # Used for creating tables and clustered indexes
    $cmdCreateLog4jTable = "CREATE TABLE [dbo].[log4jlogs](
        [t1] [nvarchar](50),
        [t2] [nvarchar](50),
        [t3] [nvarchar](50),
        [t4] [nvarchar](50),
        [t5] [nvarchar](50),
        [t6] [nvarchar](50),
        [t7] [nvarchar](50))"
    
    $cmdCreateLog4jClusteredIndex = "CREATE CLUSTERED INDEX log4jlogs_clustered_index on log4jlogs(t1)"
    
    $cmdCreateMobileTable = " CREATE TABLE [dbo].[mobiledata](
    [clientid] [nvarchar](50),
    [querytime] [nvarchar](50),
    [market] [nvarchar](50),
    [deviceplatform] [nvarchar](50),
    [devicemake] [nvarchar](50),
    [devicemodel] [nvarchar](50),
    [state] [nvarchar](50),
    [country] [nvarchar](50),
    [querydwelltime] [float],
    [sessionid] [bigint],
    [sessionpagevieworder][bigint])"
    
    $cmdCreateMobileDataClusteredIndex = "CREATE CLUSTERED INDEX mobiledata_clustered_index on mobiledata(clientid)"
    
    # HDInsight variables
    $hdinsightClusterName = $namePrefix + "hdi"
    $defaultStorageAccountName = $namePrefix + "store"
    $defaultBlobContainerName = $hdinsightClusterName
    #endregion
    
    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"
    
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
    
    #region - Create Azure resouce group
    Write-Host "`nCreating an Azure resource group ..." -ForegroundColor Green
    try{
        Get-AzureRmResourceGroup -Name $resourceGroupName
    }
    catch{
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    }
    #endregion
    
    #region - Create Azure SQL database server
    Write-Host "`nCreating an Azure SQL Database server ..." -ForegroundColor Green
    try{
        Get-AzureRmSqlServer -ServerName $sqlDatabaseServerName -ResourceGroupName $resourceGroupName}
    catch{
        Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
    
        $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
        $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
    
        $sqlDatabaseServerName = (New-AzureRmSqlServer `
                                    -ResourceGroupName $resourceGroupName `
                                    -ServerName $sqlDatabaseServerName `
                                    -SqlAdministratorCredentials $credential `
                                    -Location $location).ServerName
        Write-Host "`tThe new SQL database server name is $sqlDatabaseServerName." -ForegroundColor Cyan
    
        Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
        $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-workstation" `
            -StartIpAddress $workstationIPAddress `
            -EndIpAddress $workstationIPAddress
    
        #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. 
        #Note that this allows Azure traffic from any Azure subscription to access the server.
        New-AzureRmSqlServerFirewallRule `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -FirewallRuleName "$fireWallRuleName-Azureservices" `
            -StartIpAddress "0.0.0.0" `
            -EndIpAddress "0.0.0.0"
    }
    
    #endregion
    
    #region - Create and validate Azure SQL database
    Write-Host "`nCreating an Azure SQL database ..." -ForegroundColor Green
    
    try {
        Get-AzureRmSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName
    }
    catch {
        Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
        New-AzureRMSqlDatabase `
            -ResourceGroupName $resourceGroupName `
            -ServerName $sqlDatabaseServerName `
            -DatabaseName $sqlDatabaseName `
            -Edition "Standard" `
            -RequestedServiceObjectiveName "S1"
    }
    
    #endregion
    
    #region - Create tables
    Write-Host "Creating the log4jlogs table and the mobiledata table ..." -ForegroundColor Green
    
    $conn = New-Object System.Data.SqlClient.SqlConnection
    $conn.ConnectionString = $sqlDatabaseConnectionString
    $conn.Open()
    
    # Create the log4jlogs table and index
    $cmd = New-Object System.Data.SqlClient.SqlCommand
    $cmd.Connection = $conn
    $cmd.CommandText = $cmdCreateLog4jTable
    $ret = $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateLog4jClusteredIndex
    $cmd.ExecuteNonQuery()
    
    # Create the mobiledata table and index
    $cmd.CommandText = $cmdCreateMobileTable
    $cmd.ExecuteNonQuery()
    $cmd.CommandText = $cmdCreateMobileDataClusteredIndex
    $cmd.ExecuteNonQuery()
    
    $conn.close()
    
    #endregion
    
    
    #region - Create HDInsight cluster
    
    Write-Host "Creating the HDInsight cluster and the dependent services ..." -ForegroundColor Green
    
    # Create the default storage account
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_LRS
    
    # Create the default Blob container
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $defaultStorageAccountContext = New-AzureStorageContext `
                                        -StorageAccountName $defaultStorageAccountName `
                                        -StorageAccountKey $defaultStorageAccountKey 
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageAccountContext 
    
    # Create the HDInsight cluster
    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
    
    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $HDInsightClusterName `
        -Location $location `
        -ClusterType Hadoop `
        -OSType Windows `
        -ClusterSizeInNodes 2 `
        -HttpCredential $httpCredential `
        -DefaultStorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultStorageContainer $defaultBlobContainerName 
    
    # Validate the cluster
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName
    #endregion
    
    #region - pre-process the source file
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
    
    # Define the connection string
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
    
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = [Microsoft.WindowsAzure.Storage.CloudStorageAccount]::Parse($storageConnectionString)
    $storageClient = $storageAccount.CreateCloudBlobClient();
    $storageContainer = $storageClient.GetContainerReference($defaultBlobContainerName)
    $sourceBlob = $storageContainer.GetBlockBlobReference($sourceBlobName)
    $destBlob = $storageContainer.GetBlockBlobReference($destBlobName)
    
    # Define a MemoryStream and a StreamReader for reading from the source file
    $stream = New-Object System.IO.MemoryStream
    $stream = $sourceBlob.OpenRead()
    $sReader = New-Object System.IO.StreamReader($stream)
    
    # Define a MemoryStream and a StreamWriter for writing into the destination file
    $memStream = New-Object System.IO.MemoryStream
    $writeStream = New-Object System.IO.StreamWriter $memStream
    
    # Pre-process the source blob
    $exString = "java.lang.Exception:"
    while(-Not $sReader.EndOfStream){
        $line = $sReader.ReadLine()
        $split = $line.Split(" ")
    
        # remove the "java.lang.Exception" from the first element of the array
        # for example: java.lang.Exception: 2012-02-03 19:11:02 SampleClass8 [WARN] problem finding id 153454612
        if ($split[0] -eq $exString){
            #create a new ArrayList to remove $split[0]
            $newArray = [System.Collections.ArrayList] $split
            $newArray.Remove($exString)
    
            # update $split and $line
            $split = $newArray
            $line = $newArray -join(" ")
        }
    
        # remove the lines that has less than 7 elements
        if ($split.count -ge 7){
            write-host $line
            $writeStream.WriteLine($line)
        }
    }
    
    # Write to the destination blob
    $writeStream.Flush()
    $memStream.Seek(0, "Begin")
    $destBlob.UploadFromStream($memStream)
    
    #endregion
    
    #region - export a log file from the cluster to the SQL database
    
    Write-Host "Preprocessing the source file ..." -ForegroundColor Green
    
    $tableName_log4j = "log4jlogs"
    
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    
    $exportDir_log4j = "/tutorials/usesqoop/data"
    
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardError
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName -DefaultStorageAccountName $defaultStorageAccountName -DefaultStorageAccountKey $defaultStorageAccountKey -DefaultContainer $defaultBlobContainerName -HttpCredential $httpCredential -JobId $sqoopJob.JobId -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - export a Hive table
    
    $tableName_mobile = "mobiledata"
    $exportDir_mobile = "/hive/warehouse/hivesampletable"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_mobile --export-dir $exportDir_mobile --fields-terminated-by \t -m 1"
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion
    
    #region - import a database
    
    $targetDir_mobile = "/tutorials/usesqoop/importeddata/"
    
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "import --connect $connectionString --table $tableName_mobile --target-dir $targetDir_mobile --fields-terminated-by \t --lines-terminated-by \n -m 1"
    
    $sqoopJob = Start-AzureRmHDInsightJob `
                    -ClusterName $hdinsightClusterName `
                    -HttpCredential $httpCredential `
                    -JobDefinition $sqoopDef #-Debug -Verbose
    
    Wait-AzureRmHDInsightJob `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId
    
    Write-Host "Standard Error" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardError
    
    Write-Host "Standard Output" -BackgroundColor Green
    Get-AzureRmHDInsightJobOutput `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -DefaultStorageAccountName $defaultStorageAccountName `
        -DefaultStorageAccountKey $defaultStorageAccountKey `
        -DefaultContainer $defaultBlobContainerName `
        -HttpCredential $httpCredential `
        -JobId $sqoopJob.JobId `
        -DisplayOutputType StandardOutput
    
    #endregion



[azure-management-portal]: https://portal.azure.com/

[hdinsight-versions]:  hdinsight-component-versioning.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-storage]: ../hdinsight-hadoop-use-blob-storage.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[sqldatabase-get-started]: ../sql-database/sql-database-get-started.md
[sqldatabase-create-configue]: ../sql-database/sql-database-get-started.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx
[powershell-install]: powershell-install-configure.md
[powershell-script]: http://technet.microsoft.com/library/ee176949.aspx

[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
