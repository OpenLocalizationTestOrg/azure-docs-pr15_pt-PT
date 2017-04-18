<properties
    pageTitle="Descrição geral de métricas no Microsoft Azure | Microsoft Azure"
    description="Saiba como personalizar gráficos monitorização no Azure."
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

# <a name="overview-of-metrics-in-microsoft-azure"></a>Descrição geral de métricas no Microsoft Azure

Todos os serviços do Azure controlar métricas chave que permitem-lhe monitorizar o estado de funcionamento, desempenho, disponibilidade e a utilização dos seus serviços. Pode ver estas métricas no portal do Azure e também pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931930.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para o conjunto completo de métricas de acesso através de programação.

Para alguns serviços, poderá ter de ativar diagnóstico para poder ver qualquer métricas. Para outras pessoas, tal como máquinas virtuais, irá obter um conjunto de métricas básico, mas precisa para activar completo defina métricas de alta frequência. Consulte o artigo [Ativar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para obter mais informações.

## <a name="using-monitoring-charts"></a>Utilizar a monitorização de gráficos

Poder criar gráficos qualquer uma das métricas-las durante qualquer período de tempo que escolher.

1. No [Portal do Azure](https://portal.azure.com/), clique em **Procurar**e, em seguida, um recurso está interessado em monitorização.

2. A secção de **monitorização** contém as métricas mais importantes para cada recurso Azure. Por exemplo, uma aplicação web tem **os pedidos e erros**, onde como uma máquina virtual teria de **percentagem de CPU** e **disco ler e escrever**:  ![lente de monitorização](./media/insights-how-to-customize-monitoring/Insights_MonitoringChart.png)

3. Clicar em qualquer gráfico irá apresentar a pá **métrica** . No pá, para além do gráfico, é uma tabela que mostra-lhe agregações de métricas (por exemplo, média, mínima e máxima, sobre o intervalo de tempo que escolheu). Abaixo que são as regras de alertas para o recurso.
    ![Métrica pá](./media/insights-how-to-customize-monitoring/Insights_MetricBlade.png)

4. Para personalizar as linhas que aparecem, clique no botão **Editar** no gráfico ou, o comando **Editar gráfico** de pá métrica.

5. No pá a editar consulta que pode fazer três elementos:
    - Alterar o intervalo de tempo
    - Mudar o aspeto entre linhas e de barra
    - Selecione metics diferentes ![Editar consulta](./media/insights-how-to-customize-monitoring/Insights_EditQuery.png)

6. Alterar o intervalo de tempo é tão fácil como selecionar um intervalo diferente (como **Horas anteriores**) e clicar em **Guardar** na parte inferior da pá. Também pode escolher **personalizada**, que permite-lhe escolher qualquer período de tempo ao longo das últimas 2 semanas. Por exemplo, pode ver as duas semanas todos ou, apenas 1 hora de ontem. Escreva na caixa de texto para introduzir uma hora diferente.
    ![Intervalo de tempo personalizado](./media/insights-how-to-customize-monitoring/Insights_CustomTime.png)

7. Abaixo do intervalo de tempo, o canal escolha qualquer número de métricas para mostrar no gráfico.

8. Quando clicar em guardar as alterações serão guardadas para esse recurso. Por exemplo, se tiver duas máquinas virtuais e alterar um gráfico das,-lo será não ter um impacto da outra.

## <a name="creating-side-by-side-charts"></a>Criar gráficos lado a lado

Com a personalização poderosa no portal do pode adicionar quantas gráficos que desejar.

1. No menu **…** na parte superior da pá clique em **Adicionar mosaicos**:  
    ![Menu adicionar](./media/insights-how-to-customize-monitoring/Insights_AddMenu.png)
2. Em seguida, que pode selecionar selecionar um gráfico a partir da **Galeria** no lado direito do seu ecrã:  ![Galeria](./media/insights-how-to-customize-monitoring/Insights_Gallery.png)
3. Se não vir a métrica do que pretende, pode sempre adicionar um dos métricas predefinidas e **Editar** o gráfico para mostrar a métrica do que precisa.

## <a name="monitoring-usage-quotas"></a>Monitorizar quotas de utilização

A maior parte dos métricas mostram tendências ao longo do tempo, mas determinados dados, como as quotas de utilização, são informações de ponto-in-time com um determinado limiar.

Também pode ver as quotas de utilização no pá para recursos que tenham quotas:

![Utilização](./media/insights-how-to-customize-monitoring/Insights_UsageChart.png)

Como com métricas, pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931963.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para aceder ao conjunto completo de quotas de utilização de através de programação.

## <a name="next-steps"></a>Próximos passos

* [Receber notificações de alerta](insights-receive-alert-notifications.md) sempre que uma métrica cruza um determinado limiar.
* [Activar a monitorização e diagnóstico](insights-how-to-use-diagnostics.md) para recolher detalhadas métricas de alta frequência no seu serviço.
* [Dimensionar automaticamente a contagem de instâncias](insights-how-to-scale.md) para se certificar de que o seu serviço está disponível e a responder.
* [Monitorizar o desempenho de aplicação](../application-insights/app-insights-azure-web-apps.md) se pretender compreender exatamente como o código está a executar na nuvem.
* Utilize [Informações de aplicação para aplicações JavaScript e páginas web](../application-insights/app-insights-web-track-usage.md) para aceder a análise de cliente quais os browsers que visita uma página web.
* [Disponibilidade de monitor e consequência de qualquer página web](../application-insights/app-insights-monitor-web-app-availability.md) com informações de aplicação para que pode saber se a página é premida.
