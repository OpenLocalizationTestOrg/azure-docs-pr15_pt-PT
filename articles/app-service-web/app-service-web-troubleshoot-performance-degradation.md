<properties
    pageTitle="Diminuir o desempenho de aplicação web na aplicação de serviço | Microsoft Azure"
    description="Este artigo ajuda-o a resolução de problemas de desempenho de aplicação web lenta na aplicação de serviço de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="desempenho da aplicação Web, aplicação lenta, aplicação lenta"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-slow-web-app-performance-issues-in-azure-app-service"></a>Resolução de problemas de desempenho de aplicação web lenta na aplicação de serviço do Azure

Este artigo ajuda-o a resolução de problemas de desempenho de aplicação web lenta na [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714).

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [Azure o MSDN e os fóruns de pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Em alternativa, também pode preencher um incidente de suporte Azure. Aceda ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**.

## <a name="symptom"></a>Sintoma

Quando navega lentamente a carga de páginas do web app e, por vezes, tempo limite.

## <a name="cause"></a>Causa

Este problema é frequentemente causado por problemas ao nível da aplicação, tais como:

-   pedidos a demorar muito tempo
-   aplicação utilizando memória alta/CPU
-   aplicação falhar devido a uma exceção.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Resolução de problemas pode ser dividida em três tarefas distintas, por ordem sequencial:

1.  [Observar e monitorizar o comportamento da aplicação](#observe)
2.  [Recolher dados](#collect)
3.  [Mitigar o problema](#mitigate)

[Aplicação de serviço Web Apps](/services/app-service/web/) fornece-lhe várias opções em cada passo.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observar e monitorizar o comportamento da aplicação

#### <a name="track-service-health"></a>Controlar o estado de funcionamento do serviço

Microsoft Azure publicizes sempre que existe uma degradação do serviço interrupção ou um desempenho. Pode controlar o estado de funcionamento do serviço no [Portal do Azure](https://portal.azure.com/). Para mais informações, consulte o artigo [Estado de funcionamento do serviço de registar](../monitoring-and-diagnostics/insights-service-health.md).

#### <a name="monitor-your-web-app"></a>Monitorize a aplicação web

Esta opção permite-lhe saber se a aplicação está a ter problemas. No pá da sua aplicação web, clique no mosaico de **pedidos e erros** . O pá **métrica** irá apresentar todas as métricas que pode adicionar.

Seguem-se alguns as métricas que poderá pretender monitorizar para a sua aplicação web

-   Memória média conjunto de trabalho
-   Tempo de resposta médio
-   Tempo de CPU
-   Conjunto de memória funcional
-   Pedidos de

![monitorizar o desempenho da aplicação web](./media/app-service-web-troubleshoot-performance-degradation/1-monitor-metrics.png)

Para obter mais informações, consulte:

-   [Monitor Web Apps no Azure de aplicação de serviço](web-sites-monitor.md)
-   [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

#### <a name="monitor-web-endpoint-status"></a>Monitorizar o estado de ponto final da web

Se estiver a executar a aplicação web na **padrão** preços de camadas, Web Apps permite-lhe monitorizar os pontos 2 finais de 3 localizações geográficas.

Monitorização de ponto final configura testes web a partir de localizações distribuído geo que testar o tempo de resposta e tempo de utilização de URLs do web. O teste executa uma operação HTTP GET o URL da web para determinar o tempo de resposta e o tempo de utilização de cada localização. Cada localização configurada executa um teste cada cinco minutos.

Tempo de utilização é monitorizado com códigos de resposta HTTP e o tempo de resposta for medido em milissegundos. Um teste monitorização falha se o código de resposta HTTP é maior ou igual a 400 ou se a resposta demora mais de 30 segundos. Um ponto final é considerado disponível se a suas testes monitorização bem sucedida a partir de todas as localizações especificadas.

Para configurar, consulte o artigo [como: monitorizar o estado de ponto final de web](web-sites-monitor.md#webendpointstatus).

Além disso, consulte o artigo [manter Azure Web Sites para cima plus ponto final de monitorização - com Frederico Schackow](/documentation/videos/azure-web-sites-endpoint-monitoring-and-staying-up/) para um vídeo sobre como monitorizar o ponto final.

#### <a name="application-performance-monitoring-using-extensions"></a>Monitorização de desempenho de aplicação utilizando extensões

Também pode monitorizar o desempenho da aplicação, tirando partido _extensões de site_.

Cada aplicação do serviço de aplicação web fornece um ponto final de gestão extensible que permite-lhe tirar partido de um conjunto avançado de ferramentas implementado como extensões de site. Estas ferramentas intervalo de origem editores de código como o [Serviços de equipa do Visual Studio](https://www.visualstudio.com/products/what-is-visual-studio-online-vs.aspx) para ferramentas de gestão de recursos ligadas, tais como uma base de dados do MySQL ligado a uma aplicação web.

[Informações de aplicação do Azure](/services/application-insights/) e [Relic novo](/marketplace/partners/newrelic/newrelic/) são duas das extensões de site disponíveis de monitorização do desempenho. Para utilizar o novo Relic, instale um agente de tempo de execução. Para utilizar informações de aplicação do Azure, recriar o seu código com um SDK e também é possível instalar uma extensão que fornece acesso a dados adicionais. O SDK permite-lhe escrever código para monitorizar a utilização e o desempenho da sua aplicação mais detalhadamente.

Para utilizar a aplicação de informações, consulte o artigo [monitorizar o desempenho nas aplicações web](../application-insights/app-insights-web-monitor-performance.md).

Para utilizar Relic novo, consulte o artigo [Novo Relic de gestão de desempenho de aplicações no Azure](../store-new-relic-cloud-services-dotnet-application-performance-management.md).

<a name="collect" />
### <a name="2-collect-data"></a>2. recolher dados

####    <a name="enable-diagnostics-logging-for-your-web-app"></a>Ativar o registo de diagnóstico para a sua aplicação web

O ambiente do Web Apps fornece uma funcionalidade de diagnóstico para informações de registo do servidor web e a aplicação web. Estas são logicamente separadas para diagnósticos do servidor web e diagnósticos de aplicação.

##### <a name="web-server-diagnostics"></a>Diagnósticos do servidor Web

Pode ativar ou desativar os seguintes tipos de registos:

-   **Registo de erro detalhada** - informações de erro detalhada para códigos de estado HTTP que indiquem uma falha (código de estado 400 ou maior). Isto pode conter informações que podem ajudar a determinar porque é que o servidor devolvido o código de erro.
-   **Ocorreu uma falha de pedido de rastreio** - informações detalhadas sobre pedidos de falhados, incluindo um rastreio dos componentes do IIS utilizada para processar o pedido e o tempo despendido em cada componente. Isto pode ser útil se estiver a tentar melhorar o desempenho da aplicação web ou a isolar o que está a causar um erro HTTP específico.
-   **Registo de servidor web** - informações sobre as transações de HTTP utilizando o formato de ficheiro de registo expandido W3C. Isto é útil quando determinar métricas de aplicação web gerais, tais como o número de pedidos de processados ou pedidos de quantas são de um endereço IP específico.

##### <a name="application-diagnostics"></a>Diagnósticos de aplicação

Diagnósticos de aplicação permite-lhe capturar informações produzidas por uma aplicação web. Podem utilizar aplicações do ASP.NET a `System.Diagnostics.Trace` escolares registar informações para o registo de diagnóstico de aplicação.

Para obter instruções detalhadas sobre como configurar a sua aplicação de registo, consulte o artigo [Ativar diagnósticos do registo para web apps no Azure aplicação de serviço](web-sites-enable-diagnostic-log.md).

#### <a name="use-remote-profiling"></a>Utilizar a criação de perfis remoto

No serviço de aplicação do Azure, aplicações Web, aplicações de API e WebJobs pode ser remotamente com perfil. Se o processo de está a ser executado um desempenho mais lento que o esperado, ou a latência de pedidos de HTTP são mais elevados que normal e a utilização da CPU do processo é também alta, pode remotamente o processo de perfil e obter as pilhas de chamadas amostragem CPU para analisar a atividade de processo e caminhos de quente código.

Para obter mais informações, consulte o artigo [suporte perfis remoto na aplicação de serviço de Azure](/blog/remote-profiling-support-in-azure-app-service).


#### <a name="use-the-azure-app-service-support-portal"></a>Utilizar o Portal de suporte do Azure de aplicação de serviço

Web Apps fornece a capacidade de resolução de problemas relacionados com a aplicação web do verificando HTTP registos, registos de eventos, informações de estado do processo e mais. Pode aceder a todas estas informações utilizando o nosso portal de suporte em **http://&lt;o nome da aplicação >.scm.azurewebsites.net/Support**

Portal do serviço de aplicação do Azure suporta fornece-lhe com três separadores separadas para suportar os três passos de um cenário de resolução de problemas comuns:

1.  Observar o comportamento da atual
2.  Analisar a recolher informações de diagnóstico e executando os analisadores incorporados
3.  Mitigar

Se o problema se passa neste momento, clique em **Analisar** > **Diagnósticos** > **Diagnosticar agora** para criar uma sessão de diagnóstico para si, que irá recolher HTTP registos, Visualizador registos de eventos, informações de estado, os registos de erros PHP e PHP relatório do processo de memória.

Assim que os dados são recolhidos, também será efetuar uma análise de dados e fornecer-lhe um relatório HTML.

No caso que pretende transferir os dados, por predefinição, teria de ser armazenado na pasta D:\home\data\DaaS.

Para mais informações sobre o portal de suporte do Azure aplicação de serviço, consulte o artigo [Novas atualizações a extensão de Site de suporte para sites públicos do Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

#### <a name="use-the-kudu-debug-console"></a>Utilizar a consola de depuração Kudu

Web Apps vem com uma consola de depuração que pode utilizar para depuração, explorar e carregar ficheiros, bem como JSON pontos finais para obter informações sobre o seu ambiente. Esta opção é denominada da _Consola de Kudu_ ou o _Dashboard SMS_ para a sua aplicação web.

Pode aceder a este dashboard ao aceder à ligação **https://&lt;o nome da aplicação >.scm.azurewebsites.net/**.

Algumas das funcionalidades que fornece Kudu são:

-   definições para a sua aplicação de ambiente
-   fluxo de registo
-   informação de diagnóstico
-   consola na qual pode executar os cmdlets do Powershell e comandos que deve fazer básicos de depuração.


Outra função útil de Kudu é que, caso a aplicação é deitar hipótese de primeiro exceções, pode utilizar Kudu e copia a ferramenta de SysInternals Procdump para criar memória. Estas informações de estado da memória estão instantâneos do processo e com frequência podem ajudá-lo mais complicados resolver problemas relacionados com a aplicação web.

Para mais informações sobre as funcionalidades disponíveis no Kudu, consulte o artigo [que devem saber sobre as ferramentas de serviços de equipa de Web sites de Azure](/blog/windows-azure-websites-online-tools-you-should-know-about/).

<a name="mitigate" />
### <a name="3-mitigate-the-issue"></a>3. mitigar o problema

####    <a name="scale-the-web-app"></a>Dimensionar a aplicação web

No serviço de aplicação do Azure, para aumento do desempenho e débito, pode ajustar a escala no qual está a executar a aplicação. Escalar para cima uma aplicação web envolve acções relacionadas dois: alterar o seu plano de serviço de aplicação para uma camada preços mais elevada e configurar algumas definições depois de ter mudado na camada comparar superior.

Para mais informações sobre dimensionamento, consulte o artigo [escala de uma aplicação web na aplicação de serviço de Azure](web-sites-scale.md).

Para além disso, pode escolher executar a aplicação na instância mais do que uma. Isto não só fornece-lhe com capacidade de processamento de mais, mas também lhe dá certo período de tolerância a falhas. Se o processo for interrompido numa instância, a outra instância irão continuar servir pedidos.

Pode definir o dimensionamento para ser Manual ou automático.

####    <a name="use-autoheal"></a>Utilizar AutoHeal

AutoHeal Reciclagens do processo de trabalho para a sua aplicação com base nas definições, que selecione (como alterações à configuração, pedidos, limites de memória ou o tempo necessário para executar um pedido de). A maior parte das vezes, reciclagem o processo é a forma mais rápida para recuperar a partir de um problema. Apesar de conseguir sempre reiniciar a aplicação web da diretamente no Portal do Azure, AutoHeal irá fazê-lo automaticamente automaticamente. Tudo o que precisa de fazer é adicionar alguns accionadores na Web. config raiz para a sua aplicação web. Note que estas definições seriam funcionam da mesma forma, mesmo se a sua aplicação não é um .net um.

Para mais informações, consulte [a reparação automática Azure Web Sites](/blog/auto-healing-windows-azure-web-sites/).

####    <a name="restart-the-web-app"></a>Reinicie a aplicação web

Este é frequentemente a forma mais simples para recuperar da problemas únicos. No [Portal do Azure](https://portal.azure.com/), no pá do web app, tem as opções para parar ou reiniciar a sua aplicação.

 ![Reinicie o web app para resolver problemas de desempenho](./media/app-service-web-troubleshoot-performance-degradation/2-restart.png)

Também pode gerir a sua aplicação web através do Azure Powershell. Para mais informações, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).
