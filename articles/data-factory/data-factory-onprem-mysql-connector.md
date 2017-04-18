<properties 
    pageTitle="Mover dados do MySQL | Dados Azure fábrica" 
    description="Saiba mais sobre como mover dados de base de dados do MySQL utilizando Azure fábrica de dados." 
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

# <a name="move-data-from-mysql-using-azure-data-factory"></a>Mover dados do MySQL utilizando a fábrica de dados do Azure

Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover dados do MySQL para dados noutro armazenar. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

Serviço de dados de fábrica do mesmo suporta ligar a origens de MySQL de no local com o Data Management Gateway. Consulte o artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para saber mais sobre o Data Management Gateway e instruções passo a passo sobre como configurar o gateway. 

> [AZURE.NOTE] Gateway é necessário, mesmo se a base de dados do MySQL está alojado numa máquina de virtual Azure IaaS (VM). Pode instalar o gateway a mesma VM como o arquivo de dados ou uma VM diferentes, desde que o gateway pode ligar-se à base de dados.  

Atualmente, a fábrica de dados suporta apenas mover os dados do MySQL ao noutros arquivos de dados, mas não para mover dados a partir de outros arquivos de dados para MySQL.

## <a name="installation"></a>Instalação 
Data Management Gateway ligar à base de dados MySQL, terá de instalar o [Conector do MySQL/líquido 6.6.5 para o Microsoft Windows](http://go.microsoft.com/fwlink/?LinkId=278885) no mesmo sistema, como o Data Management Gateway.

> [AZURE.NOTE] Consulte o artigo [resolver problemas de gateway problemas](data-factory-data-management-gateway.md#troubleshoot-gateway-issues) para sugestões sobre resolução de problemas de ligação/gateway relacionados com problemas. 

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados de uma base de dados do MySQL para qualquer um dos arquivos de dados suportados sink é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar uma tubagem. Para instruções completas com instruções passo a passo, consulte o artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) . Dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure fábrica de dados.   

## <a name="sample-copy-data-from-mysql-to-azure-blob"></a>Exemplo: Copiar dados do MySQL para BLOBs do Azure
Este exemplo mostra como copiar dados a partir de uma base de dados do MySQL no local para um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  

> [AZURE.IMPORTANT] Este exemplo fornece fragmentos JSON. Não inclui instruções passo a passo para criar a fábrica de dados. Consulte o artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) para obter instruções passo a passo. 
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

