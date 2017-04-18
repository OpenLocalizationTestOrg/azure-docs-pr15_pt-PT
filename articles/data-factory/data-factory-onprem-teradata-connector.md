<properties 
    pageTitle="Mover dados do Teradata | Dados Azure fábrica" 
    description="Saiba mais sobre Teradata Connector para o serviço de dados fábrica que permite-lhe mover os dados a partir da base de dados Teradata" 
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
    ms.date="09/20/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-teradata-using-azure-data-factory"></a>Mover dados do Teradata utilizando a fábrica de dados do Azure

Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover dados do Teradata para dados noutro armazenar. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

Suporta a fábrica de dados ligar a origens de Teradata no local através do Data Management Gateway. Consulte o artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE]
Gateway é necessário, mesmo se o Teradata está alojado numa VM de IaaS Azure. Pode instalar o gateway a VM IaaS mesmo como o arquivo de dados ou uma VM diferentes, desde que o gateway pode ligar-se à base de dados. 

Fábrica dados suporta apenas mover dados do Teradata outros arquivos de dados, não a partir de outros arquivos de dados para Teradata.

## <a name="installation"></a>Instalação 

Para o Data Management Gateway ligar à base de dados Teradata, tem de instalar o [Fornecedor de dados .NET para Teradata](http://go.microsoft.com/fwlink/?LinkId=278886) no mesmo sistema como o Data Management Gateway.

> [AZURE.NOTE] Consulte o artigo [resolver problemas de gateway problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para sugestões sobre resolução de problemas de ligação/gateway relacionados com problemas. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados do Teradata para qualquer um dos arquivos de dados suportados sink é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados a partir do Teradata ao armazenamento de Blobs do Azure que sejam. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.   

### <a name="sample-copy-data-from-teradata-to-azure-blob"></a>Exemplo: Copiar dados do Teradata para BLOBs do Azure

Este exemplo mostra como copiar dados a partir de uma base de dados Teradata a um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço do tipo [OnPremisesTeradata](data-factory-onprem-teradata-connector.md#teradata-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [RelationalTable](data-factory-onprem-teradata-connector.md#teradata-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
4.  [Em curso](data-factory-create-pipelines.md) com cópia atividade que utiliza [RelationalSource](data-factory-onprem-teradata-connector.md#teradata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados a partir de um resultado de consulta numa base de dados Teradata para um blob cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

Como primeiro passo, a configuração do data management gateway. São as instruções no artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço de Teradata ligada:**

    {
        "name": "OnPremTeradataLinkedService",
        "properties": {
            "type": "OnPremisesTeradata",
            "typeProperties": {
                "server": "<server>",
                "authenticationType": "<authentication type>",
                "username": "<username>",
                "password": "<password>",
                "gatewayName": "<gatewayName>"
            }
        }
    }

**Armazenamento de Blobs do Azure ligadas serviço:**

    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorageLinkedService",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"
            }
        }
    }


**Conjunto de dados entrado de Teradata:**

O exemplo assume ter criado uma tabela "AMinhaTabela" Teradata e que contém uma coluna denominada "carimbo" para os dados da série de tempo.

A definição "externos": VERDADEIRO informa o serviço de dados fábrica do mesmo que a tabela é a fábrica de dados externa e não é produzida por uma actividade na fábrica de dados.

    {
        "name": "TeradataDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremTeradataLinkedService",
            "typeProperties": {
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
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
        "name": "AzureBlobTeradataDataSet",
        "properties": {
            "published": false,
            "location": {
                "type": "AzureBlobLocation",
                "folderPath": "mycontainer/teradata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
                ],
                "linkedServiceName": "AzureStorageLinkedService"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }


**Gasoduto com atividade copiar:**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendado para ser executado por hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados na última hora para copiar.

    {
        "name": "CopyTeradataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "$$Text.Format('select * from MyTable where timestamp >= \\'{0:yyyy-MM-ddTHH:mm:ss}\\' AND timestamp < \\'{1:yyyy-MM-ddTHH:mm:ss}\\'', SliceStart, SliceEnd)"
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "TeradataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobTeradataDataSet"
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
                    "name": "TeradataToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z",
            "isPaused": false
        }
    }


## <a name="teradata-linked-service-properties"></a>Propriedades do serviço Teradata ligada

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de Teradata ligada. 

Propriedade | Descrição | Obrigatório
-------- | ----------- | --------
tipo | A propriedade de tipo tem de estar definida: **OnPremisesTeradata** | Sim
servidor | Nome do servidor Teradata. | Sim
authenticationType | Tipo de autenticação utilizado para ligar à base de dados Teradata. Os valores possíveis são: anónima, Basic e Windows. | Sim
nome de utilizador | Especifique o nome de utilizador se estiver a utilizar Basic ou autenticação do Windows. | N 
palavra-passe | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. | N 
gatewayName | Nome do gateway que o serviço de dados fábrica devo utilizar para ligar à base de dados Teradata no local. | Sim

Para obter detalhes sobre como definir credenciais para uma origem de dados Teradata no local, consulte [definir as credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="teradata-dataset-type-properties"></a>Propriedades do tipo de conjunto de dados Teradata

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. Atualmente, não existem propriedades tipo suportadas para o conjunto de dados Teradata. 


## <a name="teradata-copy-activity-type-properties"></a>Propriedades do tipo de atividade de cópia de Teradata

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Quando é de origem do tipo **RelationalSource** (que inclui Teradata) estão disponíveis as seguintes propriedades na secção **typeProperties** :

Propriedade | Descrição | Valores permitidos | Obrigatório
-------- | ----------- | -------------- | --------
consulta | Utilize a consulta personalizada para ler dados. | Cadeia de consulta SQL. Por exemplo: selecione * from MinhaTabela. | Sim

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-teradata"></a>Mapeamento de tipo para Teradata

Tal como mencionado no artigo [atividades de movimento de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões de tipo automático a partir de tipos de origens de receber tipos com a seguinte abordagem passo 2:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Quando mover dados para Teradata, os mapeamentos seguintes são utilizados de tipo de Teradata para tipo .NET.

Tipo de base de dados Teradata | Tipo de .NET framework
----------------- | ---------------------------
CARÁCT | Cadeia
CLOB | Cadeia
Gráfico | Cadeia
VarChar | Cadeia
VarGraphic | Cadeia
Blob | Byte]
Byte | Byte]
VarByte | Byte]
BigInt | Int64
ByteInt | Int16
Decimal | Decimal
Dupla | Dupla
Número inteiro | Int32
Número | Dupla
SmallInt | Int16
Data | Data/hora
Tempo | TimeSpan
Tempo com fuso horário | Cadeia
Data/hora | Data/hora
Carimbo de data/hora com o fuso horário | DateTimeOffset
Dia de intervalo | TimeSpan
Dia do intervalo para hora | TimeSpan
Dia do intervalo minuto | TimeSpan
Dia do intervalo segundo | TimeSpan
Hora de intervalo | TimeSpan
Hora de intervalo minuto | TimeSpan
Hora de intervalo, segundo | TimeSpan
Minuto de intervalo | TimeSpan
Intervalo minuto segundo | TimeSpan
Intervalo de segundo | TimeSpan
Ano de intervalo | Cadeia
Ano de intervalo para o mês | Cadeia
Mês de intervalo | Cadeia
Period(Date) | Cadeia
Period(Time) | Cadeia
Período (tempo com o fuso horário) | Cadeia
Period(Timestamp) | Cadeia
Período (carimbo com fuso horário) | Cadeia
XML | Cadeia

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.