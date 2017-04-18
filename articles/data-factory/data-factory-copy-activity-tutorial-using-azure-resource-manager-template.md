<properties
    pageTitle="Tutorial: Criar uma tubagem utilizando o Gestor de recursos modelo | Microsoft Azure"
    description="Neste tutorial, crie uma pipeline de fábrica do Azure dados com uma atividade de cópia utilizando o Gestor de recursos do Azure modelo."
    services="data-factory"
    documentationCenter=""
    authors="spelluru"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="data-factory"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="spelluru"/>

# <a name="tutorial-create-a-pipeline-with-copy-activity-using-azure-resource-manager-template"></a>Tutorial: Criar uma tubagem com cópia atividade utilizando o modelo de Gestor de recursos do Azure
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
- [Copiar o Assistente](data-factory-copy-data-wizard-tutorial.md)
- [Portal do Azure](data-factory-copy-activity-tutorial-using-azure-portal.md)
- [Visual Studio](data-factory-copy-activity-tutorial-using-visual-studio.md)
- [PowerShell](data-factory-copy-activity-tutorial-using-powershell.md)
- [Modelo do Gestor de recursos Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md)
- [REST API](data-factory-copy-activity-tutorial-using-rest-api.md)
- [.NET API](data-factory-copy-activity-tutorial-using-dotnet-api.md)


Este tutorial mostra-lhe como criar e monitorizar uma fábrica de dados Azure utilizando um modelo de Gestor de recursos do Azure. A tubagem na fábrica dados copia os dados a partir do armazenamento de Blobs do Azure base de dados do SQL Azure.

## <a name="prerequisites"></a>Pré-requisitos
- Aceda através de [Descrição geral do Tutorial e pré-requisitos](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) e concluir os passos de **pré-requisito** .
- Siga as instruções no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a versão mais recente do Azure PowerShell no seu computador. Neste tutorial, utiliza o PowerShell para implementar entidades fábrica de dados. 
- (opcional) Consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md) para mais informações sobre modelos de Gestor de recursos do Azure.


## <a name="in-this-tutorial"></a>Neste tutorial

Neste tutorial, crie uma fábrica de dados com as seguintes entidades fábrica de dados:

Entidade | Descrição  
------ | ----------- 
Serviço de armazenamento ligado do Azure | Liga a sua conta de armazenamento do Windows Azure a fábrica de dados. Armazenamento Azure é o arquivo de dados de origem e base de dados Azure SQL é o arquivo de dados para a atividade de cópia sink no tutorial. Especifica a conta de armazenamento que contém os dados de entrada para a atividade de cópia. 
Serviço de base de dados SQL ligadas Azure| Ligações a base de dados do Azure SQL para a fábrica de dados. Especifica a base de dados do Azure SQL que contém os dados de saída para a atividade de cópia. 
Conjunto de dados de entrada Blob Azure | Refere-se para o serviço de armazenamento do Windows Azure ligadas. O serviço ligado que se refere a uma conta de armazenamento do Windows Azure e o conjunto de dados de Blobs do Azure Especifica o contentor, a pasta e o nome de ficheiro no armazenamento de que mantém os dados de entrada. 
Conjunto de dados de saída Azure SQL | Refere-se ao serviço do SQL Azure ligadas. O serviço do SQL Azure ligadas que se refere a um servidor do Azure SQL e o conjunto de dados do Azure SQL Especifica o nome da tabela que contém os dados de saída. 
Pipeline de dados | A tubagem tem uma atividade do tipo de copiar que retira o conjunto de dados de Blobs do Azure como uma entrada e o conjunto de dados do Azure SQL como um resultado. A atividade de cópia copia os dados a partir de um Azure blob a uma tabela numa base de dados Azure SQL.  

Uma fábrica de dados pode ter um ou mais tubagens. Uma tubagem pode ter um ou mais atividades. Existem dois tipos de atividades: [atividades de movimento de dados](data-factory-data-movement-activities.md) e [as atividades de transformação de dados](data-factory-data-transformation-activities.md). Neste tutorial, crie uma tubagem com uma atividade (actividade de cópia).

![Copiar BLOBs do Azure para base de dados Azure SQL](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/CopyBlob2SqlDiagram.png) 

