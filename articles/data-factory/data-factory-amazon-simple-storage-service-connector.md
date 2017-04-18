<properties 
    pageTitle="Mover os dados a partir da Amazon serviço de armazenamento simples utilizando dados fábrica | Microsoft Azure" 
    description="Saiba mais sobre como mover dados do serviço de armazenamento simples (S3) do Amazon utilizando Azure fábrica de dados." 
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
    ms.date="10/24/2016" 
    ms.author="jingwang"/>

# <a name="move-data-from-amazon-simple-storage-service-using-azure-data-factory"></a>Mover os dados a partir do Amazon simples serviço de armazenamento utilizando a fábrica de dados do Azure

Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover os dados a partir da Amazon simples serviço de armazenamento (S3) para dados noutro armazenar. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) que apresenta uma descrição geral de movimento de dados e uma lista de arquivos de dados de origem suportadas/sink com atividade de cópia.  

Atualmente, a fábrica de dados suporta apenas mover os dados da Amazon S3 outros arquivos de dados, mas não para mover dados a partir de outros arquivos de dados para Amazon S3.

## <a name="required-permissions"></a>Permissões necessárias

Para copiar dados de Amazon S3, certifique-se de que tenha sido concedidas abaixo permissões:

- **S3:GetObject** e **s3:GetObjectVersion** para operações de objeto do Amazon S3
- **S3:ListBucket** e **s3:ListAllMyBuckets** (utilizado no Assistente para copiar apenas) para Amazon S3 balde operações 

Pode encontrar a lista completa de Amazon S3 permisions com detalhes de [Especificar permissões numa política](http://docs.aws.amazon.com/AmazonS3/latest/dev/using-with-s3-actions.html).

## <a name="copy-data-wizard"></a>Copiar o Assistente de dados
A forma mais fácil para criar uma tubagem que copia os dados da Amazon S3 é utilizar o Assistente de dados de cópia. Consulte o artigo [Tutorial: criar uma tubagem através do Assistente de cópia](data-factory-copy-data-wizard-tutorial.md) para obter instruções sobre rápido sobre como criar uma tubagem utilizando o Assistente de dados de cópia. 

O exemplo seguinte fornece definições de JSON de exemplo que pode utilizar para criar uma tubagem utilizando o [Azure portal](data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](data-factory-copy-activity-tutorial-using-powershell.md). Mostra como copiar os dados da Amazon S3 ao armazenamento de Blobs do Azure. No entanto, os dados podem ser copiados para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores).

## <a name="sample-copy-data-from-amazon-s3-to-azure-blob"></a>Exemplo: Copiar dados de Amazon S3 para BLOBs do Azure
Este exemplo mostra como copiar dados a partir de um S3 Amazon para um armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a quaisquer a sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  
 
O exemplo tem as seguintes entidades de fábrica do mesmo de dados:

- Um serviço do tipo [AwsAccessKey](#linked-service-properties)ligado.
- Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
- Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [AmazonS3](#dataset-type-properties).
- Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
- Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [FileSystemSource](#copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia dados da Amazon S3 para um BLOBs do Azure cada hora. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos. 

**Serviço de S3 Amazon ligadas**

    {
        "name": "AmazonS3LinkedService",
        "properties": {
            "type": "AwsAccessKey",
            "typeProperties": {
                "accessKeyId": "<access key id>",
                "secretAccessKey": "<secret access key>"
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

**Conjunto de dados de entrada Amazon S3**

Definir **"externos": VERDADEIRO** informa o serviço de dados fábrica do mesmo que o conjunto de dados externa a fábrica de dados e não é produzido por uma actividade na fábrica de dados. Defina esta propriedade como verdadeiro num conjunto de dados de entrada que não é produzido por uma actividade no pipeline de.

    {
        "name": "AmazonS3InputDataset",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "AmazonS3LinkedService",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
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
                "folderPath": "mycontainer/fromamazons3/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

A tubagem contém uma atividade de cópia que está configurado para utilizar os conjuntos de dados de entrada e saídos e está agendada para executar a cada hora. No pipeline de definição de JSON, o tipo de **origem** está definido para **FileSystemSource** e **sink** tipo está definido como **BlobSink**. 
    
    {
        "name": "CopyAmazonS3ToBlob",
        "properties": {
            "description": "pipeline for copy activity",
            "activities": [
                {
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "FileSystemSource",
                            "recursive": true
                        },
                        "sink": {
                            "type": "BlobSink",
                            "writeBatchSize": 0,
                            "writeBatchTimeout": "00:00:00"
                        }
                    },
                    "inputs": [
                        {
                            "name": "AmazonS3InputDataset"
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
                    "name": "AmazonS3ToBlob"
                }
            ],
            "start": "2014-08-08T18:00:00Z",
            "end": "2014-08-08T19:00:00Z"
        }
    }



## <a name="linked-service-properties"></a>Propriedades do serviço ligadas

A tabela seguinte fornece descrição para elementos JSON específicos para Amazon S3 (**AwsAccessKey**) ligadas serviço.

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------- | ------- |  
| accessKeyID | ID da chave de acesso secreta. | cadeia | Sim |
| secretAccessKey | A tecla de acesso secreta própria. | Cadeia secreta encriptada | Sim | 


## <a name="dataset-type-properties"></a>Propriedades do tipo de conjunto de dados

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . As secções como estrutura, disponibilidade e a política são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).

A secção **typeProperties** é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties do conjunto de dados do tipo **AmazonS3** (que inclui Amazon S3 conjunto de dados) tem as seguintes propriedades

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------- | ------ | 
| bucketName | O nome de balde S3. | Cadeia | Sim |
| chave | A chave de objeto S3. | Cadeia | N | 
| prefixo | Prefixo para a chave de objeto S3. Objetos cujas chaves iniciar com este prefixo são selecionados. Aplica-se apenas quando chave estiver em branco. | Cadeia | N | 
| versão | A versão do objeto S3 se S3 controlo de versões está ativado. | Cadeia | N |  
| Formatar | São suportados os seguintes tipos de formato: **TextFormat**, **AvroFormat**, **JsonFormat**, **OrcFormat**, **ParquetFormat**. Defina a propriedade de **tipo** em Formatar para um destes valores. Consulte as secções [Especificando TextFormat](#specifying-textformat), [Especificando AvroFormat](#specifying-avroformat), [Especificando JsonFormat](#specifying-jsonformat), [Especificando OrcFormat](#specifying-orcformat)e [Especificando ParquetFormat](#specifying-parquetformat) para obter detalhes. Se pretender copiar os ficheiros como-é entre com base no ficheiro armazena (cópia binária), pode ignorar a secção formato em ambas as definições do conjunto de dados de entrada e saída.| N
| compressão | Especifique o tipo e o nível de compressão para os dados. Os tipos suportados são: **GZip**, **Deflate**e **BZip2** e níveis de suportados são: **Optimal** e **mais rápida**. Atualmente, as definições de compressão não são suportadas para dados **AvroFormat** ou **OrcFormat**. Para mais informações, consulte a secção de [suporte de compressão](#compression-support) .  | N |

> [AZURE.NOTE] bucketName + tecla de Especifica a localização do objeto S3 onde balde é o contentor de raiz para S3 objetos e chave é o caminho completo para S3 objeto.

### <a name="sample-dataset-with-prefix"></a>Conjunto de dados de exemplo com prefixo

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "prefix": "testFolder/test",
                "bucketName": "testbucket",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }

### <a name="sample-data-set-with-version"></a>Conjunto de dados de exemplo (com a versão)

    {
        "name": "dataset-s3",
        "properties": {
            "type": "AmazonS3",
            "linkedServiceName": "link- testS3",
            "typeProperties": {
                "key": "testFolder/test.orc",
                "bucketName": "testbucket",
                "version": "WBeMIxQkJczm0CJajYkHf0_k6LhBmkcL",
                "format": {
                    "type": "OrcFormat"
                }
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            },
            "external": true
        }
    }


### <a name="dynamic-paths-for-s3"></a>Caminhos dinâmicos para S3

No exemplo, utilizamos valores fixos para as propriedades de chave e bucketName no dataset Amazon S3. 

    "key": "testFolder/test.orc",
    "bucketName": "testbucket",

Pode ter dados fábrica utilizar para calcular a tecla e bucketName dinamicamente o tempo de execução variáveis do sistema como SliceStart.

    "key": "$$Text.Format('{0:MM}/{0:dd}/test.orc', SliceStart)"
    "bucketName": "$$Text.Format('{0:yyyy}', SliceStart)"

O que pode fazer a mesma para a propriedade do prefixo de um conjunto de dados Amazon S3. Consulte [funções de dados fábrica e as variáveis de sistema](data-factory-functions-variables.md) para obter uma lista de funções suportadas e variáveis. 


[AZURE.INCLUDE [data-factory-file-format](../../includes/data-factory-file-format.md)]
[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## <a name="copy-activity-type-properties"></a>Copiar as propriedades de tipo de atividade

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e políticas estão disponíveis para todos os tipos de atividades. 

Propriedades disponíveis na secção **typeProperties** da atividade de outro lado variam com cada tipo de atividade. Para actividade de cópia, eles variam dependendo dos tipos de origens e sumidouros.

Quando estiver origem numa atividade de copiar do tipo **FileSystemSource** (que inclui Amazon S3), estão disponíveis as seguintes propriedades na secção typeProperties:

| Propriedade | Descrição | Valores permitidos | Obrigatório |
| -------- | ----------- | -------------- | -------- | 
| recursivas | Especifica se a forma recursiva listar S3 objetos em diretório. | Verdadeiro/Falso | N | 

[AZURE.INCLUDE [data-factory-structure-for-rectangualr-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.

## <a name="next-steps"></a>Próximos passos
Consulte os artigos seguintes: 
- [Tutorial de atividade de cópia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obter instruções passo a passo para criar uma tubagem com uma atividade de cópia. 
