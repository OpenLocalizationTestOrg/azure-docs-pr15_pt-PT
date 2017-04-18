<properties
    pageTitle="Utilizar o portal do Azure para criar alertas para serviços Azure | Microsoft Azure"
    description="Utilize o portal do Azure para criar alertas Azure, que podem accionar notificações ou automatização quando as condições especificadas forem cumpridas."
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
    ms.date="09/23/2016"
    ms.author="robb"/>

# <a name="use-azure-portal-to-create-alerts-for-azure-services"></a>Utilizar o portal do Azure para criar alertas para serviços do Azure

> [AZURE.SELECTOR]
- [Portal](insights-alerts-portal.md)
- [PowerShell](insights-alerts-powershell.md)
- [CLIP](insights-alerts-command-line-interface.md)

## <a name="overview"></a>Descrição geral

Este artigo mostra-lhe como configurar alertas Azure através do portal Azure.   

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


## <a name="create-an-alert-rule-on-a-metric-with-the-azure-portal"></a>Criar uma regra de alerta sobre uma métrica com o portal do Azure

1. No [portal](https://portal.azure.com/), localize o recurso estiver interessado em monitorização e selecioná-la.

2. Selecione **alertas** ou **regras alertas** na secção de monitorização. O texto e o ícone podem variar ligeiramente para recursos diferentes.  

    ![Monitorização](./media/insights-alerts-portal/AlertRulesButton.png)


3. Selecione o comando **Adicionar alerta** e preencha os campos.

    ![Adicionar alerta](./media/insights-alerts-portal/AddAlertOnlyParamsPage.png)

4. **Nome** o alerta regra e escolha uma **Descrição**, que também apresenta em e-mails de notificação.
5. Selecione a **métrica** do que pretende monitorizar e, em seguida, escolha um valor de **condição** e **limiar** para a métrica do. Escolher também o **período** de tempo que a regra métrica tem de ser cumprida antes dos accionadores alertas. Por exemplo, se utilizar o período de tempo "PT5M" e o alerta procura CPU acima 80%, o alerta accionadores quando a CPU tiver sido acima de forma consistente 80% para 5 minutos. Assim que a primeira acionador ocorre, novamente accionadores quando a CPU permanece inferior a 80% para 5 minutos. A medição CPU ocorre minuto 1.   

6. Se pretender que os administradores e a receber uma mensagem quando o alerta é acionada, verifique **os proprietários de E-Mail...** .

7. Se pretender que os e-mails adicionais para receber uma notificação quando o alerta é acionada, adicioná-los no campo **email(s) administrador adicionais** . Separe os múltiplos e-mails com ponto e vírgula-*email@contoso.com;email2@contoso.com*

8. Coloca um URI válido no campo **Webhook** se pretender que o mesmo chamado quando é acionada o alerta.

9. Se utiliza o Azure automatização, pode selecionar um livro de execuções para ser executada quando é acionada o alerta.

10. Selecione **OK** quando terminar para criar o alerta.   

Dentro de alguns minutos, o alerta está ativo e accionadores conforme descrito anteriormente.

## <a name="managing-your-alerts"></a>Gerir os seus alertas

Depois de ter criado um alerta, pode selecionar a mesma e:

- Veja um gráfico que mostra o limiar de métrico e os valores reais do dia anterior.
- Editar ou eliminá-la.
- **Desativar** ou **ativá** -lo se pretender temporariamente parar ou retomar a receber notificações para que o alerta.



## <a name="next-steps"></a>Próximos passos

* [Obter uma descrição geral da monitorização Azure](monitoring-overview.md) incluindo os tipos de informação podem recolher e monitorizar.
* Saiba mais sobre [Configurar webhooks em alertas](insights-webhooks-alerts.md).
* Saiba mais sobre [Runbooks de automatização do Azure](..\automation\automation-starting-a-runbook.md).
* Obter uma [Descrição geral dos registos de diagnóstico](monitoring-overview-of-diagnostic-logs.md) e recolher detalhadas métricas de alta frequência no seu serviço.
* Obter uma [Descrição geral da coleção de métricas](insights-how-to-customize-monitoring.md) para se certificar de que o seu serviço está disponível e a responder.
