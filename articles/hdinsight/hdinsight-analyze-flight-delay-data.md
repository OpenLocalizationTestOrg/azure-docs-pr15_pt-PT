<properties
    pageTitle="Analisar os dados de atrasos em voos com Hadoop no HDInsight | Microsoft Azure"
    description="Saiba como utilizar um script do Windows PowerShell para criar um cluster de HDInsight, executar uma tarefa de ramo, executar uma tarefa Sqoop e eliminar o cluster."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jgao"/>

#<a name="analyze-flight-delay-data-by-using-hive-in-hdinsight"></a>Analisar os dados de atrasos em voos utilizando ramo no HDInsight

Ramo fornece um meio de tarefas em execução Hadoop MapReduce através de uma linguagem de scripts de SQL gosto denominada * [HiveQL][hadoop-hiveql]*, que podem ser aplicado no sentido resumindo, consultar e analisar grandes volumes de dados.

> [AZURE.NOTE] Os passos neste documento requerem um cluster de HDInsight baseados no Windows. Para obter os passos que funcionam com um cluster baseado em Linux, consulte o artigo [Analisar os dados de atrasos em voos utilizando ramo no HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md).

É uma das principais vantagens do Azure HDInsight a separação de armazenamento de dados e cluster. HDInsight utiliza o armazenamento de Blobs do Azure para armazenamento de dados. Uma tarefa típica envolve os três elementos:

1. **Armazenar dados no armazenamento de Blobs do Azure.**  Por exemplo, tempo dados, dados sensor, registos de web e, neste caso, os dados de atrasos em voos são guardados para o armazenamento de Blobs do Azure.
2. **Execute tarefas.** Quando está na altura para processar os dados, execute um script do Windows PowerShell (ou uma aplicação de cliente) para criar um cluster de HDInsight, executar tarefas e eliminar o cluster. As tarefas de guardar dados de saída ao armazenamento de Blobs do Azure. Os dados de saída são mantidos mesmo após o cluster é eliminado. Desta forma, pode paga a apenas o que pode ter consumidas.
3. **Obter o resultado do armazenamento de Blobs do Azure**, neste tutorial, para exportar os dados para uma base de dados do Azure SQL.

O diagrama seguinte ilustra o cenário e a estrutura deste tutorial:

![HDI. FlightDelays.flow][img-hdi-flightdelays-flow]

**Nota**: os números no diagrama correspondem aos títulos de secção. **M** designa o processo principal. **A** designa o conteúdo no anexo.

A parte principal do tutorial mostra-lhe como utilizar um script do Windows PowerShell para efetuar as seguintes tarefas:

- Crie um cluster de HDInsight.
- Execute uma tarefa de ramo num cluster para calcular a média de atrasos aeroportos. Os dados de atrasos em voos são armazenados numa conta de armazenamento de Blobs do Azure.
- Execute uma tarefa Sqoop para exportar o resultado da tarefa Hive para uma base de dados do Azure SQL.
- Elimine o cluster de HDInsight.

No apêndices, pode encontrar as instruções para carregar os dados de atrasos em voos, criar/ao carregar uma cadeia de consulta ramo e preparar a base de dados do Azure SQL para a tarefa de Sqoop.

> [AZURE.NOTE] Os passos neste documento são específicos a clusters HDInsight baseados no Windows. Para obter os passos que funcionam com um cluster baseado em Linux, consulte o artigo [Analisar os dados de atrasos em voos utilizando ramo no HDInsight (Linux)](hdinsight-analyze-flight-delay-data-linux.md)

###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter os seguintes itens:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

**Ficheiros utilizados neste tutorial**

