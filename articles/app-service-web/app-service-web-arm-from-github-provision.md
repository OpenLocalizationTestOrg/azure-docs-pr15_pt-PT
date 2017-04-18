<properties 
    pageTitle="Implementar uma aplicação web que está associada a um repositório de GitHub" 
    description="Utilize um modelo de Gestor de recursos do Azure para implementar uma aplicação web que contém um projeto a partir de um repositório de GitHub." 
    services="app-service" 
    documentationCenter="" 
    authors="cephalin" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/27/2016" 
    ms.author="cephalin"/>

# <a name="deploy-a-web-app-linked-to-a-github-repository"></a>Implementar uma aplicação web ligada a um repositório de GitHub

Este tópico vai aprender a criar um modelo de Gestor de recursos do Azure implementa uma aplicação web que está associada a um projeto num repositório de GitHub. Irá obter informações sobre como definir quais os recursos são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos.

Para mais informações sobre a criação de modelos, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md).

Para o modelo concluído, consulte o artigo [Web App ligadas a GitHub modelo](https://github.com/Azure/azure-quickstart-templates/blob/master/201-web-app-github-deploy/azuredeploy.json).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 

## <a name="what-you-will-deploy"></a>O que irá implementar

Com este modelo irá implementar uma aplicação web que contém o código de um projeto no GitHub.

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/app-service-web-arm-from-github-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-web-app-github-deploy%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

[AZURE.INCLUDE [app-service-web-deploy-web-parameters](../../includes/app-service-web-deploy-web-parameters.md)]

### <a name="repourl"></a>repoURL

O URL para o repositório de GitHub que contém o projeto para implementar. Este parâmetro contém um valor predefinido, mas este valor destina-se apenas a mostrar-lhe como fornecer o URL para o repositório. Pode utilizar este valor quando testar o modelo mas pretender fornecer o URL do seu próprio repositório quando trabalhar com o modelo.

    "repoURL": {
        "type": "string",
        "defaultValue": "https://github.com/davidebbo-test/Mvc52Application.git"
    }

### <a name="branch"></a>ramo

Ramo do repositório a utilizar quando implementar a aplicação. O valor predefinido é principal, mas pode fornecer o nome de qualquer ramo no repositório de que pretende implementar.

    "branch": {
        "type": "string",
        "defaultValue": "master"
    }
    
## <a name="resources-to-deploy"></a>Recursos para implementar

[AZURE.INCLUDE [app-service-web-deploy-web-host](../../includes/app-service-web-deploy-web-host.md)]

### <a name="web-app"></a>Aplicação Web

Cria a aplicação web que está ligada ao projecto no GitHub. 

Especifique o nome da aplicação web através do parâmetro **nome do site** e a localização da aplicação web através do parâmetro **siteLocation** . No elemento de **dependsOn** , o modelo define a aplicação web como dependentes o serviço de alojamento plano. Porque é dependente o plano para o alojamento, a aplicação web não é criada até que o plano para o alojamento tenha terminado a ser criado. O elemento **dependsOn** só é utilizado para especificar ordem de implementação. Se não marcar a aplicação web como dependentes no plano do alojamento, Azure recurso Mananger irá tentar criar ambos os recursos ao mesmo tempo e poderá receber um erro se a aplicação web é criada antes do plano de alojamento.

A aplicação web também tem um recurso de subordinados que é definido na secção de **recursos** abaixo. Este recurso subordinado define o controlo de origem para o projeto implementado com a aplicação web. Este modelo, o controlo da origem está ligado a um repositório de GitHub específico. Repositório de GitHub estiver definido com o código **"RepoUrl": "https://github.com/davidebbo-test/Mvc52Application.git"** poderá disco rígido código o URL do repositório quando pretender criar um modelo que repetidamente implementa num único projeto enquanto que exige o número mínimo de parâmetros.
Em vez de disco rígido codificação o URL do repositório, pode adicionar um parâmetro para o URL do repositório e utilizar esse valor para a propriedade **RepoUrl** .

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('siteName')]",
      "type": "Microsoft.Web/sites",
      "location": "[resourceGroup().location]",
      "dependsOn": [
        "[resourceId('Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
      ],
      "properties": {
        "serverFarmId": "[parameters('hostingPlanName')]"
      },
      "resources": [
        {
          "apiVersion": "2015-08-01",
          "name": "web",
          "type": "sourcecontrols",
          "dependsOn": [
            "[resourceId('Microsoft.Web/Sites', parameters('siteName'))]"
          ],
          "properties": {
            "RepoUrl": "[parameters('repoURL')]",
            "branch": "[parameters('branch')]",
            "IsManualIntegration": true
          }
        }
      ]
    }

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json -siteName ExampleSite -hostingPlanName ExamplePlan -siteLocation "West US" -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Clip Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-web-app-github-deploy/azuredeploy.json


 
