<properties
    pageTitle="Criar um espaço de nomes de serviço Bus com fila utilizando um modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Criar um espaço de nomes de serviço Bus e uma fila utilizando o modelo de Gestor de recursos do Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/14/2016"
    ms.author="sethm;shvija"/>

# <a name="create-a-service-bus-namespace-and-a-queue-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes de serviço Bus e uma fila utilizando um modelo de Gestor de recursos do Azure

Este artigo mostra como utilizar um modelo de Gestor de recursos do Azure que cria um espaço de nomes de serviço Bus e uma fila. Irá obter informações sobre como definir quais os recursos são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos.

Para mais informações sobre a criação de modelos, consulte o artigo [modelos de criação de Gestor de recursos do Azure][].

Para o modelo concluído, consulte o artigo o [modelo de espaço de nomes e fila de serviço Bus][] GitHub.

>[AZURE.NOTE] Os seguintes modelos de Gestor de recursos do Azure estão disponíveis para transferência e implementação.
>
>-    [Criar um espaço de nomes de serviço Bus com regras de autorização e fila de espera](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Criar um espaço de nomes de serviço Bus com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
>-    [Criar um espaço de nomes de serviço Bus](service-bus-resource-manager-namespace.md)
>-    [Criar um espaço de nomes do evento concentradores com um grupo do concentrador de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de guia de introdução do Azure][] e procurar "Serviço Bus."

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo irá implementar um espaço de nomes de serviço Bus com uma fila.

[Serviço Bus filas](service-bus-queues-topics-subscriptions.md#queues) oferta do primeiro entrega de mensagens do primeiro saída (FIFO) para um ou mais consumidores concorrentes.

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/service-bus-resource-manager-namespace-queue/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetros. Deve definir um parâmetro para esses valores serão variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que serão sempre manter-se da mesma. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo.

O modelo define seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do serviço Bus espaço de nomes para criar.

```
"serviceBusNamespaceName": {
"type": "string",
"metadata": { 
    "description": "Name of the Service Bus namespace" 
    }
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

O nome da fila criada no espaço de nomes Bus de serviço.

```
"serviceBusQueueName": {
"type": "string"
}
```

### <a name="servicebusapiversion"></a>serviceBusApiVersion

A versão de serviço Bus API do modelo.

```
"serviceBusApiVersion": {
"type": "string"
}
```

## <a name="resources-to-deploy"></a>Recursos para implementar

Cria um espaço de nomes de serviço Bus padrão de tipo de **mensagens**, com uma fila.

```
"resources ": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]",
            }
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Clip Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Próximos passos

Agora que já criou e implementado recursos utilizando o Gestor de recursos do Azure, saiba como gerir estes recursos visualizando estes artigos:

- [Gerir o serviço Bus com PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerir os recursos de serviço Bus com o Explorador de Bus de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Criação de modelos de Gestor de recursos do Azure]: ../resource-group-authoring-templates.md
  [Modelo de espaço de nomes e fila Bus de serviço]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/
  [Guia de introdução Azure modelos]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus queues]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
