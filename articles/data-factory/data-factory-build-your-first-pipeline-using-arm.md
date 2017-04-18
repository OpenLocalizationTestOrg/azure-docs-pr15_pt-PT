<properties
    pageTitle="Construir a sua primeira fábrica de dados (modelo de Gestor de recursos) | Microsoft Azure"
    description="Neste tutorial, crie um pipeline de fábrica do Azure dados de exemplo utilizando um modelo de Gestor de recursos do Azure."
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
    ms.topic="hero-article"
    ms.date="10/12/2016"
    ms.author="spelluru"/>

# <a name="tutorial-build-your-first-azure-data-factory-using-azure-resource-manager-template"></a>Tutorial: Criar a primeira fábrica de dados Azure utilizando o modelo de Gestor de recursos do Azure
> [AZURE.SELECTOR]
- [Descrição geral e pré-requisitos](data-factory-build-your-first-pipeline.md)
- [Portal do Azure](data-factory-build-your-first-pipeline-using-editor.md)
- [Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Modelo de Gestor de recursos](data-factory-build-your-first-pipeline-using-arm.md)
- [REST API](data-factory-build-your-first-pipeline-using-rest-api.md)

Neste artigo, utilizar um modelo de Gestor de recursos do Azure para criar a sua primeira fábrica de dados Azure.

## <a name="prerequisites"></a>Pré-requisitos
- Leia o artigo [Descrição geral do Tutorial](data-factory-build-your-first-pipeline.md) e concluir os passos de **pré-requisito** .
- Siga as instruções no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para instalar a versão mais recente do Azure PowerShell no seu computador.
- Consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md) para mais informações sobre modelos de Gestor de recursos do Azure. 

## <a name="in-this-tutorial"></a>Neste tutorial
Entidade | Descrição  
------ | ----------- 
Serviço de armazenamento ligado do Azure | Liga a sua conta de armazenamento do Windows Azure a fábrica de dados. A conta de armazenamento do Windows Azure contém os dados de entrada e saídos de tubagem neste exemplo. 
Serviço de ligadas HDInsight a pedido| Cluster ligações uma HDInsight a pedido para a fábrica de dados. Cluster é criado automaticamente para processar dados e é eliminado depois de concluir o processo de processamento.
Conjunto de dados de entrada Blob Azure | Refere-se para o serviço de armazenamento do Windows Azure ligadas. O serviço ligado que se refere a uma conta de armazenamento do Windows Azure e o conjunto de dados de Blobs do Azure Especifica o contentor, a pasta e o nome de ficheiro no armazenamento de que mantém os dados de entrada. 
Conjunto de dados de saída de Blobs do Azure | Refere-se para o serviço de armazenamento do Windows Azure ligadas. O serviço ligado que se refere a uma conta de armazenamento do Windows Azure e o conjunto de dados de Blobs do Azure Especifica o contentor, a pasta e o nome de ficheiro no armazenamento de que mantém os dados de saída. 
Pipeline de dados | A tubagem tem uma atividade do tipo HDInsightHive consome o conjunto de dados de entrada e produz o conjunto de dados de saída.   

Uma fábrica de dados pode ter um ou mais tubagens. Uma tubagem pode ter um ou mais atividades. Existem dois tipos de atividades: [atividades de movimento de dados](data-factory-data-movement-activities.md) e [as atividades de transformação de dados](data-factory-data-transformation-activities.md). Neste tutorial, crie uma tubagem com uma atividade (actividade de cópia).

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

