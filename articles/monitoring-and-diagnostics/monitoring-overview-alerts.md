<properties
    pageTitle="Descrição geral de alertas no Microsoft Azure | Microsoft Azure"
    description="Alertas permitem-lhe monitorizar métricas de recurso Azure, eventos ou registos de início e a ser notificado quando uma condição especificada for cumprida."
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
    ms.date="09/24/2016"
    ms.author="robb"/>

# <a name="overview-of-alerts-in-microsoft-azure"></a>Descrição geral de alertas no Microsoft Azure


Este artigo descreve o que alertas estão, os respetivos benefícios e como começar a utilizá-los.  

## <a name="what-are-alerts"></a>O que são alertas?
Os alertas estão um método de métricas de recurso Azure monitorização, eventos, ou registos de início e, em seguida, a ser notificado quando uma condição especificada é cumprida.

Pode receber alertas com base em:

- **Valores métricas**: este alerta accionadores quando o valor de uma métrica especificado este se cruza um determinado limiar que atribui em qualquer direção. Isto é, accionar ambos quando a condição é cumprida pela primeira vez e, em seguida, mais tarde quando o condição é já não está a ser cumprida.
- **Eventos de registo de atividade**: este alerta pode acionar todos os eventos ou apenas quando ocorre um determinado número de eventos.

## <a name="alerts-in-different-azure-services"></a>Alertas no diferentes serviços do Azure

Os alertas estão disponíveis nos diferentes serviços, incluindo:

- **Informações de aplicação**: permite web teste e métrica alertas. Consulte o artigo [definir alertas em informações de aplicação](../application-insights/app-insights-alerts.md) e [disponibilidade de Monitor e capacidade de qualquer site a resposta](../application-insights/app-insights-monitor-web-app-availability.md).
- **Análise de registo (operações de gestão de conjunto de aplicações)**: permite que o encaminhamento de registos de diagnóstico para a análise de registo. Conjunto de aplicações de gestão de operações permite métrica, registo e outros tipos de alerta. Para mais informações, consulte o artigo [alertas no registo de análise](../log-analytics/log-analytics-alerts.md).  
- **Azure Monitor**: permite que os alertas com base em valores métricas e eventos de registo de atividade. Azure Monitor inclui [Azure Monitor REST API](https://msdn.microsoft.com/library/dn931943.aspx).  Para mais informações, consulte o artigo [utilizar o portal do Azure, PowerShell ou a interface de comandos para criar alertas](insights-alerts-portal.md).

## <a name="alert-actions"></a>Acções de alerta
Pode configurar um alerta para fazer o seguinte:

- Envie notificações de correio eletrónico para o administrador do serviço para coadministradores ou para endereços de e-mail adicionais que especificar.
- Ligar uma webhook, que permite-lhe iniciação ações de automatização adicionais.

 ![Explicada de alertas](./media/monitoring-overview-alerts/AlertsOverviewResource3.png)


## <a name="next-steps"></a>Próximos passos

Obter informações sobre regras de alertas e configurá-las utilizando:

- [Portal do Azure](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [Interface de comandos (CLI)](insights-alerts-command-line-interface.md)
- [Azure Monitor REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx)
