<properties
    pageTitle="Monitorização de desempenho para aplicações móveis web com a análise de programador | Microsoft Azure"
    description="Desempenho da aplicação e a utilização de monitorização para programadores da aplicação móvel. , ambiente de trabalho, o serviço web e aplicações de back-end com HockeyApp e informações de aplicação."
    authors="alancameronwills"
    services="application-insights"
    documentationCenter=""
    manager="douge"/>

<tags
    ms.service="application-insights"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="na"
    ms.topic="article" 
    ms.date="09/19/2016"
    ms.author="awills"/>

# <a name="mobile-analytics-with-hockeyapp-and-application-insights"></a>Análise móvel com HockeyApp e informações de aplicação

Monitorize o desempenho e a utilização das suas teste beta e aplicações de ambiente de trabalho e móveis implementadas com [HockeyApp](https://hockeyapp.net/). Monitorize secundária serviço e back-end aplicações web com [Informações de aplicação do Visual Studio](app-insights-overview.md). Analisar os dados a partir das aplicações de cliente e no servidor em Analytics e apresentar os gráficos juntamente com os outros um dashboard Azure.

Análise de programador do Microsoft oferece duas soluções para monitorização de desempenho de aplicações:

* Aplicações **HockeyApp para dispositivos móveis** e ambiente de trabalho.
 * Distribua versões de teste para utilizadores selecionados.
 * Análise de uma falha de sistema.
 * Telemetria personalizada para uma análise de utilização.
* Aplicações de **Informações de aplicação para web sites** e serviços e back-end.
 * Métricas de desempenho e a utilização e alertas.
 * Exceção relato diagnóstico rastreio e.
 * Procurar diagnóstico com ligações de telemetria relacionados e filtragem.

Ambas as soluções oferecem:

 * Poderosa **[linguagem de consulta analítico](app-insights-analytics.md)** para análise e diagnóstico.
 * **[Exportar dados](app-insights-export-telemetry.md)** para o seu próprio armazenamento.
 * Apresentação de **dashboard integrada** de analíticos gráficos e tabelas.

## <a name="monitor-your-app-components"></a>Monitorizar os componentes de aplicação

Siga as instruções nestas páginas para instalar o SDK no seu código e começar a controlar a sua aplicação.

### <a name="web-apps---application-insights"></a>Web apps - informações de aplicação

* [Aplicação web do ASP.NET](app-insights-asp-net.md) 
* [Java web app](app-insights-java-get-started.md)
* [NODE.js web app](https://github.com/Microsoft/ApplicationInsights-node.js)
* [Serviços em nuvem Azure](app-insights-cloudservices.md)

### <a name="mobile-apps---hockeyapp"></a>Aplicações móveis - HockeyApp

* [aplicação do iOS](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Aplicação do Mac OS X](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Aplicação para Android](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [Aplicação universal Windows](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Aplicação do Windows Phone 8 e 8.1](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Apresentação do Windows Foundation aplicação](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)

Para aplicações de ambiente de trabalho do Windows, recomendamos que HockeyApp. Mas também pode [Enviar telemetria através de uma aplicação do Windows para informações de aplicação](app-insights-windows-desktop.md). Poderá pretender fazê-lo para experimentar com informações de aplicação.


## <a name="analytics-and-export-for-hockeyapp-telemetry"></a>Exportar para HockeyApp telemetria e de análise

Pode investigar HockeyApp personalizada e inicie sessão utilizando as funcionalidades de exportar contínua e de análise de informações da aplicação ao [Configurar uma bridge](app-insights-hockeyapp-bridge-app.md)de telemetria.




