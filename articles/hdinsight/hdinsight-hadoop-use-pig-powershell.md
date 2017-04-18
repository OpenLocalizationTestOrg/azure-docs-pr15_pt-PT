<properties
   pageTitle="Utilizar Hadoop porco com PowerShell no HDInsight | Microsoft Azure"
   description="Saiba como submeter porco tarefas a um cluster de Hadoop no HDInsight através do Azure PowerShell."
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
   ms.date="10/11/2016"
   ms.author="larryfr"/>

#<a name="run-pig-jobs-using-powershell"></a>Executar tarefas porco através do PowerShell

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

Este artigo fornece um exemplo de utilização do Azure PowerShell para submeter porco tarefas para um Hadoop num cluster de HDInsight. Porco permite-lhe escrever MapReduce tarefas utilizando um idioma (porco Latim), os modelos transformações de dados, em vez de mapear e reduzir funções.

> [AZURE.NOTE] Este documento não fornece uma descrição detalhada do que as declarações de porco Latim utilizadas nos exemplos fazer. Para obter informações sobre o Latim porco utilizados neste exemplo, consulte o artigo [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md).

##<a id="prereq"></a>Pré-requisitos

Para concluir os passos neste artigo, terá o seguinte.

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Uma estação de trabalho com o Azure PowerShell**.

    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]


##<a id="powershell"></a>Executar tarefas porco através do PowerShell

Azure PowerShell fornece *os cmdlets do* que permitem-lhe executar tarefas porco remotamente no HDInsight. Internamente, isto é feito usando chamadas do resto para [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente designado por Templeton) executadas no HDInsight cluster.

Os seguintes cmdlets são utilizados quando a executar tarefas porco num cluster HDInsight remoto:

* **Início de sessão AzureRmAccount**: autentica PowerShell Azure à sua subscrição do Azure

* **Novo AzureRmHDInsightPigJobDefinition**: cria uma nova *definição da tarefa* utilizando as instruções de porco Latim especificadas

* **Iniciar AzureRmHDInsightJob**: envia a definição da tarefa para HDInsight, inicia a tarefa e devolve um objecto de *tarefa* que pode ser utilizado para verificar o estado da tarefa

* **Aguarde AzureRmHDInsightJob**: usa o objeto de tarefa para verificar o estado da tarefa. -Lo será Aguarde até que a tarefa estiver concluída, ou o tempo de espera tenha sido ultrapassado.

* **Get-AzureRmHDInsightJobOutput**: utilizados para obter o resultado da tarefa

Os passos seguintes demonstram como utilizar estes cmdlets para executar uma tarefa no seu cluster HDInsight.

1. Utilizar um editor, guarde o seguinte código como **pigjob.ps1**. Tem de substituir o **nome de cluster** com o nome do seu cluster HDInsight.

        #Login to your Azure subscription
        Login-AzureRmAccount
        #Get credentials for the admin/HTTPs account
        $creds = Get-Credential

        #Specify the cluster name
        $clusterName = "CLUSTERNAME"
        
        #Get the cluster info so we can get the resource group, storage, etc.
        $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
        $resourceGroup = $clusterInfo.ResourceGroup
        $storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
        $container = $clusterInfo.DefaultStorageContainer
        $storageAccountKey = (Get-AzureRmStorageAccountKey `
            -Name $storageAccountName `
        -ResourceGroupName $resourceGroup)[0].Value

        #Store the Pig Latin into $QueryString
        $QueryString =  "LOGS = LOAD 'wasbs:///example/data/sample.log';" +
        "LEVELS = foreach LOGS generate REGEX_EXTRACT(`$0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;" +
        "FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;" +
        "GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;" +
        "FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;" +
        "RESULT = order FREQUENCIES by COUNT desc;" +
        "DUMP RESULT;"


        #Create a new HDInsight Pig Job definition
        $pigJobDefinition = New-AzureRmHDInsightPigJobDefinition `
            -Query $QueryString `
            -Arguments "-w"

        # Start the Pig job on the HDInsight cluster
        Write-Host "Start the Pig job ..." -ForegroundColor Green
        $pigJob = Start-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobDefinition $pigJobDefinition `
            -HttpCredential $creds

        # Wait for the Pig job to complete
        Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
        Wait-AzureRmHDInsightJob `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -HttpCredential $creds

        # Display the output of the Pig job.
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureRmHDInsightJobOutput `
            -ClusterName $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds

2. Abra uma nova linha de comandos do Windows PowerShell. Alterar directórios para a localização do ficheiro **pigjob.ps1** , em seguida, utilize o seguinte comando para executar o script:

        .\pigjob.ps1
        
    Irá primeiro ser-lhe iniciar sessão à sua subscrição do Azure. Em seguida, será pedido para o nome de conta de administrador/HTTPs e palavra-passe para o cluster HDInsight.

7. Quando a tarefa estiver concluída, deve devolver informações semelhante ao seguinte:

        Start the Pig job ...
        Wait for the Pig job to complete ...
        Display the standard output ...
        (TRACE,816)
        (DEBUG,434)
        (INFO,96)
        (WARN,11)
        (ERROR,6)
        (FATAL,2)

##<a id="troubleshooting"></a>Resolução de problemas

Se nenhuma informação é devolvida quando a tarefa estiver concluída, pode ter ocorrido um erro durante o processamento. Para ver informações de erro para esta tarefa, adicione o seguinte comando para o fim do ficheiro **pigjob.ps1** , guarde-o e, em seguida, executá-la novamente.

    # Print the output of the Pig job.
    Write-Host "Display the standard error output ..." -ForegroundColor Green
    Get-AzureRmHDInsightJobOutput `
            -Clustername $clusterName `
            -JobId $pigJob.JobId `
            -DefaultContainer $container `
            -DefaultStorageAccountName $storageAccountName `
            -DefaultStorageAccountKey $storageAccountKey `
            -HttpCredential $creds `
            -DisplayOutputType StandardError

Isto irá devolver as informações que foram escritas para STDERR no servidor quando executou a tarefa e podem ajudar a determinar porque é que a tarefa está a falhar.

##<a id="summary"></a>Resumo

Como pode ver, Azure PowerShell fornece uma forma fácil de executar tarefas de porco num HDInsight cluster, monitorizar o estado da tarefa e obter o resultado.

##<a id="nextsteps"></a>Próximos passos

Para obter informações gerais sobre porco no HDInsight:

* [Utilizar porco com Hadoop no HDInsight](hdinsight-use-pig.md)

Para obter informações sobre outras formas pode trabalhar com Hadoop no HDInsight:

* [Utilizar ramo com Hadoop no HDInsight](hdinsight-use-hive.md)

* [Utilizar MapReduce com Hadoop no HDInsight](hdinsight-use-mapreduce.md)