Neste tutorial utiliza o desempenho de hora dos dados de voo companhia aérea de [pesquisa e forma inovadora de tecnologia de administração, Instituto de transporte estatísticas ou RITA] [rita-website]. Foi carregada uma cópia dos dados a um contentor de armazenamento de Blobs do Azure com a permissão de acesso de BLOBs público. Uma parte do seu script PowerShell copia os dados do contentor blob público para o contentor de BLOBs predefinido do seu cluster de. O script HiveQL também é copiado ao contentor de Blobs do mesmo. Se pretender obter informações sobre como obter/carregar os dados a sua conta de armazenamento e como criar/carregar o ficheiro de script HiveQL, consulte o artigo [anexo A](#appendix-a) e [B do anexo](#appendix-b).

A tabela seguinte lista os ficheiros utilizados neste tutorial:

<table border="1">
<tr><th>Ficheiros</th><th>Descrição</th></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/flightdelays.hql</td><td>O ficheiro de script de HiveQL utilizado pela ramo de tarefa. Este script foi carregado para uma conta de armazenamento de Blobs do Azure com o acesso do público. <a href="#appendix-b">Anexo B</a> tem obter instruções sobre como preparar e carregar este ficheiro à sua própria conta de armazenamento de Blobs do Azure.</td></tr>
<tr><td>wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data</td><td>Dados de entrada para a tarefa de ramo. Os dados foi carregados para uma conta de armazenamento de Blobs do Azure com o acesso do público. <a href="#appendix-a">Anexo A</a> tem obter instruções sobre a obtenção de dados e carregar os dados a sua conta de armazenamento de Blobs do Azure.</td></tr>
<tr><td>\tutorials\flightdelays\output</td><td>O caminho para a tarefa de ramo de registo de saída. O contentor predefinido é utilizado para armazenar os dados de saída.</td></tr>
<tr><td>\tutorials\flightdelays\jobstatus</td><td>A ramo tarefa Estado pasta o contentor predefinido.</td></tr>
</table>


##<a name="create-cluster-and-run-hivesqoop-jobs"></a>Cluster de criar e executar tarefas ramo/Sqoop

Hadoop MapReduce é processamento batch. A forma mais rentável para executar uma tarefa de ramo é para um cluster de para a tarefa de criar e eliminar a tarefa depois da tarefa é concluída. O seguinte script abrange todo o processo. Para obter mais informações sobre como criar um cluster de HDInsight e ramo de tarefas em execução, consulte o artigo [clusters de criar Hadoop HDInsight] [ hdinsight-provision] e [Utilização Hive com HDInsight][hdinsight-use-hive].

**Para executar as consultas de ramo ao PowerShell do Azure**

1. Crie uma base de dados do Azure SQL e a tabela no resultado da tarefa Sqoop utilizando as instruções no [Anexo C](#appendix-c).
3. Abra o Windows PowerShell ISE e execute o seguinte script:

        $subscriptionID = "<Azure Subscription ID>"
        $nameToken = "<Enter an Alias>" 
        
        ###########################################
        # You must configure the follwing variables
        # for an existing Azure SQL Database
        ###########################################
        $existingSqlDatabaseServerName = "<Azure SQL Database Server>"
        $existingSqlDatabaseLogin = "<Azure SQL Database Server Login>"
        $existingSqlDatabasePassword = "<Azure SQL Database Server login password>"
        $existingSqlDatabaseName = "<Azure SQL Database name>"
        
        $localFolder = "E:\Tutorials\Downloads\" # A temp location for copying files. 
        $azcopyPath = "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy" # depends on the version, the folder can be different
        
        ###########################################
        # (Optional) configure the following variables
        ###########################################
        
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $location = "EAST US 2"
        
        $HDInsightClusterName = $namePrefix + "hdi"
        $httpUserName = "admin"
        $httpPassword = "<Enter the Password>"
        
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $HDInsightClusterName # use the cluster name
        
        $existingSqlDatabaseTableName = "AvgDelays"
        $sqlDatabaseConnectionString = "jdbc:sqlserver://$existingSqlDatabaseServerName.database.windows.net;user=$existingSqlDatabaseLogin@$existingSqlDatabaseServerName;password=$existingSqlDatabaseLogin;database=$existingSqlDatabaseName"
        
        $hqlScriptFile = "/tutorials/flightdelays/flightdelays.hql" 
        
        $jobStatusFolder = "/tutorials/flightdelays/jobstatus"
        
        ###########################################
        # Login
        ###########################################
        try{
            $acct = Get-AzureRmSubscription
        }
        catch{
            Login-AzureRmAccount
        }
        Select-AzureRmSubscription -SubscriptionID $subscriptionID
        
        ###########################################
        # Create a new HDInsight cluster
        ###########################################
        
        # Create ARM group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
        
        # Create the default storage account
        New-AzureRmStorageAccount -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName -Location $location -Type Standard_LRS
        
        # Create the default Blob container
        $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName)[0].Value
        $defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 
        New-AzureStorageContainer -Name $defaultBlobContainerName -Context $defaultStorageAccountContext 
        
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
            -DefaultStorageContainer $existingDefaultBlobContainerName 
        
        
        ###########################################
        # Prepare the HiveQL script and source data
        ###########################################
        
        # Create the temp location  
        New-Item -Path $localFolder -ItemType Directory -Force 
        
        # Download the sample file from Azure Blob storage
        $context = New-AzureStorageContext -StorageAccountName "hditutorialdata" -Anonymous
        $blobs = Get-AzureStorageBlob -Container "flightdelay" -Context $context
        #$blobs | Get-AzureStorageBlobContent -Context $context -Destination $localFolder
        
        # Upload data to default container
        
        $azcopycmd = "cmd.exe /C '$azcopyPath\azcopy.exe' /S /Source:'$localFolder' /Dest:'https://$defaultStorageAccountName.blob.core.windows.net/$defaultBlobContainerName/tutorials/flightdelays' /DestKey:$defaultStorageAccountKey"
        
        Invoke-Expression -Command:$azcopycmd
        
        ###########################################
        # Submit the Hive job
        ###########################################
        Use-AzureRmHDInsightCluster -ClusterName $HDInsightClusterName -HttpCredential $httpCredential
        $response = Invoke-AzureRmHDInsightHiveJob `
                        -Files $hqlScriptFile `
                        -DefaultContainer $defaultBlobContainerName `
                        -DefaultStorageAccountName $defaultStorageAccountName `
                        -DefaultStorageAccountKey $defaultStorageAccountKey `
                        -StatusFolder $jobStatusFolder 
        
        write-Host $response
        
        
        ###########################################
        # Submit the Sqoop job
        ###########################################
        $exportDir = "wasbs://$defaultBlobContainerName@$defaultStorageAccountName.blob.core.windows.net/tutorials/flightdelays/output"
        
        $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
                        -Command "export --connect $sqlDatabaseConnectionString --table $sqlDatabaseTableName --export-dir $exportDir --fields-terminated-by \001 "
        $sqoopJob = Start-AzureRmHDInsightJob `
                        -ResourceGroupName $resourceGroupName `
                        -ClusterName $hdinsightClusterName `
                        -HttpCredential $httpCredential `
                        -JobDefinition $sqoopDef #-Debug -Verbose
        
        Wait-AzureRmHDInsightJob `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $HDInsightClusterName `
            -HttpCredential $httpCredential `
            -WaitTimeoutInSeconds 3600 `
            -Job $sqoopJob.JobId
        
        
        Get-AzureRmHDInsightJobOutput `
            -ResourceGroupName $resourceGroupName `
            -ClusterName $hdinsightClusterName `
            -HttpCredential $httpCredential `
            -DefaultContainer $existingDefaultBlobContainerName `
            -DefaultStorageAccountName $defaultStorageAccountName `
            -DefaultStorageAccountKey $defaultStorageAccountKey `
            -JobId $sqoopJob.JobId `
            -DisplayOutputType StandardError
        
        ###########################################
        # Delete the cluster
        ###########################################
        Remove-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName

6. Ligar a sua base de dados do SQL e consulte o artigo apresentar atrasos em voos média por cidade na tabela AvgDelays:

    ![HDI. FlightDelays.AvgDelays.Dataset][image-hdi-flightdelays-avgdelays-dataset]



---
##<a id="appendix-a"></a>Anexo A - carregar os dados de atrasos em voos ao armazenamento de Blobs do Azure
Carregar o ficheiro de dados e os ficheiros de script HiveQL (ver [Anexo B](#appendix-b)) requer alguns um planeamento. A ideia é armazenar os ficheiros de dados e o ficheiro HiveQL antes de criar um cluster de HDInsight e executar a tarefa de ramo. Tem duas opções:

- **Utilize a mesma conta de armazenamento do Windows Azure que será utilizada pelo HDInsight cluster como o sistema de ficheiro predefinido.** Uma vez que o cluster HDInsight, terá a tecla de acesso de conta de armazenamento, não precisa de efetuar alterações adicionais.
- **Utilize uma conta de armazenamento do Windows Azure diferente a partir do sistema de ficheiros HDInsight cluster predefinido.** Se for o caso, terá de modificar a parte da criação do script do Windows PowerShell encontrado nas [cluster HDInsight criar e executar tarefas de ramo/Sqoop](#runjob) para ligar a conta de armazenamento como uma conta de armazenamento adicional. Para obter instruções, consulte o artigo [Criar Hadoop clusters no HDInsight][hdinsight-provision]. HDInsight cluster, em seguida, sabe a tecla de acesso para a conta de armazenamento.

>[AZURE.NOTE] O caminho de armazenamento de BLOBs para o ficheiro de dados é difícil codificada no ficheiro de script HiveQL. Tem de actualizá-lo em conformidade.

**Para transferir os dados de voo**

1. Navegue para a [pesquisa e tecnologia inovadora administração, Instituto de transporte estatísticas][rita-website].
2. Na página, selecione os seguintes valores:

    <table border="1">
    <tr><th>Nome</th><th>Valor</th></tr>
    <tr><td>Ano de filtro</td><td>2013 </td></tr>
    <tr><td>Filtrar período</td><td>Janeiro</td></tr>
    <tr><td>Campos</td><td>*Ano*, *Datadovoo*, *UniqueCarrier*, *Carrier*, *FlightNum*, *OriginAirportID*, *origem*, *OriginCityName*, *OriginState*, *DestAirportID*, *destino*, *DestCityName*, *DestState*, *DepDelayMinutes*, *ArrDelay*, *ArrDelayMinutes*, *CarrierDelay*, *WeatherDelay*, *NASDelay*, *SecurityDelay*, *LateAircraftDelay* (limpar todos os outros campos)</td></tr>
    </table>

3. Clique em **Transferir**.
4. Deszipe o ficheiro para a pasta **C:\Tutorials\FlightDelay\2013Data** . Cada ficheiro é um ficheiro CSV e é de cerca de 60GB de tamanho.
5.  Mudar o nome de ficheiro para o nome do mês que contém dados para. Por exemplo, o ficheiro que contêm os dados de Janeiro teria o nome *January.csv*.
6. Repita os passos 2 e 5 para transferir um ficheiro para cada um dos 12 meses de 2013. Terá um mínimo de um ficheiro para executar o tutorial.  

**Para carregar os dados de atrasos em voos para o armazenamento de Blobs do Azure**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome da variável</th><th>Notas</th></tr>
    <tr><td>$storageAccountName</td><td>A conta de armazenamento do Windows Azure onde pretende carregar os dados.</td></tr>
    <tr><td>$blobContainerName</td><td>O contentor de BLOBs onde pretende carregar os dados.</td></tr>
    </table>
2. Abra o Azure o ISE do PowerShell.
3. Cole o seguinte script no painel de script:

        [CmdletBinding()]
        Param(

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #Region - Variables
        $localFolder = "C:\Tutorials\FlightDelay\2013Data"  # The source folder
        $destFolder = "tutorials/flightdelay/2013data"     #The blob name prefix for the files to be uploaded
        #EndRegion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #Region - Copy the file from local workstation to Azure Blob storage  
        if (test-path -Path $localFolder)
        {
            foreach ($item in Get-ChildItem -Path $localFolder){
                $fileName = "$localFolder\$item"
                $blobName = "$destFolder/$item"
        
                Write-Host "Copying $fileName to $blobName" -ForegroundColor Green
        
                Set-AzureStorageBlobContent -File $fileName -Container $blobContainerName -Blob $blobName -Context $storageContext
            }
        }
        else
        {
            Write-Host "The source folder on the workstation doesn't exist" -ForegroundColor Red
        }
        
        # List the uploaded files on HDInsight
        Get-AzureStorageBlob -Container $blobContainerName  -Context $storageContext -Prefix $destFolder
        #EndRegion




4. Prima **F5** para executar o script.

Se optar por utilizar um método diferente para carregar os ficheiros, certifique-se de que o caminho do ficheiro é flightdelay/tutoriais/dados. A sintaxe para aceder os ficheiros é:

    wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/tutorials/flightdelay/data

Os tutoriais/flightdelay/dados de caminho é a pasta virtual que criou quando carregou os ficheiros. Certifique-se de que não existem 12 ficheiros, um para cada mês.

>[AZURE.NOTE] Tem de atualizar a consulta ramo lerem a nova localização.

> Quer tem de configurar a permissão de acesso de contentor para ser público ou vincular a conta de armazenamento para HDInsight cluster. Caso contrário, a cadeia de consulta ramo não conseguir aceder aos ficheiros de dados.

---
##<a id="appendix-b"></a>Anexo B - criar e carregar um script HiveQL

Utilizar o Azure PowerShell, pode executar várias declarações de HiveQL um de cada vez ou a declaração de HiveQL para um ficheiro de script do pacote. Esta secção mostra-lhe como criar um script HiveQL e carregar o script ao armazenamento de Blobs do Azure através do Azure PowerShell. Ramo requer os scripts HiveQL para serem armazenados em armazenamento de Blobs do Azure.

O script HiveQL irá efetuar o seguinte procedimento:

1. **Largue a tabela delays_raw**, caso a tabela já existe.
2. **Criar a tabela Hive externa delays_raw** apontar para a localização de armazenamento de Blobs com os ficheiros de atraso voo. Esta consulta Especifica que os campos são delimitados por "," e que as linhas são terminadas por "\n". Isto representa um problema quando valores de campo contêm vírgulas porque ramo não é possível distinguir entre um ponto e vírgula que é um delimitador e um que faz parte de um valor de campo (que é o caso nos valores de campo para a origem\_Cidade\_nome e destino\_Cidade\_nome). Para resolver este problema, a consulta cria colunas TEMP para armazenarem os dados que incorretamente são divididos em colunas.  
3. **Largue a tabela de atrasos**, caso a tabela já existe.
4. **Criar a tabela de atrasos**. É útil limpar os dados antes de processamento suplementar. Esta consulta cria uma nova tabela, *atrasos*, a partir da tabela delays_raw. Tenha em atenção que as colunas TEMP (tal como mencionado anteriormente) não são copiadas e que a função **subcadeia** é utilizada para remover as aspas dos dados.
5. **Calcule o atraso de Meteorologia média e grupos os resultados por nome de localidade.** -Lo também serão saída os resultados ao armazenamento Blob. Note que a consulta irá remover apóstrofos a partir dos dados e irá excluir linhas onde o valor para **weather_delay** é nulo. Isto é necessário porque Sqoop, utilizado mais tarde neste tutorial, não tratar esses valores correctamente por predefinição.

Para obter uma lista completa dos comandos HiveQL, consulte o artigo [Hive Data Definition Language][hadoop-hiveql]. Cada comando HiveQL tem de terminar com um ponto e vírgula.

**Para criar um ficheiro de script HiveQL**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome da variável</th><th>Notas</th></tr>
    <tr><td>$storageAccountName</td><td>A conta de armazenamento do Windows Azure onde pretende carregar o script HiveQL para.</td></tr>
    <tr><td>$blobContainerName</td><td>O contentor de BLOBs onde pretende carregar o script HiveQL para.</td></tr>
    </table>
2. Abra o Azure o ISE do PowerShell.

3. Copie e cole o seguinte script no painel de script:

        [CmdletBinding()]
        Param(

            # Azure Blob storage variables
            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure storage account name for creating a new HDInsight cluster. If the account doesn't exist, the script will create one.")]
            [String]$storageAccountName,

            [Parameter(Mandatory=$True,
                       HelpMessage="Enter the Azure blob container name for creating a new HDInsight cluster. If not specified, the HDInsight cluster name will be used.")]
            [String]$blobContainerName
        )

        #region - Define variables
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        # The HiveQL script file is exported as this file before it's uploaded to Blob storage
        $hqlLocalFileName = "e:\tutorials\flightdelay\flightdelays.hql"
        
        # The HiveQL script file will be uploaded to Blob storage as this blob name
        $hqlBlobName = "tutorials/flightdelay/flightdelays.hql"
        
        # These two constants are used by the HiveQL script file
        #$srcDataFolder = "tutorials/flightdelay/data"
        $dstDataFolder = "/tutorials/flightdelay/output"
        #endregion

        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #Region - Validate user input
        Write-Host "`nValidating the Azure Storage account and the Blob container..." -ForegroundColor Green
        # Validate the Storage account
        if (-not (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}))
        {
            Write-Host "The storage account, $storageAccountName, doesn't exist." -ForegroundColor Red
            exit
        }
        else{
            $resourceGroupName = (Get-AzureRmStorageAccount|Where-Object{$_.StorageAccountName -eq $storageAccountName}).ResourceGroupName
        }
        
        # Validate the container
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        if (-not (Get-AzureStorageContainer -Context $storageContext |Where-Object{$_.Name -eq $blobContainerName}))
        {
            Write-Host "The Blob container, $blobContainerName, doesn't exist" -ForegroundColor Red
            Exit
        }
        #EngRegion
        
        #region - Validate the file and file path
        
        # Check if a file with the same file name already exists on the workstation
        Write-Host "`nvalidating the folder structure on the workstation for saving the HQL script file ..."  -ForegroundColor Green
        if (test-path $hqlLocalFileName){
        
            $isDelete = Read-Host 'The file, ' $hqlLocalFileName ', exists.  Do you want to overwirte it? (Y/N)'
        
            if ($isDelete.ToLower() -ne "y")
            {
                Exit
            }
        }
        
        # Create the folder if it doesn't exist
        $folder = split-path $hqlLocalFileName
        if (-not (test-path $folder))
        {
            Write-Host "`nCreating folder, $folder ..." -ForegroundColor Green
        
            new-item $folder -ItemType directory  
        }
        #end region
        
        #region - Write the Hive script into a local file
        Write-Host "`nWriting the Hive script into a file on your workstation ..." `
                    -ForegroundColor Green
        
        $hqlDropDelaysRaw = "DROP TABLE delays_raw;"
        
        $hqlCreateDelaysRaw = "CREATE EXTERNAL TABLE delays_raw (" +
                "YEAR string, " +
                "FL_DATE string, " +
                "UNIQUE_CARRIER string, " +
                "CARRIER string, " +
                "FL_NUM string, " +
                "ORIGIN_AIRPORT_ID string, " +
                "ORIGIN string, " +
                "ORIGIN_CITY_NAME string, " +
                "ORIGIN_CITY_NAME_TEMP string, " +
                "ORIGIN_STATE_ABR string, " +
                "DEST_AIRPORT_ID string, " +
                "DEST string, " +
                "DEST_CITY_NAME string, " +
                "DEST_CITY_NAME_TEMP string, " +
                "DEST_STATE_ABR string, " +
                "DEP_DELAY_NEW float, " +
                "ARR_DELAY_NEW float, " +
                "CARRIER_DELAY float, " +
                "WEATHER_DELAY float, " +
                "NAS_DELAY float, " +
                "SECURITY_DELAY float, " +
                "LATE_AIRCRAFT_DELAY float) " +
            "ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' " +
            "LINES TERMINATED BY '\n' " +
            "STORED AS TEXTFILE " +
            "LOCATION 'wasbs://flightdelay@hditutorialdata.blob.core.windows.net/2013Data';"
        
        $hqlDropDelays = "DROP TABLE delays;"
        
        $hqlCreateDelays = "CREATE TABLE delays AS " +
            "SELECT YEAR AS year, " +
                "FL_DATE AS flight_date, " +
                "substring(UNIQUE_CARRIER, 2, length(UNIQUE_CARRIER) -1) AS unique_carrier, " +
                "substring(CARRIER, 2, length(CARRIER) -1) AS carrier, " +
                "substring(FL_NUM, 2, length(FL_NUM) -1) AS flight_num, " +
                "ORIGIN_AIRPORT_ID AS origin_airport_id, " +
                "substring(ORIGIN, 2, length(ORIGIN) -1) AS origin_airport_code, " +
                "substring(ORIGIN_CITY_NAME, 2) AS origin_city_name, " +
                "substring(ORIGIN_STATE_ABR, 2, length(ORIGIN_STATE_ABR) -1)  AS origin_state_abr, " +
                "DEST_AIRPORT_ID AS dest_airport_id, " +
                "substring(DEST, 2, length(DEST) -1) AS dest_airport_code, " +
                "substring(DEST_CITY_NAME,2) AS dest_city_name, " +
                "substring(DEST_STATE_ABR, 2, length(DEST_STATE_ABR) -1) AS dest_state_abr, " +
                "DEP_DELAY_NEW AS dep_delay_new, " +
                "ARR_DELAY_NEW AS arr_delay_new, " +
                "CARRIER_DELAY AS carrier_delay, " +
                "WEATHER_DELAY AS weather_delay, " +
                "NAS_DELAY AS nas_delay, " +
                "SECURITY_DELAY AS security_delay, " +
                "LATE_AIRCRAFT_DELAY AS late_aircraft_delay " +
            "FROM delays_raw;"
        
        $hqlInsertLocal = "INSERT OVERWRITE DIRECTORY '$dstDataFolder' " +
            "SELECT regexp_replace(origin_city_name, '''', ''), " +
                "avg(weather_delay) " +
            "FROM delays " +
            "WHERE weather_delay IS NOT NULL " +
            "GROUP BY origin_city_name;"
        
        $hqlScript = $hqlDropDelaysRaw + $hqlCreateDelaysRaw + $hqlDropDelays + $hqlCreateDelays + $hqlInsertLocal
        
        $hqlScript | Out-File $hqlLocalFileName -Encoding ascii -Force
        #endregion
        
        #region - Upload the Hive script to the default Blob container
        Write-Host "`nUploading the Hive script to the default Blob container ..." -ForegroundColor Green
        
        # Create a storage context object
        $storageAccountKey = (Get-AzureRmStorageAccountKey -StorageAccountName $storageAccountName -ResourceGroupName $resourceGroupName)[0].Value
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
        
        # Upload the file from local workstation to Blob storage
        Set-AzureStorageBlobContent -File $hqlLocalFileName -Container $blobContainerName -Blob $hqlBlobName -Context $destContext
        #endregion
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    Eis as variáveis utilizadas no script:

    - **$hqlLocalFileName** - o script guarda o ficheiro de script HiveQL localmente antes de carregá-lo ao armazenamento Blob. Este é o nome do ficheiro. O valor predefinido é <u>C:\tutorials\flightdelay\flightdelays.hql</u>.
    - **$hqlBlobName** - este é o nome de ficheiro script do HiveQL blob utilizado no armazenamento de Blobs do Azure. O valor predefinido é tutorials/flightdelay/flightdelays.hql. Uma vez que o ficheiro será escrito diretamente para o armazenamento de Blobs do Azure, não existe um "/" no início do nome do blob. Se pretender aceder ao ficheiro a partir do armazenamento de BLOBs, terá de adicionar um "/" no início do nome do ficheiro.
    - **$srcDataFolder** e **$dstDataFolder** - = "flightdelay/tutoriais/dados" = "flightdelay/tutoriais/saída"


