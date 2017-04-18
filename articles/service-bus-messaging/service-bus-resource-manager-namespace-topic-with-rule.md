<properties
    pageTitle="Criar um espaço de nomes de serviço Bus com tópico, subscrição e regra utilizando um modelo de Gestor de recursos do Azure | Microsoft Azure"
    description="Criar um espaço de nomes de serviço Bus com tópico, subscrição e regra de utilizando o modelo de Gestor de recursos do Azure"
    services="service-bus"
    documentationCenter=".net"
    authors="ShubhaVijayasarathy"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="tbd"
    ms.topic="article"
    ms.tgt_pltfrm="dotnet"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="ShubhaVijayasarathy"/>

# <a name="create-a-service-bus-namespace-with-topic-subscription-and-rule-using-an-azure-resource-manager-template"></a>Criar um espaço de nomes de serviço Bus com tópico, subscrição e uma regra utilizando um modelo de Gestor de recursos do Azure

Este artigo mostra como utilizar um modelo de Gestor de recursos do Azure que cria um espaço de nomes de serviço Bus com um tópico, subscrição e uma regra (filtro). Saiba como definir quais os recursos são implementados e como definir parâmetros que estão especificados quando a implementação é executada. Pode utilizar este modelo para a suas própria implementações ou personalizá-lo aos seus requisitos

Para obter mais informações sobre a criação de modelos, consulte [Gestor de recursos do Azure criação de modelos][].

Para mais informações sobre as práticas e os padrões de recursos do Azure convenções de nomenclatura, consulte o artigo [Azure convenções de nomenclatura de recursos][].

Para o modelo concluído, consulte o modelo de [serviço Bus espaço de nomes com tópico, a subscrição e a regra][] .

>[AZURE.NOTE] Os seguintes modelos de Gestor de recursos do Azure estão disponíveis para transferência e implementação.
>
>-    [Criar um espaço de nomes de serviço Bus com regras de autorização e fila de espera](service-bus-resource-manager-namespace-auth-rule.md)
>-    [Criar um espaço de nomes de serviço Bus com fila de espera](service-bus-resource-manager-namespace-queue.md)
>-    [Criar um espaço de nomes de serviço Bus](service-bus-resource-manager-namespace.md)
>-    [Criar um espaço de nomes de serviço Bus com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
>
>Para verificar os modelos mais recentes, visite a Galeria de [Modelos de guia de introdução do Azure][] e procurar Bus de serviço.

## <a name="what-will-you-deploy"></a>O que irá implementar?

Com este modelo, implementar um espaço de nomes de serviço Bus com tópico, subscrição e uma regra (filtro).

[Subscrições do serviço Bus tópicos e](service-bus-queues-topics-subscriptions.md#topics-and-subscriptions) fornecer um formulário um-para-muitos de comunicação, num padrão *Publicar/subscrever* . Quando utilizar tópicos e subscrições, componentes de uma aplicação distribuída não comunicar diretamente entre si, em vez disso, eles trocarem mensagens através do tópico que age como um intermédio. Uma subscrição de um tópico é semelhante a uma fila de espera virtual que recebe cópias das mensagens que foram enviadas para o tópico. Um filtro num permite a subscrição que para especificar quais as mensagens enviadas para um tópico deverão aparecer dentro de uma subscrição de tópico específico.

## <a name="what-are-rules-filters"></a>O que são regras (filtros)?

Em muitos cenários, todas as mensagens com características específicas têm de ser processadas formas diferentes. Para permitir que este, pode configurar as subscrições para encontrar mensagens que tenham pretendido propriedades e, em seguida, executar determinadas modificações as propriedades. Enquanto subscrições do serviço Bus ver todas as mensagens enviadas para o tópico, pode copiar apenas um subconjunto dessas mensagens para fila de espera virtual da subscrição. Isto é feito através dos filtros de subscrição. Para saber mais sobre rules(filters), consulte o artigo [filas Bus de serviço, tópicos e subscrições][].

Para executar a implementação automaticamente, clique no botão seguinte:

[![Implementar Azure](./media/service-bus-resource-manager-namespace-topic/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-servicebus-create-topic-subscription-rule%2Fazuredeploy.json)

## <a name="parameters"></a>Parâmetros

Com o Gestor de recursos do Azure, deve definir parâmetros para valores que pretende especificar quando o modelo é implementado. O modelo inclui uma secção denominada `Parameters` que contém todos os valores de parâmetro. Deve definir um parâmetro para esses valores variar com base no projeto que está a implementar ou com base no ambiente de que está a implementar. Não defina parâmetros para valores que permaneçam sempre o mesmo. Para definir os recursos que são implementados, cada valor do parâmetro é utilizado no modelo.

O modelo define seguintes parâmetros:

### <a name="servicebusnamespacename"></a>serviceBusNamespaceName

O nome do serviço Bus espaço de nomes para criar.

```
"serviceBusNamespaceName": {
"type": "string"
}
```

### <a name="servicebustopicname"></a>serviceBusTopicName

O nome do tópico criado no serviço Bus espaço de nomes.

```
"serviceBusTopicName": {
"type": "string"
}
```

### <a name="servicebussubscriptionname"></a>serviceBusSubscriptionName

O nome da subscrição criado no serviço Bus espaço de nomes.

```
"serviceBusSubscriptionName": {
"type": "string"
}
```
### <a name="servicebusrulename"></a>serviceBusRuleName

O nome da rule(filter) criado no serviço Bus espaço de nomes.

```
   "serviceBusRuleName": {
   "type": "string",
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

Cria um espaço de nomes de serviço Bus padrão de tipo de **mensagens**, com o tópico e regras e subscrição.

```
 "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "sku": {
            "name": "Standard",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusTopicName')]",
            "type": "Topics",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusTopicName')]"
            },
            "resources": [{
                "apiVersion": "[variables('sbVersion')]",
                "name": "[parameters('serviceBusSubscriptionName')]",
                "type": "Subscriptions",
                "dependsOn": [
                    "[parameters('serviceBusTopicName')]"
                ],
                "properties": {},
                "resources": [{
                    "apiVersion": "[variables('sbVersion')]",
                    "name": "[parameters('serviceBusRuleName')]",
                    "type": "Rules",
                    "dependsOn": [
                        "[parameters('serviceBusSubscriptionName')]"
                    ],
                    "properties": {
                        "filter": {
                            "sqlExpression": "StoreName = 'Store1'"
                        },
                        "action": {
                            "sqlExpression": "set FilterTag = 'true'"
                        }
                    }
                }]
            }]
        }]
    }]
