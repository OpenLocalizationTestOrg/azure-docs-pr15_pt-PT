<properties
   pageTitle="Utilizar MapReduce e PowerShell com o Hadoop | Microsoft Azure"
   description="Saiba como utilizar o PowerShell para executar remotamente MapReduce tarefas com Hadoop no HDInsight."
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
   ms.date="08/29/2016"
   ms.author="larryfr"/>

# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Executar tarefas MapReduce com Hadoop no HDInsight através do PowerShell

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Este artigo fornece um exemplo de utilização do Azure PowerShell para executar uma tarefa de MapReduce num Hadoop num cluster de HDInsight.

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte procedimento:

- **Um cluster de Azure HDInsight (Hadoop no HDInsight) (baseados no Windows ou baseado em Linux)**

- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

##<a id="powershell"></a>Executar uma tarefa de MapReduce através do PowerShell do Azure

Azure PowerShell fornece *os cmdlets do* que permitem-lhe executar remotamente MapReduce tarefas no HDInsight. Internamente, isto é feito usando chamadas do resto para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente designado por Templeton) executadas no HDInsight cluster.

Os seguintes cmdlets são utilizados quando a executar tarefas MapReduce num cluster HDInsight remoto.

* **Início de sessão AzureRmAccount**: autentica Azure PowerShell à sua subscrição do Azure

* **Novo AzureRmHDInsightMapReduceJobDefinition**: cria uma nova *definição de tarefa* , utilizando as informações de MapReduce especificadas

* **Iniciar AzureRmHDInsightJob**: envia a definição da tarefa para HDInsight, inicia a tarefa e devolve um objecto de *tarefa* que pode ser utilizado para verificar o estado da tarefa

* **Aguarde AzureRmHDInsightJob**: usa o objeto de tarefa para verificar o estado da tarefa. Aguarda até que a tarefa estiver concluída ou for excedido o tempo de espera.

* **Get-AzureRmHDInsightJobOutput**: utilizados para obter o resultado da tarefa

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no seu cluster HDInsight.

1. Utilizar um editor, guarde o seguinte código como **mapreducejob.ps1**. Tem de substituir o **nome de cluster** com o nome do seu cluster HDInsight.

        #Specify the values
        $clusterName = "CLUSTERNAME"
                
        # Login to your Azure subscription
        # Is there an active Azure subscription?
        $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
        if(-not($sub))
        {
            Login-AzureRmAccount
        }

        #Get HTTPS/Admin credentials for submitting the job later
        $creds = Get-Credential
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName=$clusterInfo.DefaultStorageAccount.split('.')[0]
        $container=$clusterInfo.DefaultStorageContainer
        $storageAccountKey=(Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Create a storage content and upload the file
        $context = New-AzureStorageContext `
            -StorageAccountName $storageAccountName `
            -StorageAccountKey $storageAccountKey
            
        #Define the MapReduce job
        #NOTE: If using an HDInsight 2.0 cluster, use hadoop-examples.jar instead.
        # -JarFile = the JAR containing the MapReduce application
        # -ClassName = the class of the application
        # -Arguments = The input file, and the output directory
        $wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
            -JarFile "wasbs:///example/jars/hadoop-mapreduce-examples.jar" `
            -ClassName "wordcount" `
            -Arguments `
                "wasbs:///example/data/gutenberg/davinci.txt", `
                "wasbs:///example/data/WordCountOutput"

        #Submit the job to the cluster
        Write-Host "Start the MapReduce job..." -ForegroundColor Green
        $wordCountJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $wordCountJobDefinition `
            -HttpCredential $creds

        #Wait for the job to complete
        Write-Host "Wait for the job to complete..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $wordCountJob.JobId `
            -HttpCredential $creds
        # Download the output
        Get-AzureStorageBlobContent `
            -Blob 'example/data/WordCountOutput/part-r-00000' `
            -Container $container `
            -Destination output.txt `
            -Context $context
        # Print the output
        Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds
            
2. Abra uma nova linha de comandos do **Azure PowerShell** . Alterar directórios para a localização do ficheiro **mapreducejob.ps1** , em seguida, utilize o seguinte comando para executar o script:

        .\mapreducejob.ps1
    
    Quando executa o script, poderá ser-lhe para autenticar à sua subscrição do Azure. Também lhe ser pedido para fornecer o nome da conta HTTPS/administrador e a palavra-passe para o cluster HDInsight.

3. Quando a tarefa estiver concluída, deverá receber saída semelhante ao seguinte:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Este resultado indica que a tarefa é concluída com êxito.

    > [AZURE.NOTE] Se o **ExitCode** for um valor diferente de 0, consulte o artigo [resolução de problemas](#troubleshooting).

    Este exemplo também irá guardar os ficheiros transferidos para um ficheiro de **saída** no diretório que vai executar o script a partir de.

###<a name="view-output"></a>Vista de saída

Abra o ficheiro de **saída** num editor de texto para ver as palavras e as contagens produzidas pela tarefa.

> [AZURE.NOTE] Os ficheiros de saída de uma tarefa de MapReduce são imutáveis. Por isso, se executar novamente neste exemplo, terá de alterar o nome de ficheiro de exportação.

##<a id="troubleshooting"></a>Resolução de problemas

Se nenhuma informação é devolvida quando a tarefa estiver concluída, pode ter ocorrido um erro durante o processamento. Para ver informações de erro para esta tarefa, adicione o seguinte comando para o fim do ficheiro **mapreducejob.ps1** , guarde-o e, em seguida, executá-la novamente.

    # Print the output of the WordCount job.
    Write-Host "Display the standard output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $wordCountJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isto devolve as informações que foram escritas para STDERR no servidor quando executou a tarefa e podem ajudar a determinar porque é que a tarefa está a falhar.

##<a id="summary"></a>Resumo

Como pode ver, Azure PowerShell fornece uma forma fácil de executar tarefas de MapReduce num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais acerca de tarefas de MapReduce no HDInsight:

* [Utilizar o MapReduce no HDInsight Hadoop](hdinsight-use-mapreduce.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)