A secção seguinte fornece o modelo de Gestor de recursos completa para definir entidades fábrica de dados para que possa rapidamente executar através do tutorial e testar o modelo. Para compreender como cada entidade fábrica de dados é definida, consulte a secção [entidades fábrica de dados no modelo](#data-factory-entities-in-the-template) .

## <a name="data-factory-json-template"></a>Modelo de fábrica do mesmo JSON de dados
O modelo de Gestor de recursos nível superior para definir uma fábrica de dados é: 

    {
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
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Crie um ficheiro JSON denominado **ADFCopyTutorialARM.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:


    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
          "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the data to be copied." } },
          "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
          "sourceBlobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
          "sourceBlobName": { "type": "string", "metadata": { "description": "Name of the blob in the container that has the data to be copied to Azure SQL Database table" } },
          "sqlServerName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Server that will hold the output/copied data." } },
          "databaseName": { "type": "string", "metadata": { "description": "Name of the Azure SQL Database in the Azure SQL server." } },
          "sqlServerUserName": { "type": "string", "metadata": { "description": "Name of the user that has access to the Azure SQL server." } },
          "sqlServerPassword": { "type": "securestring", "metadata": { "description": "Password for the user." } },
          "targetSQLTable": { "type": "string", "metadata": { "description": "Table in the Azure SQL Database that will hold the copied data." } 
          } 
        },
        "variables": {
          "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]",
          "azureSqlLinkedServiceName": "AzureSqlLinkedService",
          "azureStorageLinkedServiceName": "AzureStorageLinkedService",
          "blobInputDatasetName": "BlobInputDataset",
          "sqlOutputDatasetName": "SQLOutputDataset",
          "pipelineName": "Blob2SQLPipeline"
        },
        "resources": [
          {
            "name": "[variables('dataFactoryName')]",
            "apiVersion": "2015-10-01",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "West US",
            "resources": [
              {
                "type": "linkedservices",
                "name": "[variables('azureStorageLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureStorage",
                  "description": "Azure Storage linked service",
                  "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
                  }
                }
              },
              {
                "type": "linkedservices",
                "name": "[variables('azureSqlLinkedServiceName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlDatabase",
                  "description": "Azure SQL linked service",
                  "typeProperties": {
                    "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
                  }
                }
              },
              {
                "type": "datasets",
                "name": "[variables('blobInputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureBlob",
                  "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "Column0",
                      "type": "String"
                    },
                    {
                      "name": "Column1",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                    "fileName": "[parameters('sourceBlobName')]",
                    "format": {
                      "type": "TextFormat",
                      "columnDelimiter": ","
                    }
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  },
                  "external": true
                }
              },
              {
                "type": "datasets",
                "name": "[variables('sqlOutputDatasetName')]",
                "dependsOn": [
                  "[variables('dataFactoryName')]",
                  "[variables('azureSqlLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                  "type": "AzureSqlTable",
                  "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
                  "structure": [
                    {
                      "name": "FirstName",
                      "type": "String"
                    },
                    {
                      "name": "LastName",
                      "type": "String"
                    }
                  ],
                  "typeProperties": {
                    "tableName": "[parameters('targetSQLTable')]"
                  },
                  "availability": {
                    "frequency": "Day",
                    "interval": 1
                  }
                }
              },
              {
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
                  "start": "2016-10-02T00:00:00Z",
                  "end": "2016-10-03T00:00:00Z"
                }
              }
            ]
          }
        ]
      }

## <a name="parameters-json"></a>Parâmetros JSON 
Crie um ficheiro JSON denominado **ADFCopyTutorialARM Parameters.json** que contém os parâmetros para o modelo de Gestor de recursos do Azure. 

> [AZURE.IMPORTANT] Especifique o nome e a chave da sua conta de armazenamento do Windows Azure para os parâmetros **storageAccountName** e **storageAccountKey** .  

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": { 
            "storageAccountName": { "value": "<Name of the Azure storage account>"  },
            "storageAccountKey": {
                "value": "<Key for the Azure storage account>"
            },
            "sourceBlobContainer": { "value": "adftutorial" },
            "sourceBlobName": { "value": "emp.txt" },
            "sqlServerName": { "value": "<Name of the Azure SQL server>" },
            "databaseName": { "value": "<Name of the Azure SQL database>" },
            "sqlServerUserName": { "value": "<Name of the user who has access to the Azure SQL database>" },
            "sqlServerPassword": { "value": "<password for the user>" },
            "targetSQLTable": { "value": "emp" }
        }
    }

> [AZURE.IMPORTANT] Poderá ter parâmetro separado JSON ficheiros de desenvolvimento, testar e ambientes de produção que pode utilizar com o mesmo modelo de dados JSON de fábrica do mesmo. Ao utilizar um script de Shell de Power, pode automatizar implementar entidades de dados fábrica nestes ambientes.  

