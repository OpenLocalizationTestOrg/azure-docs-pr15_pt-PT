<properties 
    pageTitle="Modelos de utilizar o Gestor de recursos no dados fábrica | Microsoft Azure" 
    description="Saiba como criar e utilizar o Gestor de recursos do Azure modelos para criar entidades fábrica de dados." 
    services="data-factory" 
    documentationCenter="" 
    authors="sharonlo101" 
    manager="jhubbard" 
    editor=""/>

<tags 
    ms.service="data-factory" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/24/2016" 
    ms.author="shlo"/>

# <a name="use-templates-to-create-azure-data-factory-entities"></a>Utilize modelos para criar entidades fábrica de dados do Azure

## <a name="overview"></a>Descrição geral
Ao usar o Azure dados fábrica para suas necessidades de integração de dados, poderá constatar si próprio reutilizar o mesmo padrão ao longo do ambientes diferentes ou na mesma tarefa repetidamente dentro da mesma solução de execução. Modelos de ajudam a implementar e gerir estes cenários de uma forma fácil. Modelos no Azure dados fábrica são ideais para cenários que envolvam reutilização dos e de repetição.
 
Considere a situação em que uma organização tem 10 plantas de fabrico em todo o mundo. Os registos de início de cada planta são armazenados numa base de dados do SQL Server em separado no local. Pretende que a empresa criar um armazém de dados única na nuvem para análise de ad-hoc. Também pretende ter a mesma lógica mas configurações diferentes para os ambientes de desenvolvimento, teste e produção. 

Neste caso, uma tarefa tem de ser repetida dentro do mesmo ambiente, mas com diferentes valores em toda as fábricas 10 dados para cada fábrica de produção. Na verdade, **repetição** se encontra presente. Templating permite à produção deste fluxo genérico (ou seja, tubagens está a ter as mesmas actividades numa cada fábrica de dados), mas utiliza um ficheiro de parâmetro separada para cada fábrica de produção.

Além disso, tal como a organização pretende implementar fábricas estes 10 dados várias vezes em diferentes ambientes, modelos podem utilizar este **reutilização dos** recorrendo ficheiros de parâmetro separado para ambientes de desenvolvimento, teste e produção.

