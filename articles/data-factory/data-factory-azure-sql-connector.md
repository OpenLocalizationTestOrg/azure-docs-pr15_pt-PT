<properties 
    pageTitle="Mover os dados para/da base de dados do SQL Azure | Microsoft Azure" 
    description="Saiba como mover os dados para a base de dados do SQL Azure com o Azure fábrica de dados." 
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

# <a name="move-data-to-and-from-azure-sql-database-using-azure-data-factory"></a>Mover os dados de e para a base de dados do SQL Azure utilizando a fábrica de dados do Azure
Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados para a base de dados do SQL Azure ou para outro arquivo de dados. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas. 

## <a name="supported-sources-and-sinks"></a>Origens suportadas e sumidouros
Consulte a tabela de [arquivos de dados suportados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter uma lista de arquivos de dados suportados como origens ou sumidouros pela atividade de cópia. Pode mover dados a partir de qualquer arquivo de dados de origem suportados para a base de dados do SQL Azure ou de base de dados do SQL Azure para qualquer arquivo de dados suportados sink.

## <a name="create-pipeline"></a>Criar em curso
Pode criar uma tubagem com uma atividade de cópia move dados para a uma base de dados do Azure SQL utilizando ferramentas/APIs diferentes.  

- Copiar o Assistente
- Portal do Azure
- Visual Studio
- Azure PowerShell
- .NET API
- REST API

Consulte o artigo [tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar uma tubagem com uma atividade de copiar formas diferentes.   

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia dados para a base de dados do SQL Azure é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

Os exemplos seguintes fornecem definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Como copiar dados de base de dados do SQL Azure e armazenamento de Blobs do Azure e para que sejam. No entanto, podem ser copiada **diretamente** a partir de qualquer uma das origens para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.

## <a name="sample-copy-data-from-azure-sql-database-to-azure-blob"></a>Exemplo: Copiar dados de base de dados do SQL Azure para BLOBs do Azure

O mesmo define as seguintes entidades fábrica de dados:

1. Um serviço do tipo [AzureSqlDatabase](#azure-sql-linked-service-properties)ligado.
2. Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado. 
3. Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [AzureSqlTable](#azure-sql-dataset-type-properties). 
4. Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [SqlSource](#azure-sql-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados séries de tempo (hora a hora, diária, etc.) a partir de uma tabela numa base de dados Azure SQL para um blob cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos.  

**Serviço de ligadas SQL Azure**

    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

Consulte a secção de [Serviço ligadas do SQL Azure](#azure-sql-linked-service-properties) para a lista de propriedades suportadas por este serviço ligado. 

**Serviço de armazenamento ligado Blob Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Consulte o artigo de [BLOBs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) para a lista de propriedades suportadas por este serviço ligado. 

**Conjunto de dados de entrada SQL Azure**

O exemplo assume que criou uma tabela "AMinhaTabela" no Azure SQL e contém uma coluna denominada "timestampcolumn" para os dados da série de tempo. 

A definição "externos": "true" informa o serviço de fábrica do Azure dados que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica de dados.

    {
      "name": "AzureSqlInput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
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

Consulte a secção [Propriedades do tipo de conjunto de dados Azure SQL](#azure-sql-dataset-type-properties) para a lista de propriedades suportado por este tipo de conjunto de dados.  

**Conjunto de dados de saída de Blobs do Azure**

Dados escritos para um novo blob cada hora (frequência: hora, o intervalo: 1). O caminho da pasta para o blob dinamicamente é avaliado com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês, dia e partes de horas da hora de início. 

    {
      "name": "AzureBlobOutput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

Consulte a secção [Propriedades do tipo de Blobs do Azure conjunto de dados](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para a lista de propriedades suportado por este tipo de conjunto de dados.  

**Gasoduto com atividade de cópia**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **SqlSource** e **sink** tipo está definido como **BlobSink**. A consulta SQL especificada para a propriedade **SqlReaderQuery** seleciona os dados na última hora para copiar.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline for copy activity",
        "activities":[  
          {
            "name": "AzureSQLtoBlob",
            "description": "copy activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureSQLInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureBlobOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "SqlSource",
                "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

No exemplo, **sqlReaderQuery** está especificado para o SqlSource. A atividade de cópia é executado esta consulta contra a origem da base de dados do Azure SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado ao especificar a **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado leva-o até parâmetros).

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, das colunas definidas na secção estrutura do conjunto de dados JSON são utilizadas para construir uma consulta para executar contra a base de dados do SQL Azure. Por exemplo: `select column1, column2 from mytable`. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela. 


Consulte a secção [Origem Sql](#sqlsource) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para a lista de propriedades suportados pelo SqlSource e BlobSink. 


## <a name="sample-copy-data-from-azure-blob-to-azure-sql-database"></a>Exemplo: Copiar dados de Blobs do Azure para base de dados do SQL Azure

O exemplo define as seguintes entidades fábrica de dados:  

1.  Um serviço do tipo [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)ligado.
2.  Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3.  Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4.  Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties).
4.  Uma [tubagem](data-factory-create-pipelines.md) com atividade de cópia que utiliza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties).

As cópias de exemplo séries de tempo (hora a hora, diária, etc.) de dados a partir do Azure blob a uma tabela no Azure SQL da base de dados cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 


**Serviço de ligadas SQL Azure**
    
    {
      "name": "AzureSqlLinkedService",
      "properties": {
        "type": "AzureSqlDatabase",
        "typeProperties": {
          "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
        }
      }
    }

Consulte a secção de [Serviço ligadas do SQL Azure](#azure-sql-linked-service-properties) para a lista de propriedades suportadas por este serviço ligado. 

**Serviço de armazenamento ligado Blob Azure**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

Consulte o artigo de [BLOBs do Azure](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties) para a lista de propriedades suportadas por este serviço ligado.

**Conjunto de dados de entrada Blob Azure**

Dados são selecionados para cima a partir de um novo blob cada hora (frequência: hora, o intervalo: 1). O nome de ficheiro e caminho de pasta para o blob dinamicamente são avaliadas com base na hora de início no setor que está a ser processada. O caminho da pasta utiliza ano, mês e parte do dia da hora de início e nome de ficheiro utiliza a parte da hora da hora de início. "externos": "true" definição informa o serviço de dados fábrica que esta tabela é a fábrica de dados externa e não é produzida por uma actividade na fábrica de dados.

    {
      "name": "AzureBlobInput",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
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

Consulte a secção [Propriedades do tipo de Blobs do Azure conjunto de dados](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para a lista de propriedades suportado por este tipo de conjunto de dados.

**Conjunto de dados de saída Azure SQL**

O exemplo copia os dados a uma tabela com o nome "MinhaTabela" no Azure SQL. Crie a tabela no SQL Azure com o mesmo número de colunas como esperado o ficheiro CSV de BLOBs conter. Novas linhas são adicionadas à tabela de cada hora. 

    {
      "name": "AzureSqlOutput",
      "properties": {
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
          "tableName": "MyOutputTable"
        },
        "availability": {
          "frequency": "Hour",
          "interval": 1
        }
      }
    }

Consulte a secção [Propriedades do tipo de conjunto de dados Azure SQL](#azure-sql-dataset-type-properties) para a lista de propriedades suportado por este tipo de conjunto de dados.

**Gasoduto com atividade de cópia**

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **BlobSource** e **sink** tipo está definido como **SqlSink**.

    {  
        "name":"SamplePipeline",
        "properties":{  
        "start":"2014-06-01T18:00:00",
        "end":"2014-06-01T19:00:00",
        "description":"pipeline with copy activity",
        "activities":[  
          {
            "name": "AzureBlobtoSQL",
            "description": "Copy Activity",
            "type": "Copy",
            "inputs": [
              {
                "name": "AzureBlobInput"
              }
            ],
            "outputs": [
              {
                "name": "AzureSqlOutput"
              }
            ],
            "typeProperties": {
              "source": {
                "type": "BlobSource",
                "blobColumnSeparators": ","
              },
              "sink": {
                "type": "SqlSink"
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

Consulte a secção [Sql Sink](#sqlsink) e [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) para a lista de propriedades suportados pelo SqlSink e BlobSource. 


## <a name="azure-sql-linked-service-properties"></a>Propriedades do serviço ligadas SQL Azure
Nas amostras, que tiver utilizado um serviço do tipo **AzureSqlDatabase** ligado a ligar uma base de dados do Azure SQL uma fábrica de dados. A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço do SQL Azure ligadas.

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| tipo | A propriedade de tipo tem de estar definida: **AzureSqlDatabase** | Sim |
| connectionString | Especifique as informações necessárias para ligar para a instância de base de dados do Azure SQL para a propriedade connectionString. | Sim |

> [AZURE.NOTE] Configure a [Firewall de base de dados do SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure) o servidor de base de dados para [Permitir que os serviços de Azure aceder ao servidor](https://msdn.microsoft.com/library/azure/ee621782.aspx#ConnectingFromAzure). Para além disso, se estiver a copiar dados para a base de dados do SQL Azure a partir do incluindo Azure fora de origens de dados no local com o gateway fábrica de dados, configure o intervalo de endereços IP adequado para o computador que está a enviar dados para a base de dados do SQL Azure. 

## <a name="azure-sql-dataset-type-properties"></a>Propriedades de tipo de conjunto de dados SQL Azure
Nas amostras, que tiver utilizado um conjunto de dados do tipo **AzureSqlTable** para representar uma tabela numa base de dados Azure SQL. 

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.). 

A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de **typeProperties** para o conjunto de dados do tipo **AzureSqlTable** tem as seguintes propriedades:

| Propriedade | Descrição | Obrigatório |
| -------- | ----------- | -------- |
| Tabela | Nome da tabela na instância de base de dados do Azure SQL que se refira serviço ligadas a. | Sim |

## <a name="azure-sql-copy-activity-type-properties"></a>Propriedades do tipo de atividade de copiar do Azure SQL
Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades.

> [AZURE.NOTE] A atividade de cópia assume apenas uma entrada e produz apenas uma saída.

Propriedades disponíveis na secção **typeProperties** da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros. 

Se está a mover dados a partir de uma base de dados do Azure SQL, defina o tipo de origem na atividade de cópia para **SqlSource**. Da mesma forma, se estiver a mover dados para uma base de dados do Azure SQL, defina o tipo de sink na atividade de cópia para **SqlSink**. Esta secção fornece uma lista de propriedades suportados pelo SqlSource e SqlSink. 

### <a name="sqlsource"></a>SqlSource

Numa atividade de cópia, quando a origem for tipo **SqlSource**, as seguintes propriedades estão disponíveis na secção **typeProperties** :

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| sqlReaderQuery | Utilize a consulta personalizada para ler dados. | Cadeia de consulta SQL. Exemplo: `select * from MyTable`.  | N |
| sqlReaderStoredProcedureName | Nome do procedimento armazenado que lê dados a partir da tabela de origem. | Nome do procedimento armazenado. | N |
| storedProcedureParameters | Parâmetros para o procedimento armazenado. | Valor/nome pares. Os nomes e invólucro dos parâmetros tem de corresponder os nomes e as maiúsculas e minúsculas dos parâmetros de procedimento armazenado. | N |

Se o **sqlReaderQuery** for especificado para o SqlSource, a atividade de cópia é executado esta consulta contra a origem da base de dados do Azure SQL para obter os dados. Em alternativa, pode especificar um procedimento armazenado ao especificar a **sqlReaderStoredProcedureName** e **storedProcedureParameters** (se o procedimento armazenado leva-o até parâmetros). 

Se não especificar sqlReaderQuery ou sqlReaderStoredProcedureName, das colunas definidas na secção estrutura do conjunto de dados JSON são utilizadas para construir uma consulta (`select column1, column2 from mytable`) para executar contra a base de dados do SQL Azure. Se a definição do conjunto de dados não tiver a estrutura, todas as colunas são selecionadas da tabela. 

> [AZURE.NOTE] Quando utiliza **sqlReaderStoredProcedureName**, ainda precisa de especificar um valor para a propriedade da **tabela** no conjunto de dados JSON. Não existem sem validações executadas apesar contra a tabela seguinte. 

### <a name="sqlsource-example"></a>Exemplo de SqlSource

    "source": {
        "type": "SqlSource",
        "sqlReaderStoredProcedureName": "CopyTestSrcStoredProcedureWithParameters",
        "storedProcedureParameters": {
            "stringData": { "value": "str3" },
            "id": { "value": "$$Text.Format('{0:yyyy}', SliceStart)", "type": "Int"}
        }
    }

**A definição de procedimento armazenado:** 

    CREATE PROCEDURE CopyTestSrcStoredProcedureWithParameters
    (
        @stringData varchar(20),
        @id int
    )
    AS
    SET NOCOUNT ON;
    BEGIN
         select *
         from dbo.UnitTestSrcTable
         where dbo.UnitTestSrcTable.stringData != stringData
        and dbo.UnitTestSrcTable.id != id
    END
    GO


### <a name="sqlsink"></a>SqlSink 

**SqlSink** suporta as seguintes propriedades:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- |
| writeBatchTimeout | Aguarde hora para a operação de inserir batch de concluir antes de tempo limite. | TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). | N | 
| writeBatchSize | Insere os dados na tabela do SQL quando o tamanho da memória intermédia atingir writeBatchSize. | Número inteiro (número de linhas)| Não (predefinido: 10000)
| sqlWriterCleanupScript | Especificar uma consulta para copiar atividade executar assim que são desorganizados; dados de um setor específico. Para obter mais informações, consulte a [secção repetibilidade](#repeatability-during-copy). | Uma instrução de consulta.  | N |
| sliceIdentifierColumnName | Especifique um nome de coluna para copiar atividade preencher com identificador de setor automática gerada, que é utilizado para limpar os dados de um setor específico quando voltar a executar. Para obter mais informações, consulte a [secção repetibilidade](#repeatability-during-copy). | Nome da coluna de uma coluna com o tipo de dados do binary(32). | N |
| sqlWriterStoredProcedureName | Nome do procedimento armazenado esses dados upserts (atualizações/insere) para a tabela de destino. | Nome do procedimento armazenado. | N |
| storedProcedureParameters | Parâmetros para o procedimento armazenado. | Valor/nome pares. Os nomes e invólucro dos parâmetros tem de corresponder os nomes e as maiúsculas e minúsculas dos parâmetros de procedimento armazenado. | N | 
| sqlWriterTableType | Especifique um nome de tipo de tabela para ser utilizado no procedimento armazenado. Atividade de cópia torna os dados a ser movidos disponíveis numa tabela temp com este tipo de tabela. Código de procedimento armazenado, em seguida, pode intercalar os dados a ser copiados com os dados existentes. | Um nome de tipo de tabela. | N |

#### <a name="sqlsink-example"></a>Exemplo de SqlSink

    "sink": {
        "type": "SqlSink",
        "writeBatchSize": 1000000,
        "writeBatchTimeout": "00:05:00",
        "sqlWriterStoredProcedureName": "CopyTestStoredProcedureWithParameters",
        "sqlWriterTableType": "CopyTestTableType",
        "storedProcedureParameters": {
            "id": { "value": "1", "type": "Int" },
            "stringData": { "value": "str1" },
            "decimalData": { "value": "1", "type": "Decimal" }
        }
    }

## <a name="identity-columns-in-the-target-database"></a>Colunas de identidade da base de dados de destino
Esta secção fornece um exemplo para copiar dados a partir de uma tabela de origem sem uma coluna de identidade a uma tabela de destino com uma coluna de identidade. 

**Tabela de origem:** 

    create table dbo.SourceTbl
    (
           name varchar(100),
           age int
    )

**Tabela de destino:**

    create table dbo.TargetTbl
    (
           id int identity(1,1),
           name varchar(100),
           age int
    )


Repare que a tabela de destino tem uma coluna de identidade. 

**Definição de JSON do conjunto de dados de origem**

    {
        "name": "SampleSource",
        "properties": {
            "type": " SqlServerTable",
            "linkedServiceName": "TestIdentitySQL",
            "typeProperties": {
                "tableName": "SourceTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true,
            "policy": {}
        }
    }

**Definição de JSON do conjunto de dados de destino**

    {
        "name": "SampleTarget",
        "properties": {
            "structure": [
                { "name": "name" },
                { "name": "age" }
            ],
            "type": "AzureSqlTable",
            "linkedServiceName": "TestIdentitySQLSource",
            "typeProperties": {
                "tableName": "TargetTbl"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": false,
            "policy": {}
        }   
    }


Repare que, como a sua tabela de origem e destino ter diferentes esquema (destino tem uma coluna adicional com identidade). Neste cenário, tem de especificar a propriedade de **estrutura** na definição de conjunto de dados de destino, não incluir a coluna de identidade. 

Em seguida, mapear colunas a partir do conjunto de dados de origem para colunas o conjunto de dados de destino. Consulte a secção [amostras de mapeamento de colunas](#column-mapping-samples) para obter um exemplo. 

[AZURE.INCLUDE [data-factory-type-repeatability-for-sql-sources](../../includes/data-factory-type-repeatability-for-sql-sources.md)] 

[AZURE.INCLUDE [data-factory-sql-invoke-stored-procedure](../../includes/data-factory-sql-invoke-stored-procedure.md)]
 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### <a name="type-mapping-for-sql-server--azure-sql-database"></a>Mapeamento de tipo para SQL Server e base de dados do SQL Azure

Tal como mencionado na atividade de cópia de artigo de [atividades de movimento dados](data-factory-data-movement-activities.md) executa conversões de tipo automático a partir de tipos de origens de receber tipos com a seguinte abordagem passo 2:

1. Converter de tipos de origens nativo para o tipo de .NET
2. Converter a partir do tipo de .NET tipo sink nativo

Quando mover dados para & a partir do SQL Azure, SQL server, Sybase são utilizados os mapeamentos seguintes a partir do tipo de SQL tipo .NET e vice versa.

O mapeamento é o mesmo que o mapeamento de tipo de dados do SQL Server para ADO.NET.

| Tipo de motor de base de dados do SQL Server | Tipo de .NET framework |
| ------------------------------- | ------------------- |
| bigint | Int64 |
| em binário | Byte] |
| bit | Booleano |
| CARÁCT | Cadeia, Char [ |
| data | Data/hora |
| Data/hora | Data/hora |
| datetime2 | Data/hora |
| Datetimeoffset | DateTimeOffset |
| Decimal | Decimal |
| Atributo FILESTREAM (varbinary(max)) | Byte] |
| Flutuante | Dupla |
| imagem | Byte] | 
| Int | Int32 | 
| dinheiro | Decimal |
| nchar | Cadeia, Char [ |
| ntext | Cadeia, Char [ |
| numéricos | Decimal |
| nvarchar | Cadeia, Char [ |
| real | Única |
| ROWVERSION | Byte] |
| smalldatetime | Data/hora |
| smallint | Int16 |
| smallmoney | Decimal | 
| sql_variant | Objeto * |
| texto | Cadeia, Char [ |
| tempo | TimeSpan |
| data/hora | Byte] |
| tinyint | Byte |
| uniqueidentifier | GUID |
| varbinary |  Byte] |
| varchar | Cadeia, Char [ |
| XML | XML |


[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

## <a name="performance-and-tuning"></a>Desempenho e Otimização do  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.