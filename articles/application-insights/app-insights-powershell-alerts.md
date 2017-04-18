<properties
    pageTitle="Utilizar o Powershell para definir alertas nas informações de aplicação"
    description="Automatize a configuração da aplicação informações para obter as mensagens de correio eletrónico sobre as alterações métricas."
    services="application-insights"
    documentationCenter=""
    authors="alancameronwills"
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article"
    ms.date="02/19/2016"
    ms.author="awills"/>

# <a name="use-powershell-to-set-alerts-in-application-insights"></a>Utilizar o PowerShell para definir alertas nas informações de aplicação

Pode automatizar a configuração de [alertas](app-insights-alerts.md) no [Visual Studio aplicação informações](app-insights-overview.md).

Além disso, pode [Definir webhooks para automatizar a sua resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md).

## <a name="one-time-setup"></a>Configuração única

Se não utilizou o PowerShell com a sua subscrição Azure antes:

Instale o módulo Azure Powershell no computador em que pretende executar os scripts.

 * Instalar [Microsoft Web plataforma Installer (v5 ou superior)](http://www.microsoft.com/web/downloads/platform.aspx).
 * Utilizá-la para instalar o Powershell do Microsoft Azure


## <a name="connect-to-azure"></a>Ligar ao Azure

Inicie o Azure PowerShell e [Ligar](../powershell-install-configure.md)a sua subscrição:

```PowerShell

    Add-AzureAccount
    Switch-AzureMode AzureResourceManager
```


## <a name="get-alerts"></a>Receber alertas

    Get-AlertRule -ResourceGroup "Fabrikam" [-Name "My rule"] [-DetailedOutput]

## <a name="add-alert"></a>Adicionar alerta


    Add-AlertRule  -Name "{ALERT NAME}" -Description "{TEXT}" `
     -ResourceGroup "{GROUP NAME}" `
     -ResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
     -MetricName "{METRIC NAME}" `
     -Operator GreaterThan  `
     -Threshold {NUMBER}   `
     -WindowSize {HH:MM:SS}  `
     [-SendEmailToServiceOwners] `
     [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM" ] `
     -Location "East US"
     -RuleType Metric



## <a name="example-1"></a>Exemplo 1

E-mail se a resposta do servidor para pedidos de HTTP, média superior 5 minutos, é mais lenta do que 1 segundo. Recurso minhas informações de aplicação chama-se IceCreamWebApp e, ele está num grupo de recursos Fabrikam. Sou o proprietário da subscrição do Azure.

GUID é o ID da subscrição (não a chave de instrumentação da aplicação).

    Add-AlertRule -Name "slow responses" `
     -Description "email me if the server responds slowly" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "request.duration" `
     -Operator GreaterThan `
     -Threshold 1 `
     -WindowSize 00:05:00 `
     -SendEmailToServiceOwners `
     -Location "East US" -RuleType Metric

## <a name="example-2"></a>Exemplo 2

Tenho de ter uma aplicação em que utilizo [TrackMetric()](app-insights-api-custom-events-metrics.md#track-metric) para comunicar uma métrica denominada "salesPerHour." Envie que um e-mail para os meus colegas se "salesPerHour" descer abaixo 100, calcular a média superior a 24 horas.

    Add-AlertRule -Name "poor sales" `
     -Description "slow sales alert" `
     -ResourceGroup "Fabrikam" `
     -ResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
     -MetricName "salesPerHour" `
     -Operator LessThan `
     -Threshold 100 `
     -WindowSize 24:00:00 `
     -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
     -Location "East US" -RuleType Metric

A mesma regra pode ser utilizada para a métrica do comunicado utilizando o [parâmetro de medida](app-insights-api-custom-events-metrics.md#properties) da chamada de outro controlo como TrackEvent ou trackPageView.

## <a name="metric-names"></a>Métricos nomes

Métrica nome | Nome do ecrã | Descrição
---|---|---
`basicExceptionBrowser.count`|Exceções de browser|Contagem de exceções não identificadas iniciadas no browser.
`basicExceptionServer.count`|Exceções de servidor|Contagem de exceções não processadas lançadas pela aplicação
`clientPerformance.clientProcess.value`|Tempo de processamento de cliente|Tempo entre o último byte de um documento a receber até que o DOM é carregado. Ainda poderão ser processamento de pedidos de assíncrona.
`clientPerformance.networkConnection.value`|Tempo de ligação de rede de carregamento de página| Tempo que browser demora a ligar à rede. Pode ser 0 se em cache.
`clientPerformance.receiveRequest.value`|Receber o tempo de resposta| Tempo entre o browser enviar pedido para começar a receber resposta.
`clientPerformance.sendRequest.value`|Enviar o tempo do pedido| Tempo despendido pelo browser para enviar pedido.
`clientPerformance.total.value`|Tempo de carregamento de página do browser|Tempo do pedido de utilizador até DOM, folhas de estilo, scripts e as imagens são carregados.
`performanceCounter.available_bytes.value`|Memória disponível|Memória física imediatamente disponível para um processo ou para utilização do sistema.
`performanceCounter.io_data_bytes_per_sec.value`|Processo IO taxa|Total de bytes por segundo ler e escritos nos ficheiros, rede e dispositivos.
`performanceCounter.number_of_exceps_thrown_per_sec`|taxa de exceção|Exceções iniciadas por segundo.
`performanceCounter.percentage_processor_time.value`|CPU do processo|A percentagem de tempo decorrido todos os threads do processo utilizado pelo processador para instruções de execução para o processo de aplicações.
`performanceCounter.percentage_processor_total.value`|Processador de tempo|A percentagem de tempo que o processador passa em que não sejam Idle threads.
`performanceCounter.process_private_bytes.value`|Processo de bytes privados|Memória exclusivamente atribuído para processos a aplicação monitorizada.
`performanceCounter.request_execution_time.value`|Tempo de execução de pedido ASP.NET|Tempo de execução do pedido mais recente.
`performanceCounter.requests_in_application_queue.value`|Pedidos ASP.NET na fila de execução|Comprimento da fila de pedido de aplicação.
`performanceCounter.requests_per_sec`|Taxa de pedido ASP.NET|Taxa de todos os pedidos para a aplicação por segundo a partir do ASP.NET.
`remoteDependencyFailed.durationMetric.count`|Falhas de dependência|Contagem de falhadas chamadas efetuadas pela aplicação do servidor para recursos externos.
`request.duration`|Tempo de resposta do servidor|Tempo entre receber um pedido de HTTP e acabamento enviar a resposta.
`request.rate`|Taxa de pedido|Taxa de todos os pedidos para a aplicação por segundo.
`requestFailed.count`|Pedidos de falhados|Pedidos de contagem de HTTP que resultou num código de resposta > = 400
`view.count`|Vistas de página|Contagem de pedidos de utilizador do cliente para uma página web. Tráfego síntese estejam filtrado.
{o nome personalizado métrico}|{O seu nome de métrica}|O valor métrico relatados pelo [TrackMetric](app-insights-api-custom-events-metrics.md#track-metric) ou no [parâmetro de medidas de uma chamada de controlo](app-insights-api-custom-events-metrics.md#properties).

Métricas são enviadas por módulos de telemetria diferente:

Grupo métrico | Módulo Recolectores
---|---
basicExceptionBrowser,<br/>clientPerformance,<br/>vista | [Browser JavaScript](app-insights-javascript.md)
performanceCounter | [Desempenho](app-insights-configuration-with-applicationinsights-config.md#nuget-package-3)
remoteDependencyFailed| [Dependência](app-insights-configuration-with-applicationinsights-config.md#nuget-package-1)
pedido,<br/>requestFailed|[Pedido de servidor](app-insights-configuration-with-applicationinsights-config.md#nuget-package-2)

## <a name="webhooks"></a>Webhooks

Pode [automatizar a sua resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure ligarem um endereço web da sua escolha, quando um alerta é elevado.

## <a name="see-also"></a>Consulte também


* [Script para configurar a aplicação de informações](app-insights-powershell-script-create-resource.md)
* [Criar aplicações informações e recursos de teste de web a partir de modelos](app-insights-powershell.md)
* [Automatizar ligação diagnósticos do Microsoft Azure para informações de aplicação](app-insights-powershell-azure-diagnostics.md)
* [Automatizar a sua resposta a um alerta](../monitoring-and-diagnostics/insights-webhooks-alerts.md)
