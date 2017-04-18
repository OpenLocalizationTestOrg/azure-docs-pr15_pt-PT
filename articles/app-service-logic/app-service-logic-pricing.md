<properties 
    pageTitle="Aplicações de lógica preços modelo | Microsoft Azure" 
    description="Detalhes sobre como preços funciona nas aplicações de lógica" 
    authors="kevinlam1" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="10/12/2016"
    ms.author="klam"/>

# <a name="logic-apps-pricing-model"></a>Aplicações de lógica preços de modelo

Azure lógica de aplicações permite-lhe dimensionar e executar fluxos de trabalho de integração na nuvem.  Seguem-se para obter detalhes sobre a faturação e preços planos para as aplicações de lógica.

## <a name="consumption-pricing"></a>Consumo preços

Criado recentemente lógica aplicações utilizar um plano de consumo. Com o lógica aplicações consumo comparar modelo, apenas pagar para utilize.  Aplicações de lógica não são limitadas quando utilizar um plano de consumo.
Todas as ações executadas por uma execução de uma instância da aplicação de lógica são com tráfego limitadas.

### <a name="what-are-action-executions"></a>O que são execuções ação?

Cada passo uma definição de aplicação lógica é uma ação.  Isto inclui accionadores, os passos de fluxo de controlo como condições, âmbitos, para cada ciclos, efetue até ciclos, chamadas aos conectores e chamadas para ações nativas.
Accionadores são ações acabou especiais que foram concebidas para criar uma instância de uma nova instância de uma aplicação de lógica quando ocorre um evento específico.  Existem várias comportamentos diferentes para accionadores que podem afetar a forma como a aplicação de lógica é com tráfego limitada.

-   **Consulta accionador** – este accionador continuamente consulta um ponto final até que recebe uma mensagem que satisfaça os critérios para criar uma nova instância de uma aplicação de lógica.  O intervalo de consulta pode ser configurado de accionador no estruturador de lógica aplicações.  Cada pedido de inquéritos, mesmo que não será criada uma nova instância de uma aplicação de lógica, irá contam como execução uma ação.

-   **Webhook accionador** – este accionador espera para um cliente enviar um pedido-lo num determinado ponto final.  Cada pedido enviado para o ponto final webhook conta como execução uma ação. O pedido e o acionador HTTP Webhook são ambos os accionadores webhook.

-   **Acionador de periodicidade** – este accionador irá criar uma nova instância da aplicação lógica com base no intervalo de periodicidade configurado no accionador.  Por exemplo, um acionador de periodicidade pode ser configurado para executar a cada 3 dias ou até mesmo minuto.

Acionar execuções podem ser vistas no pá de recurso a lógica aplicações na parte accionador histórico.

Todas as ações que foram executadas, se terem sido efetuada com êxito ou falha são com tráfego limitado como execução uma ação.  Ações que foram ignoradas devido a uma condição não cumprida ou ações que não executar porque a aplicação de lógica terminadas antes da sua conclusão não são contabilizadas como execuções de ação.

Ações executadas dentro ciclos são contadas por iteração do ciclo.  Por exemplo, uma ação única num para cada iteração ciclo através de uma lista de 10 itens iniciais vai ser contada como a contagem de itens na lista (10) multiplicada pelo número de ações tudo (1) plus um para o início do ciclo que, neste exemplo, seria (10 * 1) + 1 = 11 execuções de ação.

Lógica as aplicações que são desativados não pode ter novas instâncias criar instâncias e, por conseguinte, durante o período de tempo que estão desativadas será não obter cobrada.  Tenha em consideração que depois de desativar uma aplicação de lógica, poderá demorar um pouco tempo para as instâncias para colocar em repouso antes de a ser desactivado completamente.

## <a name="app-service-plans"></a>Planos do serviço de aplicação

Planos do serviço de aplicação já não são necessárias para criar uma aplicação de lógica.  Também pode fazer referência um plano de serviço de aplicação com uma aplicação de lógica existente.  Aplicações de lógica previamente criadas com um plano de serviço de aplicação irão continuar a comportam-se como antes onde, consoante o plano escolhido, irá obter limitada depois de um número de execuções diárias é excedido e não vai ser faturado utilizando o indicador de execução de ação.

Planos do serviço de aplicação e os respetivos diária execuções de acção permitidos:

| |Livre/partilhados/Basic|Padrão|Premium|
|---|---|---|---|
|Execuções ação por dia| 200|10.000|50000|

### <a name="convert-from-consumption-to-app-service-plan-pricing"></a>Converter de consumo para o plano de serviço de aplicação preços

Para fazer referência a um plano de serviço de aplicação para um consumo lógica de aplicação, pode simplesmente [executar a abaixo PowerShell script](https://github.com/logicappsio/ConsumptionToAppServicePlan).  Certifique-se que tiver o [Azure PowerShell ferramentas](https://github.com/Azure/azure-powershell) instalado pela primeira vez.

``` powershell
Param(
    [string] $AppService_RG = '<app-service-resource-group>',
    [string] $AppService_Name = '<app-service-name>',
    [string] $LogicApp_RG = '<logic-app-resource-group>',
    [string] $LogicApp_Name = '<logic-app-name>',
    [string] $subscriptionId = '<azure-subscription-id>'
)

Login-AzureRmAccount 
$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId
$appserviceplan = Get-AzureRmResource -ResourceType "Microsoft.Web/serverFarms" -ResourceGroupName $AppService_RG -ResourceName $AppService_Name
$logicapp = Get-AzureRmResource -ResourceType "Microsoft.Logic/workflows" -ResourceGroupName $LogicApp_RG -ResourceName $LogicApp_Name

$sku = @{
    "name" = $appservicePlan.Sku.tier;
    "plan" = @{
      "id" = $appserviceplan.ResourceId;
      "type" = "Microsoft.Web/ServerFarms";
      "name" = $appserviceplan.Name  
    }
}

$updatedProperties = $logicapp.Properties | Add-Member @{sku = $sku;} -PassThru

$updatedLA = Set-AzureRmResource -ResourceId $logicapp.ResourceId -Properties $updatedProperties -ApiVersion 2015-08-01-preview
```

### <a name="convert-from-app-service-plan-pricing-to-consumption"></a>Converter o plano de serviço de aplicação preços consumo

Para alterar uma aplicação de lógica que tem um plano de serviço de aplicação associada a um modelo de consumo remova a referência para o plano de serviço de aplicação na definição de aplicação de lógica.  Isto pode ser feito com uma chamada para um cmdlet do PowerShell:

`Set-AzureRmLogicApp -ResourceGroupName ‘rgname’ -Name ‘wfname’ –UseConsumptionModel -Force`

## <a name="pricing"></a>Preços

Para preços detalhes, consulte [Lógica preços de aplicações](https://azure.microsoft.com/pricing/details/logic-apps/).

## <a name="next-steps"></a>Próximos passos

- [Uma descrição geral das aplicações de lógica][whatis]
- [Criar a sua primeira aplicação de lógica][create]

[pricing]: https://azure.microsoft.com/pricing/details/logic-apps/
[whatis]: app-service-logic-what-are-logic-apps.md
[create]: app-service-logic-create-a-logic-app.md