## <a name="create-data-factory"></a>Criar a fábrica de dados
1. Inicie o **PowerShell do Azure** e execute o seguinte comando:
    - Executar `Login-AzureRmAccount` e introduza o nome de utilizador e palavra-passe que utiliza para iniciar sessão portal do Azure.  
    - Executar `Get-AzureRmSubscription` para ver todas as subscrições para esta conta.
    - Executar `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` para selecionar a subscrição à qual pretende trabalhar com. 
2. Execute o seguinte comando para implementar entidades de fábrica de dados utilizando o modelo de Gestor de recursos que criou no passo 1.

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFCopyTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFCopyTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Em curso do monitor
1. Inicie sessão no [portal do Azure](https://portal.azure.com) utilizando a sua conta Azure.
2. Clique em **fábricas de dados** no menu à esquerda (ou) clique em **mais serviços** e clique em **fábricas de dados** em categoria de **informações da empresa + ANALYTICS** .

    ![Menu de fábricas de dados](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factories-menu.png)
3. Na página **fábricas de dados** , procurar e localizar a fábrica de dados. 

    ![Procurar a fábrica de dados](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/search-for-data-factory.png)  
4. Clique em fábrica dados Azure. Consulte a home page da fábrica de dados.

    ![Home page de fábrica de dados](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-home-page.png)  
5. Clique em mosaico de **diagrama** para ver a vista de diagrama da sua fábrica de dados.

    ![Vista de diagrama da fábrica de dados](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/data-factory-diagram-view.png)
6. Na vista de diagrama, faça duplo clique sobre o conjunto de dados **SQLOutputDataset**. Verá que estado no setor. Quando a operação de cópia estiver concluída, o estado definiu para **pronto**.

    ![Setor de saída no estado pronta](media/data-factory-copy-activity-tutorial-using-azure-resource-manager-template/output-slice-ready.png)
7. Quando está no setor no estado **pronta** , certifique-se de que os dados são copiados para a tabela de **emp** na base de dados Azure SQL.

Consulte o artigo [Monitor conjuntos de dados e em curso](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como utilizar os Azure pás portais para monitorizar as em curso e conjuntos de dados que criou neste tutorial.

Também pode utilizar monitor do computador e gerir App para monitorizar a sua tubagens de dados. Consulte o artigo [Monitor e gerir tubagens Azure fábrica de dados utilizando a aplicação de monitorização](data-factory-monitor-manage-app.md) para obter detalhes sobre como utilizar a aplicação.


## <a name="data-factory-entities-in-the-template"></a>Entidades de fábrica do mesmo no modelo de dados de

### <a name="define-data-factory"></a>Definir a fábrica de dados
Definir uma fábrica de dados no modelo de Gestor de recursos, conforme mostrado no exemplo seguinte:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

O dataFactoryName é definida como: 
      
    "dataFactoryName": "[concat('AzureBlobToAzureSQLDatabaseDF', uniqueString(resourceGroup().id))]"

É uma cadeia exclusiva com base no ID de grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades fábrica de dados
As seguintes entidades fábrica de dados são definidas no modelo de JSON: 

1. [Serviço de armazenamento ligado do Azure](#azure-storage-linked-service)
2. [Serviço de ligadas SQL Azure](#azure-sql-database-linked-service)
3. [Conjunto de dados de Blobs do Azure](#azure-blob-dataset)
4. [Conjunto de dados SQL Azure](#azure-sql-dataset)
5. [Pipeline de dados com uma atividade de cópia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Serviço de armazenamento ligado do Azure
Especifique o nome e a chave da sua conta de armazenamento Azure nesta secção. Ver o [armazenamento do Windows Azure ligados serviço](data-factory-azure-blob-connector.md#azure-storage-linked-service) para obter mais detalhes acerca das propriedades JSON utilizadas para definir um serviço de armazenamento do Windows Azure ligadas. 

    {
        "type": "linkedservices",
        "name": "[variables('azureStorageLinkedServiceName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureStorage",
            "description": "Azure Storage linked service",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
            }
        }
    }

ConnectionString utiliza os parâmetros storageAccountName e storageAccountKey. Os valores para estes parâmetros passados utilizando um ficheiro de configuração. A definição também utiliza variáveis: azureStroageLinkedService e dataFactoryName definidos no modelo. 
    
#### <a name="azure-sql-database-linked-service"></a>Serviço de base de dados SQL ligadas Azure
Especifique o nome de servidor do Azure SQL, nome da base de dados, nome de utilizador e palavra-passe de utilizador nesta secção. Consulte o artigo [Azure SQL ligadas serviço](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties) para obter mais detalhes acerca das propriedades JSON utilizadas para definir um serviço do SQL Azure ligadas.  

    {
        "type": "linkedservices",
        "name": "[variables('azureSqlLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlDatabase",
            "description": "Azure SQL linked service",
            "typeProperties": {
                "connectionString": "[concat('Server=tcp:',parameters('sqlServerName'),'.database.windows.net,1433;Database=', parameters('databaseName'), ';User ID=',parameters('sqlServerUserName'),';Password=',parameters('sqlServerPassword'),';Trusted_Connection=False;Encrypt=True;Connection Timeout=30')]"
            }
        }
    }

ConnectionString utiliza Nome_servidor_sql, databaseName, sqlServerUserName e sqlServerPassword parâmetros são transmitidos cujos valores utilizando um ficheiro de configuração. A definição também utiliza as seguintes variáveis do modelo: azureSqlLinkedServiceName, dataFactoryName.

#### <a name="azure-blob-dataset"></a>Conjunto de dados de Blobs do Azure
Especifique os nomes de contentor blob, a pasta e o ficheiro que contém os dados de entrada. Ver [Propriedades do conjunto de dados de Blobs do Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter mais detalhes acerca das propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure. 


    {
        "type": "datasets",
        "name": "[variables('blobInputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
            "structure": [
            {
                "name": "Column0",
                "type": "String"
            },
            {
                "name": "Column1",
                "type": "String"
            }
            ],
            "typeProperties": {
                "folderPath": "[concat(parameters('sourceBlobContainer'), '/')]",
                "fileName": "[parameters('sourceBlobName')]",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": ","
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            },
            "external": true
        }
    }

#### <a name="azure-sql-dataset"></a>Conjunto de dados SQL Azure
Especifique o nome da tabela na base de dados Azure SQL que contém os dados copiados a partir do armazenamento de Blobs do Azure. Ver [Propriedades de conjunto de dados do Azure SQL](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties) para obter mais detalhes acerca das propriedades JSON utilizadas para definir um conjunto de dados do Azure SQL. 

    {
        "type": "datasets",
        "name": "[variables('sqlOutputDatasetName')]",
        "dependsOn": [
            "[variables('dataFactoryName')]",
            "[variables('azureSqlLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "[variables('azureSqlLinkedServiceName')]",
            "structure": [
            {
                "name": "FirstName",
                "type": "String"
            },
            {
                "name": "LastName",
                "type": "String"
            }
            ],
            "typeProperties": {
                "tableName": "[parameters('targetSQLTable')]"
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }

#### <a name="data-pipeline"></a>Pipeline de dados
Definir uma tubagem que copia os dados a partir do conjunto de dados de Blobs do Azure para o conjunto de dados do Azure SQL. Consulte o artigo [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) para descrições dos elementos JSON utilizadas para definir uma tubagem neste exemplo. 

    {
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
            "start": "2016-10-02T00:00:00Z",
            "end": "2016-10-03T00:00:00Z"
        }
    }

## <a name="reuse-the-template"></a>Reutilizar o modelo 
Tutorial, criou um modelo para a definição de entidades de fábrica de dados e um modelo para passar valores para os parâmetros. Pipeline de copia os dados de uma conta de armazenamento do Windows Azure para uma base de dados do Azure SQL especificado através de parâmetros. Para utilizar o mesmo modelo para implementar o entidades fábrica de dados em diferentes ambientes, cria um ficheiro de parâmetro para cada ambiente e utilizá-lo quando implementar o nesse ambiente.     

Exemplo:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFCopyTutorialARM.json -TemplateParameterFile ADFCopyTutorialARM-Parameters-Production.json

Repare que o primeiro comando utiliza o ficheiro de parâmetro para o ambiente de desenvolvimento, uma segunda para o ambiente de teste e a uma terceira para o ambiente de produção.  

Também pode reutilizar o modelo para executar tarefas repetidas. Por exemplo, tem de criar muitos fábricas de dados com um ou mais tubagens que implementam a lógica mesmo mas cada fábrica dados utiliza armazenamento Azure diferente e contas de base de dados do Azure SQL. Neste cenário, utilize o mesmo modelo no mesmo ambiente (Dev Center, teste ou produção) com os ficheiros de diferentes parâmetros para criar fábricas de dados.   

