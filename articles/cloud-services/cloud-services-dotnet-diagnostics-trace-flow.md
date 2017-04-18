<properties
    pageTitle="Analisar o fluxo de uma aplicação de serviços na nuvem com diagnósticos do Azure | Microsoft Azure"
    description="Adicione mensagens de rastreio a uma aplicação do Azure para o ajudar a depuração, medir o desempenho, monitorização, análise de tráfego e muito mais."
    services="cloud-services"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="02/20/2016"
    ms.author="robb"/>



# <a name="trace-the-flow-of-a-cloud-services-application-with-azure-diagnostics"></a>Analisar o fluxo de uma aplicação de serviços em nuvem com diagnósticos do Azure

Rastreio é uma forma para monitorizar a execução da sua aplicação enquanto estiver em execução. Pode utilizar as classes [System.Diagnostics.Trace](https://msdn.microsoft.com/library/system.diagnostics.trace.aspx), [System.Diagnostics.Debug](https://msdn.microsoft.com/library/system.diagnostics.debug.aspx)e [System.Diagnostics.TraceSource](https://msdn.microsoft.com/library/system.diagnostics.tracesource.aspx) para registar informações sobre erros e execução de aplicações no registos, ficheiros de texto ou outros dispositivos para análise posterior. Para mais informações sobre o rastreio, consulte o artigo [rastreio e instrumentalizar aplicações](https://msdn.microsoft.com/library/zs6s4h68.aspx).


## <a name="use-trace-statements-and-trace-switches"></a>Utilizar declarações de rastreio e parâmetros de rastreio

Implementar o rastreio na sua aplicação de serviços em nuvem, adicionando o [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) para a configuração da aplicação e efetuar chamadas para System.Diagnostics.Trace ou System.Diagnostics.Debug no código da aplicação. Utilize o ficheiro de configuração *App* para a *Web. config* para funções de web e funções de trabalho. Quando cria um novo serviço alojado através de um modelo do Visual Studio, diagnósticos do Azure são automaticamente adicionados ao projeto e o DiagnosticMonitorTraceListener é adicionada ao ficheiro de configuração adequadas para as funções que adicionar.

Para obter informações sobre colocar declarações de rastreio, consulte o artigo [como: declarações de rastreio de adicionar a aplicação código](https://msdn.microsoft.com/library/zd83saa2.aspx).

Ao colocar o [Rastreio parâmetros](https://msdn.microsoft.com/library/3at424ac.aspx) no seu código, pode controlar se ocorre rastreio e é como extenso. Permite-lhe monitorizar o estado da sua aplicação num ambiente de produção. Isto é particularmente importante numa aplicação empresarial que utiliza vários componentes em execução em vários computadores. Para obter mais informações, consulte o artigo [como: configurar o rastreio comutadores](https://msdn.microsoft.com/library/t06xyy08.aspx).

## <a name="configure-the-trace-listener-in-an-azure-application"></a>Configurar o serviço de escuta rastreio numa aplicação do Azure

Rastreio, depurar e TraceSource, requerem que configura o "listeners" para recolher e registar as mensagens que são enviadas. Listeners recolhem, armazenam e encaminhar rastreio de mensagens. Estes direccionam a saída de rastreio a um destino adequada, como um registo, a janela ou o ficheiro de texto. Azure diagnóstico utiliza a classe de [DiagnosticMonitorTraceListener](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitortracelistener.aspx) .

Antes de concluir o seguinte procedimento, tem de iniciar o Azure monitor diagnóstico. Para fazer isto, consulte o artigo [Ativar diagnóstico no Microsoft Azure](cloud-services-dotnet-diagnostics.md).

Tenha em atenção que se utilizar os modelos que são fornecidos pelo Visual Studio, a configuração da escuta é adicionada automaticamente para si.


### <a name="add-a-trace-listener"></a>Adicionar uma escuta rastreio

1. Abra o ficheiro Web. config ou App para o seu perfil.
2. Adicione o seguinte código para o ficheiro. Altere o atributo de versão para utilizar o número da versão do conjunto que está a referenciar. A versão de assemblagem não seja necessariamente altera com cada versão Azure SDK, a menos que não existem atualizações para o mesmo.

    ```
    <system.diagnostics>
        <trace>
            <listeners>
                <add type="Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener,
                  Microsoft.WindowsAzure.Diagnostics,
                  Version=2.8.0.0,
                  Culture=neutral,
                  PublicKeyToken=31bf3856ad364e35"
                  name="AzureDiagnostics">
                  <filter type="" />
                </add>
            </listeners>
        </trace>
    </system.diagnostics>
    ```
    >[AZURE.IMPORTANT] Certifique-se de que tem uma referência de projecto para a assemblagem Microsoft.WindowsAzure.Diagnostics. Atualize o número da versão no xml acima para corresponder à versão do assemblagem Microsoft.WindowsAzure.Diagnostics referenciada.

3. Guarde o ficheiro de configuração.

Para mais informações sobre listeners, consulte o artigo [Listeners rastreio](https://msdn.microsoft.com/library/4y5y10s7.aspx).

Depois de concluir os passos para adicionar a escuta, pode adicionar declarações de rastreio para o seu código.


### <a name="to-add-trace-statement-to-your-code"></a>Para adicionar a declaração de rastreio ao seu código

1. Abra um ficheiro de origem para a sua aplicação. Por exemplo, o <RoleName>. cs ficheiro para a função de trabalho ou web.
2. Adicione o seguinte utilizando instrução se já não foi adicionado:
    ```
        using System.Diagnostics;
    ```
3. Adicione declarações de rastreio em que pretende capturar informações sobre o estado da sua aplicação. Pode utilizar uma variedade de métodos para formatar o resultado da instrução rastreio. Para obter mais informações, consulte o artigo [como: declarações de rastreio de adicionar a aplicação código](https://msdn.microsoft.com/library/zd83saa2.aspx).
4. Guarde o ficheiro de origem.
