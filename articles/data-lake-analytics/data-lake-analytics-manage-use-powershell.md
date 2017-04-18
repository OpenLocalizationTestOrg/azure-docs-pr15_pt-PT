<properties 
   pageTitle="Gerir Azure dados Lake Analytics através do Azure PowerShell | Azure" 
   description="Saiba como gerir tarefas de dados Lake Analytics, origens de dados, os utilizadores. " 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Gerir Azure dados Lake Analytics através do PowerShell do Azure

[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Saiba como gerir contas do Azure dados Lake Analytics, origens de dados, os utilizadores e tarefas utilizando o PowerShell Azure. Para ver tópicos de gestão de utilizar outras ferramentas, clique no separador Seleccionar acima.

**Pré-requisitos**

Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).


<!-- ################################ -->
<!-- ################################ -->


##<a name="install-azure-powershell-10-or-greater"></a>Instalar o Azure PowerShell 1.0 ou superior

Consulte a secção pré-requisito de [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](powershell-azure-resource-manager.md#prerequisites).
    
## <a name="manage-accounts"></a>Gerir contas

Antes de executar qualquer trabalhos de análise de Lake de dados, tem de ter uma conta de análise de Lake de dados. Ao contrário Azure HDInsight, não pagar para uma conta de análise quando não está em execução uma tarefa.  Pode pagar apenas para o tempo quando estiver em execução uma tarefa.  Para mais informações, consulte o artigo [Descrição geral do Azure dados Lake Analytics](data-lake-analytics-overview.md).  

###<a name="create-accounts"></a>Criar contas

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeStoreName = "<DataLakeAccountName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $location = "<Microsoft Data Center>"
    
    Write-Host "Create a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location
    
    Write-Host "Create a Data Lake account ..."  -ForegroundColor Green
    New-AzureRmDataLakeStoreAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeStoreName `
        -Location $location 
    
    Write-Host "Create a Data Lake Analytics account ..."  -ForegroundColor Green
    New-AzureRmDataLakeAnalyticsAccount `
        -Name $dataLakeAnalyticsAccountName `
        -ResourceGroupName $resourceGroupName `
        -Location $location `
        -DefaultDataLake $dataLakeStoreName
    
    Write-Host "The newly created Data Lake Analytics account ..."  -ForegroundColor Green
    Get-AzureRmDataLakeAnalyticsAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $dataLakeAnalyticsAccountName  

Também pode utilizar um modelo de grupo de recursos do Azure. É um modelo para criar uma conta de análise de Lake de dados e a conta de arquivo de Lake dados dependente no [anexo A](#appendix-a). Guarde o modelo para um ficheiro com .json modelo e, em seguida, utilize o seguinte script do PowerShell para ligá-lo para:


    $AzureSubscriptionID = "<Your Azure Subscription ID>"
    
    $ResourceGroupName = "<New Azure Resource Group Name>"
    $Location = "EAST US 2"
    $DefaultDataLakeStoreAccountName = "<New Data Lake Store Account Name>"
    $DataLakeAnalyticsAccountName = "<New Data Lake Analytics Account Name>"
    
    $DeploymentName = "MyDataLakeAnalyticsDeployment"
    $ARMTemplateFile = "E:\Tutorials\ADL\ARMTemplate\azuredeploy.json"  # update the Json template path 
    
    Login-AzureRmAccount
    
    Select-AzureRmSubscription -SubscriptionId $AzureSubscriptionID
    
    # Create the resource group
    New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location
    
    # Create the Data Lake Analytics account with the default Data Lake Store account.
    $parameters = @{"adlAnalyticsName"=$DataLakeAnalyticsAccountName; "adlStoreName"=$DefaultDataLakeStoreAccountName}
    New-AzureRmResourceGroupDeployment -Name $DeploymentName -ResourceGroupName $ResourceGroupName -TemplateFile $ARMTemplateFile -TemplateParameterObject $parameters 

 
###<a name="list-account"></a>Conta de lista

Contas de análise de Lake de dados de lista na subscrição atual

    Get-AzureRmDataLakeAnalyticsAccount
    
O resultado:

    Id         : /subscriptions/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx/resourceGroups/learn1021rg/providers/Microsoft.DataLakeAnalytics/accounts/learn1021adla
    Location   : eastus2
    Name       : learn1021adla
    Properties : Microsoft.Azure.Management.DataLake.Analytics.Models.DataLakeAnalyticsAccountProperties
    Tags       : {}
    Type       : Microsoft.DataLakeAnalytics/accounts

Contas de análise de lista dados Lake dentro de um grupo de recursos específico

    Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName

Obter detalhes de uma conta de análise de Lake dados específica

    Get-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

Testar a existência de uma conta de análise de Lake dados específica

    Test-AzureRmDataLakeAnalyticsAccount -Name $adlAnalyticsAccountName

O cmdlet irá devolver **Verdadeiro** ou **Falso**.

###<a name="delete-data-lake-analytics-accounts"></a>Eliminar dados Lake Analytics contas

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Remove-AzureRmDataLakeAnalyticsAccount -Name $dataLakeAnalyticsAccountName 

Conta de análise de eliminar dados Lake não irá eliminar a conta de armazenamento de Lake dados dependente. O exemplo seguinte elimina a conta de análise de Lake de dados e a conta de dados Lake arquivo predefinida

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount

    Remove-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName 
    Remove-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName

<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-account-data-sources"></a>Gerir origens de dados de conta

A análise de dados Lake suporta atualmente seguintes origens de dados:

- [Arquivo de Lake de dados do Azure](../data-lake-store/data-lake-store-overview.md)
- [Armazenamento Azure](storage-introduction.md)

Quando cria uma conta de análise, tem de designar uma conta de armazenamento do Windows Azure dados Lake para ser a conta de armazenamento predefinido. A conta de arquivo de dados de Lake predefinida é utilizada para armazenar os registos de auditoria de metadados e de projeto de tarefa. Depois de ter criado uma conta de análise, pode adicionar contas adicionais de armazenamento de Lake de dados e/ou a conta de armazenamento do Windows Azure. 

### <a name="find-the-default-data-lake-store-account"></a>Localizar a conta de dados Lake arquivo predefinida

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $dataLakeStoreName = (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DefaultDataLakeAccount


### <a name="add-additional-azure-blob-storage-accounts"></a>Adicionar contas adicionais de armazenamento de Blobs do Azure

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureStorageAccountName = "<AzureStorageAccountName>"
    $AzureStorageAccountKey = "<AzureStorageAccountKey>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -AzureBlob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

### <a name="add-additional-data-lake-store-accounts"></a>Adicionar contas adicionais do arquivo de Lake de dados

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    $AzureDataLakeName = "<DataLakeStoreName>"
    
    Add-AzureRmDataLakeAnalyticsDataSource -ResourceGroupName $resourceGroupName -Account $dataLakeAnalyticAccountName -DataLake $AzureDataLakeName 

### <a name="list-data-sources"></a>Lista de origens de dados:

    $resourceGroupName = "<ResourceGroupName>"
    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.DataLakeStoreAccounts
    (Get-AzureRmDataLakeAnalyticsAccount -ResourceGroupName $resourceGroupName -Name $dataLakeAnalyticAccountName).Properties.StorageAccounts
    


<!-- ################################ -->
<!-- ################################ -->
## <a name="manage-jobs"></a>Gerir tarefas

Tem de ter uma conta de análise de Lake dados antes de poder criar uma tarefa.  Para mais informações, consulte o artigo [Gerir a análise de dados Lake contas](#manage-data-lake-analytics-accounts).

### <a name="list-jobs"></a>Lista tarefas

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -State Running, Queued
    #States: Accepted, Compiling, Ended, New, Paused, Queued, Running, Scheduling, Starting
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Result Cancelled
    #Results: Cancelled, Failed, None, Successed 
    
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Name <Job Name>
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -Submitter <Job submitter>

    # List all jobs submitted on January 1 (local time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter "2015/01/01"
        -SubmittedBefore "2015/01/02"   

    # List all jobs that succeeded on January 1 after 2 pm (UTC time)
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -State Ended
        -Result Succeeded
        -SubmittedAfter "2015/01/01 2:00 PM -0"
        -SubmittedBefore "2015/01/02 -0"

    # List all jobs submitted in the past hour
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -SubmittedAfter (Get-Date).AddHours(-1)

### <a name="get-job-details"></a>Obter detalhes sobre o trabalho

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"
    Get-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName -JobID <Job ID>
    
### <a name="submit-jobs"></a>Submeter tarefas

    $dataLakeAnalyticsAccountName = "<DataLakeAnalyticsAccountName>"

    #Pass script via path
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -ScriptPath $scriptPath

    #Pass script contents
    Submit-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -Name $jobName `
        -Script $scriptContents

> [AZURE.NOTE] A prioridade predefinida de uma tarefa é 1000 e o grau de predefinido de paralelismo para uma tarefa é 1.


### <a name="cancel-jobs"></a>Cancelar tarefas

    Stop-AzureRmDataLakeAnalyticsJob -Account $dataLakeAnalyticAccountName `
        -JobID $jobID


## <a name="manage-catalog-items"></a>Gerir os itens de catálogo

O catálogo de U SQL é utilizado para estruturar o código e dados para que podem ser partilhados por scripts U-SQL. O catálogo permite que o mais alto desempenho possível com dados no Azure dados Lake. Para mais informações, consulte o artigo [utilizar U-SQL catálogo](data-lake-analytics-use-u-sql-catalog.md).

###<a name="list-catalog-items"></a>Itens da lista de catálogo

    #List databases
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database
    
    
    
    #List tables
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo"

###<a name="get-catalog-item-details"></a>Obter os detalhes do item de catálogo 

    #Get a database
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"
    
    #Get a table
    Get-AzureRmDataLakeAnalyticsCatalogItem `
        -Account $adlAnalyticsAccountName `
        -ItemType Table `
        -Path "master.dbo.mytable"

###<a name="test-existence-of--catalog-item"></a>Testar a existência de item de catálogo

    Test-AzureRmDataLakeAnalyticsCatalogItem  `
        -Account $adlAnalyticsAccountName `
        -ItemType Database `
        -Path "master"

###<a name="create-catalog-secret"></a>Criar secreta de catálogo
    New-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")

### <a name="modify-catalog-secret"></a>Modificar secreta de catálogo
    Set-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master" `
            -Secret (Get-Credential -UserName "username" -Message "Enter the password")



###<a name="delete-catalog-secret"></a>Eliminar secreta de catálogo
    Remove-AzureRmDataLakeAnalyticsCatalogSecret  `
            -Account $adlAnalyticsAccountName `
            -DatabaseName "master"


## <a name="use-azure-resource-manager-groups"></a>Utilizar grupos de Gestor de recursos do Azure

Aplicações são normalmente constituídas por vários componentes, por exemplo uma aplicação web, base de dados, servidor de base de dados, armazenamento e 3º serviços de terceiros. Gestor de recursos Azure (processador) permite-lhe trabalhar com os recursos na sua aplicação como um grupo, designado de um grupo de recursos do Azure. Pode implementar, atualizar, monitorizar ou eliminar todos os recursos para a sua aplicação numa operação de única e coordenada. Utilizar um modelo para implementação e esse modelo pode ser útil ambientes diferentes, tais como teste, teste e de produção. Pode clarificar faturação da sua organização, veja os custos de agregadas para todo o grupo. Para mais informações, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md). 

