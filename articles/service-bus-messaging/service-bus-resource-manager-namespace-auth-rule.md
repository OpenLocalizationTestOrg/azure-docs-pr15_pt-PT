<properties
    pageTitle="Criar uma regra de autorização de serviço Bus utilizando um modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Criar uma regra de autorização de Bus de serviço para o espaço de nomes e fila utilizando o modelo de Gestor de recursos do Azure"
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

# <a name="create-a-service-bus-authorization-rule-for-namespace-and-queue-using-an-azure-resource-manager-template"></a>Criar uma regra de autorização de Bus de serviço para o espaço de nomes e fila utilizando um modelo de Gestor de recursos do Azure

Este artigo mostra como utilizar um modelo de Gestor de recursos do Azure que cria uma [regra de autorização](service-bus-authentication-and-authorization.md#shared-access-signature-authentication) para um serviço Bus espaço de nomes e fila de espera. Irá obter informações sobre como definir quais os recursos são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos.

Para mais informações sobre a criação de modelos, consulte o artigo [modelos de criação de Gestor de recursos do Azure][].

Para o modelo concluído, consulte o artigo o [modelo de regra de auth serviço Bus][] GitHub.

>[AZURE.NOTE] Os seguintes modelos de Gestor de recursos do Azure estão disponíveis para transferência e implementação.
>
>-    [Criar um espaço de nomes do evento concentradores com um grupo do concentrador de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)
>-    [Criar um espaço de nomes de serviço Bus com fila de espera](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um espaço de nomes de serviço Bus com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
>-    [Criar um espaço de nomes de serviço Bus](service-bus-resource-manager-namespace.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de guia de introdução do Azure][] e procurar "Serviço Bus."

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo irá implementar uma regra de autorização de serviço Bus para um espaço de nomes e mensagens entidade (neste caso, uma fila).

Este modelo utiliza [Assinatura partilhadas do Access (SA)](service-bus-sas-overview.md) para autenticação. SA permite que as aplicações autenticar Bus serviço utilizando uma tecla de acesso configurada no espaço de nomes ou na entidade mensagens (fila ou tópico) que estão associados direitos específicos. Em seguida, pode utilizar esta tecla para gerar um token de SA clientes sucessivamente podem utilizar para autenticar bus de serviço.

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/service-bus-resource-manager-namespace-auth-rule/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F301-servicebus-create-authrule-namespace-and-queue%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de recursos do Azure, pode definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetros. Deve definir um parâmetro para esses valores serão variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que serão sempre manter-se da mesma. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo.

O modelo define seguintes parâmetros.

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do serviço Bus espaço de nomes para criar.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="namespaceauthorizationrulename"></a>namespaceAuthorizationRuleName 

O nome da regra autorização para o espaço de nomes.

```
"namespaceAuthorizationRuleName ": {
"type": "string"
}
```

### <a name="servicebusqueuename"></a>serviceBusQueueName

O nome da fila de espera no serviço Bus espaço de nomes.

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

Cria um espaço de nomes de serviço Bus padrão de tipo de **mensagens**e uma regra de autorização de Bus de serviço para o espaço de nomes e entidade.

```
"resources": [
        {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusNamespaceName')]",
            "type": "Microsoft.ServiceBus/namespaces",
            "location": "[variables('location')]",
            "kind": "Messaging",
            "sku": {
                "name": "StandardSku",
                "tier": "Standard"
            },
            "resources": [
                {
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusQueueName')]",
                    "type": "Queues",
                    "dependsOn": [
                        "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
                    ],
                    "properties": {
                        "path": "[parameters('serviceBusQueueName')]"
                    },
                    "resources": [
                        {
                            "apiVersion": "[variables('sbVersion')]",
                            "name": "[parameters('queueAuthorizationRuleName')]",
                            "type": "authorizationRules",
                            "dependsOn": [
                                "[parameters('serviceBusQueueName')]"
                            ],
                            "properties": {
                                "Rights": ["Listen"]
                            }
                        }
                    ]
                }
            ]
        }, {
            "apiVersion": "[variables('sbVersion')]",
            "name": "[variables('namespaceAuthRuleName')]",
            "type": "Microsoft.ServiceBus/namespaces/authorizationRules",
            "dependsOn": ["[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"],
            "location": "[resourceGroup().location]",
            "properties": {
                "Rights": ["Send"]
            }
        }
    ]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

### <a name="powershell"></a>PowerShell

```
New-AzureRmResourceGroupDeployment -ResourceGroupName \<resource-group-name\> -TemplateFile <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="azure-cli"></a>Clip Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/301-servicebus-create-authrule-namespace-and-queue/azuredeploy.json>
```

## <a name="next-steps"></a>Próximos passos

Agora que já criou e implementado recursos utilizando o Gestor de recursos do Azure, saiba como gerir estes recursos visualizando estes artigos:

- [Gerir o serviço Bus com PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerir os recursos de serviço Bus com o Explorador de Bus de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)
- [Autorização e Bus de serviço de autenticação](service-bus-authentication-and-authorization.md)

  [Criação de modelos de Gestor de recursos do Azure]: ../resource-group-authoring-templates.md
  [Guia de introdução Azure modelos]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Modelo do serviço Bus auth regra]: https://github.com/Azure/azure-quickstart-templates/blob/master/301-servicebus-create-authrule-namespace-and-queue/