1.  Um serviço do tipo [OnPremisesMySql](data-factory-onprem-mysql-connector.md#mysql-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [RelationalTable](data-factory-onprem-mysql-connector.md#mysql-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [RelationalSource](data-factory-onprem-mysql-connector.md#mysql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados a partir de um resultado de consulta numa base de dados do MySQL para um blob por hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

Como primeiro passo, a configuração do data management gateway. São as instruções no artigo [mover dados entre localizações no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) . 

**Serviço do MySQL ligada**

    {
      "name": "OnPremMySqlLinkedService",
      "properties": {
        "type": "OnPremisesMySql",
        "typeProperties": {
          "server": "<server name>",
          "database": "<database name>",
          "schema": "<schema name>",
          "authenticationType": "<authentication type>",
          "userName": "<user name>",
          "password": "<password>",
          "gatewayName": "<gateway>"
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

**Conjunto de dados de entrada MySQL**

O exemplo assume ter criado uma tabela "AMinhaTabela" MySQL e que contém uma coluna denominada "timestampcolumn" para os dados da série de tempo.

A definição "externos": "true" informa o serviço de dados fábrica do mesmo que a tabela é a fábrica de dados externa e não é produzida por uma actividade na fábrica de dados.
    
    {
        "name": "MySqlDataSet",
        "properties": {
            "published": false,
            "type": "RelationalTable",
            "linkedServiceName": "OnPremMySqlLinkedService",
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



**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início.

    {
        "name": "AzureBlobMySqlDataSet",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "folderPath": "mycontainer/mysql/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
        "name": "CopyMySqlToBlob",
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
                            "name": "MySqlDataSet"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "AzureBlobMySqlDataSet"
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
                    "name": "MySqlToBlob"
                }
            ],
            "start": "2014-06-01T18:00:00Z",
            "end": "2014-06-01T19:00:00Z"
        }
    }



## <a name="mysql-linked-service-properties"></a>Propriedades do serviço MySQL ligada

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço do MySQL ligada.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- | 
| tipo | A propriedade de tipo tem de estar definida: **OnPremisesMySql** | Sim |
| servidor | Nome do servidor do MySQL. | Sim |
| base de dados | Nome da base de dados MySQL. | Sim | 
| esquema  | Nome do esquema da base de dados. | N | 
| authenticationType | Tipo de autenticação utilizado para ligar à base de dados do MySQL. Os valores possíveis são: anónima, Basic e Windows. | Sim | 
| nome de utilizador | Especifique o nome de utilizador se estiver a utilizar Basic ou autenticação do Windows. | N | 
| palavra-passe | Especifique a palavra-passe da conta de utilizador que especificou para o nome de utilizador. | N | 
| gatewayName | Nome do gateway que o serviço de dados fábrica devo utilizar para ligar à base de dados do MySQL no local. | Sim |

Para obter detalhes sobre como definir credenciais para uma origem de dados do MySQL no local, consulte [definir as credenciais e segurança](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security) .

## <a name="mysql-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados do MySQL

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo **RelationalTable** (que inclui o conjunto de dados do MySQL) tem as seguintes propriedades

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| Tabela | Nome da tabela na instância de dados do MySQL referente serviço ligadas a. | N (se a **consulta** de **RelationalSource** for especificada) | 

## <a name="mysql-copy-activity-type-properties"></a>Propriedades do tipo de atividade do MySQL cópia

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, tabelas de entrada e saídas, são políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção **typeProperties** da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Quando é de origem numa atividade de copiar do tipo **RelationalSource** (que inclui MySQL) estão disponíveis as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| consulta | Utilize a consulta personalizada para ler dados. | Cadeia de consulta SQL. Por exemplo: selecione * from MinhaTabela. | N (se a **tabela** de **conjunto de dados** for especificada) | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-mysql"></a>Mapeamento de tipo do MySQL

Tal como mencionado no artigo [atividades de movimento de dados](data-factory-data-movement-activities.md) , atividade de cópia executa conversões de tipo automático a partir de tipos de origens de receber tipos com a abordagem de dois passos seguintes:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Quando mover dados para MySQL, os mapeamentos seguintes são utilizados a partir de tipos de MySQL para tipos de .NET.

| Tipo de dados do MySQL | Tipo de .NET framework |
| ------------------- | ------------------- | 
| caráter sem bigint | Decimal |
| bigint | Int64 |
| bit | Decimal |
| blob | Byte] |
| Booleano |  Booleano | 
| CARÁCT | Cadeia |
| data | Data/hora |
| data/hora | Data/hora |
| decimal | Decimal |
| precisão dupla | Dupla |
| dupla | Dupla |
| enumeração | Cadeia |
| flutuante | Única |
| Int não assinado | Int64 |
| Int | Int32 |
| número inteiro não assinado | Int64 |
| número inteiro | Int32 | 
| varbinary longo | Byte] |
| varchar longo | Cadeia |
| longblob | Byte] |
| LONGTEXT | Cadeia | 
| mediumblob |  Byte] | 
| caráter sem mediumint | Int64 |
| mediumint | Int32 | 
| mediumtext | Cadeia |
| numéricos | Decimal |
| real |  Dupla |
| definir | Cadeia |
| caráter sem smallint | Int32 |
| smallint | Int16 |
| texto | Cadeia |
| tempo | TimeSpan |
| data/hora | Data/hora |
| tinyblob | Byte] |
| caráter sem tinyint |  Int16 | 
| tinyint | Int16 |
| tinytext | Cadeia | 
| varchar | Cadeia | 
| ano | Int | 


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.

