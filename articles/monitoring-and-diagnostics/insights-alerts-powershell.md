<properties
    pageTitle="Utilizar o PowerShell para criar alertas para serviços Azure | Microsoft Azure"
    description="Utilizar o PowerShell para criar alertas Azure, que podem accionar notificações ou automatização quando as condições especificadas forem cumpridas."
    authors="rboucher"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="robb"/>

# <a name="use-powershell-to-create-alerts-for-azure-services"></a>Utilizar o PowerShell para criar alertas para serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLIP](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como configurar alertas Azure através do PowerShell.  

Poderá receber um alerta com base no métricas de monitorização para ou eventos no seus serviços Azure.

- **Métricas valores** - os accionadores alertas quando o valor de uma métrica especificado este se cruza um determinado limiar que atribuir em qualquer direção. Isto é, accionar ambos quando a condição é cumprida pela primeira vez e, em seguida, posteriormente quando o condição é já não está a ser cumprida.    
- **Eventos de registo de atividade** - um alerta pode acionar *todos os* eventos, ou, apenas quando ocorre um determinado número de eventos.

Pode configurar um alerta para fazer o seguinte quando accionar:

- enviar notificações de correio eletrónico para o administrador de serviços e coadministradores
- Envie e-mail a mensagens de correio eletrónico adicionais que especificar.
- ligar uma webhook
- começar a execução de um livro de execuções Azure (apenas a partir do portal Azure)

Pode configurar e obter informações sobre regras de alertas utilizando

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [interface de comandos (CLI)](insights-alerts-command-line-interface.md)
- [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)


Para obter informações adicionais, pode sempre escrever ```get-help``` e, em seguida, o comando PowerShell qual pretende obter ajuda.

## <a name="create-alert-rules-in-powershell"></a>Criar regras de alertas no PowerShell

1. Iniciar sessão no Azure.   

    ```PowerShell
    Login-AzureRmAccount

    ```

2. Obter uma lista das subscrições tem disponíveis. Certifique-se de que está a trabalhar com a subscrição à direita. Caso não esteja, defina-o para a correta utilizando os resultados obtidos `Get-AzureRmSubscription`.

    ```PowerShell
    Get-AzureRmSubscription
    Get-AzureRmContext
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

3.  A lista existentes de regras de um grupo de recursos, utilize o seguinte comando:

    ```PowerShell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

4. Para criar uma regra, tem de ter várias partes importantes de informações pela primeira vez. 
    - O **ID do recurso** para o recurso que pretende definir um alerta para
    - As **definições de métricas** disponível para esse recurso

    Uma forma de obter o ID do recurso é utilizar o portal Azure. Assumindo que o recurso já tiver sido criado, selecione-o no portal. Em seguida, na pá seguinte, selecione *Propriedades* na secção *Definições* . O ID do recurso é um campo a pá seguinte. Outra forma é utilizar o [Azure recurso Explorer](https://resources.azure.com/).

    É um id do recurso de exemplo para uma aplicação web

    ```
    /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename
    ```

    Pode utilizar `Get-AzureRmMetricDefinition` para ver a lista de todas as definições de métricas para um recurso específico.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id>
    ```

    O exemplo seguinte gera uma tabela com a métrica do nome e a unidade para esse métrica.

    ```PowerShell
    Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit

    ```
    Uma lista completa das opções disponíveis para obter AzureRmMetricDefinition está disponível ao executar Get-MetricDefinitions.


5. O exemplo seguinte configura um alerta num recurso de web site. Alertas accionadores sempre que qualquer tráfego de forma consistente receba para 5 minutos e novamente quando recebe sem o tráfego para 5 minutos.

    ```PowerShell
    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Description "alert on any website activity"

    ```

6. Para criar webhook ou enviar correio eletrónico quando um alerta de accionadores, crie primeiro o e-mail e/ou webhooks. Em seguida, imediatamente crie a regra posteriormente com etiqueta-ações e conforme apresentado no seguinte exemplo. Não pode associar webhook ou e-mails com já criou regras através do PowerShell.


    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmMetricAlertRule -Name myMetricRuleWithWebhookAndEmail -Location "East US" -ResourceGroup myresourcegroup -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Web/sites/mywebsitename -MetricName "BytesReceived" -Operator GreaterThan -Threshold 2 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail, $actionWebhook -Description "alert on any website activity"
    ```


7. Para criar um alerta que accionadores numa condição específica no registo de atividade, utilize os comandos do formulário seguinte

    ```PowerShell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
    $actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://www.contoso.com?token=mytoken

    Add-AzureRmLogAlertRule -Name myLogAlertRule -Location "East US" -ResourceGroup myresourcegroup -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup resourcegroupbeingmonitored -Actions $actionEmail, $actionWebhook
    ```

    -OperationName corresponde a um tipo de evento no registo de atividade. Alguns exemplos incluem *Microsoft.Compute/virtualMachines/delete* e *microsoft.insights/diagnosticSettings/write*.

    Pode utilizar o comando do PowerShell [Get-AzureRmProviderOperation](https://msdn.microsoft.com/library/mt603720.aspx) para obter uma lista de possíveis operationNames. Em alternativa, pode utilizar o portal do Azure para o registo de atividade de consulta e localizar específico anteriores operações ao qual pretende criar um alerta para. As operações mostradas na vista de gráfico registo amigável nomes. Procurar JSON para a entrada e separar o valor de OperationName.   

8. Certifique-se de que foram criados os alertas do corretamente verificando as regras individuais.

    ```PowerShell
    Get-AzureRmAlertRule -Name myMetricRuleWithWebhookAndEmail -ResourceGroup myresourcegroup -DetailedOutput

    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

9. Elimine os alertas. Estes comandos eliminam as regras que criou anteriormente neste artigo.

    ```PowerShell
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myrule
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myMetricRuleWithWebhookAndEmail
    Remove-AzureRmAlertRule -ResourceGroup myresourcegroup -Name myLogAlertRule
    ```

## <a name="next-steps"></a>Próximos passos

* [Obter uma descrição geral da monitorização Azure](monitoring-overview.md) incluindo os tipos de informação podem recolher e monitorizar.
* Saiba mais sobre [Configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks de automatização do Azure](..\automation\automation-starting-a-runbook.md).
* Obter uma [Descrição geral de recolher registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md) para recolher detalhadas métricas de alta frequência no seu serviço.
* Obter uma [Descrição geral da coleção de métricas](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.
