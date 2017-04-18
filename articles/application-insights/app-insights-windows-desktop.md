<properties 
    pageTitle="Monitorizar a utilização e desempenho para aplicações de ambiente de trabalho do Windows" 
    description="Analise a utilização e o desempenho da sua aplicação de ambiente de trabalho do Windows com HockeyApp e informações de aplicação." 
    services="application-insights" 
    documentationCenter="windows"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/26/2016" 
    ms.author="awills"/>

# <a name="monitoring-usage-and-performance-in-windows-desktop-apps"></a>Monitorizar a utilização e desempenho nas aplicações de ambiente de trabalho Windows

*Informações de aplicação está na pré-visualização.*

[Informações de aplicação do Visual Studio](app-insights-overview.md) e [HockeyApp](https://hockeyapp.net) permitem-lhe monitorizar a sua aplicação implementada de utilização e desempenho.

> [AZURE.IMPORTANT] Recomendamos que [HockeyApp](https://hockeyapp.net) para distribuir e monitorizar aplicações de ambiente de trabalho e de dispositivo. Com HockeyApp, pode gerir distribuição, testes direto e comentários de utilizador, bem como monitorizar relatórios de utilização e falha de sistema. Também pode [exportar e consultar o seu telemetria com a análise](app-insights-hockeyapp-bridge-app.md).

> Apesar de telemetria pode ser enviada para informações de aplicação a partir de uma aplicação de ambiente de trabalho, esta é principalmente útil para fins depuração e a experimentais.


## <a name="to-send-telemetry-to-application-insights-from-a-windows-application"></a>Para enviar telemetria para informações de aplicação a partir de uma aplicação do Windows

1. No [portal do Azure](https://portal.azure.com), [crie um recurso de informações da aplicação](app-insights-create-new-resource.md). Para tipo de aplicação, selecione a aplicação ASP.NET.
2. Tomar uma cópia da chave de instrumentação. Localize a chave no menu pendente Essentials do novo recurso que acabou de criar. 
3. No Visual Studio, edite os pacotes de NuGet do seu projeto de aplicação e, adicione Microsoft.ApplicationInsights.WindowsServer. (Ou selecione Microsoft.ApplicationInsights se apenas pretende o simples API, sem os módulos de coleções de sites padrão de telemetria.)
4. Defina a chave de instrumentação quer no seu código:

    `TelemetryConfiguration.Active.InstrumentationKey = "`*a chave*`";` 

    ou numa ApplicationInsights.config (se tiver instalado um dos pacotes de telemetria padrão):
 
    `<InstrumentationKey>`*a chave*`</InstrumentationKey>` 

    Se utiliza o ApplicationInsights.config, certifique-se as respetivas propriedades no Explorador de solução estão definidas para **ação criar = conteúdo, copiar para directório de saída = copiar**.
5. [Utilizar a API](app-insights-api-custom-events-metrics.md) para enviar telemetria.
6. Executar a aplicação e, consulte o artigo telemetria no recurso que criou no Portal do Azure.

## <a name="telemetry"></a>Exemplo de código

```C#

    public partial class Form1 : Form
    {
        private TelemetryClient tc = new TelemetryClient();
        ...
        private void Form1_Load(object sender, EventArgs e)
        {
            // Alternative to setting ikey in config file:
            tc.InstrumentationKey = "key copied from portal";

            // Set session data:
            tc.Context.User.Id = Environment.UserName;
            tc.Context.Session.Id = Guid.NewGuid().ToString();
            tc.Context.Device.OperatingSystem = Environment.OSVersion.ToString();

            // Log a page view:
            tc.TrackPageView("Form1");
            ...
        }

        protected override void OnClosing(CancelEventArgs e)
        {
            stop = true;
            if (tc != null)
            {
                tc.Flush(); // only for desktop apps

                // Allow time for flushing:
                System.Threading.Thread.Sleep(1000);
            }
            base.OnClosing(e);
        }

```

## <a name="next-steps"></a>Próximos passos

* [Criar um dashboard](app-insights-dashboards.md)
* [Pesquisa de diagnóstico](app-insights-diagnostic-search.md)
* [Explorar métricas](app-insights-metrics-explorer.md)
* [Escrever a análise de consultas](app-insights-analytics.md)
 
