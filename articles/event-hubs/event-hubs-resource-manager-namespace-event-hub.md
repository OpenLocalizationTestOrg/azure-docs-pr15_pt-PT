<properties
    pageTitle="Criar um espaço de nomes do evento concentradores com concentrador de evento e consumidor grupo utilizando um modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Criar um espaço de nomes do evento concentradores com concentrador de evento e o grupo do consumidor utilizando o modelo de Gestor de recursos do Azure"
    services="event-hubs"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="08/31/2016"
    ms.author="sethm;shvija"/>

# <a name="create-an-event-hubs-namespace-with-event-hub-and-consumer-group-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes do evento concentradores com concentrador de evento e consumidor grupo utilizando um modelo de Gestor de recursos do Azure

Este artigo mostra como utilizar um modelo de Gestor de recursos do Azure que cria um espaço de nomes do evento concentradores com um concentrador de evento e um grupo do consumidor. Irá obter informações sobre como definir quais os recursos são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos

Para mais informações sobre a criação de modelos, consulte o artigo [modelos de criação de Gestor de recursos do Azure][].

Para o modelo concluído, consulte o artigo o [concentrador de evento e o modelo de grupo do consumidor][] GitHub.

>[AZURE.NOTE]
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de guia de introdução do Azure][] e procurar concentradores do evento.

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo irá implementar um espaço de nomes do evento concentradores com um concentrador de evento e um grupo do consumidor.

[Evento concentradores](../event-hubs/event-hubs-what-is-event-hubs.md) é um evento serviço utilizado para fornecer penetração evento e telemetria Azure na escala grandes, com baixa latência e fiabilidade alta de processamento.

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/event-hubs-resource-manager-namespace-event-hub/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-event-hubs-create-event-hub-and-consumer-group%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetros. Deve definir um parâmetro para esses valores serão variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores sempre irão permanecer da mesma. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo.

O modelo define seguintes parâmetros.

### <a name="eventhubnamespacename"></a>eventHubNamespaceName

O nome do evento concentradores espaço de nomes para criar.

```
"eventHubNamespaceName": {
"type": "string"
}
```

### <a name="eventhubname"></a>eventHubName

O nome do centro do evento de evento criado concentradores espaço de nomes.

```
"eventHubName": {
"type": "string"
}
```

### <a name="eventhubconsumergroupname"></a>eventHubConsumerGroupName

O nome do grupo do consumidor criado para o centro do evento.

```
"eventHubConsumerGroupName": {
"type": "string"
}
```

### <a name="apiversion"></a>apiVersion

A versão da API do modelo.

```
"apiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos para implementar

Cria um espaço de nomes do tipo **EventHubs**, com um concentrador de evento e um grupo do consumidor.

```
"resources":[  
      {  
         "apiVersion":"[variables('ehVersion')]",
         "name":"[parameters('namespaceName')]",
         "type":"Microsoft.EventHub/Namespaces",
         "location":"[variables('location')]",
         "sku":{  
            "name":"Standard",
            "tier":"Standard"
         },
         "resources":[  
            {  
               "apiVersion":"[variables('ehVersion')]",
               "name":"[parameters('eventHubName')]",
               "type":"EventHubs",
               "dependsOn":[  
                  "[concat('Microsoft.EventHub/namespaces/', parameters('namespaceName'))]"
               ],
               "properties":{  
                  "path":"[parameters('eventHubName')]"
               },
               "resources":[  
                  {  
                     "apiVersion":"[variables('ehVersion')]",
                     "name":"[parameters('consumerGroupName')]",
                     "type":"ConsumerGroups",
                     "dependsOn":[  
                        "[parameters('eventHubName')]"
                     ],
                     "properties":{  

                     }
                  }
               ]
            }
         ]
      }
   ],
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json
```

## <a name="azure-cli"></a>Clip Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri [https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-event-hubs-create-event-hub-and-consumer-group/azuredeploy.json][]
```

[Criação de modelos de Gestor de recursos do Azure]: ../resource-group-authoring-templates.md
[Guia de introdução Azure modelos]:  https://azure.microsoft.com/documentation/templates/?term=event+hubs
[Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
[Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
[Modelo de grupo de concentrador e consumidor do evento]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-event-hubs-create-event-hub-and-consumer-group/
