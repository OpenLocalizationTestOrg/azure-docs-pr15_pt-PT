<properties
   pageTitle="Ver operações de implementação com PowerShell | Microsoft Azure"
   description="Descreve como utilizar o PowerShell Azure para detetar problemas de implementação do Gestor de recursos."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="06/14/2016"
   ms.author="tomfitz"/>

# <a name="view-deployment-operations-with-azure-powershell"></a>Operações de implementação de vista com PowerShell do Azure

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Clip Azure](resource-manager-troubleshoot-deployments-cli.md)
- [REST API](resource-manager-troubleshoot-deployments-rest.md)

Pode ver as operações para uma implementação através do PowerShell o Azure. Poderá ser mais interessado em visualizar as operações quando tiver recebido um erro durante a implementação para que este artigo foca-se sobre a visualização de operações tem ocorrido uma falha. PowerShell fornece cmdlets que permitem-lhe encontrar os erros e determinar correções potenciais facilmente.

[AZURE.INCLUDE [resource-manager-troubleshoot-introduction](../includes/resource-manager-troubleshoot-introduction.md)]

Pode evitar alguns erros ao modelo e infraestrutura antes de implementação a validar. Também é possível registar pedido adicional e informações de resposta durante a implementação que poderão ser úteis mais tarde para resolução de problemas. Para saber mais sobre a validar e informações de pedidos e respostas do registo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).

## <a name="use-deployment-operations-to-troubleshoot"></a>Utilizar as operações de implementação para resolução de problemas

1. Para obter o estado de uma implementação geral, utilize o comando **Get-AzureRmResourceGroupDeployment** . Pode filtrar os resultados para apenas essas implementações que tenham falhou.

        Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
    Que devolve as implementações falhadas no seguinte formato:
        
        DeploymentName          : Microsoft.Template
        ResourceGroupName       : ExampleGroup
        ProvisioningState       : Failed
        Timestamp               : 6/14/2016 9:55:21 PM
        Mode                    : Incremental
        TemplateLink            :
        Parameters              :
                    Name                Type                 Value
                    ===============     ===================  ==========
                    storageAccountName  String               tfstorage9855
                    adminUsername       String               tfadmin
                    adminPassword       SecureString
                    dnsNameforLBIP      String               dns
                    vmNamePrefix        String               myVM
                    imagePublisher      String               MicrosoftWindowsServer
                    imageOffer          String               WindowsServer
                    imageSKU            String               2012-R2-Datacenter
                    lbName              String               myLB
                    nicNamePrefix       String               nic
                    publicIPAddressName String               myPublicIP
                    vnetName            String               myVNET
                    vmSize              String               Standard_D1

        Outputs                 :
        DeploymentDebugLogLevel :

2. Cada implementação é normalmente constituída várias operações, com cada operação que representa um passo no processo de implementação. Para descobrir o que correu mal com uma implementação, normalmente, tem de ver detalhes sobre as operações de implementação. Pode ver o estado das operações com **Get-AzureRmResourceGroupDeploymentOperation**.

        Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName Microsoft.Template
        
    Que devolve várias operações com cada um no seguinte formato:
        
        Id             : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/Microsoft.Template/operations/A3EB2DA598E0A780
        OperationId    : A3EB2DA598E0A780
        Properties     : @{provisioningOperation=Create; provisioningState=Succeeded; timestamp=2016-06-14T21:55:15.0156208Z;
                         duration=PT23.0227078S; trackingId=11d376e8-5d6d-4da8-847e-6f23c6443fbf;
                         serviceRequestId=0196828d-8559-4bf6-b6b8-8b9057cb0e23; statusCode=OK; targetResource=}
        PropertiesText : {duration:PT23.0227078S, provisioningOperation:Create, provisioningState:Succeeded,
                         serviceRequestId:0196828d-8559-4bf6-b6b8-8b9057cb0e23...}

