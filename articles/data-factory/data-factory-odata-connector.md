<properties 
    pageTitle="Mover os dados a partir de origens de OData | Dados Azure fábrica" 
    description="Saiba mais sobre como mover dados de origens de OData através do Azure fábrica de dados." 
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
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-a-odata-source-using-azure-data-factory"></a>Mover os dados de origem a partir de OData utilizando a fábrica de dados do Azure
Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados de uma origem de OData ao arquivo de dados de outra. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

> [AZURE.NOTE] Este suporte de conector OData copiar dados a partir ambas as nuvem OData e origens de OData no local. Últimos, terá de instalar o Data Management Gateway. Consulte o artigo [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) artigo para obter detalhes sobre o Data Management Gateway.

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de uma origem de OData é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados de uma origem de OData a um armazenamento de Blobs do Azure que sejam. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.


## <a name="sample-copy-data-from-odata-source-to-azure-blob"></a>Exemplo: Copiar dados de origem de OData para BLOBs do Azure

Este exemplo mostra como copiar dados de uma origem de OData ao armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço ligado de tipo de [OData](#odata-linked-service-properties).
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [ODataResource](#odata-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [RelationalSource](#odata-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados da consultar contra uma origem de OData para uma BLOBs do Azure cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

**OData ligadas serviço** Este exemplo utiliza a autenticação básica. Consulte a secção [OData ligadas serviço](#odata-linked-service-properties) para diferentes tipos de autenticação pode utilizar. 

    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
                "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**Conjunto de dados de entrada OData**

A definição "externos": "true" informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica dados.
    
    {
        "name": "ODataDataset",
        "properties": 
        {
            "type": "ODataResource",
            "typeProperties": 
            {
                "path": "Products" 
            },
            "linkedServiceName": "ODataLinkedService",
            "structure": [],
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {
                "retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
                "maximumRetry": 3               
            }
        }
    }

Definição de especificar o **caminho** no conjunto de dados é opcional. 


**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

    {
        "name": "AzureBlobODataDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **RelationalSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade de **consulta** seleciona os dados mais recentes (mais recentes) a partir da origem de OData.
    
    {
        "name": "CopyODataToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "RelationalSource",
                            "query": "?$select=Name, Description&$top=5",
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "ODataDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobODataDataSet"
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
                    "name": "ODataToBlob"
                }
            ],
            "start": "2016-02-01T18:00:00Z",
            "end": "2016-02-03T19:00:00Z"
        }
    }


Especificar a **consulta** na definição do pipeline é opcional. O **URL** que o serviço de dados fábrica utiliza para obter dados é: URL especificado no serviço ligado (obrigatório) + caminho especificado no conjunto de dados (opcional) + consulta no pipeline de (opcional). 

## <a name="odata-linked-service-properties"></a>OData ligadas propriedades do serviço

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de OData ligada.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo tem de estar definida: **OData** | Sim |
| URL| URL do serviço de OData. | Sim |
| authenticationType | Tipo de autenticação utilizado para ligar à origem de OData. <br/><br/> Para a nuvem OData, os valores possíveis são acesso anónimo e Basic. Para OData no local, os valores possíveis são acesso anónimo, Basic e Windows. | Sim | 
| nome de utilizador | Especifique o nome de utilizador se estiver a utilizar autenticação básica. | Sim (apenas se estiver a utilizar autenticação básica) | 
| palavra-passe | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. | Sim (apenas se estiver a utilizar autenticação básica) | 
| gatewayName | Nome do gateway que o serviço de dados fábrica devo utilizar para ligar para o serviço de OData no local. Especifique apenas se está a copiar dados de origem de OData no local. | N |

### <a name="using-basic-authentication"></a>Utilizando a autenticação básica

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### <a name="using-anonymous-authentication"></a>Utilizando a autenticação anónima
    
    {
        "name": "ODataLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }

### <a name="using-windows-authentication-accessing-on-premises-odata-source"></a>Utilizando a autenticação do Windows aceder a origem de OData no local

    {
        "name": "inputLinkedService",
        "properties": 
        {
            "type": "OData",
            "typeProperties": 
            {
               "url": "<endpoint of on-premises OData source e.g. Dynamics CRM>",
               "authenticationType": "Windows",
                "username": "domain\\user",
               "password": "password",
               "gatewayName": "mygateway"
           }
       }
    }



## <a name="odata-dataset-type-properties"></a>Propriedades do tipo de conjunto de dados OData

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo **ODataResource** (que inclui o conjunto de dados OData) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| caminho | Caminho para o recurso de OData | N | 

## <a name="odata-copy-activity-type-properties"></a>Propriedades do tipo de atividade de cópia de OData

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Quando é de origem do tipo **RelationalSource** (que inclui OData) estão disponíveis as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Exemplo | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| consulta | Utilize a consulta personalizada para ler dados. | "? $select = nome, descrição e $top = 5" | N | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-odata"></a>Mapeamento de tipo para OData

Tal como mencionado no artigo [atividades de movimento de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automático a partir de tipos de origens de receber tipos com a seguinte abordagem passo 2:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Quando mover dados a partir de dados OData armazena, tipos de dados OData são mapeados para tipos de .NET.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e Otimização do  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.

