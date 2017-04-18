<properties
    pageTitle="Transmitir em fluxo Azure registos de diagnóstico para evento concentradores | Microsoft Azure"
    description="Saiba como transmitir em fluxo Azure registos de diagnóstico para concentradores do evento."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="johnkem"/>

# <a name="stream-azure-diagnostic-logs-to-event-hubs"></a>Transmitir em fluxo Azure registos de diagnóstico para concentradores de evento

**[Registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)** pode ser transmitido em tempo real próximo para qualquer aplicação com a opção "Exportar para o evento concentradores" incorporada no Portal do ou ao ativar o Id da regra Bus serviço numa definição de diagnóstico através dos cmdlets do PowerShell do Azure ou clip de Azure.

## <a name="what-you-can-do-with-diagnostics-logs-and-event-hubs"></a>O que pode fazer com os registos de diagnóstico e concentradores de evento
Seguem-se apenas algumas formas que pode utilizar a funcionalidade de transmissão registos de diagnóstico:

- **Registos de sequência para festa 3º registo e sistemas de telemetria** – ao longo do tempo, concentradores evento de transmissão irão tornar-se a mecanismos para encaminhar registos de diagnóstico para terceiros SIEMs e inicie sessão soluções de análise.

- **Estado de funcionamento de serviço de vista por transmissão de dados de "caminho quente" para obter** – utilizando concentradores de evento, a análise da cadeia e obter, o utilizador pode facilmente transformar os dados de diagnóstico para o próximo informações em tempo real no seus serviços Azure. [Este artigo de documentação fornece uma descrição geral de como configurar um evento concentradores, processar dados com a análise da cadeia e utilizar obter como um resultado](../stream-analytics/stream-analytics-power-bi-dashboard.md). Eis algumas sugestões para obter configurado com os registos de diagnóstico:
    - Os controladores de evento para uma categoria de registos de diagnóstico é criado automaticamente quando marque a opção no portal do ou ativá-lo através do PowerShell, para que pretende selecionar os controladores do evento no serviço Bus espaço de nomes com o nome que comece por "-informações"
    - Eis uma consulta de análise da cadeia de exemplo que pode utilizar para analisar simplesmente todos os registo dados a uma tabela de obter:

```
SELECT
records.ArrayValue.[Properties you want to track]
INTO
[OutputSourceName – the PowerBI source]
FROM
[InputSourceName] AS e
CROSS APPLY GetArrayElements(e.records) AS records
```

