<properties 
    pageTitle="Ramo de atividade" 
    description="Saiba como pode utilizar a atividade Hive numa fábrica dados Azure para executar consultas de ramo num cluster HDInsight no-pedido/seu próprio." 
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
    ms.date="10/11/2016" 
    ms.author="shlo"/>

# <a name="hive-activity"></a>Ramo de atividade
> [AZURE.SELECTOR]
[Ramo](data-factory-hive-activity.md)  
[Porco](data-factory-pig-activity.md)  
[MapReduce](data-factory-map-reduce.md)  
[Transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
[Formação de máquina](data-factory-azure-ml-batch-execution-activity.md) 
[Procedimento armazenado](data-factory-stored-proc-activity.md)
[Dados Lake Analytics U-SQL](data-factory-usql-activity.md)
[.NET personalizado](data-factory-use-custom-activities.md)

A atividade de HDInsight ramo numa fábrica de dados [em curso](data-factory-create-pipelines.md) executa ramo de consultas no [seu próprio](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou [a pedido](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) cluster baseados no Windows/Linux HDInsight. Este artigo constrói no artigo do [atividades de transformação de dados](data-factory-data-transformation-activities.md) , que apresenta uma descrição geral de transformação de dados e as atividades de transformação suportados.

## <a name="syntax"></a>Sintaxe

    {
        "name": "Hive Activity",
        "description": "description",
        "type": "HDInsightHive",
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
          "script": "Hive script",
          "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
       "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
    }
    
## <a name="syntax-details"></a>Detalhes de sintaxe

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
nome | Nome da atividade | Sim
Descrição | Texto que descreva a atividade para que é utilizada | N
tipo | HDinsightHive | Sim
entradas do tipo | Entradas consumidas pela atividade de ramo | N
resultados de | Resultados de produzidos pela atividade de ramo | Sim 
linkedServiceName | Referência ao cluster HDInsight registado como um serviço ligado numa fábrica de dados | Sim 
script | Especificar o inline de script de ramo | N
caminho de script | Armazenar o script ramo um armazenamento de Blobs do Azure e forneça o caminho para o ficheiro. Utilize a propriedade 'script' ou 'CaminhoScript'. Ambos não podem ser utilizados em conjunto. O nome do ficheiro é sensível às maiúsculas. | N 
Define | Especificar parâmetros como pares valor/chave para referenciar dentro do script ramo utilizando 'hiveconf'  | N

## <a name="example"></a>Exemplo

Vamos considere um exemplo de jogos registos de análise em que pretende identificar o tempo gasto pelos utilizadores reproduzir jogos iniciados pela sua empresa. 

O registo seguinte é um registo de jogos de exemplo, que é uma vírgula (`,`) separadas e contém os seguintes campos – ProfileID, SessionStart, duração, SrcIPAddress e GameType.

    1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
    1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
    1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
    1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
    .....

O **Hive script** para processar estes dados:

    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID       string, 
        SessionStart    string, 
        Duration        int, 
        SrcIPAddress    string, 
        GameType        string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 
    
    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (   
        ProfileID   string, 
        Duration    int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';
    
    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID

Para executar este script ramo no pipeline fábrica de dados, que tem de fazer o seguinte

1. Crie um serviço ligado para registar a [sua própria HDInsight calcular cluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) ou configurar [a pedido HDInsight calcular cluster](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vamos chamar este serviço ligado "HDInsightLinkedService".
2. Crie um [serviço ligadas](data-factory-azure-blob-connector.md) para configurar a ligação ao armazenamento de Blobs do Azure que aloja os dados. Vamos ligar este serviço ligado "StorageLinkedService"
3. Crie [conjuntos de dados](data-factory-create-datasets.md) , apontar para a entrada de dados e os dados de saída. Vamos ligar para o conjunto de dados entrado "HiveSampleIn" e o conjunto de dados de saída "HiveSampleOut"
4. Copiar a consulta ramo como um ficheiro ao armazenamento de Blobs do Azure configurado no passo #2. Se o armazenamento de que aloja os dados é a diferença entre aquele que aloja este ficheiro de consulta, crie um serviço de armazenamento do Windows Azure ligadas separado e referência ao mesmo na atividade. Utilize **CaminhoScript **para especificar o caminho para o ficheiro de ramo de consulta e **scriptLinkedService** para especificar o armazenamento Azure que contém o ficheiro de script. 

    > [AZURE.NOTE] Também pode fornecer a inline de script ramo na definição de atividade, utilizando a propriedade de **script** . Vamos não recomendamos esta abordagem como todos os carateres especiais em script dentro as necessidades de documento JSON ser retirado e pode causar problemas de depuração. A melhor prática é siga o passo #4.
5.  Crie uma tubagem com a atividade de HDInsightHive. A atividade processos/transformações os dados.

        {
          "name": "HiveActivitySamplePipeline",
          "properties": {
            "activities": [
              {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                  {
                    "name": "HiveSampleIn"
                  }
                ],
                "outputs": [
                  {
                    "name": "HiveSampleOut"
                  }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                  "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                  "scriptLinkedService": "StorageLinkedService"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
              }
            ]
          }
        }

6.  Implemente a tubagem. Consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) artigo para obter detalhes. 
7.  Monitorize pipeline de utilizar as vistas de monitorização e gestão da fábrica de dados. Consulte o artigo [monitorização e gerir dados fábrica tubagens](data-factory-monitor-manage-pipelines.md) artigo para obter detalhes. 


## <a name="specifying-parameters-for-a-hive-script"></a>Especificação de parâmetros para obter um script de ramo  
Neste exemplo, jogos são penetração diariamente para o armazenamento de Blobs do Azure e registos são armazenados numa pasta particionada com data e hora. Pretende parametrizar o script ramo e passar a localização da pasta entrada dinamicamente durante runtime e produzir também a partições com data e hora de saída.

Para utilizar parametrizado ramo de script, faça o seguinte

- Defina os parâmetros de **define**.

        {
            "name": "HiveActivitySamplePipeline",
            "properties": {
            "activities": [
                {
                    "name": "HiveActivitySample",
                    "type": "HDInsightHive",
                    "inputs": [
                        {
                            "name": "HiveSampleIn"
                          }
                    ],
                    "outputs": [
                        {
                            "name": "HiveSampleOut"
                        }
                    ],
                    "linkedServiceName": "HDInsightLinkedService",
                    "typeproperties": {
                        "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                        "scriptLinkedService": "StorageLinkedService",
                        "defines": {
                            "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)",
                            "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:%M}/dayno={0:%d}/', SliceStart)"
                        },
                        "scheduler": {
                            "frequency": "Hour",
                            "interval": 1
                        }
                    }
                }
            ]
          }
        }

- Em Hive Script, consulte o parâmetro utilizando **${hiveconf:parameterName}**. 

        DROP TABLE IF EXISTS HiveSampleIn; 
        CREATE EXTERNAL TABLE HiveSampleIn 
        (
            ProfileID   string, 
            SessionStart    string, 
            Duration    int, 
            SrcIPAddress    string, 
            GameType    string
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 
        
        DROP TABLE IF EXISTS HiveSampleOut; 
        CREATE EXTERNAL TABLE HiveSampleOut 
        (
            ProfileID   string, 
            Duration    int
        ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';
        
        INSERT OVERWRITE TABLE HiveSampleOut
        Select 
            ProfileID,
            SUM(Duration)
        FROM HiveSampleIn Group by ProfileID


## <a name="see-also"></a>Consulte também
- [Porco atividade](data-factory-pig-activity.md)
- [Atividade de MapReduce](data-factory-map-reduce.md)
- [Atividade de transmissão de Hadoop](data-factory-hadoop-streaming-activity.md)
- [Invocar programas motores](data-factory-spark.md)
- [Invocar R scripts](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/RunRScriptUsingADFSample)









