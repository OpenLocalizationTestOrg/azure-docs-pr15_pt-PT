<properties 
    pageTitle="Filtragem e pré-processamento no SDK informações aplicação | Microsoft Azure" 
    description="Escreva processadores de telemetria e inicializadores de telemetria para o SDK para filtrar ou adicionar propriedades para os dados antes de telemetria é enviada para o portal de informações da aplicação." 
    services="application-insights"
    documentationCenter="" 
    authors="beckylino" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="08/30/2016" 
    ms.author="borooji"/>

# <a name="filtering-and-preprocessing-telemetry-in-the-application-insights-sdk"></a>Filtragem e pré-processamento telemetria no SDK informações de aplicação

*Informações de aplicação está na pré-visualização.*

Pode escrever e configurar o plug-ins para o SDK de informações de aplicação personalizar como telemetria é capturada e processada antes de ter sido enviada para o serviço de informações da aplicação. 

Atualmente estas funcionalidades estão disponíveis para o SDK do ASP.NET.

* [Amostragem](app-insights-sampling.md) reduz o volume de telemetria sem afetar as estatísticas. Em conjunto mantém relacionados com pontos de dados para que pode navegar entre os dois quando diagnosticar um problema. No portal do, as contagens total são multiplicadas para o Adquirente na íntegra para a amostragem.
* [Filtragem com processadores de telemetria](#filtering) permite-lhe selecionar ou modificar telemetria no SDK antes de ter sido enviada para o servidor. Por exemplo, pode reduzir o volume de telemetria, excluindo os pedidos de robôs. Mas filtragem é uma abordagem mais básica para reduzindo o tráfego que amostragem. Permite-lhe mais controlo sobre o que é transmitido, mas tem de agrupa-afeta as estatísticas de - por exemplo, se filtrarem todos os pedidos com êxito.
* [Inicializadores de telemetria adicionar propriedades](#add-properties) a qualquer telemetria enviada a partir da sua aplicação, incluindo telemetria de módulos padrão. Por exemplo, pode adicionar valores calculados; versão números ou pelo qual filtrar os dados no portal.
* [A API SDK](app-insights-api-custom-events-metrics.md) é utilizada para enviar eventos personalizados e métricas.


Antes de começar:

* Instale a [aplicação informações SDK para ASP.NET v2](app-insights-asp-net.md) na sua aplicação. 


<a name="filtering"></a>
## <a name="filtering-itelemetryprocessor"></a>Filtragem: ITelemetryProcessor

Esta técnica dá-lhe direto mais controlo sobre o que é incluído ou excluído da sequência de telemetria. Pode utilizá-lo em conjunto com amostragem, ou em separado.

Para filtrar telemetria, escrever um processador de telemetria e registá-lo com o SDK. Todos os telemetria atravessa o processador e pode optar por largue-a partir do fluxo ou adicionar propriedades. Isto inclui telemetria de módulos padrão, tais como recolector de pedido de HTTP e o Recolectores dependência e telemetria que criaram si próprio. Pode, por exemplo, filtrar saída telemetria acerca dos pedidos de robôs ou chamadas de dependência efetuada com êxito. 

> [AZURE.WARNING] Filtragem de telemetria enviada a partir do SDK utilizem processadores pode prejudicar as estatísticas de que vê no portal do e dificultar a seguir a itens relacionados.
> 
> Em vez disso, considere utilizar [amostragem](app-insights-sampling.md).

### <a name="create-a-telemetry-processor"></a>Criar um processador de telemetria

1. Certifique-se de que o SDK de informações de aplicação no seu projeto está versão 2.0.0 ou posterior. Projeto do Visual Studio solução Explorer com o botão direito e selecione gerir pacotes de NuGet. No Gestor de pacote NuGet, verifique Microsoft.ApplicationInsights.Web.

1. Para criar um filtro, implemente ITelemetryProcessor. Este é outro ponto de expansão como módulo de telemetria, inicializador de telemetria e canal de telemetria. 

    Repare que processadores de telemetria construir uma cadeia de processamento. Quando criar uma instância de um processador de telemetria, ser efetuada com uma ligação para o processador seguinte na cadeia. Quando um ponto de dados de telemetria é transferido para o método de processo, é que o seu trabalho e, em seguida, liga para o processador de telemetria seguinte na cadeia.

    ``` C#

    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.Extensibility;

    public class SuccessfulDependencyFilter : ITelemetryProcessor
      {
        private ITelemetryProcessor Next { get; set; }

        // You can pass values from .config
        public string MyParamFromConfigFile { get; set; }

        // Link processors to each other in a chain.
        public SuccessfulDependencyFilter(ITelemetryProcessor next)
        {
            this.Next = next;
        }
        public void Process(ITelemetry item)
        {
            // To filter out an item, just return 
            if (!OKtoSend(item)) { return; }
            // Modify the item if required 
            ModifyItem(item);

            this.Next.Process(item);
        }

        // Example: replace with your own criteria.
        private bool OKtoSend (ITelemetry item)
        {
            var dependency = item as DependencyTelemetry;
            if (dependency == null) return true;

            return dependency.Success != true;
        }

        // Example: replace with your own modifiers.
        private void ModifyItem (ITelemetry item)
        {
            item.Context.Properties.Add("app-version", "1." + MyParamFromConfigFile);
        }
    }
    

    ```
2. Insira isto ApplicationInsights.config: 

```XML

    <TelemetryProcessors>
      <Add Type="WebApplication9.SuccessfulDependencyFilter, WebApplication9">
         <!-- Set public property -->
         <MyParamFromConfigFile>2-beta</MyParamFromConfigFile>
      </Add>
    </TelemetryProcessors>

```

(Isto é a mesma secção onde inicialização que um filtro de amostragem.)

Pode passar valores de cadeia a partir do ficheiro. config, fornecendo públicas propriedades com nome na sua aula. 

> [AZURE.WARNING] Tomar cuidado para corresponder o nome do tipo e quaisquer nomes de propriedade no ficheiro. config para os nomes de classe e propriedades no código. Se o ficheiro. config faz referência a um tipo de inexistente ou uma propriedade, o SDK silenciosamente poderá falhar enviar qualquer telemetria.

 
**Em alternativa,** pode iniciar o filtro no código. Uma aula inicialização adequado - por exemplo AppStart no Global.asax.cs - inserir o processador da cadeia:

```C#

    var builder = TelemetryConfiguration.Active.TelemetryProcessorChainBuilder;
    builder.Use((next) => new SuccessfulDependencyFilter(next));

    // If you have more processors:
    builder.Use((next) => new AnotherProcessor(next));

    builder.Build();

```

TelemetryClients criadas após deste ponto irá utilizar o seu processadores.

### <a name="example-filters"></a>Filtros de exemplo

#### <a name="synthetic-requests"></a>Pedidos de síntese

Filtre testes bots e da web. Apesar de métricas Explorer permite-lhe a opção para filtrar as origens de síntese, esta opção reduz o tráfego através da filtragem-los no SDK.

``` C#

    public void Process(ITelemetry item)
    {
      if (!string.IsNullOrEmpty(item.Context.Operation.SyntheticSource)) {return;}

      // Send everything else: 
      this.Next.Process(item);
    }

```

#### <a name="failed-authentication"></a>Autenticação falhadas

Filtre os pedidos de com uma resposta "401". 

```C#

public void Process(ITelemetry item)
{
    var request = item as RequestTelemetry;

    if (request != null &&
    request.ResponseCode.Equals("401", StringComparison.OrdinalIgnoreCase))
    {
        // To filter out an item, just terminate the chain: 
        return;
    }
    // Send everything else: 
    this.Next.Process(item);
}

```

#### <a name="filter-out-fast-remote-dependency-calls"></a>Filtrar as chamadas de dependência rapidamente remoto

Se apenas pretender diagnosticar chamadas são lentas, filtre aqueles rapidamente. 

> [AZURE.NOTE] Isto irá prejudicar as estatísticas de que vê no portal. O gráfico de dependência será o aspeto como se as chamadas de dependência são todas as falhas.

``` C#

public void Process(ITelemetry item)
{
    var request = item as DependencyTelemetry;
            
    if (request != null && request.Duration.Milliseconds < 100)
    {
        return;
    }
    this.Next.Process(item);
}

```

#### <a name="diagnose-dependency-issues"></a>Diagnosticar problemas de dependência

[Neste blogue](https://azure.microsoft.com/blog/implement-an-application-insights-telemetry-processor/) descreve um projeto para diagnosticar problemas de dependência ao enviar automaticamente pings normais para dependências.


<a name="add-properties"></a>
## <a name="add-properties-itelemetryinitializer"></a>Adicionar propriedades: ITelemetryInitializer

Utilize inicializadores de telemetria para definir propriedades globais que são enviadas com todos os telemetria; e para substituir selecionado comportamento dos módulos de telemetria padrão. 

Por exemplo, as informações de aplicação para o pacote Web recolhe telemetria acerca dos pedidos de HTTP. Por predefinição, sinaliza como falhou qualquer pedido com um código de resposta > = 400. Mas se quiser tratar 400 como um sucesso, pode fornecer um inicializador de telemetria que define a propriedade de sucesso.

Se fornecer um inicializador de telemetria,-chama-se sempre que qualquer um dos métodos Track*() chama-se. Isto inclui métodos designados por módulos de telemetria padrão. Por convenção, estes módulos não fazer para definir uma propriedade de que já foi definida por um inicializador. 

**Definir o seu inicializador**

*C#*

```C#

    using System;
    using Microsoft.ApplicationInsights.Channel;
    using Microsoft.ApplicationInsights.DataContracts;
    using Microsoft.ApplicationInsights.Extensibility;

    namespace MvcWebRole.Telemetry
    {
      /*
       * Custom TelemetryInitializer that overrides the default SDK 
       * behavior of treating response codes >= 400 as failed requests
       * 
       */
      public class MyTelemetryInitializer : ITelemetryInitializer
      {
        public void Initialize(ITelemetry telemetry)
        {
            var requestTelemetry = telemetry as RequestTelemetry;
            // Is this a TrackRequest() ?
            if (requestTelemetry == null) return;
            int code;
            bool parsed = Int32.TryParse(requestTelemetry.ResponseCode, out code);
            if (!parsed) return;
            if (code >= 400 && code < 500)
            {
                // If we set the Success property, the SDK won't change it:
                requestTelemetry.Success = true;
                // Allow us to filter these requests in the portal:
                requestTelemetry.Context.Properties["Overridden400s"] = "true";
            }
            // else leave the SDK to set the Success property      
        }
      }
    }
```

**Carregar a sua inicializador**

No ApplicationInsights.config:

    <ApplicationInsights>
      <TelemetryInitializers>
        <!-- Fully qualified type name, assembly name: -->
        <Add Type="MvcWebRole.Telemetry.MyTelemetryInitializer, MvcWebRole"/> 
        ...
      </TelemetryInitializers>
    </ApplicationInsights>

*Em alternativa,* pode atribuí inicializador no código de, por exemplo Global.aspx.cs:


```C#
    protected void Application_Start()
    {
        // ...
        TelemetryConfiguration.Active.TelemetryInitializers
        .Add(new MyTelemetryInitializer());
    }
```


[Veja mais deste exemplo.](https://github.com/Microsoft/ApplicationInsights-Home/tree/master/Samples/AzureEmailService/MvcWebRole)

<a name="js-initializer"></a>
### <a name="javascript-telemetry-initializers"></a>Inicializadores de telemetria JavaScript

*JavaScript*

Inserir um inicializador de telemetria imediatamente após o código de inicialização que recebeu a partir do portal: 

```JS

    <script type="text/javascript">
        // ... initialization code
        ...({
            instrumentationKey: "your instrumentation key"
        });
        window.appInsights = appInsights;


        // Adding telemetry initializer.
        // This is called whenever a new telemetry item
        // is created.

        appInsights.queue.push(function () {
            appInsights.context.addTelemetryInitializer(function (envelope) {
                var telemetryItem = envelope.data.baseData;

                // To check the telemetry item’s type - for example PageView:
                if (envelope.name == Microsoft.ApplicationInsights.Telemetry.PageView.envelopeType) {
                    // this statement removes url from all page view documents
                    telemetryItem.url = "URL CENSORED";
                }

                // To set custom properties:
                telemetryItem.properties = telemetryItem.properties || {};
                telemetryItem.properties["globalProperty"] = "boo";

                // To set custom metrics:
                telemetryItem.measurements = telemetryItem.measurements || {};
                telemetryItem.measurements["globalMetric"] = 100;
            });
        });

        // End of inserted code.

        appInsights.trackPageView();
    </script>
```

Para um resumo das propriedades que não sejam personalizada disponíveis na telemetryItem, consulte o artigo o [modelo de dados](app-insights-export-data-model.md#lttelemetrytypegt).

Pode adicionar quantas inicializadores conforme pretender. 


## <a name="itelemetryprocessor-and-itelemetryinitializer"></a>ITelemetryProcessor e ITelemetryInitializer

O que é a diferença entre processadores de telemetria e inicializadores de telemetria?

* Existem algumas sobreposições em o que pode fazer com os mesmos: ambas podem ser utilizadas para adicionar propriedades a telemetria.
* TelemetryInitializers sempre executadas antes de TelemetryProcessors.
* TelemetryProcessors permitem-lhe completamente substituir ou eliminar um item de telemetria.
* TelemetryProcessors não processar telemetria contador de desempenho.



## <a name="persistence-channel"></a>Canal persistente 

Se a aplicação é executada sempre que a ligação à internet não está sempre disponível ou lenta, considere utilizar o canal persistente em vez do canal na memória predefinido. 

O canal na memória predefinido perde qualquer telemetria que ainda não foi enviada por hora fecha a aplicação. Embora possa utilizar `Flush()` para tentar enviar quaisquer dados restante na memória intermédia,-lo ainda perde dados se existe sem ligação à internet ou, se a aplicação encerra antes de transmissão está concluída.

Por outro lado, o canal persistente serve de memória intermédia telemetria num ficheiro antes de o enviar para o portal. `Flush()`assegura que os dados são armazenados no ficheiro. Se quaisquer dados não são enviados por hora fecha a aplicação, permanece no ficheiro. Quando a aplicação for reiniciado, os dados serão, em seguida, enviados se existir uma ligação à internet. Dados é acumulado no ficheiro enquanto é necessário até uma ligação está disponível. 

### <a name="to-use-the-persistence-channel"></a>Para utilizar o canal persistente

1. Importe o pacote de NuGet [Microsoft.ApplicationInsights.PersistenceChannel](https://www.nuget.org/packages/Microsoft.ApplicationInsights.PersistenceChannel/1.2.3).
2. Inclua este código na sua aplicação, numa localização inicialização adequado:
 
    ```C# 

      using Microsoft.ApplicationInsights.Channel;
      using Microsoft.ApplicationInsights.Extensibility;
      ...

      // Set up 
      TelemetryConfiguration.Active.InstrumentationKey = "YOUR INSTRUMENTATION KEY";
 
      TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();
    
    ``` 
3. Utilizar `telemetryClient.Flush()` antes da sua fecha de aplicação, para se certificar de dados são enviados para o portal ou guardados no ficheiro.

    Note que Flush () é síncrono para o canal persistente, mas assíncrona para outros canais.

 
O canal persistente está optimizado para dispositivos cenários, onde o número de eventos produzidos pela aplicação é relativamente pequeno e a ligação é frequentemente fiável. Este canal irá escrever eventos no disco para armazenamento fiável pela primeira vez e, em seguida, tentar enviá-la. 

#### <a name="example"></a>Exemplo

Digamos que pretende monitorizar não processadas exceções. Subscrever a `UnhandledException` evento. Numa chamada de retorno, incluir uma chamada para esvaziar para se certificar de que o telemetria é mantida.
 
```C# 

AppDomain.CurrentDomain.UnhandledException += CurrentDomain_UnhandledException; 
 
... 
 
private void CurrentDomain_UnhandledException(object sender, UnhandledExceptionEventArgs e) 
{ 
    ExceptionTelemetry excTelemetry = new ExceptionTelemetry((Exception)e.ExceptionObject); 
    excTelemetry.SeverityLevel = SeverityLevel.Critical; 
    excTelemetry.HandledAt = ExceptionHandledAt.Unhandled; 
 
    telemetryClient.TrackException(excTelemetry); 
 
    telemetryClient.Flush(); 
} 

``` 

Quando a aplicação encerra, verá um ficheiro no `%LocalAppData%\Microsoft\ApplicationInsights\`, que contém os eventos comprimidos. 
 
Próxima vez que iniciar esta aplicação, o canal irá Pegue este ficheiro e entregar telemetria para as informações de aplicação, se for possível.

#### <a name="test-example"></a>Exemplo de teste

```C#

using Microsoft.ApplicationInsights;
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.Extensibility;

namespace ConsoleApplication1
{
    class Program
    {
        static void Main(string[] args)
        {
            // Send data from the last time the app ran
            System.Threading.Thread.Sleep(5 * 1000);

            // Set up persistence channel

            TelemetryConfiguration.Active.InstrumentationKey = "YOUR KEY";
            TelemetryConfiguration.Active.TelemetryChannel = new PersistenceChannel();

            // Send some data

            var telemetry = new TelemetryClient();

            for (var i = 0; i < 100; i++)
            {
                var e1 = new Microsoft.ApplicationInsights.DataContracts.EventTelemetry("persistenceTest");
                e1.Properties["i"] = "" + i;
                telemetry.TrackEvent(e1);
            }

            // Make sure it's persisted before we close
            telemetry.Flush();
        }
    }
}

```


O código do canal persistente é no [github](https://github.com/Microsoft/ApplicationInsights-dotnet/tree/v1.2.3/src/TelemetryChannels/PersistenceChannel). 


## <a name="reference-docs"></a>Documentos de referência

* [Descrição geral de API](app-insights-api-custom-events-metrics.md)

* [Referência do ASP.NET](https://msdn.microsoft.com/library/dn817570.aspx)


## <a name="sdk-code"></a>Código SDK

* [ASP.NET Core SDK](https://github.com/Microsoft/ApplicationInsights-dotnet)
* [ASP.NET 5](https://github.com/Microsoft/ApplicationInsights-aspnet5)
* [JavaScript SDK](https://github.com/Microsoft/ApplicationInsights-JS)


## <a name="next"></a>Próximos passos


* [Eventos de pesquisa e registos][diagnostic]
* [Amostragem](app-insights-sampling.md)
* [Resolução de problemas][qna]


<!--Link references-->

[client]: app-insights-javascript.md
[config]: app-insights-configuration-with-applicationinsights-config.md
[create]: app-insights-create-new-resource.md
[data]: app-insights-data-retention-privacy.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[greenbrown]: app-insights-asp-net.md
[java]: app-insights-java-get-started.md
[metrics]: app-insights-metrics-explorer.md
[qna]: app-insights-troubleshoot-faq.md
[trace]: app-insights-search-diagnostic-logs.md
[windows]: app-insights-windows-get-started.md

 
