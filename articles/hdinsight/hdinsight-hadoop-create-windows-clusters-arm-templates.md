<properties
   pageTitle="Criar clusters baseados no Windows Hadoop no HDInsight utilizar modelos de Gestor de recursos do Azure | Microsoft Azure"
    description="Saiba como criar clusters para utilizar o Gestor de recursos do Azure modelos do Azure HDInsight."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="10/19/2016"
   ms.author="jgao"/>

# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-azure-resource-manager-templates"></a>Criar clusters baseados no Windows Hadoop no HDInsight utilizar modelos de Gestor de recursos do Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Saiba como criar clusters HDInsight utilizar modelos de Gestor de recursos do Azure. Para mais informações, consulte o artigo [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md). Para outras criação do cluster ferramentas e funcionalidades, clique em Selecionar o separador na parte superior desta página ou consulte [métodos de criação de Cluster](hdinsight-provision-clusters.md#cluster-creation-methods).

##<a name="prerequisites"></a>Pré-requisitos:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Antes de começar as instruções neste artigo, tem de ter o seguinte procedimento:

- [Subscrição do azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- Clip Azure PowerShell ou do Azure

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)] 

### <a name="access-control-requirements"></a>Requisitos de controlo de acesso

[AZURE.INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="resource-manager-templates"></a>Modelos de Gestor de recursos

Modelo de Gestor de recursos torna mais fácil para criar HDInsight clusters, os respetivos recursos dependentes (tal como a conta de armazenamento predefinida) e outros recursos (como Azure a base de dados de SQL para utilizar Apache Sqoop) para a sua aplicação numa operação de única e coordenada. No modelo, tem de definir os recursos que são necessários para a aplicação e especificar parâmetros de implementação para valores para diferentes ambientes de entrada. O modelo é composta por JSON e expressões que pode utilizar para construir valores para a sua implementação.

Um modelo de Gestor de recursos para criar um cluster de HDInsight e a conta de armazenamento do Windows Azure dependente pode ser encontrado no [Anexo-A](#appx-a-arm-template). Utilize um editor de texto para guardar o modelo para um ficheiro no seu estação de trabalho. Vai aprender ligar o modelo com várias ferramentas.

Para obter mais informações sobre o modelo de Gestor de recursos, consulte o artigo

- [Modelos de autor do Gestor de recursos do Azure](../resource-group-authoring-templates.md)
- [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md)


## <a name="deploy-with-powershell"></a>Implementar com PowerShell

O procedimento seguinte cria um cluster de HDInsight.

**Para implementar um cluster utilizando o modelo de Gestor de recursos**

1. Guarde o ficheiro json no [anexo A](#appx-a-arm-template) sua estação de trabalho.
2. Defina os parâmetros caso seja necessário.
3. Execute o modelo utilizando o seguinte script do PowerShell:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

    O script do PowerShell configura apenas o nome do cluster e o nome da conta de armazenamento.  Pode definir outros valores no modelo de Gestor de recursos. 
    
Para mais informações, consulte o artigo [Implementar com PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## <a name="deploy-with-azure-cli"></a>Implementar com clip Azure

O exemplo seguinte cria um cluster e sua conta de armazenamento dependentes e contentor ao contactar um modelo de Gestor de recursos:

    azure login
    azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{\"clusterName\":{\"value\":\"hdi1229win\"},\"clusterStorageAccountName\":{\"value\":\"hdi1229store\"},\"location\":{\"value\":\"East US 2\"},\"clusterLoginPassword\":{\"value\":\"Pass@word1\"}}"





## <a name="deploy-with-rest-api"></a>Implementar com a REST API

Consulte o artigo [Implementar com a API REST](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## <a name="deploy-with-visual-studio"></a>Implementar com o Visual Studio

Com o Visual Studio, pode criar um projeto de grupo de recursos e implemente-o Azure através da interface de utilizador. Selecionar o tipo de recursos para incluir no seu projeto e os recursos são automaticamente adicionados ao modelo de Gestor de recursos. O project fornece também um script PowerShell para implementar o modelo.

Para obter uma introdução à utilização do Visual Studio com grupos de recursos, consulte [criar e implementar a grupos de recursos Azure através do Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##<a name="next-steps"></a>Próximos passos
Neste artigo, aprendeu várias formas para criar um cluster de HDInsight. Para saber mais, consulte os artigos seguintes:


- Para obter um exemplo de implementação do recursos através da biblioteca de cliente .NET, consulte o artigo [Implementar recursos utilizar bibliotecas .NET e um modelo](../virtual-machines/virtual-machines-windows-csharp-template.md).
- Para um exemplo aprofundado de implementar uma aplicação, consulte o artigo [aprovisionar e implementar microservices seja previsível no Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Para obter orientações sobre como implementar a solução para diferentes ambientes, consulte o artigo [desenvolvimento e ambientes de teste no Microsoft Azure](../solution-dev-test-environments.md).
- Para saber mais sobre as secções do modelo de Gestor de recursos do Azure, consulte o artigo [modelos de criação](../resource-group-authoring-templates.md).
- Para obter uma lista das funções que pode utilizar um modelo de Gestor de recursos do Azure, consulte [funções de modelo](../resource-group-template-functions.md).



##<a name="appx-a-resource-manager-template"></a>Modelo de Gestor de recursos AppX-r:

O modelo de Gestor de recursos do Azure seguinte cria um cluster de Hadoop baseados no Windows com a conta de armazenamento Azure dependentes.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

