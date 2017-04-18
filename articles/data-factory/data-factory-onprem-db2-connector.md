<properties 
    pageTitle="Mover os dados a partir do DB2 | Dados Azure fábrica" 
    description="Saiba mais sobre como mover os dados da base de dados DB2 utilizando a fábrica de dados do Azure" 
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
    ms.date="09/06/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-db2-using-azure-data-factory"></a>Mover dados do DB2 utilizando a fábrica de dados do Azure
Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados a partir do DB2 para dados noutro armazenar. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

Suporta a fábrica de dados ligar a origens de DB2 de no local com o Data Management Gateway. Consulte o artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE]
Utilize o gateway para ligar à DB2 mesmo se estão alojados no Azure IaaS VMs. Se está a tentar ligar a uma instância da DB2 alojado na nuvem, também pode instalar a instância do gateway na IaaS VM.

Atualmente, a fábrica de dados suporta apenas mover dados a partir do DB2 outros arquivos de dados, não a partir de outras lojas de dados para DB2. 

## <a name="installation"></a>Instalação 

O Data Management Gateway fornece um controlador DB2 incorporado que suporta o seguinte procedimento: 

- SQLAM 9 / 10 / 11
- DB2 para LUW (Linux, Unix Windows)
- DB2 para z/SO e DB2 para posso (também conhecidos por como / 400)

Por isso, já não tem de instalar manualmente os controladores ao copiar dados a partir de DB2.

> [AZURE.NOTE] Consulte o artigo [resolver problemas de gateway problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para sugestões sobre resolução de problemas de ligação/gateway relacionados com problemas. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de uma base de dados DB2 é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados da base de dados DB2 e armazenamento de Blobs do Azure que sejam. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.

## <a name="sample-copy-data-from-db2-to-azure-blob"></a>Exemplo: Copiar dados de DB2 para BLOBs do Azure

Este exemplo mostra como copiar dados a partir de uma base de dados DB2 no local para um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço do tipo [OnPremisesDb2](data-factory-onprem-db2-connector.md#db2-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado. 
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [RelationalTable](data-factory-onprem-db2-connector.md#db2-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties). 
5.  Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [RelationalSource](data-factory-onprem-db2-connector.md#db2-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties). 

O exemplo copia os dados a partir de um resultado de consulta numa base de dados DB2 para um BLOBs do Azure por hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

Como primeiro passo, instalar e configurar o Data management gateway. São as instruções no artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

**Serviço de DB2 ligadas:**

    {
        "name": "OnPremDb2LinkedService",
        "properties": {
            "type": "OnPremisesDb2",
            "typeProperties": {
                "server": "<server>",
                "database": "<database>",
                "schema": "<schema>",
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

**Conjunto de dados entrado de DB2:**

O exemplo assume ter criado uma tabela "AMinhaTabela" DB2 e que contém uma coluna denominada "carimbo" para os dados da série de tempo.

A definição "externos": VERDADEIRO informa o serviço de dados fábrica que este conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica de dados. Repare que o **tipo** está definido para **RelationalTable**. 

    {
        "name": "Db2DataSet",
        "properties": {
            "type": "RelationalTable",
            "linkedServiceName": "OnPremDb2LinkedService",
            "typeProperties": {},
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
        "name": "AzureBlobDb2DataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/db2/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

**Gasoduto com atividade copiar:**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados da tabela Encomendas.

    {
        "name": "CopyDb2ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "select * from \"Orders\""
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "inputs": [
                        {
                            "name": "Db2DataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobDb2DataSet"
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
                    "name": "Db2ToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }


## <a name="db2-linked-service-properties"></a>Propriedades do serviço DB2 ligadas

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de DB2 ligadas. 

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo tem de estar definida: **OnPremisesDB2** | Sim |
| servidor | Nome do servidor DB2. | Sim |
| base de dados | Nome da base de dados DB2. | Sim |
| esquema | Nome do esquema da base de dados. O nome do esquema é sensível às maiúsculas. | N |
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados DB2. Os valores possíveis são: anónima, Basic e Windows. | Sim |
| nome de utilizador | Especifique o nome de utilizador se estiver a utilizar Basic ou autenticação do Windows. | N |
| palavra-passe | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. | N |
| gatewayName | Nome do gateway que o serviço de dados fábrica devo utilizar para ligar à base de dados DB2 no local. | Sim |

Para obter detalhes sobre como definir credenciais para uma origem de dados DB2 no local, consulte [definir as credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) . 


## <a name="db2-dataset-type-properties"></a>Propriedades do tipo de conjunto de dados DB2

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo RelationalTable (que inclui o conjunto de dados DB2) tem as seguintes propriedades.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| Tabela | Nome da tabela na instância de base de dados DB2 referente serviço ligadas a. A tabela é sensível às maiúsculas. | N (se a **consulta** de **RelationalSource** for especificada) |

## <a name="db2-copy-activity-type-properties"></a>Propriedades do tipo de atividade de cópia de DB2

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Para copiar atividade, quando é de origem do tipo **RelationalSource** (que inclui DB2) as seguintes propriedades estão disponíveis na secção typeProperties:


| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------- | -------------- |
| consulta | Utilize a consulta personalizada para ler dados. | Cadeia de consulta SQL. Por exemplo: `"query": "select * from "MySchema"."MyTable""`. | N (se a **tabela** de **conjunto de dados** for especificada)|

> [AZURE.NOTE] Nomes das tabelas e esquema são entre maiúsculas e minúsculas. Incluir os nomes na "" (aspas faça duplo) na consulta.  

**Exemplo:**

    "query": "select * from "DB2ADMIN"."Customers""


[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

## <a name="type-mapping-for-db2"></a>Mapeamento de tipo para DB2
Tal como mencionado no artigo [atividades de movimento de dados](data-factory-data-movement-activities.md) , a atividade de cópia executa conversões de tipo automático a partir de tipos de origens de receber tipos com a seguinte abordagem passo 2:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Quando mover dados para DB2, os mapeamentos seguintes são utilizados de tipo de DB2 para tipo .NET.

Tipo de base de dados DB2 | Tipo de .NET framework 
----------------- | ------------------- 
SmallInt | Int16
Número inteiro | Int32
BigInt | Int64
Real | Única
Dupla | Dupla
Flutuante | Dupla
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Data | Data/hora
Tempo | TimeSpan
Data/hora | Data/hora
XML | Byte]
CARÁCT | Cadeia
VarChar | Cadeia
LongVarChar | Cadeia
DB2DynArray | Cadeia
Binário | Byte]
VarBinary | Byte]
LongVarBinary | Byte]
Gráfico | Cadeia
VarGraphic | Cadeia
LongVarGraphic | Cadeia
CLOB | Cadeia
Blob | Byte]
DbClob | Cadeia
SmallInt | Int16
Número inteiro | Int32
BigInt | Int64
Real | Única
Dupla | Dupla
Flutuante | Dupla
Decimal | Decimal
DecimalFloat | Decimal
Numérico | Decimal
Data | Data/hora
Tempo | TimeSpan
Data/hora | Data/hora
XML | Byte]
CARÁCT | Cadeia


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]


[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.