## <a name="templating-with-azure-resource-manager"></a>Templating com o Gestor de recursos Azure
[Gestor de recursos do Azure modelos](../azure-resource-manager/resource-group-overview.md#template-deployment) são uma excelente forma de atingir templating no Azure fábrica de dados. Os modelos de Gestor de recursos definem a infraestrutura e a configuração da sua solução Azure através de um ficheiro JSON. Uma vez que o Gestor de recursos do Azure modelos funcionam com serviços de todos os/maioria Azure, podem ser utilizado de amplamente para gerir facilmente a todos os recursos dos seus ativos Azure. Consulte o artigo [Gestor de recursos do Azure criação de modelos](../resource-group-authoring-templates.md) para saber mais sobre os modelos de Gestor de recursos no geral. 

## <a name="tutorials"></a>Tutoriais
Consulte os seguintes tutoriais para obter instruções passo a passo para criar entidades de fábrica de dados com os modelos de Gestor de recursos:

- [Tutorial: Criar uma tubagem para copiar dados utilizando o modelo de Gestor de recursos do Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [Tutorial: Criar uma tubagem para processar dados utilizando o modelo de Gestor de recursos do Azure](data-factory-build-your-first-pipeline.md)

## <a name="data-factory-templates-on-github"></a>Modelos de fábrica do mesmo de dados no Github
Consulte os artigos seguintes modelos de guia de introdução do Azure Github: 

- [Criar uma fábrica de dados para copiar dados a partir do armazenamento de Blobs do Azure para base de dados do SQL Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-blob-to-sql-copy)
- [Criar uma fábrica de dados com ramo de atividade num cluster de Azure HDInsight](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-hive-transformation)
- [Criar uma fábrica de dados para copiar dados a partir do Salesforce para Blobs do Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-salesforce-to-blob-copy)

Esteja à vontade partilhar os modelos de fábrica do Azure dados no [Azure guia](https://azure.microsoft.com/documentation/templates/). Consulte o [Guia de contribuição](https://github.com/Azure/azure-quickstart-templates/tree/master/1-CONTRIBUTION-GUIDE) durante o desenvolvimento de modelos que podem ser partilhados através deste repositório. 

As secções seguintes fornecem detalhes sobre a definição de recursos de fábrica de dados num modelo de Gestor de recursos. 

## <a name="defining-data-factory-resources-in-templates"></a>Definir recursos fábrica de dados em modelos
O modelo de nível superior para definir uma fábrica de dados é:

    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
    {
        "name": "[parameters('dataFactoryName')]",
        "apiVersion": "[variables('apiVersion')]",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "westus",
        "resources": [
        { "type": "linkedservices",
            ...
        },
        {"type": "datasets",
            ...
        },
        {"type": "dataPipelines",
            ...
        }
    }

### <a name="define-data-factory"></a>Definir a fábrica de dados

Definir uma fábrica de dados no modelo de Gestor de recursos, conforme mostrado no seguinte exemplo:

    "resources": [
    {
        "name": "[variables('<mydataFactoryName>')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "East US"
    }

O dataFactoryName é definido em "variáveis" como:

    "dataFactoryName": "[concat('<myDataFactoryName>', uniqueString(resourceGroup().id))]",

### <a name="define-linked-services"></a>Definir serviços ligados 
    
    "type": "linkedservices",
    "name": "[variables('<LinkedServiceName>')]",
    "apiVersion": "2015-10-01",
    "dependsOn": [ "[variables('<dataFactoryName>')]" ],
    "properties": {
        ...
    }


Consulte [Serviço de armazenamento ligadas](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [Calcular serviços ligados](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes sobre as propriedades JSON para o serviço ligada específico que pretende implementar. O parâmetro de "dependsOn" Especifica o nome da fábrica dados correspondente. Um exemplo de definição de um serviço ligado para o armazenamento do Windows Azure é mostrado na definição do JSON seguinte:

### <a name="define-datasets"></a>Definir conjuntos de dados

    "type": "datasets",
    "name": "[variables('<myDatasetName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<myDatasetLinkedServiceName>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        ...
    }

Consulte [suportadas arquivos de dados](data-factory-data-movement-activities.md#supported-data-stores-and-formats) para obter mais detalhes sobre as propriedades JSON para o tipo de conjunto de dados específicos que pretende implementar. Nota que o parâmetro de "dependsOn" Especifica o nome do correspondente fábrica de dados e armazenamento ligada serviço. Um exemplo de definição de tipo de conjunto de dados de armazenamento de Blobs do Azure é mostrado na definição do JSON seguinte:

    "type": "datasets",
    "name": "[variables('storageDataset')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('storageLinkedServiceName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "[variables('storageLinkedServiceName')]",
    "typeProperties": {
        "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
        "fileName": "[parameters('sourceBlobName')]",
        "format": {
            "type": "TextFormat"
        }
    },
    "availability": {
        "frequency": "Hour",
        "interval": 1
    }

### <a name="define-pipelines"></a>Definir tubagens

    "type": "dataPipelines",
    "name": "[variables('<mypipelineName>')]",
    "dependsOn": [
        "[variables('<dataFactoryName>')]",
        "[variables('<inputDatasetLinkedServiceName>')]",
        "[variables('<outputDatasetLinkedServiceName>')]",
        "[variables('<inputDataset>')]",
        "[variables('<outputDataset>')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        activities: {
            ...
        }
    }

Consulte [Definir tubagens](data-factory-create-pipelines.md#pipeline-json) para obter detalhes sobre as propriedades JSON para definir a pipeline específico e atividades que pretende implementar. Tenha em atenção o parâmetro "dependsOn" Especifica nome da fábrica dados, bem como qualquer correspondente associados serviços ou conjuntos de dados. Um exemplo de uma tubagem que copia os dados de armazenamento de Blobs do Azure base de dados do SQL Azure é apresentado no seguinte fragmento JSON:

    "type": "datapipelines",
    "name": "[variables('pipelineName')]",
    "dependsOn": [
        "[variables('dataFactoryName')]",
        "[variables('azureStorageLinkedServiceName')]",
        "[variables('azureSqlLinkedServiceName')]",
        "[variables('blobInputDatasetName')]",
        "[variables('sqlOutputDatasetName')]"
    ],
    "apiVersion": "2015-10-01",
    "properties": {
        "activities": [
        {
            "name": "CopyFromAzureBlobToAzureSQL",
            "description": "Copy data frm Azure blob to Azure SQL",
            "type": "Copy",
            "inputs": [
                {
                    "name": "[variables('blobInputDatasetName')]"
                }
            ],
            "outputs": [
                {
                    "name": "[variables('sqlOutputDatasetName')]"
                }
            ],
            "typeProperties": {
                "source": {
                    "type": "BlobSource"
                },
                "sink": {
                    "type": "SqlSink",
                    "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM {0}', 'emp')"
                },
                "translator": {
                    "type": "TabularTranslator",
                    "columnMappings": "Column0:FirstName,Column1:LastName"
                }
            },
            "Policy": {
                "concurrency": 1,
                "executionPriorityOrder": "NewestFirst",
                "retry": 3,
                "timeout": "01:00:00"
            }
        }
        ],
        "start": "2016-10-03T00:00:00Z",
        "end": "2016-10-04T00:00:00Z"

## <a name="parameterizing-data-factory-template"></a>Modelo de dados fábrica parametrizar
Para as melhores práticas na parametrizar, consulte o artigo [melhores práticas para criar modelos de Gestor de recursos do Azure](../resource-manager-template-best-practices.md#parameters) artigo. Em geral, a utilização de parâmetro deve ser minimizada, especialmente se variáveis podem ser utilizadas em vez disso. Fornece apenas parâmetros nos cenários seguintes:

- Definições variam consoante ambiente (exemplo: desenvolvimento, teste e produção)
- Segredos (como palavras-passe)

Se precisar de separar segredos do [Azure chave cofre](../key-vault/key-vault-get-started.md) quando implementar entidades Azure dados fábrica utilizar modelos, especifique o **nome secreta** e **Cofre chave** , conforme mostrado no exemplo seguinte:

    "parameters": {
        "storageAccountKey": { 
            "reference": {
                "keyVault": {
                    "id":"/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.KeyVault/vaults/<keyVaultName>",
                },
                "secretName": "<secretName>"
            }, 
        },
        ...
    }

> [AZURE.NOTE] Apesar de exportação de modelos para fábricas de dados existente atualmente ainda não é suportada, é no programa works. 


