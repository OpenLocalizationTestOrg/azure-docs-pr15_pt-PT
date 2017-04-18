<properties 
    pageTitle="Atividade de transmissão de Hadoop" 
    description="Saiba como pode utilizar a atividade de transmissão de Hadoop numa fábrica dados Azure para executar programas Hadoop transmissão no-pedido/seu próprio cluster HDInsight." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/20/2016" 
    ms.author="shlo"/>

# <a name="hadoop-streaming-activity"></a>Atividade de transmissão de Hadoop
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

Pode utilizar a atividade de HDInsightStreamingActivity invocar uma tarefa Hadoop transmissão de tubagem uma fábrica de dados do Azure. O fragmento de JSON seguinte mostra a sintaxe para utilizar o HDInsightStreamingActivity num ficheiro de JSON em curso. 

A atividade de transmissão de HDInsight numa fábrica de dados [em curso](data-factory-create-pipelines.md) executa programas Hadoop transmissão no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseados no Windows/Linux HDInsight. Este artigo constrói no artigo do [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

## <a name="json-sample"></a>Exemplo JSON
HDInsight cluster é preenchido automaticamente com dados (davinci.txt) e programas de exemplo (wc.exe e cat.exe). Por predefinição, o nome do contentor que é utilizado pelo HDInsight cluster é o nome do próprio cluster. Por exemplo, se o nome de cluster myhdicluster, o nome do contentor blob associado seria myhdicluster. 

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<nameofthecluster>/example/apps/wc.exe",
                            "<nameofthecluster>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService",
                        "getDebugInfo": "Failure"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

Tenha em atenção os seguintes pontos:

1. Defina o **linkedServiceName** para o nome do serviço ligado que aponta para o seu cluster de HDInsight no qual a tarefa mapreduce transmissão é executada.
2. Defina o tipo da atividade para **HDInsightStreaming**.
3. Para a propriedade de **mapeamento de pontos** , especifique o nome de mapeamento de pontos executável. No exemplo, cat.exe é o mapeamento de pontos executável.
4. Para a propriedade **redutor** , especifique o nome do redutor executável. No exemplo, wc.exe é o redutor executável.
5. A propriedade tipo de **entrada** , especifique o ficheiro de entrada (incluindo a localização) para o mapeamento de pontos. No exemplo: "wasb://adfsample@ <account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt ": adfsample é o contentor blob, dados/exemplo/Gutenberg é a pasta e davinci.txt é o blob.
6. Para a propriedade de tipo de **resultado** , especifique o ficheiro de exportação (incluindo a localização) para o redutor. O resultado da tarefa Hadoop transmissão de é escrito para a localização especificada para esta propriedade.
7. Na secção **filePaths** , especifique os caminhos para os executáveis mapeamento de pontos e redutor. No exemplo: "adfsample/example/apps/wc.exe", adfsample é o contentor blob, exemplo/aplicações é a pasta e wc.exe é um executável.
8. A propriedade **fileLinkedService** , especifique o serviço de armazenamento do Windows Azure ligadas que representa o armazenamento Azure que contém os ficheiros especificados na secção filePaths.
9. A propriedade de **argumentos** , especifique os argumentos para a tarefa de transmissão.
10. A propriedade **getDebugInfo** é um elemento opcional. Quando está definido para falha, os registos são transferidos apenas em caso de falha. Quando está definido para todos, os registos sempre são transferidos independentemente o estado de execução.

> [AZURE.NOTE] Como é mostrado no exemplo, especificar um conjunto de dados de saída para a atividade de transmissão de Hadoop para a propriedade **exporta** . Este conjunto de dados é apenas um fictício conjunto de dados que é necessário para conduzem a agenda de tubagem. Não tem de especificar quaisquer dataset da atividade para a propriedade de **entradas do tipo** de entrada.  

    
## <a name="example"></a>Exemplo
A tubagem neste tutorial executa o programa de mapa/reduzir de transmissão de contagem de palavras no seu cluster Azure HDInsight. 

### <a name="linked-services"></a>Serviços ligados

#### <a name="azure-storage-linked-service"></a>Serviço de armazenamento ligado do Azure
Primeiro, crie um serviço ligado para ligar o armazenamento do Azure que é utilizado pelo cluster Azure HDInsight para a fábrica de dados Azure. Se que copiar/colar o código seguinte, não se esqueça de substituir o nome da conta e chave de conta com o nome e uma tecla do seu armazenamento do Windows Azure. 

    {
        "name": "StorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
            }
        }
    }

#### <a name="azure-hdinsight-linked-service"></a>Serviço do Azure HDInsight ligada
Em seguida, criar um serviço ligado para ligar o seu cluster Azure HDInsight a fábrica dados Azure. Se lhe copiar/colar o código seguinte, substitua o nome do cluster HDInsight com o nome do seu cluster HDInsight e alterar os valores de nome e palavra-passe de utilizador. 
    
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "StorageLinkedService"
            }
        }
    }

### <a name="datasets"></a>Conjuntos de dados

#### <a name="output-dataset"></a>Conjunto de dados de saída
A tubagem neste exemplo não tomar qualquer entradas do tipo. Especifique um conjunto de dados de saída para a atividade de transmissão de HDInsight. Este conjunto de dados é apenas um fictício conjunto de dados que é necessário para conduzem a agenda de tubagem. 

    {
        "name": "StreamingOutputDataset",
        "properties": {
            "published": false,
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "adftutorial/streamingdata/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                },
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Em curso

A tubagem neste exemplo tem apenas uma atividade que é do tipo: **HDInsightStreaming**. 

O HDInsight cluster é preenchido automaticamente com dados (davinci.txt) e programas de exemplo (wc.exe e cat.exe). Por predefinição, o nome do contentor que é utilizado pelo HDInsight cluster é o nome do próprio cluster. Por exemplo, se o nome de cluster myhdicluster, o nome do contentor blob associado seria myhdicluster.  

    {
        "name": "HadoopStreamingPipeline",
        "properties": {
            "description": "Hadoop Streaming Demo",
            "activities": [
                {
                    "type": "HDInsightStreaming",
                    "typeProperties": {
                        "mapper": "cat.exe",
                        "reducer": "wc.exe",
                        "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                        "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                        "filePaths": [
                            "<blobcontainer>/example/apps/wc.exe",
                            "<blobcontainer>/example/apps/cat.exe"
                        ],
                        "fileLinkedService": "StorageLinkedService"
                    },
                    "outputs": [
                        {
                            "name": "StreamingOutputDataset"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "executionPriorityOrder": "NewestFirst",
                        "retry": 1
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "RunHadoopStreamingJob",
                    "description": "Run a Hadoop streaming job",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-04T00:00:00Z",
            "end": "2014-01-05T00:00:00Z"
        }
    }

## <a name="see-also"></a>Consulte também
- [Ramo de atividade](data-factory-hive-activity.md)
- [Porco atividade](data-factory-pig-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Invocar programas motores](data-factory-spark.md)
- [Invocar R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)

