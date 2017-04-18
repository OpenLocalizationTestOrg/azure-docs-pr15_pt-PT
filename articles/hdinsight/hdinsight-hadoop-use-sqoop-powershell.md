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

# <a name="run-sqoop-jobs-using-azure-powershell-for-hadoop-in-hdinsight"></a>Executar tarefas Sqoop através do PowerShell do Azure para Hadoop no HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Saiba como utilizar o Azure PowerShell para executar tarefas Sqoop no HDInsight para importar e exportar entre HDInsight cluster e base de dados Azure SQL ou base de dados do SQL Server.

> [AZURE.NOTE] Os passos neste artigo podem ser utilizados com um de um baseado no Windows ou Linux HDInsight cluster; No entanto, estes passos funcionam apenas a partir de um cliente do Windows. Para outros métodos de submissão de tarefa, clique no Seletor de tabulação na parte superior o artigo.


###<a name="prerequisites"></a>Pré-requisitos

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Cluster de respostas Hadoop em HDInsight**. Consulte o artigo [Criar cluster e base de dados SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

    
## <a name="run-sqoop-using-powershell"></a>Executar Sqoop através do PowerShell

O seguinte script do PowerShell previamente processa o ficheiro de origem e exporta-lo para uma base de dados do Azure SQL:

    $resourceGroupName = "<AzureResourceGroupName>"
    $hdinsightClusterName = "<HDInsightClusterName>"

    $httpUserName = "admin"
    $httpPassword = "<Password>"

    $defaultStorageAccountName = $hdinsightClusterName + "store"
    $defaultBlobContainerName = $hdinsightClusterName


    $sqlDatabaseServerName = $hdinsightClusterName + "dbserver"
    $sqlDatabaseName = $hdinsightClusterName + "db"
    $sqlDatabaseLogin = "sqluser"
    $sqlDatabasePassword = "<Password>"

    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion
        
    #region - pre-process the source file
        
    Write-Host "`nPreprocessing the source file ..." -ForegroundColor Green
        
    # This procedure creates a new file with $destBlobName
    $sourceBlobName = "example/data/sample.log"
    $destBlobName = "tutorials/usesqoop/data/sample.log"
        
    # Define the connection string
    $defaultStorageAccountKey = (Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName)[0].Value
    $storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=$defaultStorageAccountName;AccountKey=$defaultStorageAccountKey"
        
    # Create block blob objects referencing the source and destination blob.
    $storageAccount = Get-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $defaultStorageAccountName
    $storageContainer = ($storageAccount |Get-AzureStorageContainer -Name $defaultBlobContainerName).CloudBlobContainer
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
        
    #region - export the log file from the cluster to the SQL database
        
    Write-Host "Exporting the log file ..." -ForegroundColor Green

    $pw = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$pw)
        
    # Connection string for Azure SQL Database.
    # Comment if using SQL Server
    $connectionString = "jdbc:sqlserver://$sqlDatabaseServerName.database.windows.net;user=$sqlDatabaseLogin@$sqlDatabaseServerName;password=$sqlDatabasePassword;database=$sqlDatabaseName"
    # Connection string for SQL Server.
    # Uncomment if using SQL Server.
    #$connectionString = "jdbc:sqlserver://$sqlDatabaseServerName;user=$sqlDatabaseLogin;password=$sqlDatabasePassword;database=$sqlDatabaseName"
        
    $tableName_log4j = "log4jlogs"
    $exportDir_log4j = "/tutorials/usesqoop/data"
    $sqljdbcdriver = "/user/oozie/share/lib/sqoop/sqljdbc41.jar"
        
    # Submit a Sqoop job
    $sqoopDef = New-AzureRmHDInsightSqoopJobDefinition `
        -Command "export --connect $connectionString --table $tableName_log4j --export-dir $exportDir_log4j --input-fields-terminated-by \0x20 -m 1" `
        -Files $sqljdbcdriver

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

##<a name="limitations"></a>Limitações

* Em volume export - baseado em com Linux HDInsight, o conector de Sqoop utilizado para exportar dados para o Microsoft SQL Server ou base de dados do SQL Azure atualmente não suporta insere em volume.

* De lotes - com baseado em Linux HDInsight, ao utilizar o `-batch` mudar quando efetuar inserções, Sqoop irá efetuar várias insere em vez de lotes as operações de inserir.

##<a name="next-steps"></a>Próximos passos

Agora que tem aprendeu como utilizar Sqoop. Para obter mais informações, consulte o artigo:

- [Utilizar Oozie com HDInsight](hdinsight-use-oozie.md): utilizar Sqoop ação num fluxo de trabalho Oozie.
- [Analisar os dados de atrasos em voos utilizando HDInsight](hdinsight-analyze-flight-delay-data.md): utilizar Hive para analisar voo atrasar dados e, em seguida, utilize Sqoop para exportar dados para uma base de dados do Azure SQL.
- [Transferir dados do HDInsight](hdinsight-upload-data.md): encontrar outros métodos para carregar os dados ao armazenamento Blob HDInsight/Azure.


[sqoop-user-guide-1.4.4]: https://sqoop.apache.org/docs/1.4.4/SqoopUserGuide.html