Crie um ficheiro JSON denominado **ADFTutorialARM.json** na pasta **C:\ADFGetStarted** com o seguinte conteúdo:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
            "storageAccountName": { "type": "string", "metadata": { "description": "Name of the Azure storage account that contains the input/output data." } },
            "storageAccountKey": { "type": "securestring", "metadata": { "description": "Key for the Azure storage account." } },
            "blobContainer": { "type": "string", "metadata": { "description": "Name of the blob container in the Azure Storage account." } },
            "inputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that has the input file." } },
            "inputBlobName": { "type": "string", "metadata": { "description": "Name of the input file/blob." } },
            "outputBlobFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that will hold the transformed data." } },
            "hiveScriptFolder": { "type": "string", "metadata": { "description": "The folder in the blob container that contains the Hive query file." } },
            "hiveScriptFile": { "type": "string", "metadata": { "description": "Name of the hive query (HQL) file." } }
        },
        "variables": {
            "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",
            "azureStorageLinkedServiceName": "AzureStorageLinkedService",
            "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
            "blobInputDatasetName": "AzureBlobInput",
            "blobOutputDatasetName": "AzureBlobOutput",
            "pipelineName": "HiveTransformPipeline"
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
                "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "HDInsightOnDemand",
                    "typeProperties": {
                        "clusterSize": 1,
                        "version": "3.2",
                        "timeToLive": "00:05:00",
                        "osType": "windows",
                        "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
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
                    "typeProperties": {
                        "fileName": "[parameters('inputBlobName')]",
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
                        "interval": 1
                    },
                    "external": true
                }
            },
            {
                "type": "datasets",
                "name": "[variables('blobOutputDatasetName')]",
                "dependsOn": [
                    "[variables('dataFactoryName')]",
                    "[variables('azureStorageLinkedServiceName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "type": "AzureBlob",
                    "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
                    "typeProperties": {
                        "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
                        "format": {
                            "type": "TextFormat",
                            "columnDelimiter": ","
                        }
                    },
                    "availability": {
                        "frequency": "Month",
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
                    "[variables('hdInsightOnDemandLinkedServiceName')]",
                    "[variables('blobInputDatasetName')]",
                    "[variables('blobOutputDatasetName')]"
                ],
                "apiVersion": "2015-10-01",
                "properties": {
                    "description": "Pipeline that transforms data using Hive script.",
                    "activities": [
                    {
                        "type": "HDInsightHive",
                        "typeProperties": {
                            "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                            "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                            "defines": {
                                "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                                "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                            }
                        },
                        "inputs": [
                            {
                                "name": "[variables('blobInputDatasetName')]"
                            }
                        ],
                        "outputs": [
                            {
                                "name": "[variables('blobOutputDatasetName')]"
                            }
                        ],
                        "policy": {
                            "concurrency": 1,
                            "retry": 3
                        },
                        "scheduler": {
                            "frequency": "Month",
                            "interval": 1
                        },
                        "name": "RunSampleHiveActivity",
                        "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
                    }
                    ],
                    "start": "2016-10-01T00:00:00Z",
                    "end": "2016-10-02T00:00:00Z",
                    "isPaused": false
                }
            }
            ]
        }
        ]
    }

> [AZURE.NOTE] Pode encontrar outro exemplo de modelo de Gestor de recursos para a criação de uma fábrica dados Azure no [Tutorial: criar uma tubagem com cópia atividade utilizando um modelo de Gestor de recursos do Azure](data-factory-copy-activity-tutorial-using-azure-resource-manager-template.md).  

## <a name="parameters-json"></a>Parâmetros JSON 
Crie um ficheiro JSON denominado **ADFTutorialARM Parameters.json** que contém os parâmetros para o modelo de Gestor de recursos do Azure.  

> [AZURE.IMPORTANT] Especifique o nome e a chave da sua conta de armazenamento do Windows Azure para os parâmetros **storageAccountName** e **storageAccountKey** neste ficheiro parâmetro. 

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "storageAccountName": {
                "value": "<Name of your Azure Storage account>"
            },
            "storageAccountKey": {
                "value": "<Key of your Azure Storage account>"
            },
            "blobContainer": {
                "value": "adfgetstarted"
            },
            "inputBlobFolder": {
                "value": "inputdata"
            },
            "inputBlobName": {
                "value": "input.log"
            },
            "outputBlobFolder": {
                "value": "partitioneddata"
            },
            "hiveScriptFolder": {
                "value": "script"
            },
            "hiveScriptFile": {
                "value": "partitionweblogs.hql"
            }
        }
    }

> [AZURE.IMPORTANT] Poderá ter parâmetro separado JSON ficheiros de desenvolvimento, testar e ambientes de produção que pode utilizar com o mesmo modelo de dados JSON de fábrica do mesmo. Ao utilizar um script de Shell de Power, pode automatizar implementar entidades de dados fábrica nestes ambientes. 

## <a name="create-data-factory"></a>Criar a fábrica de dados

1. Inicie o **PowerShell do Azure** e execute o seguinte comando: 
    - Executar `Login-AzureRmAccount` e introduza o nome de utilizador e palavra-passe que utiliza para iniciar sessão portal do Azure.  
    - Executar `Get-AzureRmSubscription` para ver todas as subscrições para esta conta.
    - Executar `Get-AzureRmSubscription -SubscriptionName <SUBSCRIPTION NAME> | Set-AzureRmContext` para selecionar a subscrição à qual pretende trabalhar com. Esta subscrição deve ser igual a que utilizou no portal do Azure.