```

## <a name="commands-to-run-deployment"></a>Comandos para executar a implementação

[AZURE.INCLUDE [app-service-deploy-commands](../../includes/app-service-deploy-commands.md)]

## <a name="powershell"></a>PowerShell

```
New-AzureResourceGroupDeployment -Name \<deployment-name\> -ResourceGroupName \<resource-group-name\> -TemplateUri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="azure-cli"></a>Clip Azure

```
azure config mode arm

azure group deployment create \<my-resource-group\> \<my-deployment-name\> --template-uri <https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-servicebus-create-topic-subscription-rule/azuredeploy.json>
```

## <a name="next-steps"></a>Próximos passos

Agora que já criou e implementado recursos utilizando o Gestor de recursos do Azure, saiba como gerir estes recursos visualizando estes artigos:

- [Gerir Azure Service Bus utilizando a automatização do Azure](service-bus-automation-manage.md)
- [Gerir o serviço Bus com PowerShell](service-bus-powershell-how-to-provision.md)
- [Gerir os recursos de serviço Bus com o Explorador de Bus de serviço](https://code.msdn.microsoft.com/Service-Bus-Explorer-f2abca5a)


  [Criação de modelos de Gestor de recursos do Azure]: ../resource-group-authoring-templates.md
  [Guia de introdução Azure modelos]: https://azure.microsoft.com/documentation/templates/?term=service+bus
  [Learn more about Service Bus topics and subscriptions]: service-bus-queues-topics-subscriptions.md
  [Using Azure PowerShell with Azure Resource Manager]: ../powershell-azure-resource-manager.md
  [Using the Azure CLI for Mac, Linux, and Windows with Azure Resource Management]: ../xplat-cli-azure-resource-manager.md
  [Recursos Azure convenções de nomenclatura]: https://azure.microsoft.com/en-us/documentation/articles/guidance-naming-conventions/
  [Espaço de nomes de serviço Bus com tópico, subscrição e uma regra]: https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-topic-subscription-rule/
  [Serviço Bus filas, tópicos e subscrições]:service-bus-queues-topics-subscriptions.md
  
