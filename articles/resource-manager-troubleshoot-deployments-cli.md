<properties
   pageTitle="Ver operações de implementação com Azure clip | Microsoft Azure"
   description="Descreve como utilizar o clip do Azure para detetar problemas de implementação do Gestor de recursos."
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
   ms.date="08/15/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-cli"></a>Operações de implementação de vista com o clip do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Clip Azure](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Se recebeu um erro quando implementar o recursos Azure, poderá querer ver mais detalhes sobre as operações de implementação que foram executadas. Clip Azure fornece comandos que permitem-lhe localizar os erros e determinar potenciais correções.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Pode evitar alguns erros ao modelo e infraestrutura antes da implementação a validar. Também é possível registar pedido adicional e informações de resposta durante a implementação que poderão ser úteis mais tarde para resolução de problemas. Para saber mais sobre a validar e informações de pedidos e respostas do registo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy-cli.md).

## <a name="use-audit-logs-to-troubleshoot"></a>Registos de auditoria de utilização para resolução de problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver erros para uma implementação, utilize os seguintes passos:

1. Para ver os registos de auditoria, execute o comando **Mostrar de registo do grupo azure** . Pode incluir o **– última implementação** opção para recuperar apenas o registo para a implementação mais recente.

        azure group log show ExampleGroup --last-deployment

2. O comando **Mostrar de registo do grupo azure** devolve muitas informações. Resolução de problemas, normalmente, pretende focar-se em operações falhou. O script seguinte utiliza a opção **– json** e que o [jq](https://stedolan.github.io/jq/) JSON utilitário para procurar o registo para falhas de implementação.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"Target\":null,\"Details\":[{\"Message\":\"Website with given name
            mysite already exists.\"},{\"Code\":\"Conflict\"},{\"ErrorEntity\":{\"Code\":\"Conflict\",\"Message\":\"Website with given name mysite already exists.\",\"ExtendedCode\":
            \"54001\",\"MessageTemplate\":\"Website with given name {0} already exists.\",\"Parameters\":[\"mysite\"],\"InnerErrors\":null}}],\"Innererror\":null}"
        },
        ...

    Pode ver **as propriedades** inclui informações em json sobre a operação de falha.

    Pode utilizar o **– verboso** e opções de **-lado** para ver mais informações a partir dos registos.  Utilizar o **– verboso** opção para apresentar os passos as operações analisá no `stdout`. Para um histórico de pedido de conclusão, utilize a opção **- lado** . As mensagens com frequência fornecem pistas essenciais sobre a causa de qualquer falhas.

3. Para se concentrar na mensagem de estado para as entradas falhadas, utilize o seguinte comando:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == \"Failed\") | .properties.statusMessage"


## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizar as operações de implementação para resolução de problemas

1. Obter o estado geral de uma implementação com o comando **Mostrar de implementação do azure grupo** . No exemplo abaixo, a implementação falhou.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Para ver a mensagem para operações de falhadas para uma implementação, utilize:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == \"Failed\") | .properties.statusMessage.Message"


## <a name="next-steps"></a>Próximos passos

- Para obter ajuda com resolver erros de implementação específica, consulte o artigo [resolver erros comuns ao implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber como utilizar os registos de auditoria para monitorizar a outros tipos de ações, consulte o artigo [Auditar operações com o Gestor de recursos](resource-group-audit.md).
- Para validar a sua implementação antes de executá-lo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).
