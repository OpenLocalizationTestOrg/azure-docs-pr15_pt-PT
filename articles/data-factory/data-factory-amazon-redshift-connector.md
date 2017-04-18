<properties 
    pageTitle="Mover os dados a partir do Redshift Amazon utilizando dados fábrica | Microsoft Azure" 
    description="Saiba mais sobre como mover dados de Redshift Amazon utilizando Azure fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-redshift-using-azure-data-factory"></a>Mover os dados a partir do Redshift de Amazon utilizando a fábrica de dados do Azure

Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados a partir da Amazon Redshift para dados noutro armazenar. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e uma lista de arquivos de dados de origem/sink.  

Atualmente, a fábrica de dados suporta apenas mover os dados da Amazon Redshift outros arquivos de dados, mas não para mover dados a partir de outras lojas de dados para Amazon Redshift.

## <a name="prerequisites"></a>Pré-requisitos

- Se está a mover dados a um arquivo de dados no local, conceda o acesso ao cluster Amazon Redshift data Management Gateway (endereço IP da utilização do computador). Consulte [autorizar acesso ao cluster](http://docs.aws.amazon.com/redshift/latest/gsg/rs-gsg-authorize-cluster-access.html) para obter instruções. 
- Se está a mover dados a um arquivo de dados Azure, consulte o artigo [Intervalos de IP do Azure dados Centro](https://www.microsoft.com/download/details.aspx?id=41653) para calcular endereço intervalos IP (incluindo SQL intervalos) utilizados pelos centros de dados do Microsoft Azure. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de Amazon Redshift é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar os dados da Amazon Redshift ao armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-redshift-to-azure-blob"></a>Exemplo: Copiar dados de Amazon Redshift para BLOBs do Azure
Este exemplo mostra como copiar dados a partir de uma base de dados Amazon Redshift para um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

- Um serviço do tipo [AmazonRedshift](#linked-service-properties)ligado.
- Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
- Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [RelationalTable](#dataset-type-properties).
- Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [RelationalSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados a partir de um resultado de consulta no Amazon Redshift para um blob cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

**Serviço de Redshift Amazon ligadas**

    {
        "name": "AmazonRedshiftLinkedService",
        "properties":
        {
            "type": "AmazonRedshift",
            "typeProperties":
            {
                "server": "< The IP address or host name of the Amazon Redshift server >",
                "port": <The number of the TCP port that the Amazon Redshift server uses to listen for client connections.>,
                "database": "<The database name of the Amazon Redshift database>",
                "username": "<username>",
                "password": "<password>"
            }
        }
    }


**Serviço de armazenamento ligado do Azure**

    {
      "name": "AzureStorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada Amazon Redshift**

Definir **"externos": VERDADEIRO** informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica de dados. Defina esta propriedade como verdadeiro num conjunto de dados de entrada que não é produzido por uma actividade no pipeline de.

    {
        "name": "AmazonRedshiftInputDataset",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "AmazonRedshiftLinkedService",
            "typeProperties": {
                "tableName": "<Table name>"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

    {
        "name": "AzureBlobOutputDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/fromamazonredshift/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
                "format": {
                    "type": "TextFormat",
                    "rowDelimiter": "\n",
                    "columnDelimiter": "\t"
                },
                "partitionedBy": [
                    {
                        "name": "Year",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "yyyy"
                        }
                    },
                    {
                        "name": "Month",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "MM"
                        }
                    },
                    {
                        "name": "Day",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "dd"
                        }
                    },
                    {
                        "name": "Hour",
                        "value": {
                            "type": "DateTime",
                            "date": "SliceStart",
                            "format": "HH"
                        }
                    }
                ]
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }



**Gasoduto com atividade de cópia**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.
    
    {
        "name": "CopyAmazonRedshiftToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', WindowStart, WindowEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonRedshiftInputDataset"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutputDataSet"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "AmazonRedshiftToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriedades do serviço ligadas

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de Redshift Amazon ligadas.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo tem de estar definida: **AmazonRedshift**. | Sim |
| servidor | Endereço IP ou anfitrião nome do servidor Amazon Redshift. | Sim |
| porta | O número da porta TCP que o servidor Amazon Redshift utiliza para ouvir para ligações de cliente. | Valor predefinido não,: 5439 |
| base de dados | Nome da base de dados Amazon Redshift. | Sim |
| nome de utilizador | Nome do utilizador que tenha acesso à base de dados.| Sim |
| palavra-passe | Palavra-passe da conta de utilizador.| Sim |


## <a name="dataset-type-properties"></a>Propriedades do tipo de conjunto de dados

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo **RelationalTable** (que inclui Amazon Redshift conjunto de dados) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| Tabela | Nome da tabela na base de dados Amazon Redshift referente serviço ligadas a. | N (se a **consulta** de **RelationalSource** for especificada) | 

## <a name="copy-activity-type-properties"></a>Copiar as propriedades de tipo de atividade

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção **typeProperties** da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Quando é de origem da atividade de copiar do tipo **RelationalSource** (que inclui Amazon Redshift) estão disponíveis as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| consulta | Utilize a consulta personalizada para ler dados. | Cadeia de consulta SQL. Por exemplo: selecione * from MinhaTabela. | N (se a **tabela** de **conjunto de dados** for especificada) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-amazon-redshift"></a>Mapeamento de tipo para Amazon Redshift

Tal como mencionado no artigo [atividades de movimento de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automático a partir de tipos de origens de receber tipos com a abordagem de dois passos seguintes:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Quando mover dados para Amazon Redshift, os mapeamentos seguintes serão utilizados a partir de tipos de Amazon Redshift para tipos de .NET.

Tipo de Redshift Amazon | .NET com base no tipo
-------------------- | ---------------
SMALLINT | Int16
NÚMERO INTEIRO | Int32
BIGINT | Int64
DECIMAL | Decimal
REAL | Única
PRECISÃO DUPLA | Dupla
BOOLEANO | Cadeia
CARÁCT | Cadeia
VARCHAR | Cadeia
DATA | Data/hora
DATA/HORA | Data/hora
TEXTO | Cadeia



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.

## <a name="next-steps"></a>Próximos passos
Consulte os artigos seguintes: 
- [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar uma tubagem com uma atividade de cópia. 