- **Criar um telemetria personalizada e a plataforma de registo** – se já tiver uma plataforma de telemetria personalizado ou são apenas a pensar sobre a criação de um, altamente dimensionáveis publiquem-subscrever natureza do evento concentradores permite-lhe flexível ingerir esta última registos de diagnóstico. [Consulte Duarte Rosanova do Guia para utilizar o evento concentradores numa plataforma de telemetria do escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="enable-streaming-of-diagnostic-logs"></a>Ativar a transmissão de registos de diagnóstico
Pode ativar a transmissão de registos de diagnóstico através de programação, através do portal do, ou utilizando a [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931943.aspx). Qualquer forma, que escolher um espaço de nomes do serviço Bus e concentradores um evento é criado no espaço de nomes para cada categoria de registo que ativar. Uma **Categoria de registo** de diagnóstico é um tipo de registo que um recurso poderá recolher. Pode selecionar as categorias de registo que gostaria de recolher para um recurso em particular no Portal do Azure em pá o diagnóstico.

![Categorias de registo no Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/log-categories.png)

> [AZURE.WARNING] Ativar e transmissão registos de diagnóstico do cluster recursos (por exemplo, VMs ou serviço ferro) [requer um conjunto diferente de passos](../event-hubs/event-hubs-streaming-azure-diags-data.md).

### <a name="via-powershell-cmdlets"></a>Via os cmdlets do PowerShell
Para ativar a transmissão via os [Cmdlets do PowerShell Azure](insights-powershell-samples.md), pode utilizar o `Set-AzureRmDiagnosticSetting` cmdlet com estes parâmetros:

```
Set-AzureRmDiagnosticSetting -ResourceId [your resource Id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
```

O ID da regra Bus serviço é uma cadeia com neste formato: `{service bus resource ID}/authorizationrules/{key name}`, por exemplo, `/subscriptions/{subscription ID}/resourceGroups/Default-ServiceBus-WestUS/providers/Microsoft.ServiceBus/namespaces/{service bus namespace}/authorizationrules/RootManageSharedAccessKey`.


### <a name="via-azure-cli"></a>Através do clip Azure
Para ativar streaming através do [Azure clip](insights-cli-samples.md), pode utilizar o `insights diagnostic set` comando da seguinte forma:

```
azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
```

Utilize o mesmo formato para serviço Bus ID da regra, conforme explicado para o Cmdlet do PowerShell.

###<a name="via-azure-portal"></a>Através do Portal do Azure
Para ativar streaming através do Portal do Azure, navegue para as definições de diagnósticos de um recurso e selecione 'Exportar para o evento concentrador'.

![Exportar para o evento concentradores no Portal](./media/monitoring-stream-diagnostic-logs-to-event-hubs/portal-export.png)

Configurá-lo, selecione um espaço de nomes de Bus de serviço existente. O espaço de nomes selecionado será onde os controladores do evento é criado (se esta for os primeira vez transmissão registos de diagnóstico) ou transmitido em sequência para (se já existem recursos que são transmissão nessa categoria de registo para este espaço de nomes), e a política define as permissões que o dispositivo de transmissão. Hoje em dia, transmissão a um evento concentradores necessita de permissões Gerir, ler e enviar. Pode criar ou modificar as políticas de acesso do espaço de nomes do Bus serviço partilhadas no portal do clássico no separador "Configurar" para o seu espaço de nomes do serviço Bus. Para atualizar uma destas definições de diagnóstico, o cliente tem de ter permissão ListKey na regra de autorização de Bus de serviço.

##<a name="how-do-i-consume-the-log-data-from-event-hubs"></a>Como posso consumir os dados do registo de evento concentradores?
Eis os dados de saída de exemplo a partir dos controladores de evento:

```
{
    "records": [
        {
            "time": "2016-07-15T18:00:22.6235064Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330013509921957/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Error",
            "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T17:58:55.048482Z",
                "endTime": "2016-07-15T18:00:22.4109204Z",
                "status": "Failed",
                "code": "BadGateway",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330013509921957",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "29a9862f-969b-4c70-90c4-dfbdc814e413",
                    "clientTrackingId": "08587330013509921958"
                }
            }
        },
        {
            "time": "2016-07-15T18:01:15.7532989Z",
            "workflowId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA",
            "resourceId": "/SUBSCRIPTIONS/DF602C9C-7AA0-407D-A6FB-EB20C8BD1192/RESOURCEGROUPS/JOHNKEMTEST/PROVIDERS/MICROSOFT.LOGIC/WORKFLOWS/JOHNKEMTESTLA/RUNS/08587330012106702630/ACTIONS/SEND_EMAIL",
            "category": "WorkflowRuntime",
            "level": "Information",
            "operationName": "Microsoft.Logic/workflows/workflowActionStarted",
            "properties": {
                "$schema": "2016-04-01-preview",
                "startTime": "2016-07-15T18:01:15.5828115Z",
                "status": "Running",
                "resource": {
                    "subscriptionId": "df602c9c-7aa0-407d-a6fb-eb20c8bd1192",
                    "resourceGroupName": "JohnKemTest",
                    "workflowId": "243aac67fe904cf195d4a28297803785",
                    "workflowName": "JohnKemTestLA",
                    "runId": "08587330012106702630",
                    "location": "westus",
                    "actionName": "Send_email"
                },
                "correlation": {
                    "actionTrackingId": "042fb72c-7bd4-439e-89eb-3cf4409d429e",
                    "clientTrackingId": "08587330012106702632"
                }
            }
        }
    ]
}
```

| Nome do elemento | Descrição                                            |
|--------------|--------------------------------------------------------|
|registos       | Uma matriz de todos os eventos de registo de carga este útil.            |
|tempo          | Data/hora em que ocorreu o evento.                      |
|categoria      | Categoria de registo para este evento.                           |
|resourceId    | ID do recurso do recurso que gerou este evento. |
|operationName | Nome da operação.                                 |
|nível         | Opcional. Indica o nível de registo de eventos.               |
|Propriedades    | Propriedades do evento.                               |


Pode ver uma lista de todos os recursos fornecedores que suportam a transmissão a concentrador de evento [aqui](monitoring-overview-of-diagnostic-logs.md).

##<a name="next-steps"></a>Próximos passos
- [Leia mais sobre registos de diagnóstico do Azure](monitoring-overview-of-diagnostic-logs.md)
- [Introdução ao evento concentradores](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
