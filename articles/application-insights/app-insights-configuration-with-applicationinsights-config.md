<properties 
    pageTitle="Configurar a aplicação informações SDK com ApplicationInsights.config ou. XML" 
    description="Activar ou desactivar módulos de recolha de dados e adicionar contadores de desempenho e outros parâmetros" 
    services="application-insights"
    documentationCenter="" 
    authors="OlegAnaniev-MSFT"
    editor="alancameronwills" 
    manager="douge"/>
 
<tags 
    ms.service="application-insights" 
    ms.workload="tbd" 
    ms.tgt_pltfrm="ibiza" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="03/12/2016" 
    ms.author="awills"/>

# <a name="configuring-the-application-insights-sdk-with-applicationinsightsconfig-or-xml"></a>Configurar as informações de aplicação SDK com ApplicationInsights.config ou. XML

A aplicação informações .NET SDK é composta por um número de pacotes NuGet. O [pacote de core](http://www.nuget.org/packages/Microsoft.ApplicationInsights) fornece a API para o envio de telemetria para as informações de aplicação. [Pacotes adicionais](http://www.nuget.org/packages?q=Microsoft.ApplicationInsights) fornecer de telemetria _módulos_ e _inicializadores_ para monitorizar telemetria automaticamente a partir da aplicação e o respectivo contexto. Ajustando o ficheiro de configuração, pode ativar ou desativar módulos de telemetria e inicializadores e definir parâmetros para alguns dos mesmos.

O ficheiro de configuração é denominado `ApplicationInsights.config` ou `ApplicationInsights.xml`, consoante o tipo de aplicação. É adicionado automaticamente ao seu projeto quando [instalar a maioria das versões do SDK][start]. Também é adicionada para uma aplicação web pelo [Monitor de estado num servidor IIS][redfield], ou quando seleciona a informações de Appplication [extensão para um Web site Azure ou VM](app-insights-azure-web-apps.md).

Não existe um ficheiro equivalente para controlar o [SDK numa página web][client].

Este documento descreve as secções, que consulte na configuração do ficheiro, como controlam os componentes do SDK, e que pacotes NuGet carregar esses componentes.

## <a name="telemetry-modules-aspnet"></a>Módulos de telemetria (ASP.NET)

Cada módulo de telemetria recolhe um tipo específico de dados e utiliza o essencial API para enviar os dados. Os módulos são instalados pelo diferentes de NuGet de pacotes, também adicionar as linhas necessárias para o ficheiro. config.

Existe um nó no ficheiro de configuração para cada módulo. Para desativar um módulo, eliminar o nó ou comentário-la.



### <a name="dependency-tracking"></a>Dependência de controlo

[Dependência de rastreio](app-insights-dependencies.md) recolhe telemetria relativas a chamadas a sua aplicação faz com que para bases de dados e serviços externos e bases de dados. Para permitir que este módulo trabalhar no servidor do IIS, tem de [instalar o Monitor de estado][redfield]. Para utilizá-lo no Azure web apps ou VMs, [Selecione a extensão de informações da aplicação](app-insights-azure-web-apps.md).

Também pode escrever o seu próprio código utilizar a [TrackDependency API](app-insights-api-custom-events-metrics.md#track-dependency)de rastreio de dependência.


* `Microsoft.ApplicationInsights.DependencyCollector.DependencyTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.DependencyCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.DependencyCollector) NuGet pacote.

### <a name="performance-collector"></a>Recolector de desempenho

[Recolhe contadores de desempenho do sistema](app-insights-performance-counters.md) , como o carregamento de memória e CPU, a rede das instalações do IIS. Pode especificar quais os contadores para recolher, incluindo contadores de desempenho que configurou si mesmo.

* `Microsoft.ApplicationInsights.Extensibility.PerfCounterCollector.PerformanceCollectorModule`
* [Microsoft.ApplicationInsights.PerfCounterCollector](http://www.nuget.org/packages/Microsoft.ApplicationInsights.PerfCounterCollector) NuGet pacote.


### <a name="application-insights-diagnostics-telemetry"></a>Aplicação informações diagnósticos telemetria

O `DiagnosticsTelemetryModule` relatar erros na aplicação informações instrumentação próprio código. Por exemplo, se o código não é possível aceder contadores de desempenho ou se uma `ITelemetryInitializer` inicia uma exceção. Telemetria rastreio controlada por este módulo aparece na [Pesquisa diagnóstico][diagnostic]. Envia os dados de diagnóstico para dc.services.vsallin.net.
 
* `Microsoft.ApplicationInsights.Extensibility.Implementation.Tracing.DiagnosticsTelemetryModule`
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet pacote. Se instalar apenas este pacote, o ficheiro ApplicationInsights.config não é criado automaticamente. 

### <a name="developer-mode"></a>Modo de programador

`DeveloperModeWithDebuggerAttachedTelemetryModule`força as informações de aplicação `TelemetryChannel` para enviar dados de imediato, item de uma telemetria cada vez, quando um depurador é anexado ao processo de aplicação. Este procedimento reduz a quantidade de tempo entre o momento em que, quando a aplicação controla telemetria e quando este aparecer no portal de informações da aplicação. -Faz com que uma sobrecarga significativa largura de banda de rede e CPU.

* `Microsoft.ApplicationInsights.WindowsServer.DeveloperModeWithDebuggerAttachedTelemetryModule`
* [Aplicação informações Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote NuGet

### <a name="web-request-tracking"></a>Controlo de pedido de Web

O [código de tempo e o resultado de resposta](app-insights-asp-net.md) de pedidos de HTTP de relatórios. 

* `Microsoft.ApplicationInsights.Web.RequestTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet pacote

### <a name="exception-tracking"></a>Controlo de exceção

`ExceptionTrackingTelemetryModule`faixas exceções não processada na sua aplicação web. Consulte o artigo [falhas e exceções][exceptions].

* `Microsoft.ApplicationInsights.Web.ExceptionTrackingTelemetryModule`
* [Microsoft.ApplicationInsights.Web](http://www.nuget.org/packages/Microsoft.ApplicationInsights.Web) NuGet pacote


* `Microsoft.ApplicationInsights.WindowsServer.UnobservedExceptionTelemetryModule`-controla [exceções tarefa não observado](http://blogs.msdn.com/b/pfxteam/archive/2011/09/28/task-exception-handling-in-net-4-5.aspx).
* `Microsoft.ApplicationInsights.WindowsServer.UnhandledExceptionTelemetryModule`-controla não processadas exceções para funções de trabalho, serviços do windows e aplicações de consola.
* [Aplicação informações Windows Server](http://www.nuget.org/packages/Microsoft.ApplicationInsights.WindowsServer/) Pacote de NuGet.

### <a name="microsoftapplicationinsights"></a>Microsoft.ApplicationInsights

O pacote de Microsoft.ApplicationInsights fornece o [core API](https://msdn.microsoft.com/library/mt420197.aspx) do SDK. Utilizam esta opção os outros módulos de telemetria e também pode [utilizá-lo para definir o seu próprio telemetria](app-insights-api-custom-events-metrics.md).

* Nenhuma entrada no ApplicationInsights.config.
* [Microsoft.ApplicationInsights](http://www.nuget.org/packages/Microsoft.ApplicationInsights) NuGet pacote. Se instalar apenas este NuGet, é gerado sem ficheiro. config.

## <a name="telemetry-channel"></a>Canal de telemetria

O canal de telemetria gere a memória intermédia e transmissão de telemetria para o serviço de informações da aplicação. 

* `Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.ServerTelemetryChannel`é o canal predefinido para os serviços. -Serve de memória intermédia dados na memória.
* `Microsoft.ApplicationInsights.PersistenceChannel`é uma alternativa às aplicações da consola. -Pode guardar quaisquer dados unflushed armazenamento persistente quando a sua aplicação fecha-se para baixo e irá enviá-lo quando a aplicação é iniciado novamente.


## <a name="telemetry-initializers-aspnet"></a>Inicializadores de telemetria (ASP.NET)

Inicializadores de telemetria definir propriedades de contexto que são enviadas juntamente com todos os itens de telemetria. 

Pode [escrever o seus próprio inicializadores](app-insights-api-filtering-sampling.md#add-properties) para definir propriedades de contexto.

Os inicializadores padrão estão todas definidos pelos pacotes Web ou WindowsServer NuGet:


* `AccountIdTelemetryInitializer`Define a propriedade AccountId.
* `AuthenticatedUserIdTelemetryInitializer`Define a propriedade AuthenticatedUserId tal como definido pelo SDK JavaScript.
* `AzureRoleEnvironmentTelemetryInitializer`atualizações de `RoleName` e `RoleInstance` propriedades do `Device` contexto para todos os itens de telemetria com informações extraídas do ambiente de Azure runtime.
* `BuildInfoConfigComponentVersionTelemetryInitializer`atualizações de `Version` propriedade do `Component` contexto para todos os itens de telemetria com o valor extraídas do `BuildInfo.config` ficheiro produzidos pela MS Build.
* `ClientIpHeaderTelemetryInitializer`atualizações `Ip` propriedade do `Location` contexto de todos os itens de telemetria com base na `X-Forwarded-For` cabeçalho de HTTP do pedido.
* `DeviceTelemetryInitializer`Atualiza as seguintes propriedades do `Device` contexto para todos os itens de telemetria.
 - `Type`está definido para "PC"
 - `Id`está definido para o nome de domínio do computador onde está a ser executado a aplicação web.
 - `OemName`está definido para o valor extraído do `Win32_ComputerSystem.Manufacturer` utilizando o WMI de campo.
 - `Model`está definido para o valor extraído do `Win32_ComputerSystem.Model` utilizando o WMI de campo.
 - `NetworkType`está definido para o valor extraído do `NetworkInterface`.
 - `Language`está definido para o nome do `CurrentCulture`.
* `DomainNameRoleInstanceTelemetryInitializer`atualizações de `RoleInstance` propriedade do `Device` contexto para todos os itens de telemetria com o nome de domínio do computador onde está a ser executado a aplicação web.
* `OperationNameTelemetryInitializer`atualizações de `Name` propriedade do `RequestTelemetry` e o `Name` propriedade do `Operation` contexto de todos os itens de telemetria com base no método de HTTP, bem como nomes de controlador de ASP.NET MVC e ação chamado para processar o pedido.
* `OperationIdTelemetryInitializer`ou `OperationCorrelationTelemetryInitializer` atualizações a `Operation.Id` propriedade de contexto de todos os itens de telemetria registadas enquanto processamento um pedido de com gerados automaticamente `RequestTelemetry.Id`.
* `SessionTelemetryInitializer`atualizações de `Id` propriedade do `Session` contexto para todos os itens de telemetria com valor extraídas do `ai_session` cookie gerado pelo código ApplicationInsights JavaScript instrumentação em execução no browser do utilizador. 
* `SyntheticTelemetryInitializer`ou `SyntheticUserAgentTelemetryInitializer` atualizações a `User`, `Session` e `Operation` propriedades contextos de todos os itens de telemetria registadas quando processamento um pedido de uma origem de síntese, tal como a disponibilidade de uma teste ou bot do motor de pesquisa. Por predefinição, [Métricas Explorer](app-insights-metrics-explorer.md) não apresenta telemetria síntese. 

    O `<Filters>` definir propriedades dos pedidos de identificar.
* `UserAgentTelemetryInitializer`atualizações a `UserAgent` propriedade do `User` contexto de todos os itens de telemetria com base na `User-Agent` cabeçalho de HTTP do pedido.
* `UserTelemetryInitializer`atualizações de `Id` e `AcquisitionDate` propriedades de `User` contexto para todos os itens de telemetria com valores extraídas do `ai_user` cookie gerado pelo código de instrumentação JavaScript de informações da aplicação em execução no browser do utilizador.
* `WebTestTelemetryInitializer`Define o id de utilizador, id da sessão e propriedades da origem síntese para pedidos HTTP provêm de [testes de disponibilidade](app-insights-monitor-web-app-availability.md).
O `<Filters>` definir propriedades dos pedidos de identificar.

## <a name="telemetry-processors-aspnet"></a>Processadores de telemetria (ASP.NET)

Processadores de telemetria podem filtrar e modificar a cada item de telemetria antes de ter sido enviada a partir do SDK do portal.

Pode [escrever o seus próprio processadores de telemetria](app-insights-api-filtering-sampling.md#filtering).


#### <a name="adaptive-sampling-telemetry-processor-from-200-beta3"></a>Processador de telemetria amostragem ajustável (a partir do 2.0.0-beta3)

Isto está ativado por predefinição. Se a sua aplicação envia muitas telemetria, este processador remove algum.

```xml

    <TelemetryProcessors>
      <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.AdaptiveSamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">
        <MaxTelemetryItemsPerSecond>5</MaxTelemetryItemsPerSecond>
      </Add>
    </TelemetryProcessors>

```

O parâmetro fornece de destino que o algoritmo tenta alcançar. Cada instância do SDK funciona de forma independente, para que se o servidor pertencer a um cluster de várias máquinas, o volume real de telemetria será multiplicado em conformidade.

[Saber mais sobre amostragem](app-insights-sampling.md).



#### <a name="fixed-rate-sampling-telemetry-processor-from-200-beta1"></a>Processador de telemetria amostragem de taxa de juro fixa (a partir do 2.0.0-beta1)

Também é um padrão [processador de telemetria amostragem](app-insights-api-filtering-sampling.md#sampling) (a partir do 2.0.1):

```XML

    <TelemetryProcessors>
     <Add Type="Microsoft.ApplicationInsights.WindowsServer.TelemetryChannel.SamplingTelemetryProcessor, Microsoft.AI.ServerTelemetryChannel">

     <!-- Set a percentage close to 100/N where N is an integer. -->
     <!-- E.g. 50 (=100/2), 33.33 (=100/3), 25 (=100/4), 20, 1 (=100/100), 0.1 (=100/1000) -->
     <SamplingPercentage>10</SamplingPercentage>
     </Add>
   </TelemetryProcessors>

```



## <a name="channel-parameters-java"></a>Parâmetros do canal (Java)

Estes parâmetros afetam como o SDK Java deve armazenar e esvaziar os dados de telemetria-recolhe.

#### <a name="maxtelemetrybuffercapacity"></a>MaxTelemetryBufferCapacity

O número de itens de telemetria que podem ser armazenados numa armazenamento incorporado na memória o SDK. Quando é atingido este número, a memória intermédia de telemetria é esvaziada - ou seja, os itens de telemetria são enviados para o servidor de informações da aplicação.

-   Mínimo: 1
-   Máximo: 1000
-   Predefinido: 500

```

  <ApplicationInsights>
      ...
      <Channel>
       <MaxTelemetryBufferCapacity>100</MaxTelemetryBufferCapacity>
      </Channel>
      ...
  </ApplicationInsights>
```

#### <a name="flushintervalinseconds"></a>FlushIntervalInSeconds 

Determina com que frequência os dados que estão armazenados no armazenamento na memória devem ser esvaziados (enviado para informações de aplicação).

-   Mínimo: 1
-   Máximo: 300
-   Predefinido: 5

```

    <ApplicationInsights>
      ...
      <Channel>
        <FlushIntervalInSeconds>100</FlushIntervalInSeconds>
      </Channel>
      ...
    </ApplicationInsights>
```

#### <a name="maxtransmissionstoragecapacityinmb"></a>MaxTransmissionStorageCapacityInMB

Determina o tamanho máximo em MB que é atribuído ao armazenamento de persistente no disco local. Este armazenamento é utilizado para itens de telemetria persistentes que ocorreu uma falha ao transmitidas para o ponto final de informações da aplicação. Quando o tamanho de armazenamento foi satisfeito, novos itens de telemetria serão eliminados.

-   Mínimo: 1
-   Máximo: 100
-   Predefinido: 10

```

   <ApplicationInsights>
      ...
      <Channel>
        <MaxTransmissionStorageCapacityInMB>50</MaxTransmissionStorageCapacityInMB>
      </Channel>
      ...
   </ApplicationInsights>
```



## <a name="instrumentationkey"></a>InstrumentationKey

Isto determina o recurso de informações da aplicação na qual os seus dados é apresentada. Normalmente cria um recurso separado, com uma chave de separada para cada uma das suas aplicações.

Se pretender definir a chave dinamicamente - por exemplo, se pretender enviar resultados a partir da sua aplicação para recursos diferentes - pode omitir a chave no ficheiro de configuração e defini-lo no código.

Para definir a chave para todas as instâncias do TelemetryClient, incluindo módulos de telemetria padrão, defina a chave no TelemetryConfiguration.Active. Faça o seguinte num método de inicialização, tal como global.aspx.cs num serviço ASP.NET:

```C#

    protected void Application_Start()
    {
      Microsoft.ApplicationInsights.Extensibility.
        TelemetryConfiguration.Active.InstrumentationKey = 
          // - for example -
          WebConfigurationManager.Settings["ikey"];
      //...
```

Se apenas quiser enviar um conjunto específico de eventos a um recurso diferente, pode definir a chave para um TelemetryClient específica:

```C#

    var tc = new TelemetryClient();
    tc.Context.InstrumentationKey = "----- my key ----";
    tc.TrackEvent("myEvent");
    // ...

```

[Saiba mais sobre a API][api].

Para obter uma nova chave, [criar um novo recurso no portal do aplicação informações][new].

<!--Link references-->

[api]: app-insights-api-custom-events-metrics.md
[client]: app-insights-javascript.md
[diagnostic]: app-insights-diagnostic-search.md
[exceptions]: app-insights-asp-net-exceptions.md
[netlogs]: app-insights-asp-net-trace-logs.md
[new]: app-insights-create-new-resource.md
[redfield]: app-insights-monitor-performance-live-website-now.md
[start]: app-insights-overview.md

