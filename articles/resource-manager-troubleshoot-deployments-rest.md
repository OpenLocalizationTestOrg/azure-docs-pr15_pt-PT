<properties
   pageTitle="Ver operações de implementação com a REST API | Microsoft Azure"
   description="Descreve como utilizar o Gestor de recursos do Azure REST API para detetar problemas de implementação do Gestor de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/13/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-resource-manager-rest-api"></a>Operações de implementação de vista com a API de REST do Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Clip Azure](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Se recebeu um erro quando implementar o recursos Azure, poderá querer ver mais detalhes sobre as operações de implementação que foram executadas. REST API fornece operações que permitem-lhe localizar os erros e determinar potenciais correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Pode evitar alguns erros ao modelo e infraestrutura antes de implementação a validar. Também é possível registar pedido adicional e informações de resposta durante a implementação que poderão ser úteis mais tarde para resolução de problemas. Para saber mais sobre a validar e informações de pedidos e respostas do registo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy-rest.md).

## <a name="troubleshoot-with-rest-api"></a>Resolver problemas com a REST API

1. Implemente os recursos com a operação de [criar uma implementação do modelo](https://msdn.microsoft.com/library/azure/dn790564.aspx) . Para manter informações que poderão ser útil para depuração, defina a propriedade de **debugSetting** no pedido JSON para **requestContent** e/ou **responseContent**. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
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
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Por predefinição, o valor de **debugSetting** está definido para **nenhum**. Quando especificar o valor de **debugSetting** , considere cuidadosamente o tipo de informação que está transmitir durante a implementação. Por informações sobre a resposta ou um pedido de registo, poderia expor potencialmente confidenciais dados que são obtidos através de operações de implementação. 

2. Obter informações sobre uma implementação com a operação de [obter informações sobre uma implementação do modelo](https://msdn.microsoft.com/library/azure/dn790565.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Na resposta, tenha em atenção em particular os elementos **provisioningState** , **correlationId** e **erros** . O **correlationId** é utilizado para controlar eventos relacionados e pode ser útil quando trabalhar com o suporte técnico para resolver um problema.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  \"error\": {\r\n    \"message\": \"Conflict\",\r\n    \"code\": \"Conflict\"\r\n  }\r\n}"}]
            }  
          }
        }

3. Obter informações sobre as operações de implementação com a operação de [todas as operações de implementação do modelo de lista](https://msdn.microsoft.com/library/azure/dn790518.aspx) . 

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    A resposta irá incluir informações pedido e/ou resposta com base no que especificou na propriedade **debugSetting** durante a implementação.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Obtenha eventos dos registos de auditoria para a implementação com a operação [Listar os eventos de gestão de uma subscrição](https://msdn.microsoft.com/library/azure/dn931934.aspx) .

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## <a name="next-steps"></a>Próximos passos

- Para obter ajuda com resolver erros de implementação específica, consulte o artigo [resolver erros comuns ao implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber como utilizar os registos de auditoria para monitorizar a outros tipos de ações, consulte o artigo [Auditar operações com o Gestor de recursos](resource-group-audit.md).
- Para validar a sua implementação antes de executá-lo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).
