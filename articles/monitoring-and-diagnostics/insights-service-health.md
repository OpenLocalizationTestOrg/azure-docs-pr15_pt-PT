<properties
    pageTitle="Controlar o estado de funcionamento do serviço Azure utilizando Azure Monitor de atividade registos | Microsoft Azure"
    description="Saber para quando Azure Ocorreu interrupções degradação ou serviço de desempenho. "
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

# <a name="track-azure-service-health-using-azure-monitor-activity-logs"></a>Controlar o estado de funcionamento do serviço Azure utilizando Azure Monitor de atividade de registos

Azure publicizes sempre que existe uma degradação do serviço interrupção ou um desempenho. Pode procurar estes eventos no portal do Azure e também pode utilizar a [REST API](https://msdn.microsoft.com/library/azure/dn931927.aspx) ou [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights/) para aceder ao conjunto completo de eventos através de programação.

## <a name="browse-the-service-health-logs-for-your-subscription"></a>Procurar registos de estado de funcionamento do serviço para a sua subscrição

1. Inicie sessão no [Portal do Azure](https://portal.azure.com/).

2. Na **Home** deverá ver um mosaico designado por **Estado de funcionamento do serviço**. Clique na mesma.

    ![Home page](./media/insights-service-health/Insights_Home.png)

3. Verá uma lista de todas as regiões no Azure. Clique em qualquer região para ativar a consulta de registo de atividade mostra incidentes de serviço que tenham afetada qualquer uma das suas subscrições na últimas 24 horas.

    ![Funcionamento de serviços de subscrição de registo de atividade](./media/insights-service-health/AzureActivityLogServiceHealth3.png)

4. Pode ver os detalhes de um incidente de individual ao clicar nesse evento na tabela.

5. Altere o **Timespan** para ver um período definido mais longo.

## <a name="next-steps"></a>Próximos passos

* [Disponibilidade de monitor e consequência de qualquer página web](../application-insights/app-insights-monitor-web-app-availability.md) com informações de aplicação para que pode saber se a página é premida.
