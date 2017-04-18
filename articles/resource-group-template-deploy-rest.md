<properties
   pageTitle="Implementar recursos REST API e modelo com | Microsoft Azure"
   description="Utilize o Gestor de recursos do Azure e o Gestor de recursos REST API para implementar um recursos Azure. Os recursos são definidos num modelo de Gestor de recursos."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/11/2016"
   ms.author="tomfitz"/>

# <a name="deploy-resources-with-resource-manager-templates-and-resource-manager-rest-api"></a>Implementar recursos com os modelos de Gestor de recursos e o Gestor de recursos REST API

> [AZURE.SELECTOR]
- [PowerShell](resource-group-template-deploy.md)
- [Clip Azure](resource-group-template-deploy-cli.md)
- [Portal](resource-group-template-deploy-portal.md)
- [REST API](resource-group-template-deploy-rest.md)

Este artigo explica como utilizar o Gestor de recursos REST API com modelos de Gestor de recursos para implementar os recursos do Azure.  

> [AZURE.TIP] Para obter ajuda com a depuração de um erro durante a implementação, consulte:
>
> - [Operações de implementação de vista com a REST API](resource-manager-troubleshoot-deployments-rest.md) para saber mais sobre como obter as informações que o ajuda a resolver o erro
> - [Resolver problemas de erros comuns quando implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md) para saber como resolver erros comuns de implementação

O modelo pode ser um ficheiro local ou um ficheiro externo que está disponível através de um URI. Quando o seu modelo encontra-se numa conta de armazenamento, pode restringir o acesso ao modelo e fornecer um token de assinatura (SA) de acesso partilhado durante a implementação.

[AZURE.INCLUDE [resource-manager-deployments](../includes/resource-manager-deployments.md)]

## <a name="deploy-with-the-rest-api"></a>Implementar com a API REST
1. Definir [parâmetros e cabeçalhos comuns](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common), incluindo tokens de autenticação.
2. Se não tiver um grupo de recursos existente, crie um grupo de recursos. Forneça o seu id da subscrição, o nome do novo grupo de recursos e da localização que precisa para a sua solução. Para mais informações, consulte o artigo [criar um grupo de recursos](https://msdn.microsoft.com/library/azure/dn790525.aspx).

        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
          <common headers>
          {
            "location": "West US",
            "tags": {
               "tagname1": "tagvalue1"
            }
          }
   
3. Valide a sua implementação antes de executá-lo ao executar a operação de [Validar uma implementação do modelo](https://msdn.microsoft.com/library/azure/dn790547.aspx) . Quando testar a implementação, fornece parâmetros exatamente como faria ao executar a implementação (mostrada no passo seguinte).

3. Crie uma implementação. Forneça o seu id da subscrição, o nome do grupo de recursos para implementar, o nome da implementação e uma ligação ao seu modelo. Para obter informações sobre o ficheiro de modelo, consulte o artigo [ficheiro parâmetro](#parameter-file). Para mais informações sobre a API do resto para criar um grupo de recursos, consulte o artigo [criar uma implementação do modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx). Repare que o **modo** está definido para **incremento**. Para executar uma implementação concluída, defina o **modo** como **concluída**. Tenha cuidado quando utilizar o modo completo como inadvertidamente pode eliminar recursos que não estão no seu modelo.
    
        PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",
              }
            }
          }
   
      Se pretender iniciar sessão conteúdo da resposta, o conteúdo do pedido, ou ambas, incluem **debugSetting** no pedido de.

        "debugSetting": {
          "detailLevel": "requestContent, responseContent"
        }

      Pode configurar a sua conta de armazenamento para utilizar um assinatura (SA) token de acesso partilhado. Para mais informações, consulte o artigo [Delegar acesso com uma assinatura de acesso partilhado](https://msdn.microsoft.com/library/ee395415.aspx).

4. Obter o estado de implementação do modelo. Para mais informações, consulte o artigo [obter informações sobre uma implementação do modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx).

          GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

[AZURE.INCLUDE [resource-manager-parameter-file](../includes/resource-manager-parameter-file.md)]

## <a name="next-steps"></a>Próximos passos
- Para obter um exemplo de implementação do recursos através da biblioteca de cliente .NET, consulte o artigo [Implementar recursos utilizar bibliotecas .NET e um modelo](virtual-machines/virtual-machines-windows-csharp-template.md).
- Para definir parâmetros no modelo, consulte o artigo [modelos de criação](resource-group-authoring-templates.md#parameters).
- Para obter orientações sobre como implementar a solução para diferentes ambientes, consulte o artigo [desenvolvimento e ambientes de teste no Microsoft Azure](solution-dev-test-environments.md).
- Para obter detalhes sobre como utilizar uma referência de KeyVault para passar valores seguros, consulte o artigo [passar valores seguros durante a implementação](resource-manager-keyvault-parameter.md).