---
##<a id="appendix-c"></a>Anexo C - preparar uma base de dados do Azure SQL no resultado da tarefa Sqoop
**Preparar a base de dados SQL (unir isto com o script Sqoop)**

1. Prepare os parâmetros:

    <table border="1">
    <tr><th>Nome da variável</th><th>Notas</th></tr>
    <tr><td>$sqlDatabaseServerName</td><td>O nome do servidor de base de dados Azure SQL. Introduza nada para criar um novo servidor.</td></tr>
    <tr><td>$sqlDatabaseUsername</td><td>O nome de início de sessão para o servidor de base de dados do Azure SQL. Se $sqlDatabaseServerName for um servidor existente, o ID de utilizador e palavra-passe de início de sessão são utilizadas para autenticar com o servidor. Caso contrário, são utilizados para criar um novo servidor.</td></tr>
    <tr><td>$sqlDatabasePassword</td><td>A palavra-passe de início de sessão para o servidor de base de dados do Azure SQL.</td></tr>
    <tr><td>$sqlDatabaseLocation</td><td>Este valor é utilizado apenas quando estiver a criar um novo servidor de base de dados Azure.</td></tr>
    <tr><td>$sqlDatabaseName</td><td>Base de dados SQL utilizada para criar a tabela AvgDelays para a tarefa de Sqoop. Deixe-lo em branco, irá criar uma base de dados denominada HDISqoop. O nome da tabela no resultado da tarefa Sqoop é AvgDelays. </td></tr>
    </table>
