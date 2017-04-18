<properties
   pageTitle="Carregar grandes quantidades de dados para o arquivo de dados de Lake através de métodos offline | Microsoft Azure"
   description="Utilize a ferramenta de AdlCopy para copiar dados a partir do Azure armazenamento de Blobs ao arquivo de dados de Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/07/2016"
   ms.author="nitinme"/>

# <a name="use-azure-import-export-service-for-offline-copy-of-data-to-data-lake-store"></a>Utilizar o serviço de importar exportar Azure para cópia offline da dados ao arquivo de dados de Lake

Neste artigo, irá obter informações sobre como copiar enormes conjuntos de dados (> 200GB) para um arquivo de Lake Azure dados através de métodos de cópia offline, como o [Serviço do Azure importar/exportar](../storage/storage-import-export-service.md). Especificamente, o ficheiro utilizado como um exemplo neste artigo é 339,420,860,416 bytes, ou seja, cerca de 319GB no disco. Vamos chamar 319GB.tsv este ficheiro.

Serviço de importação/exportação Azure permite-lhe transferir em segurança grandes quantidades de dados ao armazenamento de Blobs do Azure ao endereço de envio, unidades de disco rígido para um centro de dados Azure.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar este artigo, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

- **Conta de armazenamento do Windows azure**.

- **Conta azure dados Lake Analytics (opcional)** - consulte o artigo [Introdução ao Azure dados Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md) para obter instruções sobre como criar uma conta do arquivo de Lake de dados.


## <a name="preparing-the-data"></a>Preparar os dados

