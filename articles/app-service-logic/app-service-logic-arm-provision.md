<properties 
    pageTitle="Criar uma aplicação de lógica de utilização de modelos de Gestor de recursos do Azure na aplicação de serviço de Azure | Microsoft Azure" 
    description="Utilize um modelo de Gestor de recursos do Azure para implementar uma aplicação de lógica vazia para a definição de fluxos de trabalho." 
    services="logic-apps" 
    documentationCenter="" 
    authors="MSFTMan" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="logic-apps" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/25/2016" 
    ms.author="deonhe"/>

# <a name="create-a-logic-app-using-a-template"></a>Criar uma aplicação de lógica através de um modelo

Utilize um modelo de Gestor de recursos do Azure para criar uma aplicação vazia lógica que pode ser utilizada para definir fluxos de trabalho. Pode definir quais os recursos são implementados e como definir parâmetros são especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos.

Para obter mais detalhes sobre as propriedades da aplicação de lógica, consulte o artigo [API de gestão de fluxo de trabalho de aplicação de lógica](https://msdn.microsoft.com/library/azure/mt643788.aspx). 

Para obter exemplos da definição de si, consulte o artigo [definições de aplicação de lógica de autor](app-service-logic-author-definitions.md). 

Para mais informações sobre a criação de modelos, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md).

Para o modelo concluído, consulte o [modelo de aplicação de lógica](https://github.com/Azure/azure-quickstart-templates/blob/master/101-logic-app-create/azuredeploy.json).

## <a name="what-you-will-deploy"></a>O que irá implementar

Com este modelo, implementar uma aplicação de lógica.

Para executar a implementação automaticamente, selecione o botão seguinte:  

[![Implementar Azure](media/app-service-logic-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-logic-app-create%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

[AZURE.INCLUDE [app-service-logic-deploy-parameters](../../includes/app-service-logic-deploy-parameters.md)]

### <a name="testuri"></a>testUri

     "testUri": {
        "type": "string",
        "defaultValue": "http://azure.microsoft.com/en-us/status/feed/"
      }
    
## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="logic-app"></a>Aplicação de lógica

Cria a aplicação de lógica.

Os modelos utiliza um valor de parâmetro para o nome da aplicação lógica. Define a localização da aplicação lógica na mesma localização, como o grupo de recursos. 

Esta definição determinada executa uma vez por hora e a localização especificada no parâmetro **testUri** o ping. 

    {
      "type": "Microsoft.Logic/workflows",
      "apiVersion": "2016-06-01",
      "name": "[parameters('logicAppName')]",
      "location": "[resourceGroup().location]",
      "tags": {
        "displayName": "LogicApp"
      },
      "properties": {
        "definition": {
          "$schema": "http://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "testURI": {
              "type": "string",
              "defaultValue": "[parameters('testUri')]"
            }
          },
          "triggers": {
            "recurrence": {
              "type": "recurrence",
              "recurrence": {
                "frequency": "Hour",
                "interval": 1
              }
            }
          },
          "actions": {
            "http": {
              "type": "Http",
              "inputs": {
                "method": "GET",
                "uri": "@parameters('testUri')"
              },
              "runAfter": {}
            }
          },
          "outputs": {}
        },
        "parameters": {}
      }
    }


## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -ResourceGroupName ExampleDeployGroup

### <a name="azure-cli"></a>Clip Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-logic-app-create/azuredeploy.json -g ExampleDeployGroup


 
