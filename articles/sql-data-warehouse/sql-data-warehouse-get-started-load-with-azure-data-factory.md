<properties
   pageTitle="Carregar os dados com o Azure dados fábrica | Microsoft Azure"
   description="Saiba como carregar os dados com a fábrica de dados do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="twounder"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/13/2016"
   ms.author="mausher;barbkess"/>

# <a name="load-data-with-azure-data-factory"></a>Carregar os dados com dados Azure fábrica 

> [AZURE.SELECTOR]
- [Redgate](sql-data-warehouse-load-with-redgate.md)  
- [Fábrica de dados](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
- [BCP](sql-data-warehouse-load-with-bcp.md)  

Este tutorial mostra-lhe como criar uma tubagem no Azure dados fábrica para mover dados a partir do Azure armazenamento Blob para armazém de dados do SQL Azure. Com os seguintes passos irá:

+ Configure os dados de exemplo num Blob de armazenamento do Azure.
+ Ligar recursos a fábrica de dados do Azure.
+ Crie uma tubagem para mover dados a partir do armazenamento de Blobs para armazém de dados SQL.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## <a name="before-you-begin"></a>Antes de começar

Para se familiarizar com Azure fábrica de dados, consulte [Introdução ao Azure fábrica de dados][].

### <a name="create-or-identify-resources"></a>Criar ou identificar recursos

Antes de iniciar este tutorial, tem de ter os seguintes recursos:

   + **Azure armazenamento Blob**: Este tutorial utiliza Azure armazenamento Blob como a origem de dados para pipeline de fábrica do Azure dados e, por isso, tem de ter um disponíveis para armazenar os dados de exemplo. Se ainda não tiver uma, saiba como [criar uma conta de armazenamento][].

   + **Armazém de dados SQL**: Este tutorial move os dados a partir do Azure armazenamento Blob para armazém de dados SQL e por isso, precisa de ter um armazém de dados online que é carregado com os dados de exemplo AdventureWorksDW. Se ainda não tiver um armazém de dados, saiba como [pode aprovisionar um][Create a SQL Data Warehouse]. Se tiver um armazém de dados, mas não o aprovisionar com os dados de exemplo, é possível [carregá-lo manualmente][Load sample data into SQL Data Warehouse].

   + **Azure dados fábrica**: Azure dados fábrica conclui a carga real e por isso, tem de ter uma que pode utilizar para criar pipeline de movimento de dados. Se ainda não tiver uma, saiba como criar uma no passo 1 do [artigo Introdução ao Azure fábrica de dados (dados fábrica Editor)][].

   + **AZCopy**: É necessário AZCopy para copiar os dados de exemplo a partir do seu cliente local para o seu Blob de armazenamento do Azure. Para obter instruções de instalação, consulte a [documentação de AZCopy][].

## <a name="step-1-copy-sample-data-to-azure-storage-blob"></a>Passo 1: Copiar dados de exemplo para Azure armazenamento Blob

Assim que tiver todas as peças prontas, está pronto para copiar dados de exemplo para o seu Blob de armazenamento do Azure.

1. [Transferir dados de exemplo][]. Estes dados adiciona outra três anos de dados de vendas aos seus dados de exemplo AdventureWorksDW.

2. Utilize este comando AZCopy para copiar os três anos de dados para o seu Blob de armazenamento do Azure.

    ````
    AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
    ````


## <a name="step-2-connect-resources-to-azure-data-factory"></a>Passo 2: Ligar recursos a fábrica de dados do Azure

Agora que os dados estão num local podemos criar pipeline de Azure dados fábrica para mover os dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL.

Para começar a utilizar, abra o [portal do Azure][] e selecione a fábrica de dados a partir do menu à esquerda.

### <a name="step-21-create-linked-service"></a>Passo 2.1: Criar serviço ligadas

Ligar a sua conta de armazenamento Azure e armazém de dados SQL para a fábrica de dados.  

1. Em primeiro lugar, começar o processo de registo ao clicar em secção de «Serviços ligados» da sua fábrica de dados e, em seguida, clique em 'novos dados arquivo.' Selecione um nome para registar o armazenamento azure em, selecione armazenamento do Windows Azure ao escrever e, em seguida, introduza o seu nome de conta e chave da conta.

2. Para registar armazém de dados SQL navegue para a secção 'Autor e implementar', selecione 'Arquivo de dados novo' e 'Armazém de dados do SQL Azure'. Copie e cole neste modelo e, em seguida, preencha as informações específicas.

    ```JSON
    {
        "name": "<Linked Service Name>",
        "properties": {
            "description": "",
            "type": "AzureSqlDW",
            "typeProperties": {
                 "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
             }
        }
    }
    ```

### <a name="step-22-define-the-dataset"></a>Passo 2.2: Definir o conjunto de dados

Depois de criar os serviços ligados, temos de definir os conjuntos de dados.  Aqui Isto significa que define a estrutura dos dados que está a ser movidos a partir do seu armazenamento para o seu armazém de dados.  Pode ler mais sobre a criação de

1. Inicie este processo ao navegar para a secção 'Autor e implementar' a fábrica de dados.

2. Clique em 'Novo conjunto de dados' e 'Armazenamento de Blobs do Azure' para ligar o armazenamento à sua fábrica de dados.  Pode utilizar a abaixo script para definir os seus dados na armazenamento de Blobs do Azure:

    ```JSON
    {
        "name": "<Dataset Name>",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "<linked storage name>",
            "typeProperties": {
                "folderPath": "<containter name>",
                "fileName": "FactInternetSales.csv",
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
    ```

3. Agora, vamos definir os nossos conjunto de dados para o armazém de dados SQL. Vamos começar da mesma forma, ao clicar em 'Novo conjunto de dados' e, em seguida, 'Armazém de dados do SQL Azure'.

    ```JSON
    {
        "name": "DWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": "AzureSqlDWLinkedService",
            "typeProperties": {
                "tableName": "FactInternetSales"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```

## <a name="step-3-create-and-run-your-pipeline"></a>Passo 3: Criar e executar a sua em curso

Por fim, estamos configurar e executar as em curso no Azure fábrica de dados.  Esta é a operação que conclui a circulação dados reais.  Pode encontrar uma vista completa das operações que pode concluir com armazém de dados SQL e Azure dados fábrica [aqui][Move data to and from Azure SQL Data Warehouse using Azure Data Factory].

Na secção 'Autor e implementar', clique em 'Mais comandos' e, em seguida, Pipeline de novo.  Depois de criar a tubagem, pode utilizar o por baixo de código para transferir os dados para o seu armazém de dados:

```JSON
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [
          {
            "type": "Copy",
            "typeProperties": {
                "source": {
                    "type": "BlobSource",
                    "skipHeaderLineCount": 1
                },
                "sink": {
                    "type": "SqlDWSink",
                    "writeBatchSize": 0,
                    "writeBatchTimeout": "00:00:10"
                }
            },
            "inputs": [
              {
                "name": "<Storage Dataset>"
              }
            ],
            "outputs": [
              {
                "name": "<Data Warehouse Dataset>"
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
            "name": "Sample Copy",
            "description": "Copy Activity"
          }
        ],
        "start": "<Date YYYY-MM-DD>",
        "end": "<Date YYYY-MM-DD>",
        "isPaused": false
    }
}
```

## <a name="next-steps"></a>Próximos passos

Para saber mais, comece por visualização:

- [Caminho de formação do azure fábrica de dados][].
- [Dados do azure SQL armazém conexão][]. Este é o tópico de referência core para utilizar o Azure dados fábrica com armazém de dados do SQL Azure.


Estes tópicos fornecem informações detalhadas sobre a fábrica de dados do Azure. Discutir a base de dados do SQL Azure ou HDInsight, mas as informações também se aplica ao armazém de dados do SQL Azure.

- [Tutorial: introdução ao Azure dados fábrica][] Este é o tutorial core para processamento de dados com o Azure fábrica de dados. Neste tutorial, irá criar a sua primeira pipeline que utiliza HDInsight para transformar e analisar os registos de web mensais. Note que não existe nenhuma actividade de cópia neste tutorial.
- [Tutorial: copiar dados a partir do Azure armazenamento Blob para base de dados do SQL Azure][]. Neste tutorial, crie uma tubagem no Azure dados fábrica para copiar dados a partir do Azure armazenamento Blob para base de dados do SQL Azure.

<!--Image references-->

<!--Article references-->
[Documentação AZCopy]: ../storage/storage-use-azcopy.md
[Conector de armazém de dados do Azure SQL]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[BCP]: sql-data-warehouse-load-with-bcp.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md
[Criar uma conta de armazenamento]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Data Factory]: sql-data-warehouse-get-started-load-with-azure-data-factory.md
[Introdução ao Azure fábrica de dados (dados fábrica Editor)]: ../data-factory/data-factory-build-your-first-pipeline-using-editor.md
[Introdução ao Azure dados fábrica]: ../data-factory/data-factory-introduction.md
[Load sample data into SQL Data Warehouse]: sql-data-warehouse-load-sample-databases.md
[Move data to and from Azure SQL Data Warehouse using Azure Data Factory]: ../data-factory/data-factory-azure-sql-data-warehouse-connector.md
[PolyBase]: sql-data-warehouse-get-started-load-with-polybase.md
[Tutorial: Copiar dados a partir do Azure armazenamento Blob para base de dados do SQL Azure]: ../data-factory/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[Tutorial: Introdução ao Azure fábrica de dados]: ../data-factory/data-factory-build-your-first-pipeline.md

<!--MSDN references-->

<!--Other Web references-->
[Caminho de formação do Azure fábrica de dados]: https://azure.microsoft.com/documentation/learning-paths/data-factory
[Portal do Azure]: https://portal.azure.com
[Transferir dados de exemplo]: https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv
