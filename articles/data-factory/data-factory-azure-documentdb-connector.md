<properties 
    pageTitle="Mover os dados para/de DocumentDB | Microsoft Azure" 
    description="Saiba como mover os dados para/da colecção de Azure DocumentDB utilizando a fábrica de dados do Azure" 
    services="data-factory, documentdb" 
    documentationCenter="" 
    authors="linda33wj" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="multiple" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/26/2016" 
    ms.author="jingwang"/>

# <a name="move-data-to-and-from-documentdb-using-azure-data-factory"></a>Mover os dados de e para a DocumentDB utilizando a fábrica de dados do Azure

Este artigo descreve como pode utilizar a atividade de cópia numa fábrica dados Azure para mover dados para Azure DocumentDB a partir de outros dados de arquivo e mover dados do DocumentDB para outro arquivo de dados. Este artigo constrói no artigo do [atividades de movimento de dados](data-factory-data-movement-activities.md) , que apresenta uma descrição geral de movimento de dados com cópia atividade e combinações de arquivo de dados suportadas.

Os exemplos seguintes mostram como copiar dados para e a partir do Azure DocumentDB e armazenamento de Blobs do Azure. No entanto, podem ser copiada **diretamente** a partir de qualquer uma das origens para qualquer um da sumidouros indicado [aqui](data-factory-data-movement-activities.md#supported-data-stores) utilizando a atividade de cópia no Azure dados fábrica dados.  

> [AZURE.NOTE] Copiar dados a partir de dados de IaaS no-local/Azure armazena para Azure DocumentDB e vice-versa são suportados com o Data Management Gateway versão 2.1 e acima.

## <a name="sample-copy-data-from-documentdb-to-azure-blob"></a>Exemplo: Copiar dados de DocumentDB para BLOBs do Azure

Mostra o exemplo abaixo:

1. Um serviço do tipo [DocumentDb](#azure-documentdb-linked-service-properties)ligado.
2. Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado. 
3. Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [DocumentDbCollectionSource](#azure-documentdb-copy-activity-type-properties) e [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties).

O exemplo copia os dados no Azure DocumentDB para BLOBs do Azure. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos.

**Azure DocumentDB ligadas serviço:**

    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Armazenamento de Blobs do Azure ligadas serviço:**

    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Conjunto de dados de entrada documento DB Azure:**

O exemplo assume que tem uma coleção de denominada **pessoa** numa base de dados Azure DocumentDB.
 
A definição "externos": "true" e especificando externalData política informações o serviço de fábrica do Azure dados que a tabela é a fábrica de dados externa e não são produzidos por uma actividade na fábrica de dados.

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }


**Conjunto de dados de saída de Blobs do Azure:**

Dados são copiados para um novo blob cada hora com o caminho para o blob refletir a data/hora específica com granularidade de hora.

    {
      "name": "PersonBlobTableOut",
      "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

Documento JSON de exemplo na coleção de pessoa numa base de dados DocumentDB: 

    {
      "PersonId": 2,
      "Name": {
        "First": "Jane",
        "Middle": "",
        "Last": "Doe"
      }
    }

DocumentDB suporta ao consultar documentos utilizando o SQL como sintaxe através de documentos JSON hierárquicos. 

Exemplo: SELECIONE Person.Name.Middle Person.PersonId, NomePróprio como Person.Name.First, como MiddleName, como Person.Name.Last apelido da pessoa

A seguinte tubagem copia os dados da coleção de pessoa na base de dados DocumentDB para um BLOBs do Azure. Como parte da atividade copiar a entrada e saída conjuntos de dados foram especificados.  
    
    {
      "name": "DocDbToBlobPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "DocumentDbCollectionSource",
                "query": "SELECT Person.Id, Person.Name.First AS FirstName, Person.Name.Middle as MiddleName, Person.Name.Last AS LastName FROM Person",
                "nestingSeparator": "."
              },
              "sink": {
                "type": "BlobSink",
                "blobWriterAddHeader": true,
                "writeBatchSize": 1000,
                "writeBatchTimeout": "00:00:59"
              }
            },
            "inputs": [
              {
                "name": "PersonDocumentDbTable"
              }
            ],
            "outputs": [
              {
                "name": "PersonBlobTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromDocDbToBlob"
          }
        ],
        "start": "2015-04-01T00:00:00Z",
        "end": "2015-04-02T00:00:00Z"
      }
    }