Antes de utilizar o serviço de importação/exportação, podemos tem de interromper o ficheiro de dados para ser transferidos **para cópias que são menos de 200 GB** de tamanho. Isto acontece porque a ferramenta de importação não funciona com os ficheiros maiores do que 200GB. Para cumprir esta situação, neste tutorial podemos dividir o ficheiro em blocos de 100GB bytes. Pode fazê-lo facilmente utilizando [Cygwin](https://cygwin.com/install.html). Cygwin suporta comando Linux que permite que os utilizadores para fazê-lo facilmente. Para os nossos caso, utilizamos o seguinte comando.

    split -b 100m 319GB.tsv

A operação de divisão cria ficheiros com os nomes abaixo.

    319GB.tsv-part-aa

    319GB.tsv-part-ab

    319GB.tsv-part-ac

    319GB.tsv-part-ad

## <a name="get-disks-ready-with-data"></a>Preparar discos com dados

Siga as instruções em [Utilizar o serviço de importação/exportação de Azure](../storage/storage-import-export-service.md) (em secção **preparar os seus discos** ) para preparar o seu unidades de disco rígido. Eis o fluxo geral sobre como preparar a unidade.

1. Adquirir um disco rígido que cumpre os requisitos para ser utilizado para o serviço de importação/exportação Auzre.

2. Identificar uma conta de armazenamento do Windows Azure onde os dados serão copiou uma vez por si enviadas para o Centro de dados Azure.

3. Utilize a [Ferramenta de importar/exportar Azure](http://go.microsoft.com/fwlink/?LinkID=301900&clcid=0x409), um utilitário de linha de comandos. Eis um fragmento de exemplo sobre como utilizar a ferramenta.

    ````
    WAImportExport PrepImport /sk:<StorageAccountKey> /t: <TargetDriveLetter> /format /encrypt /logdir:e:\myexportimportjob\logdir /j:e:\myexportimportjob\journal1.jrn /id:myexportimportjob /srcdir:F:\demo\ExImContainer /dstdir:importcontainer/vf1/
    ````
    Consulte o artigo [Utilizar o serviço do Azure importar/exportar](../storage/storage-import-export-service.md) para fragmentos de exemplo mais sobre como utilizar a **Ferramenta do Azure importar/exportar**.

4. O comando acima cria um ficheiro de diário na localização especificada. Irá utilizar este ficheiro diário para criar uma tarefa de importação a partir do [Azure clássica Portal](https://manage.windowsazure.com).

## <a name="create-an-import-job"></a>Criar uma tarefa de importação

Agora pode criar uma tarefa de importação utilizando as instruções em [Utilizar o serviço de importação/exportação de Azure](../storage/storage-import-export-service.md) (em secção **criar a tarefa de importação** ). Para esta tarefa de importar, com outros detalhes, também fornecem o ficheiro de diário criado ao preparar as unidades de disco.

## <a name="physically-ship-the-disks"></a>Enviar física discos

Agora pode enviar física discos para um centro de dados Azure onde os dados são copiados através de Blobs do armazenamento de Azure fornecida ao criar a tarefa de importação. Além disso, enquanto cria a tarefa, caso tenha optado por forneça as informações de controlo mais tarde, que agora pode aceder novamente para o seu trabalho de importação e atualizado o número de controlo.

## <a name="copy-data-from-azure-storage-blobs-to-azure-data-lake-store"></a>Copiar dados a partir do Azure armazenamento de Blobs ao arquivo de Lake de dados do Azure

Assim que o estado da tarefa de importação de apresenta concluído, pode verificar se os dados estão disponíveis de Blobs do armazenamento de Azure tinha especificado. Em seguida, pode utilizar uma variedade de métodos para mover os dados de Blobs de armazenamento o Azure para Azure dados Lake loja. Para todas as opções disponíveis para carregar os dados, consulte o artigo [Ingesting dados para o arquivo de dados de Lake](data-lake-store-data-scenarios.md#ingest-data-into-data-lake-store).

Nesta secção, fornecemos-lhe com as definições de JSON que pode utilizar para criar um pipeline de fábrica do Azure dados para copiar os dados. Pode utilizar estas definições JSON a partir da [Azure portal](../data-factory/data-factory-copy-activity-tutorial-using-azure-portal.md) ou [Visual Studio](../data-factory/data-factory-copy-activity-tutorial-using-visual-studio.md) ou [Azure PowerShell](../data-factory/data-factory-copy-activity-tutorial-using-powershell.md).

### <a name="source-linked-service-azure-storage-blob"></a>Origem ligadas serviço (Azure armazenamento Blob)

````
{
    "name": "AzureStorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "description": "",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
        }
    }
}
````

### <a name="target-linked-service-azure-data-lake-store"></a>Destino ligadas serviço (Azure dados Lake Store)

````
{
    "name": "AzureDataLakeStoreLinkedService",
    "properties": {
        "type": "AzureDataLakeStore",
        "description": "",
        "typeProperties": {
            "authorization": "<Click 'Authorize' to allow this data factory and the activities it runs to access this Data Lake Store with your access rights>",
            "dataLakeStoreUri": "https://<adls_account_name>.azuredatalakestore.net/webhdfs/v1",
            "sessionId": "<OAuth session id from the OAuth authorization session. Each session id is unique and may only be used once>"
        }
    }
}
````
### <a name="input-data-set"></a>Conjunto de dados de entrada
````
{
    "name": "InputDataSet",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties": {
            "folderPath": "importcontainer/vf1/"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
````
### <a name="output-data-set"></a>Conjunto de dados de saída
````
{
"name": "OutputDataSet",
"properties": {
  "published": false,
  "type": "AzureDataLakeStore",
  "linkedServiceName": "AzureDataLakeStoreLinkedService",
  "typeProperties": {
    "folderPath": "/importeddatafeb8job/"
    },
  "availability": {
    "frequency": "Hour",
    "interval": 1
    }
  }
}
````
### <a name="pipeline-copy-activity"></a>Em curso (copiar actividade)
````
{
    "name": "CopyImportedData",
    "properties": {
        "description": "Pipeline with copy activity",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "AzureDataLakeStoreSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "InputDataSet"
                    }
                ],
                "outputs": [
                    {
                        "name": "OutputDataSet"
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
                "name": "AzureBlobtoDataLake",
                "description": "Copy Activity"
            }
        ],
        "start": "2016-02-08T22:00:00Z",
        "end": "2016-02-08T23:00:00Z",
        "isPaused": false,
        "pipelineMode": "Scheduled"
    }
}
````
Para mais informações sobre como utilizar o Azure dados fábrica para mover os dados a partir do Azure armazenamento Blob e Azure dados Lake loja, consulte o artigo [mover os dados a partir do Azure armazenamento Blob ao arquivo de Lake Azure dados utilizando a fábrica de dados do Azure](../data-factory/data-factory-azure-datalake-connector.md#sample-copy-data-from-azure-blob-to-azure-data-lake-store).

## <a name="reconstruct-the-data-files-in-azure-data-lake-store"></a>Reconstruir os ficheiros de dados no arquivo de Lake de dados do Azure

Vamos iniciado com um ficheiro que foi 319GB de tamanho e descontinuado-lo em ficheiros de tamanho mais pequeno, para que possam ser transferido com o serviço de importação/exportação Azure. Agora que os dados estiverem no Azure dados Lake arquivo, podemos reconstrcut o ficheiro para o respetivo tamanho original. Pode utilizar o Azure PowerShell cmldts seguintes para fazê-lo.

````
# Login to our account
Login-AzureRmAccount

# List your subscriptions
Get-AzureRmSubscription

# Switch to the subscription you want to work with
Set-AzureRmContext –SubscriptionId
Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"

# Join  the files
Join-AzureRmDataLakeStoreItem -AccountName "<adls_account_name" -Paths "/importeddatafeb8job/319GB.tsv-part-aa","/importeddatafeb8job/319GB.tsv-part-ab", "/importeddatafeb8job/319GB.tsv-part-ac", "/importeddatafeb8job/319GB.tsv-part-ad" -Destination "/importeddatafeb8job/MergedFile.csv”
````

## <a name="next-steps"></a>Próximos passos

- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
- [Utilizar a análise de dados Azure Lake com o arquivo de dados de Lake](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Utilizar o Azure HDInsight com o arquivo de dados de Lake](data-lake-store-hdinsight-hadoop-use-portal.md)