2. Abra o Azure o ISE do PowerShell.
3. Copie e cole o seguinte script no painel de script:

        [CmdletBinding()]
        Param(
        
            # Azure Resource group variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure resource group name. It will be created if it doesn't exist.")]
            [String]$resourceGroupName,
        
            # SQL database server variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database Server Name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseServer, 
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user.")]
            [String]$sqlDatabaseLogin,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the Azure SQL Database admin user password.")]
            [String]$sqlDatabasePassword,
        
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the region to create the Database in.")]
            [String]$sqlDatabaseLocation,   #For example, West US.
        
            # SQL database variables
            [Parameter(Mandatory=$True,
                    HelpMessage="Enter the database name. It will be created if it doesn't exist.")]
            [String]$sqlDatabaseName # specify the database name if you have one created. Otherwise use "" to have the script create one for you.
        )
        
        # Treat all errors as terminating
        $ErrorActionPreference = "Stop"
        
        #region - Constants and variables
        
        # IP address REST service used for retrieving external IP address and creating firewall rules
        [String]$ipAddressRestService = "http://bot.whatismyipaddress.com"
        [String]$fireWallRuleName = "FlightDelay"
        
        # SQL database variables
        [String]$sqlDatabaseMaxSizeGB = 10
        
        #SQL query string for creating AvgDelays table
        [String]$sqlDatabaseTableName = "AvgDelays"
        [String]$sqlCreateAvgDelaysTable = " CREATE TABLE [dbo].[$sqlDatabaseTableName](
                    [origin_city_name] [nvarchar](50) NOT NULL,
                    [weather_delay] float,
                CONSTRAINT [PK_$sqlDatabaseTableName] PRIMARY KEY CLUSTERED
                (
                    [origin_city_name] ASC
                )
                )"
        #endregion
        
        #Region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #EndRegion
        
        #region - Create and validate Azure resouce group
        try{
            Get-AzureRmResourceGroup -Name $resourceGroupName
        }
        catch{
            New-AzureRmResourceGroup -Name $resourceGroupName -Location $sqlDatabaseLocation
        }
        
        #EndRegion
        
        #region - Create and validate Azure SQL database server
        try{
            Get-AzureRmSqlServer -ServerName $sqlDatabaseServer -ResourceGroupName $resourceGroupName}
        catch{
            Write-Host "`nCreating SQL Database server ..."  -ForegroundColor Green
        
            $sqlDatabasePW = ConvertTo-SecureString -String $sqlDatabasePassword -AsPlainText -Force
            $credential = New-Object System.Management.Automation.PSCredential($sqlDatabaseLogin,$sqlDatabasePW)
        
            $sqlDatabaseServer = (New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -SqlAdministratorCredentials $credential -Location $sqlDatabaseLocation).ServerName
            Write-Host "`tThe new SQL database server name is $sqlDatabaseServer." -ForegroundColor Cyan
        
            Write-Host "`nCreating firewall rule, $fireWallRuleName ..." -ForegroundColor Green
            $workstationIPAddress = Invoke-RestMethod $ipAddressRestService
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-workstation" -StartIpAddress $workstationIPAddress -EndIpAddress $workstationIPAddress
        
            #To allow other Azure services to access the server add a firewall rule and set both the StartIpAddress and EndIpAddress to 0.0.0.0. Note that this allows Azure traffic from any Azure subscription to access the server.
            New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -FirewallRuleName "$fireWallRuleName-Azureservices" -StartIpAddress "0.0.0.0" -EndIpAddress "0.0.0.0"
        }
        
        #endregion
        
        #region - Create and validate Azure SQL database
        
        try {
            Get-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName
        }
        catch {
            Write-Host "`nCreating SQL Database, $sqlDatabaseName ..."  -ForegroundColor Green
            New-AzureRMSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $sqlDatabaseServer -DatabaseName $sqlDatabaseName -Edition "Standard" -RequestedServiceObjectiveName "S1"
        }
        
        #endregion
        
        #region -  Execute an SQL command to create the AvgDelays table
        
        Write-Host "`nCreating SQL Database table ..."  -ForegroundColor Green
        $conn = New-Object System.Data.SqlClient.SqlConnection
        $conn.ConnectionString = "Data Source=$sqlDatabaseServer.database.windows.net;Initial Catalog=$sqlDatabaseName;User ID=$sqlDatabaseLogin;Password=$sqlDatabasePassword;Encrypt=true;Trusted_Connection=false;"
        $conn.open()
        $cmd = New-Object System.Data.SqlClient.SqlCommand
        $cmd.connection = $conn
        $cmd.commandtext = $sqlCreateAvgDelaysTable
        $cmd.executenonquery()
        
        $conn.close()
        
        Write-host "`nEnd of the PowerShell script" -ForegroundColor Green

    >[AZURE.NOTE] O script utiliza um serviço de transferência (REST) Estado representational, http://bot.whatismyipaddress.com, para obter o seu endereço IP externo. O endereço IP é utilizado para criar uma regra de firewall para o seu servidor de base de dados SQL.  

    Aqui estão algumas variáveis utilizadas no script:

    - **$ipAddressRestService** - o valor predefinido é http://bot.whatismyipaddress.com. É um endereço IP público serviço REST para o seu endereço IP externo. Pode utilizar outros serviços se pretender. O endereço IP externo obtido através do serviço vai ser utilizado para criar uma regra de firewall para o seu servidor de base de dados do Azure SQL, para que possa aceder a base de dados a partir do seu estação de trabalho (ao utilizar um script do Windows PowerShell).
    - **$fireWallRuleName** - este é o nome da regra de firewall para o servidor de base de dados do Azure SQL. O nome predefinido é <u>FlightDelay</u>. Pode alterá-lo, se pretender.
    - **$sqlDatabaseMaxSizeGB** - este valor é utilizada apenas quando estiver a criar um novo servidor de base de dados do Azure SQL. O valor predefinido é 10GB. 10GB é suficiente para este tutorial.
    - **$sqlDatabaseName** - este valor é utilizada apenas quando estiver a criar uma nova base de dados do Azure SQL. O valor predefinido é HDISqoop. Se mudar o nome-lo, tem de atualizar o script Sqoop Windows PowerShell, consoante adequado.

