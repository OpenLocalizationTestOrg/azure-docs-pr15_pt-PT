<properties 
    pageTitle="Porco atividade" 
    description="Saiba como pode utilizar a atividade de porco numa fábrica dados Azure para executar scripts de porco num cluster HDInsight no-pedido/seu próprio." 
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
    ms.date="08/31/2016" 
    ms.author="shlo"/>

# <a name="pig-activity"></a>Porco atividade
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

A atividade de HDInsight porco numa fábrica de dados [em curso](data-factory-create-pipelines.md) executa consultas porco no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseados no Windows/Linux HDInsight. Este artigo constrói no artigo do [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

## <a name="syntax"></a>Sintaxe

    {
        "name": "HiveActivitySamplePipeline",
        "properties": {
        "activities": [
            {
                "name": "Pig Activity",
                "description": "description",
                "type": "HDInsightPig",
                "inputs": [
                    {
                        "name": "input tables"
                    }
                ],
                "outputs": [
                    {
                        "name": "output tables"
                    }
                ],
                "linkedServiceName": "MyHDInsightLinkedService",
                "typeProperties": {
                    "script": "Pig script",
                    "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
                    "defines": {
                        "param1": "param1Value"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
            }
        ]
      }
    }

## <a name="syntax-details"></a>Detalhes de sintaxe

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
nome | Nome da atividade | Sim
Descrição | Texto que descreva a atividade para que é utilizada | N
tipo | HDinsightPig | Sim
entradas do tipo | Um ou mais entradas consumidas pela atividade de porco | N
resultados de | Resultados de uma ou mais produzidos pela atividade de porco | Sim
linkedServiceName | Referência ao cluster HDInsight registado como um serviço ligado numa fábrica de dados | Sim
script | Especificar o inline de script porco | N
caminho de script | Armazene o script porco num armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'CaminhoScript'. Ambos não podem ser utilizados em conjunto. O nome do ficheiro é sensível às maiúsculas. | N
Define | Especificar parâmetros como pares valor/chave para referenciar dentro do script porco | N

## <a name="example"></a>Exemplo

Vamos considere um exemplo de jogos registos de análise em que pretende identificar o tempo gasto por leitores de reprodução de jogos iniciados pela sua empresa.
 
O registo de jogos de exemplo seguinte é um ficheiro separado por vírgulas (,). Contém os campos seguintes – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

O **script porco** para processar estes dados:

    PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    
    GroupProfile = Group PigSampleIn all;
    
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    
    Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');

Para executar este script porco no pipeline fábrica de dados, faça o seguinte:

1. Crie um serviço ligado para registar a [sua própria HDInsight calcular cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar [a pedido HDInsight calcular cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar este serviço ligado **HDInsightLinkedService**.
2.  Crie um [serviço ligadas](data-factory-azure-blob-connector.md) para configurar a ligação ao armazenamento de Blobs do Azure que aloja os dados. Vamos chamar este serviço ligado **StorageLinkedService**.
3.  Crie [conjuntos de dados](data-factory-create-datasets.md) , apontar para a entrada de dados e os dados de saída. Vamos ligar para o conjunto de dados de entrada **PigSampleIn** e o conjunto de dados de saída **PigSampleOut**.
4.  Copie a consulta porco num ficheiro de armazenamento de Blobs do Azure configurado no passo #2. Se o armazenamento Azure que aloja os dados é a diferença entre aquele que aloja o ficheiro de consulta, crie um serviço de armazenamento do Windows Azure ligadas em separado. Referem-se para o serviço ligado na configuração de atividade. Utilize **CaminhoScript **para especificar o caminho para o ficheiro de script de porco e **scriptLinkedService**. 
    
    > [AZURE.NOTE] Também pode fornecer a inline de script porco na definição de atividade, utilizando a propriedade de **script** . No entanto, estamos não recomendamos esta abordagem como todos os carateres especiais nas necessidades de script ser retirado e pode causar problemas de depuração. A melhor prática é siga o passo #4.
5. Crie a tubagem com a atividade de HDInsightPig. Esta actividade processa dados de entrada ao executar o script porco num cluster de HDInsight.

        {
          "name": "PigActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "PigActivitySample",
                "type": "HDInsightPig",
                "inputs": [
                  {
                    "name": "PigSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "PigSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                }
              }
            ]
          }
        } 
6. Implemente a tubagem. Consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) artigo para obter detalhes. 
7. Monitorize pipeline de utilizar as vistas de monitorização e gestão da fábrica de dados. Consulte o artigo [monitorização e gerir dados fábrica tubagens](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes.

## <a name="specifying-parameters-for-a-pig-script"></a>Especificação de parâmetros para obter um script porco 

Considere o seguinte exemplo: jogos registos são penetração diariamente para o armazenamento de Blobs do Azure e armazenados numa pasta de data com base com partições e a hora. Pretende parametrizar o script porco e passar a localização da pasta entrada dinamicamente durante runtime e produzir também a partições com data e hora de saída.
 
Para utilizar parametrizada porco script, faça o seguinte:

- Defina os parâmetros de **define**.

        {
            "name": "PigActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "PigActivitySample",
                    "type": "HDInsightPig",
                    "inputs": [
                        {
                            "name": "PigSampleIn"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "PigSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0: %M}/dayno={0: %d}/',SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        }
                    },
                    "scheduler": {
                        "frequency": "Day",
                        "interval": 1
                    }
                }
            ]
          }
        }  
      
- Em Script porco, referir-se para os parâmetros de utilizando '**$parameterName**', conforme mostrado no exemplo seguinte:

        PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);   
        GroupProfile = Group PigSampleIn all;       
        PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);      
        Store PigSampleOut into '$Output' USING PigStorage (','); 


## <a name="see-also"></a>Consulte também
- [Ramo de atividade](data-factory-hive-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Atividade de transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas motores](data-factory-spark.md)
- [Invocar R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)


