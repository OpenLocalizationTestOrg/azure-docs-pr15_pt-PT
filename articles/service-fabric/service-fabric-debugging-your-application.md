<properties
   pageTitle="Depurar sua aplicação no Visual Studio | Microsoft Azure"
   description="Melhore a fiabilidade e desempenho dos seus serviços através do desenvolvimento e depuração-los no Visual Studio num cluster de desenvolvimento local."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/21/2016"
   ms.author="vturecek;mikhegn"/>

# <a name="debug-your-service-fabric-application-by-using-visual-studio"></a>Depurar a aplicação de serviço ferro utilizando o Visual Studio

## <a name="debug-a-local-service-fabric-application"></a>Depurar uma aplicação de serviço ferro local

Pode poupar tempo e dinheiro ao implementar e depurar sua aplicação Azure Service ferro num cluster de desenvolvimento do computador local. Visual Studio pode implementar a aplicação para o cluster local e ligar-se automaticamente o depurador a todas as instâncias da aplicação.

1. Inicie um cluster de desenvolvimento local, seguindo os passos [para configurar o seu ambiente de desenvolvimento ferro de serviço](service-fabric-get-started.md).

2. Prima **F5** ou clique em **Depurar** > **Iniciar depuração**.

    ![Iniciar a depuração de uma aplicação][startdebugging]

3. Definir pontos de interrupção no seu código e passo através da aplicação ao clicar em comandos no menu **Depurar** .

    > [AZURE.NOTE] Anexa Visual Studio para todas as instâncias da aplicação. Enquanto vai ausentar-se através de código, poderão obter clicados pontos de interrupção por vários processos que resulta em sessões em simultâneo. Experimente desativar interrupção depois de que está a clicar, ao efetuar cada ponto de interrupção condicional sobre o ID de tópico ou ao utilizar eventos de diagnóstico.

4. A janela de **Eventos de diagnóstico** serão abertos automaticamente para que possa ver eventos de diagnóstico em tempo real.

    ![Ver eventos diagnóstico em tempo real][diagnosticevents]

5. Também pode abrir a janela de **Eventos de diagnóstico** no Explorador de nuvem.  Em **Ferro de serviço**, qualquer nó com o botão direito e selecione **Rastreios de transmissão de vista**.

    ![Abrir a janela de eventos de diagnóstico][viewdiagnosticevents]

    Se pretender filtrar os seus rastreios para uma aplicação ou serviço específico, basta ative rastreios transmissão nesse serviço específico ou a aplicação.

6. Os eventos de diagnóstico podem ser vistos no ficheiro **ServiceEventSource.cs** gerado automaticamente e chamam a partir do código da aplicação.

    ```csharp
    ServiceEventSource.Current.ServiceMessage(this, "My ServiceMessage with a parameter {0}", result.Value.ToString());
    ```

7. A janela de **Eventos de diagnóstico** suporta a filtragem, interromper e da inspeção de eventos em tempo real.  O filtro é uma pesquisa de cadeia simples da mensagem evento, incluindo os seus conteúdos.

    ![Filtrar, coloque o cursor e retomar ou inspecionar eventos em tempo real][diagnosticeventsactions]

8. Serviços de depuração é semelhante a depuração de qualquer outra aplicação. Normalmente definirá pontos de interrupção através do Visual Studio para depuração fácil. Apesar de replicadas fiável coleções de sites em vários nós, ainda implementar IEnumerable. Isto significa que pode utilizar a vista de resultados no Visual Studio durante a depuração para ver o que armazenou no interior. Basta defina um ponto de interrupção em qualquer lugar no seu código.

    ![Iniciar a depuração de uma aplicação][breakpoint]

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->

## <a name="debug-a-remote-service-fabric-application"></a>Depurar uma aplicação de serviço ferro remota

Se as aplicações de serviço ferro estiverem em execução num cluster de serviço ferro no Azure, que conseguem remotamente depurar estas, diretamente a partir do Visual Studio.

> [AZURE.NOTE] A funcionalidade requer o [serviço ferro SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).    

<!-- -->
> [AZURE.WARNING] Depuração remota se destinar para cenários de Dev Center/teste e não para ser utilizado em ambientes de produção, devido ao impacto nas aplicações em execução.

1. Navegue até ao seu cluster no **Explorador de nuvem**, botão direito do rato e selecione **Ativar depuração**

    ![Activar depuração remota][enableremotedebugging]

    Isto irá pontapé o processo de ativação a extensão de depuração remoto no seu nós de cluster, bem como as configurações de rede necessários.

2. O nó do cluster no **Explorador de nuvem**com o botão direito e selecione **Anexar Depurador**

    ![Anexar Depurador][attachdebugger]