1. Execute o seguinte comando para implementar entidades de fábrica de dados utilizando o modelo de Gestor de recursos que criou no passo 1. 

        New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json -TemplateParameterFile C:\ADFGetStarted\ADFTutorialARM-Parameters.json

## <a name="monitor-pipeline"></a>Em curso do monitor
 
1.  Após o início de sessão no [portal do Azure](https://portal.azure.com/), clique em **Procurar** e selecione **fábricas de dados**.
        ![Procurar -> fábricas de dados](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.  Na pá **Fábricas de dados** , clique na fábrica de dados (**TutorialFactoryARM**) que criou.   
2.  No pá a **Fábrica de dados** para a fábrica de dados, clique em **diagrama**.
        ![Mosaico do diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.  Na **Vista de diagrama**, pode ver uma descrição geral da tubagens e conjuntos de dados utilizados neste tutorial.
    
    ![Vista de diagrama](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Na vista de diagrama, faça duplo clique sobre o conjunto de dados **AzureBlobOutput**. Verá que no setor que está atualmente a ser processado.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Quando concluir o processo processamento, verá setor no estado **pronta** . Criação de um cluster de HDInsight a pedido normalmente leva-o até algures (cerca de 20 minutos). Espera por conseguinte, a tubagem para demorar **cerca de 30 minutos** para processar no setor.

    ![Conjunto de dados](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png) 
10. Quando está no setor no estado **pronta** , selecione a pasta **partitioneddata** no contentor **adfgetstarted** no seu armazenamento blob para os dados de saída.  

Consulte o artigo [Monitor conjuntos de dados e em curso](data-factory-monitor-manage-pipelines.md) para obter instruções sobre como utilizar os Azure pás portais para monitorizar as em curso e conjuntos de dados que criou neste tutorial.

Também pode utilizar monitor do computador e gerir App para monitorizar a sua tubagens de dados. Consulte o artigo [Monitor e gerir tubagens Azure fábrica de dados utilizando a aplicação de monitorização](data-factory-monitor-manage-app.md) para obter detalhes sobre como utilizar a aplicação. 

> [AZURE.IMPORTANT] O ficheiro de entrada é eliminado quando no setor é processado com êxito. Por isso, se pretender executar novamente no setor ou não faça novamente o tutorial, carregue o ficheiro de entrada (input.log) para a pasta de inputdata do contentor adfgetstarted.

## <a name="data-factory-entities-in-the-template"></a>Entidades de fábrica do mesmo no modelo de dados de
### <a name="define-data-factory"></a>Definir a fábrica de dados
Definir uma fábrica de dados no modelo de Gestor de recursos, conforme mostrado no seguinte exemplo:  

    "resources": [
    {
        "name": "[variables('dataFactoryName')]",
        "apiVersion": "2015-10-01",
        "type": "Microsoft.DataFactory/datafactories",
        "location": "West US"
    }

O dataFactoryName é definida como: 
      
      "dataFactoryName": "[concat('HiveTransformDF', uniqueString(resourceGroup().id))]",

É uma cadeia exclusiva com base no ID de grupo de recursos.  

### <a name="defining-data-factory-entities"></a>Definir entidades fábrica de dados
As seguintes entidades fábrica de dados são definidas no modelo de JSON: 

- [Serviço de armazenamento ligado do Azure](#azure-storage-linked-service)
- [Serviço de ligadas HDInsight a pedido](#hdinsight-on-demand-linked-service)
- [Conjunto de dados de entrada de Blobs do Azure](#azure-blob-input-dataset)
- [Conjunto de dados de saída de Blobs do Azure](#azure-blob-output-dataset)
- [Pipeline de dados com uma atividade de cópia](#data-pipeline)

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

**ConnectionString** utiliza os parâmetros storageAccountName e storageAccountKey. Os valores para estes parâmetros passados utilizando um ficheiro de configuração. A definição também utiliza variáveis: azureStroageLinkedService e dataFactoryName definidos no modelo. 
    
#### <a name="hdinsight-on-demand-linked-service"></a>Serviço de ligadas HDInsight a pedido
Consulte o artigo [Calcular serviços ligados](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) artigo para obter mais detalhes acerca das propriedades JSON utilizadas para definir um serviço de ligadas a pedido HDInsight.  

      {
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "HDInsightOnDemand",
          "typeProperties": {
            "clusterSize": 1,
            "version": "3.2",
            "timeToLive": "00:05:00",
            "osType": "windows",
            "linkedServiceName": "[variables('azureStorageLinkedServiceName')]"
          }
        }
      }

Tenha em atenção os seguintes pontos: 

- A fábrica de dados cria um cluster de HDInsight **baseados no Windows** por si com o JSON acima. Pode também ter-criar um cluster de HDInsight **baseado em Linux** . Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes. 
- Pode utilizar o **seu próprio cluster HDInsight** em vez de utilizar um cluster de HDInsight a pedido. Consulte o artigo [Serviço ligadas HDInsight](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) para obter detalhes.
- HDInsight cluster cria um **contentor predefinido** no armazenamento de Blobs do que especificou na JSON (**linkedServiceName**). HDInsight não elimina neste contentor quando o cluster é eliminado. Este comportamento é. Com o serviço de HDInsight ligada a pedido, um HDInsight cluster é criado sempre que um setor necessita de ser processado, a menos que não existe uma existente live cluster (**timeToLive**) e é eliminado quando concluir o processamento.

    Como são processados os setores do gráfico mais, consulte contentores muitos no seu armazenamento de Blobs do Azure. Se não precisá-los para resolução de problemas das tarefas, poderá eliminá-los para reduzir o custo de armazenamento. Os nomes nestes contentores siga um padrão de: "adf**yourdatafactoryname**-**linkedservicename**- datetimestamp". Utilize ferramentas, como o [Explorador de armazenamento da Microsoft](http://storageexplorer.com/) para eliminar contentores no seu armazenamento de Blobs do Azure.

Consulte [a pedido HDInsight ligadas serviço](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para obter detalhes.



#### <a name="azure-blob-input-dataset"></a>Conjunto de dados de entrada de Blobs do Azure
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
          "typeProperties": {
            "fileName": "[parameters('inputBlobName')]",
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('inputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          },
          "external": true
        }
      }

Esta definição utiliza os seguintes parâmetros definidos no modelo de parâmetro: blobContainer, inputBlobFolder e inputBlobName. 

#### <a name="azure-blob-output-dataset"></a>Conjunto de dados de saída de Blobs do Azure
Especifique os nomes dos contentor blob e na pasta que contém os dados de saída. Ver [Propriedades do conjunto de dados de Blobs do Azure](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties) para obter mais detalhes acerca das propriedades JSON utilizadas para definir um conjunto de dados de Blobs do Azure.  

      {
        "type": "datasets",
        "name": "[variables('blobOutputDatasetName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "type": "AzureBlob",
          "linkedServiceName": "[variables('azureStorageLinkedServiceName')]",
          "typeProperties": {
            "folderPath": "[concat(parameters('blobContainer'), '/', parameters('outputBlobFolder'))]",
            "format": {
              "type": "TextFormat",
              "columnDelimiter": ","
            }
          },
          "availability": {
            "frequency": "Month",
            "interval": 1
          }
        }
      }

Esta definição utiliza os seguintes parâmetros definidos no modelo de parâmetro: blobContainer e outputBlobFolder. 

#### <a name="data-pipeline"></a>Pipeline de dados
Definir uma tubagem que transformar dados ao executar o script ramo num cluster Azure HDInsight a pedido. Consulte o artigo [Pipeline JSON](data-factory-create-pipelines.md#pipeline-json) para descrições dos elementos JSON utilizadas para definir uma tubagem neste exemplo. 

    {
        "type": "datapipelines",
        "name": "[variables('pipelineName')]",
        "dependsOn": [
          "[variables('dataFactoryName')]",
          "[variables('azureStorageLinkedServiceName')]",
          "[variables('hdInsightOnDemandLinkedServiceName')]",
          "[variables('blobInputDatasetName')]",
          "[variables('blobOutputDatasetName')]"
        ],
        "apiVersion": "2015-10-01",
        "properties": {
          "description": "Pipeline that transforms data using Hive script.",
          "activities": [
            {
              "type": "HDInsightHive",
              "typeProperties": {
                "scriptPath": "[concat(parameters('blobContainer'), '/', parameters('hiveScriptFolder'), '/', parameters('hiveScriptFile'))]",
                "scriptLinkedService": "[variables('azureStorageLinkedServiceName')]",
                "defines": {
                  "inputtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('inputBlobFolder'))]",
                  "partitionedtable": "[concat('wasb://', parameters('blobContainer'), '@', parameters('storageAccountName'), '.blob.core.windows.net/', parameters('outputBlobFolder'))]"
                }
              },
              "inputs": [
                {
                  "name": "[variables('blobInputDatasetName')]"
                }
              ],
              "outputs": [
                {
                  "name": "[variables('blobOutputDatasetName')]"
                }
              ],
              "policy": {
                "concurrency": 1,
                "retry": 3
              },
              "scheduler": {
                "frequency": "Month",
                "interval": 1
              },
              "name": "RunSampleHiveActivity",
              "linkedServiceName": "[variables('hdInsightOnDemandLinkedServiceName')]"
            }
          ],
          "start": "2016-10-01T00:00:00Z",
          "end": "2016-10-02T00:00:00Z",
          "isPaused": false
        }
      }

## <a name="reuse-the-template"></a>Reutilizar o modelo 
Tutorial, criou um modelo para a definição de entidades de fábrica de dados e um modelo para passar valores para os parâmetros. Para utilizar o mesmo modelo para implementar o entidades fábrica de dados em diferentes ambientes, cria um ficheiro de parâmetro para cada ambiente e utilizá-lo quando implementar o nesse ambiente.     

Exemplo:  

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Dev.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Test.json

    New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json -TemplateParameterFile ADFTutorialARM-Parameters-Production.json

Repare que o primeiro comando utiliza o ficheiro de parâmetro para o ambiente de desenvolvimento, uma segunda para o ambiente de teste e a uma terceira para o ambiente de produção.  

Também pode reutilizar o modelo para executar tarefas repetidas. Por exemplo, tem de criar muitos fábricas de dados com um ou mais tubagens que implementam a lógica mesmo mas cada fábrica dados utiliza armazenamento Azure diferente e contas de base de dados do Azure SQL. Neste cenário, utilize o mesmo modelo no mesmo ambiente (Dev Center, teste ou produção) com os ficheiros de diferentes parâmetros para criar fábricas de dados. 

## <a name="resource-manager-template-for-creating-a-gateway"></a>Modelo de Gestor de recursos para criar um gateway
Eis um modelo de Gestor de recursos de exemplo para criar um gateway lógico na parte posterior. Instalar um gateway no seu computador local ou Azure IaaS VM e registar o gateway com o serviço de fábrica de dados com uma chave. Para obter mais detalhes, consulte [mover dados entre no local e na nuvem](data-factory-move-data-between-onprem-and-cloud.md) .

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {
        },
        "variables": {
            "dataFactoryName":  "GatewayUsingArmDF",
            "apiVersion": "2015-10-01",
            "singleQuote": "'"
        },
        "resources": [
            {
                "name": "[variables('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "eastus",
                "resources": [
                    {
                        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
                        "type": "gateways",
                        "apiVersion": "[variables('apiVersion')]",
                        "name": "GatewayUsingARM",
                        "properties": {
                            "description": "my gateway"
                        }
                    }            
                ]
            }
        ]
    }

Este modelo cria uma fábrica de dados denominada GatewayUsingArmDF com um gateway com o nome: GatewayUsingARM. 

## <a name="see-also"></a>Consulte também
| Tópico | Descrição |
| :---- | :---- |
| [Atividades de transformação de dados](data-factory-data-transformation-activities.md) | Este artigo fornece uma lista de atividades de transformação de dados (como HDInsight ramo transformação utilizados neste tutorial) suportados pelo Azure fábrica de dados. |
| [Agendamento e execução](data-factory-scheduling-and-execution.md) | Este artigo explica os aspectos agendamento e execução de modelo de aplicação do Azure fábrica de dados. |
| [Tubagens](data-factory-create-pipelines.md) | Este artigo ajuda-o a compreender tubagens e atividades no Azure fábrica de dados e como utilizá-las para construir ponto a ponto condicionados por dados fluxos de trabalho para o seu cenário ou empresas. |
| [Conjuntos de dados](data-factory-create-datasets.md) | Este artigo ajuda-o a compreender conjuntos de dados no Azure fábrica de dados.
| [Monitorizar e gerir tubagens utilizando a aplicação de monitorização](data-factory-monitor-manage-app.md) | Este artigo descreve como monitorizar, gerir e depurar tubagens utilizando a monitorização e gestão de aplicação. 

  

