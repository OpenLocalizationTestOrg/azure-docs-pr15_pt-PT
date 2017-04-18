<properties 
    pageTitle="Resolução de problemas e questões sobre informações de aplicação" 
    description="Algo no Visual Studio aplicação informações pouco claras ou não funciona? Experimente aqui." 
    services="application-insights" 
    documentationCenter=".net"
    authors="alancameronwills" 
    manager="douge"/>

<tags 
    ms.service="application-insights" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/24/2016" 
    ms.author="awills"/>
 
# <a name="questions---application-insights-for-aspnet"></a>Perguntas - informações de aplicação para ASP.NET

## <a name="configuration-problems"></a>Problemas de configuração

*Estou a ter problemas ao meu:*

* [Aplicação .NET](app-insights-asp-net-troubleshoot-no-data.md)
* [Monitorização de uma aplicação já em execução](app-insights-monitor-performance-live-website-now.md#troubleshooting)
* [Diagnósticos do Azure](app-insights-azure-diagnostics.md)
* [Java web app](app-insights-java-troubleshoot.md)
* [Noutras plataformas](app-insights-platforms.md)

*Posso obter sem dados a partir do meu servidor*

* [Conjunto de firewall exceções](app-insights-ip-addresses.md)
* [Configurar um servidor do ASP.NET](app-insights-monitor-performance-live-website-now.md)
* [Configurar um servidor de Java](app-insights-java-agent.md)


## <a name="can-i-use-application-insights-with-"></a>Posso utilizar a aplicação informações com …?

[Consulte o artigo plataformas][platforms]


## <a name="is-it-free"></a>É gratuito?

* Sim, se optar por gratuito [preços de camadas](app-insights-pricing.md). Obter a maioria das funcionalidades e uma quota generosa de dados. 
* Tem de fornecer os seus dados do cartão de crédito para registar com Microsoft Azure, mas não taxas serão feitas a menos que utilize o outro pagar para Azure serviço ou atualizar explicitamente para uma camada de pagamento.
* Se a sua aplicação envia dados mais do que a quota de mensal para a camada gratuita, deixa de ser tem sessão iniciada. Se o que acontece, pode optar por iniciar pagamentos ou aguarde até que a quota de é reposta no final do mês.
* Dados de utilização e sessão básico não estão sujeito a uma quota.
* Também existe uma avaliação de 30 dias gratuita durante o qual receber as funcionalidades pagas gratuitamente.
* Cada recurso da aplicação tem uma quota separada e configurar o seu camada comparar independentemente de quaisquer outros.

#### <a name="what-do-i-get-if-i-pay"></a>O que posso obter se posso pagar?

* Maior [quota mensal de dados](https://azure.microsoft.com/pricing/details/application-insights/).
* Opção de pagamento 'ultrapassadas' para continuar a recolha de dados a quota de mensal. Se os seus dados abrange quota, está a cobrado por Mb.
* [Exportar contínuo](app-insights-export-telemetry.md).


## <a name="q14"></a>O que modificar informações de aplicação no meu projecto?

Os detalhes variam consoante o tipo de projeto. Para uma aplicação web:


+ Adiciona estes ficheiros ao projeto:

 + ApplicationInsights.config. 
 + ai.js


+ Instalações estes pacotes NuGet:

 -  *API de informações da aplicação* - o essencial API

 -  *Informações de aplicações API para aplicações Web* - utilizado para enviar telemetria do servidor

 -  *Informações de aplicações API para aplicações JavaScript* - utilizado para enviar telemetria a partir do cliente

    Os pacotes incluem estes assemblagem:

 - Microsoft.ApplicationInsights

 - Microsoft.ApplicationInsights.Platform

+ Insere itens para o:

 - Web. config

 - Packages.config

+ (Novos projectos apenas - se a [Adicionar informações de aplicação a um projeto existente][start], tem de fazer isto manualmente.) Insere fragmentos o código do cliente e servidor a inicialização-los com o ID do recurso informações de aplicação. Por exemplo, numa aplicação do MVC, código é inserido a página mestra Views/Shared/_Layout.cshtml


## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>Como atualizar a partir de versões mais antigas do SDK?

Consulte as [notas de lançamento](app-insights-release-notes.md) para o SDK adequado para o tipo de aplicação. 



## <a name="update"></a>Como posso alterar qual meu projecto envia dados para o recurso Azure?

No Explorador de solução, com o botão direito `ApplicationInsights.config` e selecione **Atualizar informações de aplicação**. Pode enviar os dados para um recurso existente ou novo no Azure. O Assistente de actualização alterações na chave instrumentação ApplicationInsights.config, que determina onde o servidor SDK envia os seus dados. A menos que desmarque a opção "Actualizar tudo", também irá alterar a chave de onde aparece nas suas páginas web.


#### <a name="data"></a>Quanto tempo são guardados os dados no portal do? É segura?

Veja [retenção de dados e privacidade][data].

## <a name="logging"></a>Funcionalidade de registo

#### <a name="post"></a>Como posso ver dados POST na pesquisa diagnóstico?

Vamos não iniciar dados POST automaticamente, mas pode utilizar uma chamada de TrackTrace: colocar os dados no parâmetro da mensagem. Isto tem um limite de tamanho mais longo do que os limites de propriedades da cadeia, apesar de não é possível filtrar no mesmo. 

## <a name="security"></a>Segurança

#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>Os meus dados é segura no portal do? Quanto tempo é-retido?

Consulte o artigo [dados retenção e privacidade][data].


## <a name="q17"></a>Posso ativou tudo na aplicação informações?

<table border="1">
<tr><th>O que deverá ver</th><th>Como o obter</th><th>Por que motivo pretende</th></tr>
<tr><td>Gráficos de disponibilidade</td><td><a href="../app-insights-monitor-web-app-availability/">Testes Web</a></td><td>Sabe que a aplicação web é para cima</td></tr>
<tr><td>Desempenho de aplicação do servidor: tempos de resposta,...
</td><td><a href="../app-insights-asp-net/">Adicionar informações da aplicação ao seu projeto</a><br/>ou <br/><a href="../app-insights-monitor-performance-live-website-now/">Instalar AI Monitor de estado no servidor</a> (ou escrever o seu próprio código para <a href="../app-insights-api-custom-events-metrics/#track-dependency">controlar as dependências</a>)</td><td>Detetar problemas de desempenho</td></tr>
<tr><td>Telemetria de dependência</td><td><a href="../app-insights-monitor-performance-live-website-now/">Instalar AI Monitor de estado no servidor</a></td><td>Diagnosticar problemas com bases de dados ou outros componentes externos</td></tr>
<tr><td>Obter rastreios de exceções</td><td><a href="../app-insights-search-diagnostic-logs/#exceptions">Inserir TrackException chamadas no seu código</a> (mas alguns são reportados automaticamente)</td><td>Detetar e diagnosticar exceções</td></tr>
<tr><td>Rastreios de registo de pesquisa</td><td><a href="../app-insights-search-diagnostic-logs/">Adicionar uma placa de registo</a></td><td>Diagnosticar exceções, problemas de desempenho</td></tr>
<tr><td>Noções básicas sobre a utilização de cliente: vistas de página, sessões,...</td><td><a href="../app-insights-javascript/">JavaScript inicializador em páginas web</a></td><td>Análise de utilização</td></tr>
<tr><td>Métricas personalizadas do cliente</td><td><a href="../app-insights-api-custom-events-metrics/">Controlo de chamadas em páginas web</a></td><td>Melhorar a experiência de utilizador</td></tr>
<tr><td>Métrica personalizada do servidor</td><td><a href="../app-insights-api-custom-events-metrics/">Chamadas de controlo no código do servidor</a></td><td>Informações da empresa</td></tr>
</table>


## <a name="automation"></a>Automatização

Pode [escrever scripts de PowerShell](app-insights-powershell.md) para criar e actualizar recursos de informações da aplicação.

## <a name="more-answers"></a>Mais respostas

* [Fórum de informações da aplicação](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md

 