<properties 
    pageTitle="Resolução de problemas sem dados - informações de aplicação para .NET" 
    description="Não ver as informações de aplicação do Visual Studio dados? Experimente aqui." 
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
    ms.date="10/24/2016" 
    ms.author="awills"/>
 
# <a name="troubleshooting-no-data---application-insights-for-net"></a>Resolução de problemas sem dados - informações de aplicação para .NET

## <a name="some-of-my-telemetry-is-missing"></a>Alguns dos meus telemetria está em falta

*Em informações de aplicação, só vejo a uma fração dos eventos gerados pelo minha aplicação.*

* Se estiver a ver forma consistente a mesma fração, é provavelmente devido ao ajustável [amostragem](app-insights-sampling.md). Para o confirmar, abra a pesquisa (a partir do pá descrição geral) e veja uma instância de um pedido de ou outro evento. Na parte inferior da secção Propriedades clique em "…" para obter detalhes de propriedade completo. Se pedido contar > 1, em seguida, amostragem é numa operação. 
* Caso contrário, é possível que que está a atingir um [limite de taxa de dados](app-insights-pricing.md#limits-summary) para o seu plano comparar. Estes limites são aplicados por minuto.

## <a name="no-data-from-my-server"></a>Sem dados a partir do meu servidor

*Instalei minha aplicação no meu servidor web e agora não vejo qualquer telemetria a partir dos mesmos. Funcionou OK no meu computador Dev Center.*

* Provavelmente um problema de firewall. [Definir exceções de firewall para informações de aplicação enviar dados](app-insights-ip-addresses.md).

*Posso [instalado Monitor de estado](app-insights-monitor-performance-live-website-now.md) no meu servidor web para monitorizar aplicações existentes. Não vejo todos os resultados.*

* Consulte o artigo [resolução de Monitor de estado](app-insights-monitor-performance-live-website-now.md#troubleshooting). 


## <a name="q01"></a>Opção sem 'Adicionar informações de aplicação' no Visual Studio

*Quando posso criar um novo projeto no Visual Studio, ou quando posso um projeto existente no Explorador de soluções de contexto, não vejo as opções de informações da aplicação.*

+ Nem todos os tipos de projeto .NET são suportados pelas ferramentas. Projetos de Web e WCF são suportados. Para outros tipos de projeto como as aplicações de ambiente de trabalho ou serviços, pode ainda [adicionar manualmente uma SDK de informações da aplicação ao seu projeto](app-insights-windows-desktop.md).
+ Certifique-se de que tem o [Visual Studio 2013 atualização 3 ou posterior](http://go.microsoft.com/fwlink/?LinkId=397827). É fornecido com ferramentas de informações da aplicação pré-instaladas.
+ Selecione **Ferramentas**, **extensões e atualizações** e verifique se as **Ferramentas de informações da aplicação** está instalada e ativada. Se Sim, clique em **atualizações** para ver se existir uma atualização disponível.
+ Abra a caixa de diálogo novo projeto e selecione a aplicação Web do ASP.NET. Se vir a opção de informações da aplicação não existem, as ferramentas estão instaladas. Caso contrário, experimente desinstalar e, em seguida, voltar a instalar as ferramentas de informações da aplicação.


## <a name="q02"></a>Falha ao adicionar informações de aplicação

*Quando posso criar um novo projeto de web, ou ao tentar adicionar informações de aplicação a um projeto existente, vejo uma mensagem de erro.*

Causas prováveis:

* Falha na comunicação com o portal de informações da aplicação; ou
* Existe algum problema com a sua conta Azure;
* Só tem [acesso de leitura para a subscrição ou o grupo onde estava a tentar criar o novo recurso](app-insights-resources-roles-access-control.md).

CORREÇÃO:

+ Verifique que fornecido credenciais de início de sessão para a conta Azure para a direita. 
+ No seu browser, verifique que tem de aceder ao [Azure portal](https://portal.azure.com). Abra Definições e ver se existe qualquer restrição.
+ [Adicionar informações de aplicação ao seu projeto existente](app-insights-asp-net.md): no Explorador de solução, clique com o botão direito do rato em seu projeto e selecione "Adicionar informações de aplicação".
+ Se ainda não funcionar, siga o [procedimento manual](app-insights-windows-services.md) para adicionar um recurso no portal do e, em seguida, adicionar o SDK ao seu projeto. 

## <a name="emptykey"></a>Recebo um erro "chave instrumentação não pode estar vazio"

Parece algo correu mal enquanto foram a instalar a aplicação informações ou talvez uma placa de registo.

No Explorador de solução, com o botão direito `ApplicationInsights.config` e selecione **Configurar informações de aplicação**. Irá obter uma caixa de diálogo que lhe pede para iniciar sessão no Azure e crie a um recurso de informações da aplicação, ou reutilize existente.


##<a name="NuGetBuild"></a>"Os pacotes NuGet estão em falta" no meu servidor de compilação

*Tudo o que constrói OK quando posso estou a depuração na minha máquina de desenvolvimento, mas recebo um erro de NuGet no servidor de compilação.*

Consulte o artigo [NuGet pacote restaurar](http://docs.nuget.org/Consume/Package-Restore) e [Restaurar automática do pacote](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore).

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Comando de menu em falta abrir informações de aplicação a partir do Visual Studio

*Quando posso com o botão direito meu project Explorer de solução, não consigo ver todos os comandos de informações da aplicação ou não consigo ver um comando de informações de aplicação aberta.*

Causas prováveis:

* Se tiver criado o recurso de informações da aplicação manualmente ou se o projeto for de um tipo de que não é suportado pelas ferramentas de informações da aplicação.
* As ferramentas de informações da aplicação estão desactivadas no seu Visual Studio.
* O Visual Studio for anterior ao 2013 actualização 3.

CORREÇÃO:

* Certifique-se a que sua versão do Visual Studio for atualização 2013 3 ou posterior.
* Selecione **Ferramentas**, **extensões e atualizações** e verifique se as **Ferramentas de informações da aplicação** está instalada e ativada. Se Sim, clique em **atualizações** para ver se existir uma atualização disponível.
* Botão direito do rato o seu projeto no Explorador de soluções. Se vir o comando **Configurar informações de aplicação**, utilizá-lo para ligar o seu projeto para o recurso no serviço de informações da aplicação.


Caso contrário, o tipo de projecto diretamente não é suportado pelas ferramentas de informações da aplicação. Para ver o seu telemetria, inicie sessão no [portal do Azure](https://portal.azure.com), selecione informações da aplicação na barra de navegação do lado esquerdo e selecione a aplicação.

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>'Acesso negado' de abertura de informações de aplicação a partir do Visual Studio

*O comando de menu 'Informações de aplicação aberta' leva-me ao portal do Azure, mas recebo um erro acesso negado.*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

O início de sessão-in do Microsoft que utilizou última no seu browser predefinido não tem acesso ao [recurso que foi criado quando as informações de aplicação foi adicionada para esta aplicação](app-insights-asp-net.md). Existem duas razões prováveis: 

* Tem mais do que uma conta Microsoft - talvez uma trabalho e uma conta Microsoft pessoal? O início de sessão no que utilizou última no seu browser predefinido foi para uma conta diferente daquele que tenha acesso ao [Adicionar informações da aplicação ao projeto](app-insights-asp-net.md). 

 * CORREÇÃO: Clique em seu nome na parte superior direita da janela do browser e terminar sessão. Em seguida, inicie sessão com a conta que tenha acesso. Em seguida, na barra de navegação esquerdo, clique em informações de aplicação e selecione a sua aplicação.

* Informações de aplicação de outra pessoa adicionada ao projeto, e se tenha esquecido de dão-lhe [acesso ao grupo de recursos](app-insights-resources-roles-access-control.md) , na qual foi criada. 

 * CORREÇÃO: Se utilizaram uma conta institucional, podem adicionar lhe à equipa do; ou podem conceder-lhe acesso individuais ao grupo de recursos.



## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>'Elementos não encontrado' de abertura de informações de aplicação a partir do Visual Studio

*O comando de menu 'Informações de aplicação aberta' leva-me ao portal do Azure, mas recebo um erro "elementos não encontrado".*

Causas prováveis:

* O recurso de informações de aplicação para a aplicação foi eliminado; ou
* A chave de instrumentação foi definida ou alterada no ApplicationInsights.config ao editá-lo diretamente, sem actualizar o ficheiro de projeto. 

A chave de instrumentação nos controlos de ApplicationInsights.config onde a telemetria é enviada. Uma linha no ficheiro de projeto controla qual o recurso que é aberto ao utilizar o comando no Visual Studio. 

CORREÇÃO:

* No Explorador de solução, com o botão direito do projeto e selecione informações de aplicação, Configure informações de aplicação. Na caixa de diálogo, ou pode optar por enviar telemetria para um recurso existente ou crie um novo. Ou:
* Abra o recurso diretamente. Inicie sessão no [portal do Azure](https://portal.azure.com), clique em informações de aplicação na barra de navegação do lado esquerdo e, em seguida, selecione a sua aplicação.



## <a name="where-do-i-find-my-telemetry"></a>Onde posso encontrar a minha telemetria?

*Posso iniciar sessão [portal do Microsoft Azure](https://portal.azure.com)e estou à procura no dashboard de casa Azure. Por isso, onde posso encontrar os meus dados de informações da aplicação?*

* Na barra de navegação esquerdo, clique em informações de aplicação, em seguida, o nome da aplicação. Se não tiver quaisquer projectos lá, tem de [Adicionar ou configurar a aplicação de informações no projeto web](app-insights-asp-net.md).

    Não existem verá alguns gráficos de resumo. Pode clicar nas mesmas para ver mais detalhes.

* No Visual Studio, enquanto estiver a depuração a sua aplicação, clique no botão de informações da aplicação.


## <a name="q03"></a>Sem dados de servidor (ou sem dados sequer)

*Posso executou a minha aplicação e, em seguida, aberto o serviço de informações de aplicação no Microsoft Azure, mas todos os gráficos mostram 'Saiba como recolher...' ou 'Não está configurado.'* Ou, *apenas vista de página e o utilizador dados, mas sem dados de servidor.*

+ Execute a aplicação no modo de depuração no Visual Studio (F5). Utilize a aplicação publicados para gerar algumas telemetria. Verifique se o pode ver os eventos que tem sessão iniciados na janela de saída do Visual Studio. 

    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)

+ No portal de informações da aplicação, abra a [Pesquisa de diagnóstico](app-insights-diagnostic-search.md). Dados normalmente é apresentado aqui pela primeira vez.
+ Clique no botão atualizar. O pá atualiza propriamente dito periodicamente, mas pode fazê-lo também manualmente. O intervalo de atualização é mais longo para intervalos de tempo maiores.
+ Verifique as teclas de instrumentação correspondem. Na pá principal para a sua aplicação no portal do informações de aplicação, no menu pendente **Essentials** , veja **chave instrumentação**. Em seguida, no seu projeto no Visual Studio, abra o ApplicationInsights.config e localize o `<instrumentationkey>`. Verifique se as duas chaves estão iguais. Se não for:
 + No portal do, clique em informações de aplicação e procure o recurso de aplicação com a tecla da direita; ou
 + No Explorador de solução do Visual Studio, com o botão direito do projeto e selecione informações de aplicação, Configure. Repor a aplicação para enviar telemetria para o recurso à direita.
 + Se não conseguir encontrar as teclas correspondentes, verifique que está a utilizar as mesmas credenciais de início de sessão no Visual Studio como em portal.

    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
    
+ No [dashboard de casa do Microsoft Azure](https://portal.azure.com), observe o mapa de estado de funcionamento do serviço. Se existirem algumas indicações alertas, aguarde até que tenham sido devolvidos ao OK e, em seguida, feche e abra novamente o seu pá da aplicação de informações da aplicação.
+ Consulte também o [nosso blogue de estado](http://blogs.msdn.com/b/applicationinsights-status/).
+ Tenha escrito qualquer código para o [SDK do lado do servidor](app-insights-api-custom-events-metrics.md) que podem ser alterados na chave instrumentação `TelemetryClient` instâncias ou no `TelemetryContext`? Ou fez escrever uma [configuração de filtro ou amostragem](app-insights-api-filtering-sampling.md) que pode estar a filtrar saída demasiado?
+ Se editou ApplicationInsights.config, cuidadosamente Verifique a configuração de [TelemetryInitializers e TelemetryProcessors](app-insights-api-filtering-sampling.md). Um tipo de forma incorreta-com o nome ou parâmetro pode causar o SDK enviar sem dados.

## <a name="q04"></a>Sem dados nas vistas de página, Browsers, utilização

*Vejo dados em gráficos de tempo de resposta do servidor e pedidos de servidor, mas sem dados em tempo de carregamento de vista de página ou em pás de Browser ou utilização de.*

Os dados provêm de scripts em páginas web. 

+ Se tiver adicionado as informações de aplicação a um projeto existente web, [que tem de adicionar os scripts manualmente](app-insights-javascript.md).
+ Certifique-se de que Internet Explorer não estiver a apresentar o seu site no modo de compatibilidade.
+ Utilizar a funcionalidade de depuração do browser (F12 em alguns browsers, em seguida, selecione rede) para confirmar que são a ser enviados dados para `dc.services.visualstudio.com`.

## <a name="no-dependency-or-exception-data"></a>Sem dados dependência ou exceção

Consulte o artigo [telemetria dependência](app-insights-asp-net-dependencies.md) e [telemetria exceção](app-insights-asp-net-exceptions.md).

## <a name="no-performance-data"></a>Sem dados de desempenho

Dados de desempenho (CPU, taxa IO e assim sucessivamente) está disponível para [Java serviços web](app-insights-java-collectd.md), [aplicações de ambiente de trabalho do Windows](app-insights-windows-desktop.md), [IIS web aplicações e serviços se instalar o monitor de estado](app-insights-monitor-performance-live-website-now.md)e [Serviços em nuvem Azure](app-insights-azure.md). encontrará-lo em definições, servidores.

Não está disponível para sites públicos do Azure.

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>Sem dados (servidor), desde que publicado a aplicação para o meu servidor

+ Verifique que realmente copiado todos os a Microsoft. ApplicationInsights DLLs para o servidor, juntamente com Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll
+ Na sua firewall, poderá ter de [abrir algumas portas TCP](app-insights-ip-addresses.md#data-access-api).
+ Se tiver de utilizar um proxy para enviar fora rede da sua empresa, definir [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) na Web. config
+ Windows Server 2008: Certifique-se de que instalou as atualizações do seguintes: [KB2468871](https://support.microsoft.com/kb/2468871), [KB2533523](https://support.microsoft.com/kb/2533523), [KB2600217](https://support.microsoft.com/kb/2600217).


## <a name="i-used-to-see-data-but-it-has-stopped"></a>Posso utilizada para ver dados, mas deixou de

* Verificar o [Estado de blogue](http://blogs.msdn.com/b/applicationinsights-status/).
* Acertar sua quota mensal de pontos de dados? Abra o definições/Quota e preços para descobrir. Se Sim, pode atualizar o plano ou pagar a capacidade adicional. Consulte o artigo [preços esquema](https://azure.microsoft.com/pricing/details/application-insights/).


## <a name="i-dont-see-all-the-data-im-expecting"></a>Não consigo ver todos os dados que estou a esperar

Se a sua aplicação envia muitos dados e estiver a utilizar o SDK de informações de aplicação para o ASP.NET versão 2.0.0-beta3 ou posterior, a funcionalidade de [amostragem ajustável](app-insights-sampling.md) pode funcionar e enviar apenas uma percentagem do seu telemetria. 

Pode desativá-lo, mas não é recomendado. Amostragem foi concebida para que telemetria relacionada corretamente é transmitida, para fins de diagnóstico. 

## <a name="wrong-geographical-data-in-user-telemetry"></a>Dados geográficos mal no telemetria de utilizador

A cidade, região e dimensões de país/região derivem das endereços IP e não são sempre correctos.

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Exceção "método não encontrado" sobre como executar no Azure serviços em nuvem

Construir para .NET 4.6? 4.6 automaticamente não é suportada nas funções de serviços em nuvem Azure. [Instalar 4.6 cada função](../cloud-services/cloud-services-dotnet-install-dotnet.md) antes de executar a sua aplicação.

## <a name="still-not-working"></a>Continuar a não funcionar...

* [Fórum de informações da aplicação](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