3. Na caixa de diálogo **anexar a processar** , selecione o processo que pretende depurar e clique em **anexar**

    ![Escolher processo][chooseprocess]

    O nome do processo que pretende anexar, for igual ao nome do seu nome de assemblagem de projeto de serviço.

    O depurador irá anexar a todos os nós de executar o processo.
    - No caso de onde são depuração um serviço sem estado, todas as instâncias do serviço em todos os nós de fazem parte da sessão de depuração.
    - Se depuração de um serviço de estado, a réplica principal de qualquer partição estará ativa e, consequentemente, capturadas pelo depurador. Se a réplica principal move o cursor durante a sessão de depuração, da transformação de que a réplica continuarão parte da sessão de depuração.
    - Para poder chamar apenas a partições relevantes ou instâncias de um determinado serviço, pode utilizar pontos de interrupção condicional apenas interromper uma partição específica ou instância.

    ![Ponto de interrupção condicional][conditionalbreakpoint]

    > [AZURE.NOTE] Atualmente não suportamos depuração de um cluster de serviço ferro com várias instâncias o mesmo nome executável do serviço.

4. Assim que acabar de depuração de aplicação, pode desativar a extensão de depuração remoto clicando com o cluster no **Explorador de nuvem** e selecione **Desativar depuração**

    ![Desativar a depuração remota][disableremotedebugging]

## <a name="streaming-traces-from-a-remote-cluster-node"></a>Transmissão rastreios a partir de um nó de cluster remoto

Também é possível para rastreios da cadeia diretamente a partir de um nó cluster remoto para Visual Studio. Esta funcionalidade permite-lhe eventos rastreio ETW sequência, produzidos num nó de cluster ferro de serviço, diretamente no Visual Studio.

> [AZURE.NOTE] A funcionalidade requer o [serviço ferro SDK 2.0](http://www.microsoft.com/web/handlers/webpi.ashx?command=getinstallerredirect&appid=MicrosoftAzure-ServiceFabric-VS2015) e [Azure SDK para .NET 2.9](https://azure.microsoft.com/downloads/).

<!-- -->
> [AZURE.WARNING]Transmissão rastreios se destinar para cenários de Dev Center/teste e não para ser utilizado em ambientes de produção, devido ao impacto nas aplicações em execução.
> Num cenário de produção, deve confiar no eventos utilizando os diagnósticos do Azure de reencaminhamento de chamadas.

1. Navegue até ao seu cluster no **Explorador de nuvem**, com o botão direito e selecione **Ativar a transmissão rastreios**

    ![Ativar rastreios transmissão remotos][enablestreamingtraces]

    Isto irá pontapé o processo de ativação a extensão de rastreios transmissão no seu nós de cluster, bem como as configurações de rede necessários.

2. Expanda o elemento de **nós** no **Explorador de nuvem**, com o botão direito no nó que pretende para transmitir em fluxo rastreios a partir do e selecione **Rastreios de transmissão de vista**

    ![Vista remoto transmissão rastreios][viewremotestreamingtraces]

    Repita o passo 2 para tantas nós à medida que pretende ver os seus rastreios a partir de. Cada sequência de nós irá aparecer numa janela do dedicada.

    Agora está ver rastreios emitidos pelo ferro de serviço e os seus serviços. Se pretender filtrar os eventos para mostrar apenas uma aplicação específica, basta escreva o nome a aplicação no filtro.

    ![Visualização transmissão rastreios][viewingstreamingtraces]

4. Depois de ter rastreios transmissão a partir do seu cluster, pode desativar rastreios transmissão remotos, clicando com o cluster no **Explorador de nuvem** e selecione **Desativar a transmissão rastreios**

    ![Desativar rastreios transmissão remotos][disablestreamingtraces]

## <a name="next-steps"></a>Próximos passos

- [Um serviço de ferro de serviço de teste](service-fabric-testability-overview.md).
- [Gerir as suas aplicações de serviço ferro no Visual Studio](service-fabric-manage-application-in-visual-studio.md).

<!--Image references-->
[startdebugging]: ./media/service-fabric-debugging-your-application/startdebugging.png
[diagnosticevents]: ./media/service-fabric-debugging-your-application/diagnosticevents.png
[viewdiagnosticevents]: ./media/service-fabric-debugging-your-application/viewdiagnosticevents.png
[diagnosticeventsactions]: ./media/service-fabric-debugging-your-application/diagnosticeventsactions.png
[breakpoint]: ./media/service-fabric-debugging-your-application/breakpoint.png
[enableremotedebugging]: ./media/service-fabric-debugging-your-application/enableremotedebugging.png
[attachdebugger]: ./media/service-fabric-debugging-your-application/attachdebugger.png
[chooseprocess]: ./media/service-fabric-debugging-your-application/chooseprocess.png
[conditionalbreakpoint]: ./media/service-fabric-debugging-your-application/conditionalbreakpoint.png
[disableremotedebugging]: ./media/service-fabric-debugging-your-application/disableremotedebugging.png
[enablestreamingtraces]: ./media/service-fabric-debugging-your-application/enablestreamingtraces.png
[viewingstreamingtraces]: ./media/service-fabric-debugging-your-application/viewingstreamingtraces.png
[viewremotestreamingtraces]: ./media/service-fabric-debugging-your-application/viewremotestreamingtraces.png
[disablestreamingtraces]: ./media/service-fabric-debugging-your-application/disablestreamingtraces.png
