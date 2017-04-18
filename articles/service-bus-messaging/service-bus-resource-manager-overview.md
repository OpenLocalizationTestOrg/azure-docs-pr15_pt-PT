<properties
    pageTitle="Criar recursos de serviço Bus utilizando o Gestor de recursos do Azure modelos | Microsoft Azure"
    description="Utilize o Gestor de recursos do Azure modelos para automatizar a criação de recursos de serviço Bus"
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
    ms.author="sethm"/>

# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Criar os recursos de serviço Bus utilizar modelos de Gestor de recursos do Azure

Este artigo descreve como criar e implementar Bus de serviço e evento concentradores recursos utilizando o Gestor de recursos do Azure modelos, PowerShell e o fornecedor de recurso Bus de serviço.

Modelos de Gestor de recursos Azure ajudá-lo definir os recursos para implementar uma solução e para especificar parâmetros e variáveis que permitem-lhe valores para diferentes ambientes de entrada. O modelo é composta por JSON e expressões que pode utilizar para construir valores para a sua implementação. Para obter informações detalhadas sobre como escrever modelos de Gestor de recursos do Azure e um debate do formato de modelo, consulte o artigo [modelos de criação de Gestor de recursos do Azure](../resource-group-authoring-templates.md). 

>[AZURE.NOTE] Os exemplos neste artigo mostram como utilizar o Gestor de recursos do Azure para criar um espaço de nomes de serviço Bus e entidade mensagens (fila). Para outros exemplos de modelo, visite a [Galeria de modelos de guia de introdução do Azure][] e procurar "Serviço Bus."

## <a name="service-bus-and-event-hubs-resource-manager-templates"></a>Modelos de serviço Bus e Gestor de recursos do evento concentradores

Estes modelos Bus de serviço e Gestor de recursos do evento concentradores Azure estão disponíveis para transferência e implementação. Clique nas ligações seguintes para obter detalhes sobre cada um, com ligações para os modelos em GitHub: 

- [Criar um espaço de nomes de serviço Bus](service-bus-resource-manager-namespace.md)
- [Criar um espaço de nomes de serviço Bus com fila de espera](service-bus-resource-manager-namespace-queue.md)
- [Criar um espaço de nomes de serviço Bus com tópico e subscrição](service-bus-resource-manager-namespace-topic.md)
- [Criar um espaço de nomes de serviço Bus com regras de autorização e fila de espera](service-bus-resource-manager-namespace-auth-rule.md)
- [Criar um espaço de nomes do evento concentradores com um grupo do concentrador de evento e consumidor](../event-hubs/event-hubs-resource-manager-namespace-event-hub.md)

## <a name="deploy-with-powershell"></a>Implementar com PowerShell

O procedimento seguinte descreve como utilizar o PowerShell para implementar um modelo de Gestor de recursos do Azure que cria um espaço de nomes de serviço Bus de camada **padrão** e uma fila dentro desse espaço de nomes. Este exemplo é baseado no modelo de [criar um espaço de nomes de serviço Bus com fila de espera](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue) . O fluxo de trabalho aproximado é da seguinte forma:

1. Instale o PowerShell.
2. Crie o modelo e (opcionalmente) um ficheiro de parâmetro.
2. No PowerShell, inicie sessão na sua conta Azure.
3. Se não existir, crie um novo grupo de recursos.
4. Teste a implementação.
5. Se pretender, defina o modo de implementação.
6. Implemente o modelo.

Para obter informações completas sobre como implementar modelos de Gestor de recursos do Azure, consulte o artigo [Implementar recursos com modelos de Gestor de recursos do Azure][].

### <a name="install-powershell"></a>Instalar o PowerShell

Instale o Azure PowerShell ao seguir as instruções no [artigo como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

### <a name="create-a-template"></a>Criar um modelo

Clonar ou copie o modelo de [201-servicebus-criar-fila de espera](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) da GitHub:

```
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
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
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>Criar um ficheiro de parâmetros (opcional)

Para utilizar um ficheiro de parâmetros opcionais, copie o ficheiro [201-servicebus-criar-fila de espera](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) . Substitua o valor de `serviceBusNamespaceName` com o nome do espaço de nomes Bus de serviço que pretende criar nesta implementação e substitua o valor de `serviceBusQueueName` com o nome da fila que pretende criar. 

```
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

Para mais informações, consulte o tópico de [ficheiro do parâmetro](../resource-group-template-deploy.md#parameter-file) .

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Inicie sessão no Azure e definir a subscrição do Azure

A partir de uma linha de comandos do PowerShell, execute o seguinte comando:

```
Login-AzureRmAccount
```

Lhe for pedido para iniciar sessão na sua conta Azure. Depois de iniciar sessão, execute o seguinte comando para ver as suas subscrições disponíveis.

```
Get-AzureRMSubscription
```

Este comando devolve uma lista de subscrições Azure disponíveis. Selecione uma subscrição para a sessão actual, executando o seguinte comando. Substituir `<YourSubscriptionId>` com o GUID para a subscrição Azure que pretende utilizar.

```
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>Definir o grupo de recursos

Se não tiver um grupo de recursos existente, crie um novo grupo de recursos com o comando **Novo AzureRmResourceGroup** . Fornece o nome do grupo de recursos e da localização que pretende utilizar. Por exemplo:

```
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

Se tiver êxito, é apresentado um resumo do novo grupo de recursos.

```
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>Testar a implementação

Validar a sua implementação ao executar o `Test-AzureRmResourceGroupDeployment` cmdlet. Quando testar a implementação, fornece parâmetros exatamente como faria ao executar a implementação.

```
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>Criar a implementação

Para criar a nova implementação, execute o `New-AzureRmResourceGroupDeployment` comando e fornecer os parâmetros necessários quando lhe for pedido. Os parâmetros de incluem um nome para a sua implementação, o nome do seu grupo de recursos e o caminho ou URL para o ficheiro de modelo. Se o **modo de** parâmetro não for especificado, é utilizado o valor predefinido de **incremento** . Para mais informações, consulte o artigo [implementações concluídas e incremento](../resource-group-template-deploy.md#incremental-and-complete-deployments).

O seguinte comando pede-lhe os três parâmetros na janela do PowerShell:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

Para especificar um ficheiro de parâmetros em vez disso, utilize o seguinte comando.

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

Também pode utilizar parâmetros inline ao executar o cmdlet implementação. O comando é da seguinte forma:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

Para executar uma implementação [concluída](../resource-group-template-deploy.md#incremental-and-complete-deployments) , defina o parâmetro do **modo** como **concluída**:

```
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json 
```

### <a name="verify-the-deployment"></a>Verificar a implementação

Se os recursos são implementados com êxito, é apresentado um resumo da implementação na janela do PowerShell:

```
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>Próximos passos

Agora que já viu o fluxo de trabalho básico e comandos para implementar um modelo de Gestor de recursos do Azure. Para informações mais detalhadas, visite as ligações seguintes:

- [Descrição geral do Gestor de recursos Azure][]
- [Implementar recursos com modelos de Gestor de recursos do Azure][]
- [Criação de modelos](../resource-group-authoring-templates.md)


[Descrição geral do Gestor de recursos Azure]: ../resource-group-overview.md
[Implementar recursos com modelos de Gestor de recursos do Azure]: ../resource-group-template-deploy.md
[Galeria de modelos de guia de introdução Azure]: https://azure.microsoft.com/documentation/templates/?term=service+bus