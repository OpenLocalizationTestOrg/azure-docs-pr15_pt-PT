<properties
   pageTitle="Utilizar ramo de Hadoop com PowerShell no HDInsight | Microsoft Azure"
   description="Utilizar o PowerShell para executar consultas de ramo no Hadoop no HDInsight."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
    tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/07/2016"
   ms.author="larryfr"/>

#<a name="run-hive-queries-using-powershell"></a>Executar consultas de ramo através do PowerShell

[AZURE.INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Este artigo fornece um exemplo de utilização do Azure PowerShell no modo de grupo de recursos do Azure para executar consultas de ramo num Hadoop num cluster de HDInsight.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que as instruções do HiveQL que são utilizadas nos exemplos fazer. Para obter informações sobre o HiveQL utilizados neste exemplo, consulte o artigo [Utilizar Hive com Hadoop no HDInsight](hdinsight-use-hive.md).


**Pré-requisitos**

Para concluir os passos neste artigo, terá o seguinte.

- **Um cluster de Azure HDInsight (Hadoop no HDInsight) (baseados no Windows ou baseado em Linux)**
- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a name="run-hive-queries-using-azure-powershell"></a>Executar consultas de ramo através do PowerShell do Azure

Azure PowerShell fornece *os cmdlets do* que permitem-lhe executar remotamente ramo de consultas no HDInsight. Internamente, isto é feito usando chamadas do resto para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente designado por Templeton) executadas no HDInsight cluster.

Os seguintes cmdlets são utilizados quando executar consultas de ramo num cluster HDInsight remoto:

* **Adicionar AzureRmAccount**: autentica Azure PowerShell à sua subscrição do Azure

* **Novo AzureRmHDInsightHiveJobDefinition**: cria uma nova *definição da tarefa* utilizando as instruções de HiveQL especificadas

* **Iniciar AzureRmHDInsightJob**: envia a definição da tarefa para HDInsight, inicia a tarefa e devolve um objecto de *tarefa* que pode ser utilizado para verificar o estado da tarefa

* **Aguarde AzureRmHDInsightJob**: usa o objeto de tarefa para verificar o estado da tarefa. -Lo será Aguarde até que a tarefa estiver concluída ou for excedido o tempo de espera.

* **Get-AzureRmHDInsightJobOutput**: utilizados para obter o resultado da tarefa

* **Invocar AzureRmHDInsightHiveJob**: utilizado para executar HiveQL declarações. Isto irá bloquear a consulta for concluída, em seguida, devolve o resultado

* **Utilizar AzureRmHDInsightCluster**: define o cluster atual para utilizar o comando **Invocar AzureRmHDInsightHiveJob**

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no seu cluster HDInsight:

1. Utilizar um editor, guarde o seguinte código como **hivejob.ps1**. Tem de substituir o **nome de cluster** com o nome do seu cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
        $creds=Get-Credential

        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Add-AzureRmAccount
        }

        #HiveQL
        #Note: set hive.execution.engine=tez; is not required for
        #      Linux-based HDInsight
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"

        #Create an HDInsight Hive job definition
        $hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString 

        #Submit the job to the cluster
        Write-Host "Start the Hive job..." -ForegroundColor Green

        $hiveJob = Start-AzureRmHDInsightJob -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $creds

        #Wait for the Hive job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $creds

        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        # Print the output
        Write-Host "Display the standard output..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $hiveJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra uma nova linha de comandos do **Azure PowerShell** . Alterar directórios para a localização do ficheiro **hivejob.ps1** , em seguida, utilize o seguinte comando para executar o script:

        .\hivejob.ps1

    Quando executa o script, vai ser-lhe para introduzir as credenciais da conta HTTPS/administrador para o seu cluster. Pode também ser-lhe iniciar sessão à sua subscrição do Azure.
    
7. Quando a tarefa estiver concluída, deve devolver informações semelhante ao seguinte:

        Display the standard output...
        2012-02-03      18:35:34        SampleClass0    [ERROR] incorrect       id
        2012-02-03      18:55:54        SampleClass1    [ERROR] incorrect       id
        2012-02-03      19:25:27        SampleClass4    [ERROR] incorrect       id

4. Como mencionado anteriormente, **Invocar ramo** podem ser utilizados para executar uma consulta e aguarde que a resposta. Utilize os seguintes comandos e substituir o **nome de cluster** com o nome do seu cluster:

        Use-AzureRmHDInsightCluster -ClusterName $clusterName -HttpCredential $creds
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value
        $queryString = "set hive.execution.engine=tez;" +
                    "DROP TABLE log4jLogs;" +
                    "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';" +
                    "SELECT * FROM log4jLogs WHERE t4 = '[ERROR]';"
        Invoke-AzureRmHDInsightHiveJob `
            -StatusFolder "statusout" `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -Query $queryString

    O resultado terá o seguinte o seguinte aspeto:

        2012-02-03  18:35:34    SampleClass0    [ERROR] incorrect   id
        2012-02-03  18:55:54    SampleClass1    [ERROR] incorrect   id
        2012-02-03  19:25:27    SampleClass4    [ERROR] incorrect   id

    > [AZURE.NOTE] Para mais longa HiveQL consultas, pode utilizar o cmdlet do Azure PowerShell **Cadeias aqui** ou ficheiros de script de HiveQL. O fragmento que se segue mostra como utilizar o cmdlet **Invocar Hive** para executar um ficheiro de script HiveQL. O ficheiro de script HiveQL tem de ser carregado para wasbs: / /.
    >
    > `Invoke-AzureRmHDInsightHiveJob -File "wasbs://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
    >
    > Para mais informações sobre **Cadeias de aqui**, consulte o artigo <a href="http://technet.microsoft.com/library/ee692792.aspx" target="_blank">Utilizar o Windows PowerShell aqui-cadeias</a>.

##<a name="troubleshooting"></a>Resolução de problemas

Se nenhuma informação é devolvida quando a tarefa estiver concluída, pode ter ocorrido um erro durante o processamento. Para ver informações de erro para esta tarefa, adicione o seguinte para o fim do ficheiro **hivejob.ps1** , guarde-o e, em seguida, executá-la novamente.

    # Print the output of the Hive job.
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $job.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isto devolve as informações que são escritas STDERR no servidor quando executou a tarefa e podem ajudar a determinar porque é que a tarefa está a falhar.

##<a name="summary"></a>Resumo

Como pode ver, Azure PowerShell fornece uma forma fácil de executar consultas de ramo num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

##<a name="next-steps"></a>Próximos passos

Para obter informações gerais sobre ramo no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
