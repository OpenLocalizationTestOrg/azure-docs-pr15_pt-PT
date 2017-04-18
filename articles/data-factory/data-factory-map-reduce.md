<properties 
    pageTitle="Invocar MapReduce programa a partir do Azure dados fábrica" 
    description="Saiba como a processar dados ao executar programas MapReduce num cluster Azure HDInsight a partir de uma fábrica dados Azure." 
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
    ms.date="09/12/2016" 
    ms.author="shlo"/>

# <a name="invoke-mapreduce-programs-from-data-factory"></a>Invocar MapReduce programas a partir de dados fábrica
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

A atividade de HDInsight MapReduce numa fábrica de dados [em curso](data-factory-create-pipelines.md) executa programas MapReduce no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseados no Windows/Linux HDInsight. Este artigo constrói no artigo do [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

## <a name="introduction"></a>Introdução 
Uma tubagem numa fábrica dados Azure processa dados nos serviços do armazenamento ligadas, utilizando os serviços de cluster ligadas. Contém uma sequência de atividades onde cada actividade executa uma operação de processamento específico. Este artigo descreve como utilizar a atividade de MapReduce HDInsight.
 
Consulte o artigo [porco](data-factory-pig-activity.md) e [Hive](data-factory-hive-activity.md) para obter detalhes sobre como executar scripts de porco/ramo num cluster baseados no Windows/Linux HDInsight a partir de uma tubagem utilizando HDInsight porco e ramo de atividades. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON para HDInsight MapReduce atividade 

Na definição do JSON para a atividade de HDInsight: 
 
1. Defina o **tipo** da **atividade** ao **HDInsight**.
3. Especifique o nome a classe de propriedade **NomeClasse** .
4. Especifique o caminho para o ficheiro para caixa, incluindo o nome do ficheiro para a propriedade **jarFilePath** .
5. Especifique o serviço ligado que refere-se para o armazenamento de Blobs do Azure que contém o ficheiro para caixa de propriedade **jarLinkedService** .   
6. Especificar quaisquer argumentos para o programa MapReduce na secção de **argumentos** . O tempo de execução, verá alguns argumentos extra (por exemplo: mapreduce.job.tags) do quadro MapReduce. Para diferenciar os argumentos com os argumentos MapReduce, considere utilizar a opção e valor como argumentos, conforme mostrado no exemplo seguinte (- s, – entrada, – saída, etc., são opções imediatamente seguidas pelos respetivos valores).

        {
            "name": "MahoutMapReduceSamplePipeline",
            "properties": {
                "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
                "activities": [
                    {
                        "type": "HDInsightMapReduce",
                        "typeProperties": {
                            "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                            "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                            "jarLinkedService": "StorageLinkedService",
                            "arguments": [
                                "-s",
                                "SIMILARITY_LOGLIKELIHOOD",
                                "--input",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
                                "--output",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
                                "--maxSimilaritiesPerItem",
                                "500",
                                "--tempDir",
                                "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
                            ]
                        },
                        "inputs": [
                            {
                                "name": "MahoutInput"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "MahoutOutput"
                            }
                        ],
                        "policy": {
                            "timeout": "01:00:00",
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        },
                        "name": "MahoutActivity",
                        "description": "Custom Map Reduce to generate Mahout result",
                        "linkedServiceName": "HDInsightLinkedService"
                    }
                ],
                "start": "2014-01-03T00:00:00Z",
                "end": "2014-01-04T00:00:00Z",
                "isPaused": false,
                "hubName": "mrfactory_hub",
                "pipelineMode": "Scheduled"
            }
        }
    
    

Pode utilizar a atividade de MapReduce HDInsight para executar qualquer ficheiro de para caixa MapReduce num HDInsight cluster. A seguinte definição de JSON exemplo de uma tubagem, a atividade de HDInsight é configurada para executar um ficheiro Mahout JAR.

## <a name="sample-on-github"></a>Amostra de GitHub
Pode transferir uma amostra para utilizar a atividade de MapReduce HDInsight a partir do: [Amostras de fábrica do mesmo de dados no GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Executar o programa de contagem de palavras
A tubagem neste exemplo executa o programa de mapa/reduzir de contagem de palavras no seu cluster Azure HDInsight.   

### <a name="linked-services"></a>Serviços ligados
Primeiro, crie um serviço ligado para ligar o armazenamento do Azure que é utilizado pelo cluster Azure HDInsight para a fábrica de dados Azure. Se que copiar/colar o código seguinte, não se esqueça de substituir o **nome da conta** e **chave de conta** com o nome e uma tecla do seu armazenamento do Windows Azure. 

#### <a name="azure-storage-linked-service"></a>Serviço de armazenamento ligado do Azure

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
Em seguida, criar um serviço ligado para ligar o seu cluster Azure HDInsight a fábrica dados Azure. Se lhe copiar/colar o código seguinte, substitua o **nome do cluster HDInsight** com o nome do seu cluster HDInsight e alterar os valores de nome e palavra-passe de utilizador.   

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
A tubagem neste exemplo não tomar qualquer entradas do tipo. Especifique um conjunto de dados de saída para a atividade de MapReduce HDInsight. Este conjunto de dados é apenas um fictício conjunto de dados que é necessário para conduzem a agenda de tubagem.  

    {
        "name": "MROutput",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "fileName": "WordCountOutput1.txt",
                "folderPath": "example/data/",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

### <a name="pipeline"></a>Em curso
A tubagem neste exemplo tem apenas uma atividade que é do tipo: HDInsightMapReduce. Algumas das propriedades importantes na JSON são: 

Propriedade | Notas
:-------- | :-----
tipo | O tipo de tem de estar definido para **HDInsightMapReduce**. 
NomeClasse | Nome da categoria é: **wordcount**
jarFilePath | Caminho para o ficheiro para caixa que contém a classe. Se lhe copiar/colar o código seguinte, não se esqueça alterar o nome do cluster. 
jarLinkedService | Serviço de armazenamento ligado Azure que contém o ficheiro para caixa. Este serviço ligado refere-se ao armazenamento de que está associado ao cluster HDInsight. 
argumentos | O programa wordcount leva dois argumentos, de uma entrada e um resultado. O ficheiro de entrada é o ficheiro davinci.txt.
frequência/intervalo | Os valores para estas propriedades corresponder o conjunto de dados de saída. 
linkedServiceName | refere-se para o serviço de HDInsight ligado tiver criado anteriormente.   

    {
        "name": "MRSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run the Word Count Program",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "wordcount",
                        "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                        "jarLinkedService": "StorageLinkedService",
                        "arguments": [
                            "/example/data/gutenberg/davinci.txt",
                            "/example/data/WordCountOutput1"
                        ]
                    },
                    "outputs": [
                        {
                            "name": "MROutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    },
                    "name": "MRActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2014-01-03T00:00:00Z",
            "end": "2014-01-04T00:00:00Z"
        }
    }

## <a name="run-spark-programs"></a>Motores são executados
Pode utilizar MapReduce atividade para executar motores programas no seu cluster de motores de HDInsight. Consulte o artigo [invocar motores programas a partir do Azure dados fábrica](data-factory-spark.md) para obter detalhes.  

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com
 
## <a name="see-also"></a>Consulte também
- [Ramo de atividade](data-factory-hive-activity.md)
- [Porco atividade](data-factory-pig-activity.md)
- [Atividade de transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas motores](data-factory-spark.md)
- [Invocar R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)