3. Para obter mais detalhes sobre operações falhadas, obtenha as propriedades para operações de estado de **Falha** .

        (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed
        
    Que devolve todas as operações falhadas com cada um no seguinte formato:
        
        provisioningOperation : Create
        provisioningState     : Failed
        timestamp             : 2016-06-14T21:54:55.1468068Z
        duration              : PT3.1449887S
        trackingId            : f4ed72f8-4203-43dc-958a-15d041e8c233
        serviceRequestId      : a426f689-5d5a-448d-a2f0-9784d14c900a
        statusCode            : BadRequest
        statusMessage         : @{error=}
        targetResource        : @{id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
                                Microsoft.Network/publicIPAddresses/myPublicIP;
                                resourceType=Microsoft.Network/publicIPAddresses; resourceName=myPublicIP}

    Tenha em atenção o ID de rastreio a operação de. Irá utilizar que no próximo passo para focar-se numa operação em particular.

4. Para obter a mensagem de estado de uma operação de falha específica, utilize o seguinte comando:

        ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName Microsoft.Template -ResourceGroupName ExampleGroup).Properties | Where-Object trackingId -eq f4ed72f8-4203-43dc-958a-15d041e8c233).StatusMessage.error
        
    Qual devolve:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}

## <a name="use-audit-logs-to-troubleshoot"></a>Registos de auditoria de utilização para resolução de problemas

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Para ver erros para uma implementação, utilize os seguintes passos:

1. Para obter as entradas de registo, execute o comando **Get-AzureRmLog** . Pode utilizar os parâmetros **ResourceGroup** e de **Estado** para devolver apenas os eventos que falhou para um grupo de recursos único. Se não especificar uma hora de início e de fim, são devolvidas entradas de última hora.
Por exemplo, para obter as operações falhadas para a hora passada executar:

        Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Pode especificar um determinado timespan. O exemplo seguinte, veremos para ações falhadas do último dia. 

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -Status Failed
      
    Em alternativa, pode definir um exato início e hora de fim para falhadas ações:

        Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Se este comando devolver demasiadas entradas e propriedades, pode concentrar-se os esforços de auditoria através da obtenção a propriedade de **Propriedades** . Recomendamos também irá incluir o parâmetro **DetailedOutput** para ver as mensagens de erro.

        (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-1) -DetailedOutput).Properties
        
    Propriedades das entradas de registo que devolve no seguinte formato:
        
        Content
        -------
        {} 
        {[statusCode, BadRequest], [statusMessage, {"error":{"code":"DnsRecordInUse","message":"DNS record dns.westus.clouda...
        {[statusCode, BadRequest], [serviceRequestId, a426f689-5d5a-448d-a2f0-9784d14c900a], [statusMessage, {"error":{"code...

3. Com base nestes resultados, vamos concentrar-no segundo elemento. Pode ainda aperfeiçoar os resultados verificando a mensagem de estado para essa entrada.

        ((Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput -StartTime (Get-Date).AddDays(-1)).Properties[1].Content["statusMessage"] | ConvertFrom-Json).error
        
    Qual devolve:
        
        code           message                                                                        details
        ----           -------                                                                        -------
        DnsRecordInUse DNS record dns.westus.cloudapp.azure.com is already used by another public IP. {}



## <a name="next-steps"></a>Próximos passos

- Para obter ajuda com resolver erros de implementação específica, consulte o artigo [resolver erros comuns ao implementar recursos para Azure com o Gestor de recursos do Azure](resource-manager-common-deployment-errors.md).
- Para saber como utilizar os registos de auditoria para monitorizar a outros tipos de ações, consulte o artigo [Auditar operações com o Gestor de recursos](resource-group-audit.md).
- Para validar a sua implementação antes de executá-lo, consulte o artigo [Implementar um grupo de recursos com o modelo de Gestor de recursos do Azure](resource-group-template-deploy.md).

