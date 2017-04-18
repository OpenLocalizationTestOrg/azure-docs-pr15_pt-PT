<properties
    pageTitle="Depurar e analisar os serviços de Hadoop com informações de estado da pilha | Microsoft Azure"
    description="Recolher informações de estado da pilha para serviços de Hadoop e coloque a conta de armazenamento de Blobs do Azure para análise e depuração automaticamente."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
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


# <a name="collect-heap-dumps-in-blob-storage-to-debug-and-analyze-hadoop-services"></a>Pilha recolher regista no armazenamento Blob do depurar e analisar Hadoop serviços

[AZURE.INCLUDE [heapdump-selector](../../includes/hdinsight-selector-heap-dump.md)]

Informações de estado da pilha de conter um instantâneo da memória a aplicação, incluindo os valores de variáveis ao tempo a informação que foi criada. Para que sejam muito útil para diagnosticar os problemas que ocorrem em tempo de execução. Informações de estado da pilha podem ser automaticamente recolhidas para serviços de Hadoop e colocadas dentro da conta de armazenamento de Blobs do Azure de um utilizador em HDInsightHeapDumps /. 

Recolha de informações de estado da pilha para vários serviços tem de estar ativada para serviços em clusters individuais. A predefinição para esta funcionalidade é desativada para um cluster de. Estas informações de estado da pilha podem ser grandes, por isso, é aconselhável para monitorizar a conta de armazenamento de BLOBs onde estes estão a ser guardadas assim que a coleção de ter sido ativada.

> [AZURE.NOTE] As informações neste artigo apenas se aplica ao HDInsight baseados no Windows. Para obter informações sobre baseado em Linux HDInsight, consulte o artigo [Ativar informações de estado da pilha para serviços de Hadoop no baseado em Linux HDInsight](hdinsight-hadoop-collect-debug-heap-dump-linux.md)

## <a name="eligible-services-for-heap-dumps"></a>Serviços elegíveis para informações de estado da área de dados dinâmicos

Pode ativar a pilha informações de estado para os seguintes serviços:

*  **hcatalog** - tempelton
*  **ramo** - hiveserver2, metastore, derbyserver
*  **mapreduce** - jobhistoryserver
*  **fio** - resourcemanager, nodemanager, timelineserver
*  **hdfs** - datanode, secondarynamenode, namenode

## <a name="configuration-elements-that-enable-heap-dumps"></a>Elementos de configuração que permitem informações de estado da área de dados dinâmicos

Para ativar as informações de estado da pilha para um serviço, tem de definir os elementos de configuração apropriada na secção desse serviço, que é indicado pelo **service_name**.

    "javaargs.<service_name>.XX:+HeapDumpOnOutOfMemoryError" = "-XX:+HeapDumpOnOutOfMemoryError",
    "javaargs.<service_name>.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\Dumps\<service_name>_%date:~4,2%%date:~7,2%%date:~10,2%%time:~0,2%%time:~3,2%%time:~6,2%.hprof"

O valor de **service_name** pode ser qualquer um dos serviços listados acima: tempelton, hiveserver2, metastore, derbyserver, jobhistoryserver, resourcemanager, nodemanager, timelineserver, datanode, secondarynamenode, ou namenode.

## <a name="enable-using-azure-powershell"></a>Ativar através do PowerShell do Azure

Por exemplo, para ativar as informações de estado da área de dados dinâmicos ao utilizar o Azure PowerShell para jobhistoryserver, teria faça o seguinte:

[AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

    $MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'

    $MapRedConfigValues.Configuration = @{ "javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError"="-XX:+HeapDumpOnOutOfMemoryError" ; "javaargs.jobhistoryserver.XX:HeapDumpPath" = "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof" }

## <a name="enable-using-net-sdk"></a>Ativar utilizando .NET SDK

Por exemplo, para ativar as informações de estado da área de dados dinâmicos ao utilizar o Azure HDInsight .NET SDK para jobhistoryserver, teria faça o seguinte:

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:+HeapDumpOnOutOfMemoryError", "-XX:+HeapDumpOnOutOfMemoryError"));

    clusterInfo.MapReduceConfiguration.ConfigurationCollection.Add(new KeyValuePair<string, string>("javaargs.jobhistoryserver.XX:HeapDumpPath", "-XX:HeapDumpPath=c:\\Dumps\\jobhistoryserver_%date:~4,2%_%date:~7,2%_%date:~10,2%_%time:~0,2%_%time:~3,2%_%time:~6,2%.hprof"));
