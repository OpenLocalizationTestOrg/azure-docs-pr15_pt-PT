<properties
    pageTitle="Corrigir 502 gateway inválido, 503 Serviço indisponíveis erros | Microsoft Azure"
    description="Resolver problemas de 502 gateway inválido e 503 Serviço indisponível erros a aplicação web do alojado no serviço de aplicação do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
    tags="top-support-issue"
    keywords="502 gateway inválido, 503 Serviço indisponível, o erro 503, Erro 502"/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cephalin"/>

# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-your-azure-web-apps"></a>Resolver problemas de erros HTTP de "502 gateway inválido" e "503 Serviço indisponível" no seu Azure web apps

"502 gateway inválido" e "503 Serviço indisponível" são os erros comuns na aplicação web do alojado no [Serviço de aplicação do Azure](http://go.microsoft.com/fwlink/?LinkId=529714). Este artigo ajuda-o a resolver estes erros.

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [Azure o MSDN e os fóruns de pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Em alternativa, também pode preencher um incidente de suporte Azure. Aceda ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**.

## <a name="symptom"></a>Sintoma

Quando navega para o web app, devolve um HTTP Erro de "502 Gateway inválido" ou um HTTP "503 Serviço indisponível" erro.

## <a name="cause"></a>Causa

Este problema é frequentemente causado por problemas ao nível da aplicação, tais como:

-   pedidos a demorar muito tempo
-   aplicação utilizando memória alta/CPU
-   aplicação falhar devido a uma exceção.

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>Passos de resolução de problemas para resolver erros de "503 Serviço indisponível" e "502 gateway inválido"

Resolução de problemas pode ser dividida em três tarefas distintas, por ordem sequencial:

1.  [Observar e monitorizar o comportamento da aplicação](#observe)
2.  [Recolher dados](#collect)
3.  [Mitigar o problema](#mitigate)

[Aplicação de serviço Web Apps](/services/app-service/web/) fornece-lhe várias opções em cada passo.

<a name="observe" />
### <a name="1-observe-and-monitor-application-behavior"></a>1. observar e monitorizar o comportamento da aplicação

####    <a name="track-service-health"></a>Controlar o estado de funcionamento do serviço

Microsoft Azure publicizes sempre que existe uma degradação do serviço interrupção ou um desempenho. Pode controlar o estado de funcionamento do serviço no [Portal do Azure](https://portal.azure.com/). Para mais informações, consulte o artigo [Estado de funcionamento do serviço de registar](../monitoring-and-diagnostics/insights-service-health.md).

####    <a name="monitor-your-web-app"></a>Monitorize a aplicação web

Esta opção permite-lhe saber se a aplicação está a ter problemas. No pá da sua aplicação web, clique no mosaico de **pedidos e erros** . O pá **métrica** irá apresentar todas as métricas que pode adicionar.

Seguem-se alguns as métricas que poderá pretender monitorizar para a sua aplicação web

-   Memória média conjunto de trabalho
-   Tempo de resposta médio
-   Tempo de CPU
-   Conjunto de memória funcional
-   Pedidos de

![Monitorize a aplicação web no sentido de resolução de erros de HTTP de 502 gateway inválido e 503 Serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

Para obter mais informações, consulte:

-   [Monitor Web Apps no Azure de aplicação de serviço](web-sites-monitor.md)
-   [Receber notificações de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />
### <a name="2-collect-data"></a>2. recolher dados

####    <a name="use-the-azure-app-service-support-portal"></a>Utilizar o Portal de suporte do Azure de aplicação de serviço

Web Apps fornece a capacidade de resolução de problemas relacionados com a aplicação web do verificando HTTP registos, registos de eventos, informações de estado do processo e mais. Pode aceder a todas estas informações utilizando o nosso portal de suporte em **http://&lt;o nome da aplicação >.scm.azurewebsites.net/Support**

Portal do serviço de aplicação do Azure suporta fornece-lhe com três separadores separadas para suportar os três passos de um cenário de resolução de problemas comuns:

1.  Observar o comportamento da atual
2.  Analisar a recolher informações de diagnóstico e executando os analisadores incorporados
3.  Mitigar

Se o problema se passa neste momento, clique em **Analisar** > **Diagnósticos** > **Diagnosticar agora** para criar uma sessão de diagnóstico para si, que irá recolher HTTP registos, Visualizador registos de eventos, informações de estado, os registos de erros PHP e PHP relatório do processo de memória.

Assim que os dados são recolhidos, também será efetuar uma análise de dados e fornecer-lhe um relatório HTML.

No caso que pretende transferir os dados, por predefinição, teria de ser armazenado na pasta D:\home\data\DaaS.

Para mais informações sobre o portal de suporte do Azure aplicação de serviço, consulte o artigo [Novas atualizações a extensão de Site de suporte para sites públicos do Azure](/blog/new-updates-to-support-site-extension-for-azure-websites).

####    <a name="use-the-kudu-debug-console"></a>Utilizar a consola de depuração Kudu

Web Apps vem com uma consola de depuração que pode utilizar para depuração, explorar e carregar ficheiros, bem como JSON pontos finais para obter informações sobre o seu ambiente. Esta opção é denominada da _Consola de Kudu_ ou o _Dashboard SMS_ para a sua aplicação web.

Pode aceder a este dashboard ao aceder à ligação **https://&lt;o nome da aplicação >.scm.azurewebsites.net/**.

Algumas das funcionalidades que fornece Kudu são:

-   definições para a sua aplicação de ambiente
-   fluxo de registo
-   informação de diagnóstico
-   consola na qual pode executar os cmdlets do Powershell e comandos que deve fazer básicos de depuração.


Outra função útil de Kudu é que, caso a aplicação é deitar hipótese de primeiro exceções, pode utilizar Kudu e copia a ferramenta de SysInternals Procdump para criar memória. Estas informações de estado da memória estão instantâneos do processo e com frequência podem ajudá-lo mais complicados resolver problemas relacionados com a aplicação web.

Para mais informações sobre as funcionalidades disponíveis no Kudu, consulte o artigo [sites públicos do Azure ferramentas online que devem saber sobre](/blog/windows-azure-websites-online-tools-you-should-know-about/).

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

 ![Reinicie a aplicação para resolver erros de HTTP de 502 gateway inválido e 503 Serviço indisponível](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

Também pode gerir a sua aplicação web através do Azure Powershell. Para mais informações, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).
