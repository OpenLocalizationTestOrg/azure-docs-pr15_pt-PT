<properties 
    pageTitle="Aprovisionar uma Cache Redis | Microsoft Azure" 
    description="Utilize o Gestor de recursos do Azure modelo para implementar uma Cache de Redis Azure." 
    services="app-service" 
    documentationCenter="" 
    authors="steved0x" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="cache" 
    ms.workload="web" 
    ms.tgt_pltfrm="cache-redis" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/27/2016" 
    ms.author="sdanie"/>

# <a name="create-a-redis-cache-using-a-template"></a>Criar uma Cache Redis através de um modelo

Neste tópico, saiba como criar um modelo de Gestor de recursos do Azure implementa uma Cache de Redis Azure. A cache pode ser utilizada com uma conta existente do armazenamento para manter os dados de diagnóstico. Saiba também como para definir os recursos que são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos.

Atualmente, as definições de diagnóstico são partilhadas para todas as caches na mesma região para uma subscrição. Atualizar uma cache na região afeta todos os outros caches na região.

Para mais informações sobre a criação de modelos, consulte o artigo [Criação de modelos de Gestor de recursos do Azure](../resource-group-authoring-templates.md).

Para o modelo concluído, consulte o artigo [Redis Cache modelo](https://github.com/Azure/azure-quickstart-templates/blob/master/101-redis-cache/azuredeploy.json).

>[AZURE.NOTE] Modelos de Gestor de recursos para a nova [camada Premium](cache-premium-tier-intro.md) estão disponíveis. 
>
>-    [Criar uma Cache de Redis Premium com clusters](https://azure.microsoft.com/documentation/templates/201-redis-premium-cluster-diagnostics/)
>-    [Criar a Cache de Redis Premium com recorrência de dados](https://azure.microsoft.com/documentation/templates/201-redis-premium-persistence/)
>-    [Criar a Cache de Redis Premium com VNet e clusters opcional](https://azure.microsoft.com/documentation/templates/201-redis-premium-vnet-cluster-diagnostics/)
>
>Para verificar os modelos mais recentes, consulte o artigo [Modelos de guia de introdução do Azure](https://azure.microsoft.com/documentation/templates/) e procurar `Redis Cache`.

## <a name="what-you-will-deploy"></a>O que irá implementar

Este modelo, irá implementar um Azure Redis Cache que utiliza uma conta existente do armazenamento para os dados de diagnóstico.

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/cache-redis-cache-arm-provision/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada parâmetros que contém todos os valores de parâmetro.
Deve definir um parâmetro para esses valores variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que permaneçam sempre o mesmo. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo. 


[AZURE.INCLUDE [app-service-web-deploy-redis-parameters](../../includes/cache-deploy-parameters.md)]

### <a name="rediscachelocation"></a>redisCacheLocation

A localização da Redis Cache. Para um melhor desempenho, utilize a mesma localização como a aplicação para ser utilizada com a cache.

    "redisCacheLocation": {
      "type": "string"
    }

### <a name="existingdiagnosticsstorageaccountname"></a>existingDiagnosticsStorageAccountName

O nome da conta de armazenamento existente para utilizar para diagnósticos. 

    "existingDiagnosticsStorageAccountName": {
      "type": "string"
    }

### <a name="enablenonsslport"></a>enableNonSslPort

Um valor booleano que indica se a autorização de acesso através das portas não SSL.

    "enableNonSslPort": {
      "type": "bool"
    }

### <a name="diagnosticsstatus"></a>diagnosticsStatus

Um valor que indica se diagnósticos está ativado. Utilizar ou DESATIVE.

    "diagnosticsStatus": {
      "type": "string",
      "defaultValue": "ON",
      "allowedValues": [
            "ON",
            "OFF"
        ]
    }
    
## <a name="resources-to-deploy"></a>Recursos para implementar

### <a name="redis-cache"></a>Redis Cache

Cria a Cache de Azure Redis.

    {
      "apiVersion": "2015-08-01",
      "name": "[parameters('redisCacheName')]",
      "type": "Microsoft.Cache/Redis",
      "location": "[parameters('redisCacheLocation')]",
      "properties": {
        "enableNonSslPort": "[parameters('enableNonSslPort')]",
        "sku": {
          "capacity": "[parameters('redisCacheCapacity')]",
          "family": "[parameters('redisCacheFamily')]",
          "name": "[parameters('redisCacheSKU')]"
        }
      },
      "resources": [
        {
          "apiVersion": "2015-07-01",
          "type": "Microsoft.Cache/redis/providers/diagnosticsettings",
          "name": "[concat(parameters('redisCacheName'), '/Microsoft.Insights/service')]",
          "location": "[parameters('redisCacheLocation')]",
          "dependsOn": [
            "[concat('Microsoft.Cache/Redis/', parameters('redisCacheName'))]"
          ],
          "properties": {
            "status": "[parameters('diagnosticsStatus')]",
            "storageAccountName": "[parameters('existingDiagnosticsStorageAccountName')]"
          }
        }
      ]
    }



## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)] 

### <a name="powershell"></a>PowerShell

    New-AzureRmResourceGroupDeployment -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -ResourceGroupName ExampleDeployGroup -redisCacheName ExampleCache -redisCacheLocation "West US"

### <a name="azure-cli"></a>Clip Azure

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-redis-cache/azuredeploy.json -g ExampleDeployGroup


