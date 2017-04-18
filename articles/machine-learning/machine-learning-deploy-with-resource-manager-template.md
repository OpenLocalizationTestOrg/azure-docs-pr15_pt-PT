<properties
    pageTitle="Implementar a área de trabalho utilizando o modelo do Azure Gestor de recursos de aprendizagem automática | Microsoft Azure"
    description="Como implementar uma área de trabalho para formação de máquina Azure utilizando o modelo de Gestor de recursos do Azure"
    services="machine-learning"
    documentationCenter=""
    authors="ahgyger"
    manager="haining"
    editor="garye"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/23/2016"
    ms.author="ahgyger"/>
# <a name="deploy-machine-learning-workspace-using-azure-resource-manager"></a>Implementar a área de trabalho utilizando Azure Gestor de recursos de aprendizagem automática

## <a name="introduction"></a>Introdução
Utilizar um Gestor de recursos do Azure modelo de implementação do guarda tempo por que lhe dá uma maneira de dimensionável implementar interligados componentes com a validação de uma e tente novamente mecanismo. Para configurar áreas de trabalho do Azure máquina formação, por exemplo, tem primeiro de configurar uma conta de armazenamento Azure e, em seguida, implementar a área de trabalho. Imagine fazê-lo manualmente em centenas de áreas de trabalho. É uma alternativa mais fácil utilizar um modelo de Gestor de recursos do Azure para implementar uma área de trabalho de aprendizagem do Azure máquina e todas as suas dependências. Este artigo guiá-lo durante este processo passo a passo. Para uma descrição geral do Gestor de recursos do Azure, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

## <a name="step-by-step-create-a-machine-learning-workspace"></a>Passo a passo: criar uma área de trabalho de aprendizagem automática
Iremos irá criar um grupo de recursos Azure, em seguida, implementar uma nova conta de armazenamento Azure e uma nova Azure máquina aprendizagem área de trabalho através de um modelo de Gestor de recursos. Uma vez concluída a implementação, recomendamos que imprimam informações importantes sobre as áreas de trabalho que foram criadas (a chave primária, o workspaceID e o URL para a área de trabalho).

### <a name="create-an-azure-resource-manager-template"></a>Criar um modelo de Gestor de recursos do Azure
Uma área de trabalho de aprendizagem máquina requer uma conta de armazenamento Azure para armazenar o conjunto de dados ligado à mesma.
O modelo seguinte utiliza o nome do grupo de recursos para gerar o nome da conta de armazenamento e o nome da área de trabalho.  Também utiliza o nome da conta de armazenamento como uma propriedade quando criar a área de trabalho.

```
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "variables": {
        "namePrefix": "[resourceGroup().name]",
        "location": "[resourceGroup().location]",
        "mlVersion": "2016-04-01",
        "stgVersion": "2015-06-15",
        "storageAccountName": "[concat(variables('namePrefix'),'stg')]",
        "mlWorkspaceName": "[concat(variables('namePrefix'),'mlwk')]",
        "mlResourceId": "[resourceId('Microsoft.MachineLearning/workspaces', variables('mlWorkspaceName'))]",
        "stgResourceId": "[resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName'))]",
        "storageAccountType": "Standard_LRS"
    },
    "resources": [
        {
            "apiVersion": "[variables('stgVersion')]",
            "name": "[variables('storageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "[variables('mlVersion')]",
            "type": "Microsoft.MachineLearning/workspaces",
            "name": "[variables('mlWorkspaceName')]",
            "location": "[variables('location')]",
            "dependsOn": ["[variables('stgResourceId')]"],
            "properties": {
                "UserStorageAccountId": "[variables('stgResourceId')]"
            }
        }
    ],
    "outputs": {
        "mlWorkspaceObject": {"type": "object", "value": "[reference(variables('mlResourceId'), variables('mlVersion'))]"},
        "mlWorkspaceToken": {"type": "string", "value": "[listWorkspaceKeys(variables('mlResourceId'), variables('mlVersion')).primaryToken]"},
        "mlWorkspaceWorkspaceID": {"type": "string", "value": "[reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId]"},
        "mlWorkspaceWorkspaceLink": {"type": "string", "value": "[concat('https://studio.azureml.net/Home/ViewWorkspace/', reference(variables('mlResourceId'), variables('mlVersion')).WorkspaceId)]"}
    }
}

```
Guarde este modelo como ficheiro mlworkspace.json em c:\temp\.

### <a name="deploy-the-resource-group-based-on-the-template"></a>Implementar o grupo de recursos, baseado no modelo
* Abrir PowerShell
* Instalar módulos para o Gestor de recursos do Azure e gestão de serviço do Azure  

```
# Install the Azure Resource Manager modules from the PowerShell Gallery (press “A”)
Install-Module AzureRM -Scope CurrentUser

# Install the Azure Service Management modules from the PowerShell Gallery (press “A”)
Install-Module Azure -Scope CurrentUser
```

   Estes passos transfira e instale os módulos necessários para concluir os passos restantes. Só necessita de ser executadas uma vez no ambiente onde está a executar os comandos do PowerShell.   

* Autenticar Azure  

```
# Authenticate (enter your credentials in the pop-up window)
Add-AzureRmAccount
```
Este passo tem de ser repetida para cada sessão. Uma vez autenticado, as informações de subscrição devem ser apresentadas.

![Conta Azure][1]

Agora que temos acesso ao Azure, podemos criar o grupo de recursos.

* Criar um grupo de recursos

```
$rg = New-AzureRmResourceGroup -Name "uniquenamerequired523" -Location "South Central US"
$rg
```

Certifique-se de que o grupo de recursos está aprovisionado corretamente. **ProvisioningState** deve ser "foi bem sucedida."
O nome do grupo de recursos é utilizado pelo modelo para gerar o nome da conta de armazenamento. O nome da conta de armazenamento tem de estar entre 3 e 24 carateres de comprimento e utilizar números e letras minúsculas apenas.

![Grupo de recursos][2]

* Utilizando a implementação de grupo de recursos, implemente uma nova área de trabalho de formação de máquina.

```
# Create a Resource Group, TemplateFile is the location of the JSON template.
$rgd = New-AzureRmResourceGroupDeployment -Name "demo" -TemplateFile "C:\temp\mlworkspace.json" -ResourceGroupName $rg.ResourceGroupName
```

Assim que a implementação estiver concluída, é muito simples, aceda às propriedades da área de trabalho que implementado. Por exemplo, pode aceder a Token de chave primária.

```
# Access Azure ML Workspace Token after its deployment.
$rgd.Outputs.mlWorkspaceToken.Value
```

Outra forma de obter os tokens da área de trabalho existente é utilizar o comando invocar AzureRmResourceAction. Por exemplo, pode listar os tokens principais e secundários de todas as áreas de trabalho.

```  
# List the primary and secondary tokens of all workspaces
Get-AzureRmResource |? { $_.ResourceType -Like "*MachineLearning/workspaces*"} |% { Invoke-AzureRmResourceAction -ResourceId $_.ResourceId -Action listworkspacekeys -Force}  
```
Depois da área de trabalho está aprovisionada, também pode automatizar muitas tarefas Azure máquina aprendizagem Studio a utilizar o [Módulo do PowerShell para Azure máquina aprendizagem](http://aka.ms/amlps).

## <a name="next-steps"></a>Próximos passos 
* Saiba mais sobre [a cocriação modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md). 
* Ter um olhar sobre o [Repositório de modelos de guia de introdução do Azure](https://github.com/Azure/azure-quickstart-templates). 
* Veja este vídeo sobre como [Gestor de recursos do Azure](https://channel9.msdn.com/Events/Ignite/2015/C9-39). 
 
<!--Image references-->
[1]: ../media/machine-learning-deploy-with-resource-manager-template/azuresubscription.png
[2]: ../media/machine-learning-deploy-with-resource-manager-template/resourcegroupprovisioning.png


<!--Link references-->