4. Prima **F5** para executar o script.
5. Valide a saída de script. Certifique-se de que o script foi executado com êxito.

##<a id="nextsteps"></a>Próximos passos
Agora compreender como carregar um ficheiro para o armazenamento de Blobs do Azure, como preencher uma tabela Hive utilizando os dados a partir do armazenamento de Blobs do Azure, como executar consultas de ramo e como utilizar Sqoop para exportar dados do HDFS para uma base de dados do Azure SQL. Para saber mais, consulte os artigos seguintes:

* [Introdução ao HDInsight][hdinsight-get-started]
* [Utilizar ramo com HDInsight][hdinsight-use-hive]
* [Utilizar Oozie com HDInsight][hdinsight-use-oozie]
* [Utilizar Sqoop com HDInsight][hdinsight-use-sqoop]
* [Utilizar porco com HDInsight][hdinsight-use-pig]
* [Desenvolver Java MapReduce programas para HDInsight][hdinsight-develop-mapreduce]



[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/


[rita-website]: http://www.transtats.bts.gov/DL_SelectFields.asp?Table_ID=236&DB_Short_Name=On-Time
[powershell-install-configure]: powershell-install-configure.md

[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-storage]: hdinsight-hadoop-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce-linux.md

[hadoop-hiveql]: https://cwiki.apache.org/confluence/display/Hive/LanguageManual+DDL
[hadoop-shell-commands]: http://hadoop.apache.org/docs/r0.18.3/hdfs_shell.html

[technetwiki-hive-error]: http://social.technet.microsoft.com/wiki/contents/articles/23047.hdinsight-hive-error-unable-to-rename.aspx

[image-hdi-flightdelays-avgdelays-dataset]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.AvgDelays.DataSet.png
[img-hdi-flightdelays-run-hive-job-output]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.RunHiveJob.Output.png
[img-hdi-flightdelays-flow]: ./media/hdinsight-analyze-flight-delay-data/HDI.FlightDelays.Flow.png
