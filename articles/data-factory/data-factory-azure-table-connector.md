<properties 
    pageTitle="Mover os dados para/da tabela do Azure | Microsoft Azure" 
    description="Saiba como mover os dados para a armazenamento de tabela do Azure através do Azure fábrica de dados." 
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
    ms.date="09/13/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-azure-table-using-azure-data-factory"></a>Mover os dados de tabela do Azure utilizando a fábrica de dados do Azure e para

Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados para a tabela do Azure ou para outro arquivo de dados. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral das movimento de dados e as combinações de arquivo de dados suportadas com atividade de cópia.

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia dados para a armazenamento de tabela do Azure é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados de armazenamento de tabela do Azure e base de dados do Azure Blob e para que sejam. No entanto, podem ser copiada **diretamente** a partir de qualquer uma das origens para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.

## <a name="sample-copy-data-from-azure-table-to-azure-blob"></a>Exemplo: Copiar dados de tabela do Azure para BLOBs do Azure

O exemplo seguinte mostra:

1.  Um serviço ligado tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilizado para tabela e blob).
2.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [Azure](#azure-table-dataset-type-properties).
3.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
3.  [Em curso](data-factory-create-pipelines.md) com atividade de cópia que utiliza [AzureTableSource](#azure-table-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

O exemplo copia os dados que pertencem à partição predefinida numa tabela do Azure para um blob a cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos.

**Serviço de armazenamento Azure ligado:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure dados fábrica suporta dois tipos de serviços de armazenamento do Windows Azure ligadas: **AzureStorage** e **AzureStorageSas**. Para a primeira parte, pode especificar a cadeia de ligação que inclui a chave de conta e para o mais tarde, especifique o Uri de assinatura partilhadas do Access (SA). Consulte a secção [Serviços ligados](#linked-services) para obter detalhes.  

**Conjunto de dados de entrada tabela Azure:**

O exemplo assume que tenha criado uma tabela "AMinhaTabela" na tabela do Azure.
 
A definição "externos": "true" informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica dados.

    {
      "name": "AzureTableInput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyTable"
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Conjunto de dados de saída de Blobs do Azure:**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": "\t",
            "rowDelimiter": "\n"
          }
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Gasoduto com a atividade de cópia:**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **AzureTableSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada com **AzureTableSourceQuery** propriedade seleciona os dados a partir do partição predefinida cada hora para copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
            "start":"2014-06-01T18:00:00",
            "end":"2014-06-01T19:00:00",
            "description":"pipeline for copy activity",
            "activities":[  
                {
                    "name": "AzureTabletoBlob",
                    "description": "copy activity",
                    "type": "Copy",
                    "inputs": [
                        {
                            "name": "AzureTableInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobOutput"
                        }
                    ],
                    "typeProperties": {
                        "source": {
                            "type": "AzureTableSource",
                            "AzureTableSourceQuery": "PartitionKey eq 'DefaultPartitionKey'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },              
                    "policy": {
                        "concurrency": 1,
                        "executionPriorityOrder": "OldestFirst",
                        "retry": 0,
                        "timeout": "01:00:00"
                    }
                }
             ]  
        }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-table"></a>Exemplo: Copiar dados de Blobs do Azure para tabela do Azure

O exemplo seguinte mostra:

1.  Um serviço ligado tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) (utilizado para tabela e blob)
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [Azure](#azure-table-dataset-type-properties). 
4.  [Em curso](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [AzureTableSink](#azure-table-copy-activity-type-properties). 


As cópias de exemplo séries de tempo de dados a partir de um Azure blob para um Azure hora a hora da tabela. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos.

**Serviço de armazenamento do Azure (para tabela do Azure e Blob) ligado:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Azure dados fábrica suporta dois tipos de serviços de armazenamento do Windows Azure ligadas: **AzureStorage** e **AzureStorageSas**. Para a primeira parte, pode especificar a cadeia de ligação que inclui a chave de conta e para o mais tarde, especifique o Uri de assinatura partilhadas do Access (SA). Consulte a secção [Serviços ligados](#linked-services) para obter detalhes. 

**Conjunto de dados de entrada Blob Azure:**

Dados são selecionados para cima a partir de um novo blob cada hora (frequência: hora, o intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliadas com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte da hora da hora de início. "externos": "true" definição informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica de dados.
    
    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
          "fileName": "{Hour}.csv",
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
          ],
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "rowDelimiter": "\n"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "externalData": {
            "retryInterval": "00:01:00",
            "retryTimeout": "00:10:00",
            "maximumRetry": 3
          }
        }
      }
    }

**Tabela do Azure saída conjunto de dados:**

O exemplo copia os dados a uma tabela com o nome "MinhaTabela" numa tabela do Azure. Crie uma tabela do Azure com o mesmo número de colunas como esperado o ficheiro CSV de BLOBs conter. Novas linhas são adicionadas à tabela de cada hora. 

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

**Gasoduto com a atividade de cópia:**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **BlobSource** e **sink** tipo está definido como **AzureTableSink**. 


    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoTable",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureTableOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "AzureTableSink",
                "writeBatchSize": 100,
                "writeBatchTimeout": "01:00:00"
              }
            },
            "scheduler": {
              "frequency": "Hour",
              "interval": 1
            },                      
            "policy": {
              "concurrency": 1,
              "executionPriorityOrder": "OldestFirst",
              "retry": 0,
              "timeout": "01:00:00"
            }
          }
          ]
       }
    }