Um serviço de dados Lake Analytics pode incluir os seguintes componentes:

- Conta de análise do Azure dados Lake
- Conta de armazenamento do Windows Azure dados Lake predefinidos necessários
- Contas de armazenamento adicional Azure dados Lake
- Contas adicionais de armazenamento do Windows Azure

Pode criar todos estes componentes num processador grupo para torná-las mais fácil de gerir.

![Conta de análise do Azure dados Lake e armazenamento](./media/data-lake-analytics-manage-use-portal/data-lake-analytics-arm-structure.png)

Uma conta de análise de Lake de dados e as contas de armazenamento dependentes devem ser colocadas no Centro de dados Azure do mesmo.
O grupo de processador no entanto pode estar num centro de dados diferentes.  

##<a name="see-also"></a>Consulte também 

- [Descrição geral da análise Lake de dados do Microsoft Azure](data-lake-analytics-overview.md)
- [Começar a trabalhar com dados Lake Analytics através do Portal do Azure](data-lake-analytics-get-started-portal.md)
- [Gerir Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-manage-use-portal.md)
- [Monitorizar e resolver problemas de tarefas do Azure dados Lake Analytics através do Portal do Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

##<a name="appendix-a---data-lake-analytics-arm-template"></a>Anexo A - modelo de dados Lake Analytics processador

O modelo de processador seguinte pode ser utilizado para implementar uma conta de análise de Lake de dados e a sua conta de arquivo de Lake dados dependente.  Guardá-lo como um ficheiro de json e, em seguida, utilize o script do PowerShell para ligar o modelo. Para mais informações, consulte o artigo [Implementar uma aplicação com o modelo de Gestor de recursos do Azure](../resource-group-template-deploy.md#deploy-with-powershell) e [modelos de criação de Gestor de recursos do Azure](../resource-group-authoring-templates.md).

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "adlAnalyticsName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Analytics account to create."
          }
        },
        "adlStoreName": {
          "type": "string",
          "metadata": {
            "description": "The name of the Data Lake Store account to create."
          }
        }
      },
      "resources": [
        {
          "name": "[parameters('adlStoreName')]",
          "type": "Microsoft.DataLakeStore/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ ],
          "tags": { }
        },
        {
          "name": "[parameters('adlAnalyticsName')]",
          "type": "Microsoft.DataLakeAnalytics/accounts",
          "location": "East US 2",
          "apiVersion": "2015-10-01-preview",
          "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
          "tags": { },
          "properties": {
            "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
            "dataLakeStoreAccounts": [
              { "name": "[parameters('adlStoreName')]" }
            ]
          }
        }
      ],
      "outputs": {
        "adlAnalyticsAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
        },
        "adlStoreAccount": {
          "type": "object",
          "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
        }
      }
    }

