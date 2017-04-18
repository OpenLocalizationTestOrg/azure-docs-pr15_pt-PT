<properties
    pageTitle="Receber notificações de alerta para serviços Azure | Microsoft Azure"
    description="Ser notificado quando as condições de regras de alerta forem cumpridas."
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
    ms.date="09/08/2015"
    ms.author="robb"/>

# <a name="receive-alert-notifications"></a>Receber notificações de alerta

Poderá receber um alerta com base no métricas de monitorização para ou eventos no seus serviços Azure.

Para uma regra de alerta sobre um valor métrico, quando o valor de uma métrica especificado este se cruza um determinado limiar atribuído, a regra de alerta fica ativa e pode enviar uma notificação. Para uma regra de alerta de eventos, uma regra pode enviar uma notificação no *todos os* eventos, ou, apenas quando ocorrer um determinado número de eventos.

Quando cria uma regra de alerta, pode selecionar opções para enviar uma notificação de e-mail para o administrador de serviços e coadministradores ou para outro administrador pode especificar. Um e-mail de notificação é enviado quando a regra fica ativa e, quando uma condição alerta é resolvida.

Pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931945.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para configurar e obter informações sobre regras de alertas através de programação.

## <a name="create-an-alert-rule"></a>Criar uma regra de alerta

1. No [portal](https://portal.azure.com/), clique em **Procurar**e, em seguida, um recurso está interessado em monitorização.

2. Clique no mosaico **regras alertas** na lente de **operações** .

3. Clique no comando de **alerta de adicionar** .

    ![Adicionar alerta](./media/insights-receive-alert-notifications/Insights_AddAlert.png)

4. Pode atribuir um nome a sua regra alerta e escolha uma descrição que será apresentado no e-mail notificação.

5. Quando seleciona **métricas** irá Selecione uma condição e o limiar de valor para a métrica do. Este é o período de tempo que utiliza o Azure a atividade de alerta do monitor do computador e desenho.

    ![Condição e limiar](./media/insights-receive-alert-notifications/Insights_ConditionAndThreshold.png)

6. Também pode escolher **eventos**e receber uma notificação quando um determinado evento acontece.

    ![Eventos](./media/insights-receive-alert-notifications/Insights_Events.png)

7. Por fim, pode optar por enviar notificação de e-mail aos administradores responsáveis.

Depois de clicar em **Guardar**, dentro de alguns minutos será informado sempre que a métrica do que escolher excede o limite.

## <a name="managing-your-alert-rules"></a>Gerir as suas regras de alertas

Assim que tiver criado uma regra de alerta, pode ver uma pré-visualização do alerta limiar em comparação com a métrica do dia anterior.

![Eventos](./media/insights-receive-alert-notifications/Insights_EditAlert.png)


Obviamente pode editar esta regra alerta e **Ativar** ou **desativar** a mesma se pretender temporariamente deixar de receber notificações acerca do mesmo.

## <a name="next-steps"></a>Próximos passos

* [Configurar webhooks no seus alertas](insights-webhooks-alerts.md) a rota de notificações para vários canais
* [Métricas de serviço do monitor](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.
* [Activar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para recolher detalhadas métricas de alta frequência no seu serviço.
* [Disponibilidade de monitor e consequência de qualquer página web](../application-insights/app-insights-monitor-web-app-availability.md) com informações de aplicação para que pode saber se a página é premida.
* [Monitorizar o desempenho de aplicação](../application-insights/app-insights-azure-web-apps.md) se pretender compreender exatamente como o código está a executar na nuvem.
* [Ver eventos e registos de auditoria](insights-debugging-with-events.md) para saber tudo o que aconteceu no seu serviço.
* [Estado de funcionamento do serviço de pista](insights-service-health.md) para saber quando Azure Ocorreu interrupções degradação ou serviço de desempenho.