## <a name="linked-services"></a>Serviços ligados
Existem dois tipos de serviços ligados que pode utilizar para ligar um armazenamento de Blobs do Azure a uma fábrica dados Azure. São: **AzureStorage** ligadas serviço **AzureStorageSas** ligado e o serviço. O serviço de armazenamento do Windows Azure ligadas fornece a fábrica de dados com o global access para o armazenamento do Windows Azure. Considerando que, o Azure armazenamento SA (partilhados assinatura do Access) ligadas serviço fornece a fábrica de dados com acesso restrito/tempo-vínculo para o armazenamento do Windows Azure. Não existem outros diferenças entre estes dois serviços ligados. Selecione o serviço ligado que se adequa às suas necessidades. As secções seguintes fornecem mais detalhes sobre estes dois serviços ligados.

[AZURE.INCLUDE [data-factory-azure-storage-linked-services](../../includes/data-factory-azure-storage-linked-services.md)]

## <a name="azure-table-dataset-type-properties"></a>Azure propriedades do tipo de conjunto de dados de tabela

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de **typeProperties** para o conjunto de dados do tipo **Azure** tem as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| Tabela | Nome da tabela na instância de base de dados do Azure tabela que se refira serviço ligadas a. | Sim. Quando uma tabela não é especificada sem uma azureTableSourceQuery, todos os registos a partir da tabela são copiados para o destino. Se também é especificado um azureTableSourceQuery, os registos a partir da tabela que satisfaz a consulta são copiados para o destino. |

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Esquema de royalties dados lojas como tabela do Azure, o serviço de dados fábrica infere o esquema de uma das seguintes formas:

1.  Se especificar a estrutura dos dados utilizando a propriedade de **estrutura** na definição do conjunto de dados, o serviço de dados fábrica respeita esta estrutura como o esquema. Neste caso, se uma linha não contiver um valor de uma coluna, um valor nulo é fornecido para a mesma.
2. Se não especificar a estrutura dos dados utilizando a propriedade de **estrutura** na definição do conjunto de dados, dados fábrica infere o esquema, utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo, algumas colunas são perdidas no resultado da operação de cópia.

Por conseguinte, esquema de royalties origens de dados, a melhor prática é especificar a estrutura dos dados utilizando a propriedade de **estrutura** .

## <a name="azure-table-copy-activity-type-properties"></a>Azure propriedades do tipo de atividade de cópia de tabela

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída conjuntos de dados e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