## <a name="sample-copy-data-from-azure-blob-to-azure-documentdb"></a>Exemplo: Copiar dados de Blobs do Azure para Azure DocumentDB

Mostra o exemplo abaixo:

1. Um serviço do tipo [DocumentDb](#azure-documentdb-linked-service-properties)ligado.
2. Um serviço do tipo [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)ligado.
3. Um [conjunto de dados](data-factory-create-datasets.md) entrada do tipo [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties).
4. Um resultado [conjunto de dados](data-factory-create-datasets.md) do tipo [DocumentDbCollection](#azure-documentdb-dataset-type-properties). 
4. Uma [tubagem](data-factory-create-pipelines.md) com cópia atividade que utiliza [BlobSource](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) e [DocumentDbCollectionSink](#azure-documentdb-copy-activity-type-properties).


O exemplo copia os dados a partir do Azure blob para Azure DocumentDB. As propriedades JSON utilizadas nestas amostras são descritas nas secções seguir os exemplos.

**Armazenamento de Blobs do Azure ligadas serviço:**
    
    {
      "name": "StorageLinkedService",
      "properties": {
        "type": "AzureStorage",
        "typeProperties": {
          "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
      }
    }

**Azure DocumentDB ligadas serviço:**
    
    {
      "name": "DocumentDbLinkedService",
      "properties": {
        "type": "DocumentDb",
        "typeProperties": {
          "connectionString": "AccountEndpoint=<EndpointUrl>;AccountKey=<AccessKey>;Database=<Database>"
        }
      }
    }

**Conjunto de dados de entrada Blob Azure:**

    {
      "name": "PersonBlobTableIn",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "FirstName",
            "type": "String"
          },
          {
            "name": "MiddleName",
            "type": "String"
          },
          {
            "name": "LastName",
            "type": "String"
          }
        ],
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
          "fileName": "input.csv",
          "folderPath": "docdb",
          "format": {
            "type": "TextFormat",
            "columnDelimiter": ",",
            "nullValue": "NULL"
          }
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

**Conjunto de dados de saída Azure DocumentDB:**

O exemplo copia dados para uma coleção de denominada "Pessoa".

    {
      "name": "PersonDocumentDbTableOut",
      "properties": {
        "structure": [
          {
            "name": "Id",
            "type": "Int"
          },
          {
            "name": "Name.First",
            "type": "String"
          },
          {
            "name": "Name.Middle",
            "type": "String"
          },
          {
            "name": "Name.Last",
            "type": "String"
          }
        ],
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

A seguinte tubagem copia dados BLOBs do Azure para a coleção de pessoa a DocumentDB. Como parte da atividade copiar a entrada e saída conjuntos de dados foram especificados. 
    
    {
      "name": "BlobToDocDbPipeline",
      "properties": {
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
              "source": {
                "type": "BlobSource"
              },
              "sink": {
                "type": "DocumentDbCollectionSink",
                "nestingSeparator": ".",
                "writeBatchSize": 2,
                "writeBatchTimeout": "00:00:00"
              }
              "translator": {
                  "type": "TabularTranslator",
                  "ColumnMappings": "FirstName: Name.First, MiddleName: Name.Middle, LastName: Name.Last, BusinessEntityID: BusinessEntityID, PersonType: PersonType, NameStyle: NameStyle, Title: Title, Suffix: Suffix, EmailPromotion: EmailPromotion, rowguid: rowguid, ModifiedDate: ModifiedDate"
              }
            },
            "inputs": [
              {
                "name": "PersonBlobTableIn"
              }
            ],
            "outputs": [
              {
                "name": "PersonDocumentDbTableOut"
              }
            ],
            "policy": {
              "concurrency": 1
            },
            "name": "CopyFromBlobToDocDb"
          }
        ],
        "start": "2015-04-14T00:00:00Z",
        "end": "2015-04-15T00:00:00Z"
      }
    }
 
Se a entrada de Blobs do exemplo for como 

    1,John,,Doe

Em seguida, o resultado JSON no DocumentDB será como:

    {
      "Id": 1,
      "Name": {
        "First": "John",
        "Middle": null,
        "Last": "Doe"
      },
      "id": "a5e8595c-62ec-4554-a118-3940f4ff70b6"
    }
    
DocumentDB é um arquivo de NoSQL documentos JSON, onde são permitidas estruturas aninhadas. Azure fábrica de dados permite que o utilizador indicam hierarquia através do **nestingSeparator**, que é "." Neste exemplo. Com o separador, a atividade de cópia irá gerar o objeto "Nome" com elementos de três subordinados em primeiro lugar, nome e os apelidos, de acordo com "Name.First", "Name.Middle" e "Name.Last" a definição da tabela.

## <a name="azure-documentdb-linked-service-properties"></a>Propriedades de serviço ligadas DocumentDB Azure

A tabela seguinte fornece uma descrição para elementos JSON específicas do serviço de DocumentDB Azure ligadas. 

| **Propriedade** | **Descrição** | **Obrigatório** |
| -------- | ----------- | --------- |
| tipo | A propriedade de tipo tem de estar definida: **DocumentDb** | Sim |
| connectionString | Especifique as informações necessárias para ligar à base de dados do Azure DocumentDB. | Sim |

## <a name="azure-documentdb-dataset-type-properties"></a>Propriedades do tipo de Azure DocumentDB conjunto de dados

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de conjuntos de dados, consulte o artigo [criar conjuntos de dados](data-factory-create-datasets.md) . Secções como estrutura, disponibilidade e a política de um conjunto de dados JSON são semelhantes para todos os tipos de conjunto de dados (Azure SQL, BLOBs do Azure, tabela do Azure, etc.).
 
A secção typeProperties é diferente para cada tipo de conjunto de dados e fornece informações sobre a localização dos dados no arquivo de dados. A secção de typeProperties para o conjunto de dados do tipo **DocumentDbCollection** tem as seguintes propriedades.

| **Propriedade** | **Descrição** | **Obrigatório** |
| -------- | ----------- | -------- |
| NomeColecção | Nome da colecção de documento DocumentDB. | Sim |


Exemplo:

    {
      "name": "PersonDocumentDbTable",
      "properties": {
        "type": "DocumentDbCollection",
        "linkedServiceName": "DocumentDbLinkedService",
        "typeProperties": {
          "collectionName": "Person"
        },
        "external": true,
        "availability": {
          "frequency": "Day",
          "interval": 1
        }
      }
    }

### <a name="schema-by-data-factory"></a>Esquema pela fábrica de dados
Esquema de royalties dados lojas como DocumentDB, o serviço de dados fábrica infere o esquema de uma das seguintes formas:  

1.  Se especificar a estrutura dos dados utilizando a propriedade de **estrutura** na definição do conjunto de dados, o serviço de dados fábrica respeita esta estrutura como o esquema. Neste caso, se uma linha não contiver um valor de uma coluna, fornecido um valor nulo para a mesma.
2.  Se não especificar a estrutura dos dados utilizando a propriedade de **estrutura** na definição do conjunto de dados, o serviço de dados fábrica infere o esquema, utilizando a primeira linha dos dados. Neste caso, se a primeira linha não contiver o esquema completo, algumas colunas irão estar em falta no resultado da operação de cópia.

Por conseguinte, esquema de royalties origens de dados, a melhor prática é especificar a estrutura dos dados utilizando a propriedade de **estrutura** .

## <a name="azure-documentdb-copy-activity-type-properties"></a>Propriedades do tipo de atividade de cópia DocumentDB Azure

Para obter uma lista completa das secções e propriedades que estão disponíveis para a definição de atividades, consulte o artigo [Criar tubagens](data-factory-create-pipelines.md) . Propriedades, tais como o nome, descrição, de entrada e saída tabelas e política estão disponíveis para todos os tipos de atividades.
 
**Nota:** A atividade de cópia assume apenas uma entrada e produz apenas uma saída.

Propriedades disponíveis na secção typeProperties da atividade de outro lado variam com cada tipo de atividade e em caso de atividade de cópia variam consoante os tipos de origens e sumidouros.

Em caso de atividade de cópia quando é de origem do tipo **DocumentDbCollectionSource** as seguintes propriedades estão disponíveis na secção **typeProperties** :

| **Propriedade** | **Descrição** | **Valores permitidos** | **Obrigatório** |
| ------------ | --------------- | ------------------ | ------------ |
| consulta | Especifique a ler os dados da consulta. | Cadeia suportada pelo DocumentDB de consulta. <br/><br/>Exemplo:`SELECT c.BusinessEntityID, c.PersonType, c.NameStyle, c.Title, c.Name.First AS FirstName, c.Name.Last AS LastName, c.Suffix, c.EmailPromotion FROM c WHERE c.ModifiedDate > \"2009-01-01T00:00:00\"` | N <br/><br/>Se não for especificado, a instrução SQL que é executada:`select <columns defined in structure> from mycollection` 
| nestingSeparator | Caráter especial para indicar que o documento estiver aninhado | Qualquer caráter. <br/><br/>DocumentDB é um arquivo de NoSQL documentos JSON, onde são permitidas estruturas aninhadas. Azure fábrica de dados permite que o utilizador indicam hierarquia através do nestingSeparator, que é "." nos exemplos anteriores. Com o separador, a atividade de cópia irá gerar o objeto "Nome" com elementos de três subordinados em primeiro lugar, nome e os apelidos, de acordo com "Name.First", "Name.Middle" e "Name.Last" a definição da tabela. | N

**DocumentDbCollectionSink** suporta as seguintes propriedades:

| **Propriedade** | **Descrição** | **Valores permitidos** | **Obrigatório** |
| -------- | ----------- | -------------- | -------- |
| nestingSeparator | É necessário um caráter especial no nome da coluna de origem para indicar esse documento aninhado. <br/><br/>Por exemplo acima: `Name.First` no resultado da tabela produz a seguinte estrutura JSON no documento DocumentDB:<br/><br/>"Nome": {<br/>  "Primeiro": "João"<br/>}, | Caráter que é utilizado para separar os níveis de aninhamento.<br/><br/>Valor predefinido é `.` (ponto). | Caráter que é utilizado para separar os níveis de aninhamento. <br/><br/>Valor predefinido é `.` (ponto). | N | 
| writeBatchSize | Número de paralelos pedidos de serviço DocumentDB para criar documentos.<br/><br/>Pode ajustar o desempenho ao copiar dados para a DocumentDB utilizando esta propriedade. Que pode esperar um melhor desempenho quando aumentar writeBatchSize, porque mais pedidos paralelos para DocumentDB são enviados. No entanto terá de evitar a limitação que pode gerar a mensagem de erro: "Request taxa for grande".<br/><br/>Limitação for decidido por um número de factores, incluindo o tamanho dos documentos, número de termos em documentos, indexação política da coleção de destino, etc. Para operações de cópia, pode utilizar uma coleção de melhor (por exemplo, S3) para ter mais débito disponível (2500 pedido unidades/segundo). | Número inteiro | Não (predefinido: 10000) |
| writeBatchTimeout | Aguarde hora para a operação de concluir antes de tempo limite. | TimeSpan<br/><br/> Exemplo: "00: 30:00" (30 minutos). | N |
 
## <a name="appendix"></a>Anexo
1. **Pergunta:**  
    é que a atualização de suporte de atividade de cópia de registos existentes?

    **Resposta:**  
    não.

2. **Pergunta:**  
    como é que uma repetição de uma cópia para DocumentDB negócio com já copiado registos?

    **Resposta:**  
    se registos têm um campo de "ID" e a operação de cópia tenta inserir um registo com o mesmo ID, a operação de cópia emitir um erro.  
 
3. **Pergunta:** 
    dados fábrica suporta [intervalo ou dados baseadas em hash a partições](https://azure.microsoft.com/documentation/articles/documentdb-partition-data/)? 

    **Resposta:** 
    não. 
4. **Pergunta:** 
    pode posso especificar mais de uma coleção de DocumentDB para uma tabela?
    
    **Resposta:** 
    não. Colecção de apenas uma pode ser especificada neste momento.
     
## <a name="performance-and-tuning"></a>Desempenho e optimização  
Consulte o artigo [desempenho de atividade de cópia e Otimização do guia](data-factory-copy-activity-performance.md) para saber mais sobre os fatores chaves a que o desempenho impacto de movimento de dados (copiar actividade) no Azure fábrica de dados e várias formas para otimizar o-lo.