**AzureTableSource** suporta as seguintes propriedades, na secção typeProperties:

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | -------- 
azureTableSourceQuery | Utilize a consulta personalizada para ler dados. | Cadeia de consulta de tabela do Azure. Consulte os exemplos na secção seguinte. | Não. Quando uma tabela não é especificada sem uma azureTableSourceQuery, todos os registos a partir da tabela são copiados para o destino. Se também é especificado um azureTableSourceQuery, os registos a partir da tabela que satisfaz a consulta são copiados para o destino.
azureTableSourceIgnoreTableNotFound | Indicam se não existir swallow a exceção da tabela. | VERDADEIRO<br/>FALSO | N |

### <a name="azuretablesourcequery-examples"></a>Exemplos de azureTableSourceQuery

Se a coluna da tabela do Azure for do tipo de cadeia: 

    azureTableSourceQuery": "$$Text.Format('PartitionKey ge \\'{0:yyyyMMddHH00_0000}\\' and PartitionKey le \\'{0:yyyyMMddHH00_9999}\\'', SliceStart)"

Se a coluna da tabela do Azure for do tipo de data/hora: 

    "azureTableSourceQuery": "$$Text.Format('DeploymentEndTime gt datetime\\'{0:yyyy-MM-ddTHH:mm:ssZ}\\' and DeploymentEndTime le datetime\\'{1:yyyy-MM-ddTHH:mm:ssZ}\\'', SliceStart, SliceEnd)"


**AzureTableSink** suporta as seguintes propriedades, na secção typeProperties:


Propriedade | Descrição | Valores permitidos | Obrigatório  
-------- | ----------- | -------------- | -------- 
azureTableDefaultPartitionKeyValue | Predefinição partição valor de chave que pode ser utilizada por truncagem. | Um valor de cadeia. | N 
azureTablePartitionKeyName | Especifique o nome da coluna cujos valores são utilizados como chaves partição. Se não for especificado, AzureTableDefaultPartitionKeyValue é utilizado como chave partição. | Um nome de coluna. | N |
azureTableRowKeyName | Especifique o nome da coluna cujos valores de coluna são utilizados como chave de linha. Se não for especificado, utilize um GUID para cada linha. | Um nome de coluna. | N  
azureTableInsertType | O modo para inserir dados na tabela do Azure.<br/><br/>Esta propriedade controla se linhas existentes na tabela de resultados com correspondentes teclas partição e linha têm os respetivos valores substituídos ou intercalados. <br/><br/>Para saber mais sobre como funcionam estas definições (em série e substituir), consulte o artigo tópicos de [Inserir ou entidade de impressão em série](https://msdn.microsoft.com/library/azure/hh452241.aspx) e [Inserir ou substituir entidade](https://msdn.microsoft.com/library/azure/hh452242.aspx) . <br/><br> Esta definição aplica-se ao nível da linha, não o nível de tabela e nenhuma opção elimina as linhas na tabela de resultados que não existam na entrada de dados. | impressão em série (predefinição)<br/>substituir | N 
writeBatchSize | Insere os dados na tabela Azure quando o writeBatchSize ou writeBatchTimeout é atingido. | Número inteiro (número de linhas)| Não (predefinido: 10000) 
writeBatchTimeout | Insere os dados da tabela Azure quando o writeBatchSize ou writeBatchTimeout é visitas | TimeSpan<br/><br/>Exemplo: "00: 20:00" (20 minutos) | Não (a predefinição para o limite de tempo do armazenamento cliente predefinido valor 90 sec)

### <a name="azuretablepartitionkeyname"></a>azureTablePartitionKeyName
Mapear uma coluna de origem para uma coluna de destino utilizando o conversor propriedade JSON antes de poder utilizar a coluna de destino como o azureTablePartitionKeyName.

No seguinte exemplo, a coluna de origem DivisionID é mapeada para a coluna de destino: DivisionID.  

    "translator": {
        "type": "TabularTranslator",
        "columnMappings": "DivisionID: DivisionID, FirstName: FirstName, LastName: LastName"
    } 

O DivisionID é especificado como chave partição. 

    "sink": {
        "type": "AzureTableSink",
        "azureTablePartitionKeyName": "DivisionID",
        "writeBatchSize": 100,
        "writeBatchTimeout": "01:00:00"
    }


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-azure-table"></a>Mapeamento de tipo de tabela do Azure

Tal como mencionado no artigo [atividades de movimento de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automático a partir de tipos de origens de receber tipos com a abordagem de dois passos seguintes.

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

As seguintes [mapeamentos definido pelo serviço de tabela do Azure](https://msdn.microsoft.com/library/azure/dd179338.aspx) são utilizadas quando mover dados para & a partir da tabela do Azure, a partir de tipos de OData de tabela do Azure tipo .NET e vice versa. 

| Tipo de dados OData | Tipo de .NET | Detalhes |
| --------------- | --------- | ------- |
| Edm.Binary | byte] | Uma matriz de bytes até 64 KB. |
| Edm.Boolean | Booleano | Um valor booleano. |
| Edm.DateTime | Data/hora | Um valor de 64 bits, expresso como a hora Universal Coordenada (UTC). O intervalo de data/hora suportado começa a partir de 12:00 à meia-noite, 1 de Janeiro de 1601 D.C. (D.C.), UTC. O intervalo termina em 31 de Dezembro de 9999. |
| Edm.Double | dupla | Um valor de vírgula flutuante de 64 bits. |
| Edm.Guid | GUID | Um identificador exclusivo global 128-bit. |
| Edm.Int32 | Int32 ou int | Um número inteiro de 32 bits. |
| Edm.Int64 | Int64 ou longo | Um número inteiro de 64 bits. |
| Edm.String | Cadeia | Um valor codificado em UTF-16. Valores de cadeia podem ser até 64 KB. |

### <a name="type-conversion-sample"></a>Exemplo de conversão de tipo

O exemplo seguinte é para copiar dados a partir de um BLOBs do Azure a tabela do Azure com conversões de tipo. 

Suponha que o conjunto de dados de Blobs está no formato CSV e contém três colunas. Uma delas é uma coluna de data/hora com um formato de data/hora personalizados, utilizando nomes francês abreviados para o dia da semana. 

Defina o conjunto de dados de origem Blob da seguinte forma juntamente com definições de tipos de para as colunas.
    
    {
        "name": " AzureBlobInput",
        "properties":
        {
             "structure": 
              [
                    { "name": "userid", "type": "Int64"},
                    { "name": "name", "type": "String"},
                    { "name": "lastlogindate", "type": "Datetime", "culture": "fr-fr", "format": "ddd-MM-YYYY"}
              ],
            "type": "AzureBlob",
            "linkedServiceName": "StorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/myfolder",
                "fileName":"myfile.csv",
                "format":
                {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "external": true,
            "availability":
            {
                "frequency": "Hour",
                "interval": 1,
            },
            "policy": {
                "externalData": {
                    "retryInterval": "00:01:00",
                    "retryTimeout": "00:10:00",
                    "maximumRetry": 3
                }
            }
        }
    }

Dada o mapeamento de tipo de tipo de Azure tabela OData ao tipo .NET, iria definir a tabela na tabela do Azure com o esquema que se segue. 

**Esquema de tabela do Azure:**

Nome da coluna | Tipo
----------- | --------
ID de utilizador | Edm.Int64
nome | Edm.String 
lastlogindate | Edm.DateTime

Em seguida, defina o conjunto de dados de tabela do Azure da seguinte forma. Não tem de especificar secção "estrutura" com o tipo de informações de uma vez que o tipo de informações já está especificado no arquivo de dados subjacentes.

    {
      "name": "AzureTableOutput",
      "properties": {
        "type": "AzureTable",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Neste caso, fábrica dados automaticamente conversões de tipos de incluindo no campo de data/hora com o formato de data/hora personalizados utilizando a cultura "US f" ao mover dados do Blob para tabela do Azure.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Para saber mais sobre os fatores chaves que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo, consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md).







