<properties
   pageTitle="Comunicação com a API de Web do ASP.NET do serviço | Microsoft Azure"
   description="Saiba como implementar a comunicação do serviço passo a passo ao utilizar a API de Web do ASP.NET com OWIN alojamento personalizada na API do fiável serviços."
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
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="get-started-service-fabric-web-api-services-with-owin-self-hosting"></a>Introdução: serviços de serviço ferro Web API com OWIN alojamento personalizada

Azure Service ferro coloca a potência nas suas mãos quando está a decidir como pretende que os seus serviços para comunicar com utilizadores e com os outros. Neste tutorial foca-se em execução comunicação do serviço de utilizar a API do Web do ASP.NET com a Interface de Web aberta para .NET (OWIN) alojamento personalizada fiável serviços API do serviço ferro do. Vamos irá o delve fortemente para comunicação incorporável serviços fiáveis API. Vamos também utilizar a API do Web um exemplo passo a passo para mostrar-lhe como configurar uma escuta de comunicação personalizado.


## <a name="introduction-to-web-api-in-service-fabric"></a>Introdução ao Web API no ferro de serviço

Web do ASP.NET API é uma estrutura mais popular e poderosa para a criação de HTTP APIs na parte superior do .NET Framework. Se não ainda estiver familiarizado com a arquitetura, consulte o artigo [Introdução ao ASP.NET Web API 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api) para obter mais informações.

Web API no serviço ferro é a mesma ASP.NET Web API que conhece e adora. A diferença é na como *anfitrião de* uma aplicação Web API. Não estar a utilizar serviços de informação Internet (IIS) da Microsoft. Para compreender melhor a diferença, vamos dividi-la em duas partes:

 1. A aplicação Web API (incluindo os controladores e modelos de)
 2. Anfitrião do (o servidor web, normalmente IIS)

Não altera uma aplicação Web API própria. É não diferente a partir de aplicações Web API que pode ter escrito no passado e deverá conseguir mover simplesmente sobre a maior parte das código da aplicação. Mas, se tiver sido alojar no IIS, onde o anfitrião da aplicação poderá ser um pouco diferente do está habituado a. Antes de recebemos para a peça alojamento, vamos começar pelos algo mais familiar: a aplicação Web API.


## <a name="create-the-application"></a>Criar a aplicação

Comece por criar uma nova aplicação de serviço ferro com um único serviço sem estado no Visual Studio 2015:

![Criar uma nova aplicação de serviço ferro](media/service-fabric-reliable-services-communication-webapi/webapi-newproject.png)

Um modelo do Visual Studio para um serviço sem estado utilizando Web API está disponível para si. Neste tutorial, irá criar um projeto de Web API de raiz que resulta em o que obteria se tiver selecionado este modelo.

Selecione um projeto em branco do serviço sem estado para saber como criar um projeto de Web API do zero ou pode começar com o modelo de Web API sem estado do serviço e simplesmente segui-los.  

![Criar um único serviço sem estado](media/service-fabric-reliable-services-communication-webapi/webapi-newproject2.png)

O primeiro passo é separar no alguns pacotes NuGet para Web API. O pacote que pretende utilizar está Microsoft.AspNet.WebApi.OwinSelfHost. Este pacote inclui todos os pacotes necessários da Web API e os pacotes de *anfitrião* . Isto vai ser importante mais tarde.

![Criar Web API utilizando o Gestor de pacote NuGet](media/service-fabric-reliable-services-communication-webapi/webapi-nuget.png)

Depois dos pacotes de tem sido instalados, pode começar edifício saída a estrutura do project Web API básica. Caso tenha utilizado Web API, a estrutura do projecto deverá ter um aspeto muito familiar. Comece por adicionar um `Controllers` diretório e um controlador de valores simples:

**ValuesController.cs**

```csharp
using System.Collections.Generic;
using System.Web.Http;
    
namespace WebService.Controllers
{
    public class ValuesController : ApiController
    {
        // GET api/values 
        public IEnumerable<string> Get()
        {
            return new string[] { "value1", "value2" };
        }

        // GET api/values/5 
        public string Get(int id)
        {
            return "value";
        }

        // POST api/values 
        public void Post([FromBody]string value)
        {
        }

        // PUT api/values/5 
        public void Put(int id, [FromBody]string value)
        {
        }

        // DELETE api/values/5 
        public void Delete(int id)
        {
        }
    }
}

```

Em seguida, adicione uma classe de arranque na raiz project para registar o encaminhamento, formatters e outro programa de configuração de configuração. É também onde Web API liga para o *anfitrião*, que irão ser revistas novamente mais tarde. 

**Startup.CS**

```csharp
using System.Web.Http;
using Owin;

namespace WebService
{
    public static class Startup
    {
        public static void ConfigureApp(IAppBuilder appBuilder)
        {
            // Configure Web API for self-host. 
            HttpConfiguration config = new HttpConfiguration();

            config.Routes.MapHttpRoute(
                name: "DefaultApi",
                routeTemplate: "api/{controller}/{id}",
                defaults: new { id = RouteParameter.Optional }
            );

            appBuilder.UseWebApi(config);
        }
    }
}
```

Já está para a peça de aplicação. Neste momento, podemos configurou apenas o esquema de projeto de Web API básico. Até ao momento, não devem estar muito diferente de projetos Web API que pode ter escrito no passado ou a partir do modelo básico do Web API. Lógica de negócio são introduzidas nos controladores e os modelos de como costuma fazer.

Agora, o que fazer sobre o alojamento para que realmente podemos pode executá-la?

## <a name="service-hosting"></a>Serviço de alojamento

Ferro de serviço, o serviço é executado num *processo do anfitrião do serviço*, um ficheiro executável que executa o seu código de serviço. Quando escreve um serviço utilizando a fiável API de serviços, o seu projeto do serviço compila apenas para um ficheiro executável que regista o tipo de serviço e executa o seu código. Esta situação for verdadeira na maioria dos casos quando escreve um serviço no serviço ferro no .NET. Quando abre Program.cs no projeto sem estado do serviço, deverá ver:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric.Services.Runtime;

internal static class Program
{
    private static void Main()
    {
        try
        {
            ServiceRuntime.RegisterServiceAsync("WebServiceType",
                context => new WebService(context)).GetAwaiter().GetResult();

            ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(WebService).Name);

            // Prevents this host process from terminating so services keeps running. 
            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
            throw;
        }
    }
}

```

Se que aspeto forma suspeita o ponto de entrada para uma aplicação de consola, é porque é.

São mais detalhes sobre o processo de anfitrião de serviço e registo do serviço para além do âmbito deste artigo. Mas é importante saber por agora que o *que seu código do serviço está a ser executado no seu próprio processo*.

## <a name="self-host-web-api-with-an-owin-host"></a>Alojar personalizada Web API com um anfitrião OWIN

Dado que o seu código da aplicação Web API está alojado no seu próprio processo, como pode associá-la por excesso para um servidor web? Introduza [OWIN](http://owin.org/). OWIN é simplesmente um contrato entre as aplicações de web do .NET e servidores web. Tradicionais quando é utilizado ASP.NET (até MVC 5), a aplicação web está intimamente ao forma ao IIS através de System. Web. No entanto, Web API implementa OWIN, por isso pode escrever uma aplicação web que é desacoplada do servidor web que aloja-lo. Por esta razão, pode utilizar um *personalizada alojado* OWIN servidor web que pode começar a sua própria processo. Este se ajuste perfeitamente com o modelo de alojamento de serviço ferro que acabámos de descrever.

Neste artigo, vamos utilizar Katana como o anfitrião OWIN para a aplicação Web API. Katana é uma implementação de anfitrião OWIN abrir origem criada com base em [System.Net.HttpListener](https://msdn.microsoft.com/library/system.net.httplistener.aspx) e a [API do servidor HTTP](https://msdn.microsoft.com/library/windows/desktop/aa364510.aspx)do Windows.

> [AZURE.NOTE] Para saber mais sobre Katana, vá para o [Katana site](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana). Para obter uma descrição geral rápida de como utilizar Katana personalizada alojar Web API, consulte o artigo [Utilizar OWIN para Self-Host ASP.NET Web API 2](http://www.asp.net/web-api/overview/hosting-aspnet-web-api/use-owin-to-self-host-web-api).


## <a name="set-up-the-web-server"></a>Configurar o servidor web

A API serviços fiável fornece um ponto de entrada de comunicação onde pode Plug-in pilhas de comunicação que permitem aos utilizadores e os clientes ligar ao serviço:

```csharp

protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    ...
}

```

O servidor web (e outros pilha de comunicação que utilizar no futuro, tal como WebSockets) devem utilizar a interface de ICommunicationListener para integrar corretamente com o sistema. Motivos para isto irão tornar-se mais evidentes nos passos seguintes.

Primeiro, crie uma classe denominada OwinCommunicationListener implementa ICommunicationListener:

**OwinCommunicationListener.cs**

```csharp
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;
using System;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        public void Abort()
        {
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
        }
    }
}
```

A interface de ICommunicationListener fornece três métodos para gerir um ouvinte de comunicação do seu serviço:

 - *OpenAsync*. Começar a escutar para pedidos de.
 - *CloseAsync*. Parar de escutar para pedidos de, concluir a quaisquer pedidos em voo e encerrar correctamente.
 - *Cancelar*. Cancelar tudo e parar imediatamente.

Para começar, adicione membros de classe privado para coisas que a escuta terá de funcionar. Estes serão inicializados através do construtor e utilização posterior ao configurar o URL de espera.

```csharp
internal class OwinCommunicationListener : ICommunicationListener
{
    private readonly ServiceEventSource eventSource;
    private readonly Action<IAppBuilder> startup;
    private readonly ServiceContext serviceContext;
    private readonly string endpointName;
    private readonly string appRoot;

    private IDisposable webApp;
    private string publishAddress;
    private string listeningAddress;

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
        : this(startup, serviceContext, eventSource, endpointName, null)
    {
    }

    public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
    {
        if (startup == null)
        {
            throw new ArgumentNullException(nameof(startup));
        }

        if (serviceContext == null)
        {
            throw new ArgumentNullException(nameof(serviceContext));
        }

        if (endpointName == null)
        {
            throw new ArgumentNullException(nameof(endpointName));
        }

        if (eventSource == null)
        {
            throw new ArgumentNullException(nameof(eventSource));
        }

        this.startup = startup;
        this.serviceContext = serviceContext;
        this.endpointName = endpointName;
        this.eventSource = eventSource;
        this.appRoot = appRoot;
    }
   

    ...

```

## <a name="implement-openasync"></a>Implementar OpenAsync

Para configurar o servidor web, necessita de dois blocos de informações:

 - *Prefixo de caminho de URL*. Apesar de isto é opcional, é boa ideia para configurar esta agora para que possa com segurança aloje vários serviços web na sua aplicação.
 - *Uma porta*.

Antes de obter uma porta para o servidor web, é importante que compreendem a que o serviço ferro fornece uma camada de aplicação que age como um intervalo de tempo entre a aplicação e o sistema operativo subjacente que é executado no. Como tal, o serviço ferro fornece uma forma para configurar *os pontos finais* para os seus serviços. Serviço ferro garante que os pontos finais estão disponíveis do seu serviço de utilizar. Desta forma, não tem de configurá-los manualmente no ambiente do SO subjacente. Pode alojar facilmente a aplicação de serviço ferro em ambientes diferentes sem ter de fazer as alterações à aplicação. (Por exemplo, pode alojar a mesma aplicação no Azure ou no seu próprio Centro de dados.)

Configure um ponto final HTTP PackageRoot\ServiceManifest.xml:

```xml

<Resources>
    <Endpoints>
        <Endpoint Name="ServiceEndpoint" Type="Input" Protocol="http" Port="8281" />
    </Endpoints>
</Resources>

```

Este passo é importante porque o processo de anfitrião do serviço é executado sob restritas credenciais (serviço de rede no Windows). Isto significa que o seu serviço não têm acesso ao configurar um ponto final HTTP no seu próprio. Ao utilizar a configuração de ponto final, o serviço ferro sabe para configurar a lista de controlo de acesso adequado (ACL) para o URL que o serviço escutar. Serviço ferro também fornece um local padrão para configurar os pontos finais.


Novamente na OwinCommunicationListener.cs, pode começar a execução OpenAsync. Este é onde pode inicia o servidor web. Em primeiro lugar, obter as informações de ponto final e crie o URL que o serviço escutar. O URL vai ser diferente dependendo se o serviço de escuta é utilizado num serviço sem estado ou num serviço de estado. Para um serviço com estado, a escuta tem de criar um endereço exclusivo para cada réplica serviço com estado-recebe. Para os serviços sem estado, o endereço pode ser muito mais simples. 

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
    var protocol = serviceEndpoint.Protocol;
    int port = serviceEndpoint.Port;

    if (this.serviceContext is StatefulServiceContext)
    {
        StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}{3}/{4}/{5}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/',
            statefulServiceContext.PartitionId,
            statefulServiceContext.ReplicaId,
            Guid.NewGuid());
    }
    else if (this.serviceContext is StatelessServiceContext)
    {
        this.listeningAddress = string.Format(
            CultureInfo.InvariantCulture,
            "{0}://+:{1}/{2}",
            protocol,
            port,
            string.IsNullOrWhiteSpace(this.appRoot)
                ? string.Empty
                : this.appRoot.TrimEnd('/') + '/');
    }
    else
    {
        throw new InvalidOperationException();
    }
    
    ...

```

Tenha em atenção que "http://+" é utilizada aqui. Este é para se certificar de que o servidor web está a escutar todos os endereços disponíveis, incluindo localhost, FQDN e o período de máquina inquérito.

A implementação de OpenAsync é uma das razões mais importantes porque é que o servidor web (ou qualquer pilha de comunicação) é implementada como um ICommunicationListener, em vez de apenas ser necessário abre diretamente a partir do `RunAsync()` no serviço. O valor de retorno do OpenAsync é o endereço que está a escutar o servidor web. Quando este endereço é devolvido ao sistema, regista o endereço com o serviço. Serviço ferro fornece uma API que permite aos clientes e outros serviços, em seguida, pergunte para este endereço, nome do serviço. Isto é importante porque o endereço de serviço não é estático. Serviços são movidos à volta do cluster para fins de equilíbrio e disponibilidade de recursos. Este é o mecanismo que permite que os clientes resolver o endereço escutar para um serviço.

Com que deve ter em conta, OpenAsync inicia o servidor web e devolve o endereço que está a escutar. Nota que recebe no "http://+", mas antes de OpenAsync devolve o endereço, o "+" é substituído por IP ou FQDN do nó está ligado. O endereço que é devolvido por este método é o que está registado com o sistema. Também é o que os clientes e outros serviços veem quando pedem para o endereço de um serviço. Para que os clientes corretamente ligá-la, precisam um IP ou um FQDN real o endereço.

```csharp
    ...

    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

    try
    {
        this.eventSource.Message("Starting web server on " + this.listeningAddress);

        this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

        this.eventSource.Message("Listening on " + this.publishAddress);

        return Task.FromResult(this.publishAddress);
    }
    catch (Exception ex)
    {
        this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

        this.StopWebServer();

        throw;
    }
}

```

Tenha em atenção que este referencia a classe de arranque que foi transmitida para OwinCommunicationListener no construtor. Esta instância de arranque é utilizada pelo servidor web arranque a aplicação Web API do sistema.

O `ServiceEventSource.Current.Message()` linha irá aparecer na janela de eventos de diagnóstico mais tarde, quando executar a aplicação para confirmar que o servidor web foi iniciado com êxito.

## <a name="implement-closeasync-and-abort"></a>Implementar CloseAsync e cancelamento

Por fim, implemente CloseAsync e cancelamento para parar o servidor web. O servidor web pode ser terminado pelo eliminação a alça de servidor que foi criada durante a OpenAsync.

```csharp
public Task CloseAsync(CancellationToken cancellationToken)
{
    this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);
            
    this.StopWebServer();

    return Task.FromResult(true);
}

public void Abort()
{
    this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);
    
    this.StopWebServer();
}

private void StopWebServer()
{
    if (this.webApp != null)
    {
        try
        {
            this.webApp.Dispose();
        }
        catch (ObjectDisposedException)
        {
            // no-op
        }
    }
}
```

Neste exemplo de implementação, CloseAsync e cancelamento simplesmente interrompe o servidor web. Pode optar por permitir para executar um encerramento mais correctamente coordenado do servidor web no CloseAsync. Por exemplo, o encerramento poderia aguardar em voo pedidos para ser concluída antes do retorno.

## <a name="start-the-web-server"></a>Iniciar o servidor web

Agora está pronto para criar e devolver uma instância da OwinCommunicationListener para iniciar o servidor web. Novamente na classe de serviço (WebService.cs), substituir o `CreateServiceInstanceListeners()` método:

```csharp
protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
{
    var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                           .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                           .Select(endpoint => endpoint.Name);

    return endpoints.Select(endpoint => new ServiceInstanceListener(
        serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
}
```

Este é onde a *aplicação* de Web API e o OWIN *anfitrião* finalmente cumprem. O anfitrião (OwinCommunicationListener) é dado uma instância da *aplicação* (Web API) através da classe de arranque. Serviço ferro, em seguida, faz a gestão dos seu ciclo de vida. Normalmente, pode ser seguido este mesmo padrão com qualquer pilha de comunicação.

## <a name="put-it-all-together"></a>Juntar tudo

Neste exemplo, não precisa de fazer nada `RunAsync()` método, para que esse substituição simplesmente pode ser removida.

A aplicação de serviço final deve ser muito simple. Só precisa criar a escuta de comunicação:

```csharp
using System;
using System.Collections.Generic;
using System.Fabric;
using System.Fabric.Description;
using System.Linq;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Microsoft.ServiceFabric.Services.Runtime;

namespace WebService
{
    internal sealed class WebService : StatelessService
    {
        public WebService(StatelessServiceContext context)
            : base(context)
        { }

        protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
        {
            var endpoints = Context.CodePackageActivationContext.GetEndpoints()
                                   .Where(endpoint => endpoint.Protocol == EndpointProtocol.Http || endpoint.Protocol == EndpointProtocol.Https)
                                   .Select(endpoint => endpoint.Name);

            return endpoints.Select(endpoint => new ServiceInstanceListener(
                serviceContext => new OwinCommunicationListener(Startup.ConfigureApp, serviceContext, ServiceEventSource.Current, endpoint), endpoint));
        }
    }
}
```

O concluído `OwinCommunicationListener` classe:

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Globalization;
using System.Threading;
using System.Threading.Tasks;
using Microsoft.Owin.Hosting;
using Microsoft.ServiceFabric.Services.Communication.Runtime;
using Owin;

namespace WebService
{
    internal class OwinCommunicationListener : ICommunicationListener
    {
        private readonly ServiceEventSource eventSource;
        private readonly Action<IAppBuilder> startup;
        private readonly ServiceContext serviceContext;
        private readonly string endpointName;
        private readonly string appRoot;

        private IDisposable webApp;
        private string publishAddress;
        private string listeningAddress;

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName)
            : this(startup, serviceContext, eventSource, endpointName, null)
        {
        }

        public OwinCommunicationListener(Action<IAppBuilder> startup, ServiceContext serviceContext, ServiceEventSource eventSource, string endpointName, string appRoot)
        {
            if (startup == null)
            {
                throw new ArgumentNullException(nameof(startup));
            }

            if (serviceContext == null)
            {
                throw new ArgumentNullException(nameof(serviceContext));
            }

            if (endpointName == null)
            {
                throw new ArgumentNullException(nameof(endpointName));
            }

            if (eventSource == null)
            {
                throw new ArgumentNullException(nameof(eventSource));
            }

            this.startup = startup;
            this.serviceContext = serviceContext;
            this.endpointName = endpointName;
            this.eventSource = eventSource;
            this.appRoot = appRoot;
        }

        public Task<string> OpenAsync(CancellationToken cancellationToken)
        {
            var serviceEndpoint = this.serviceContext.CodePackageActivationContext.GetEndpoint(this.endpointName);
            var protocol = serviceEndpoint.Protocol;
            int port = serviceEndpoint.Port;

            if (this.serviceContext is StatefulServiceContext)
            {
                StatefulServiceContext statefulServiceContext = this.serviceContext as StatefulServiceContext;

                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}{3}/{4}/{5}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/',
                    statefulServiceContext.PartitionId,
                    statefulServiceContext.ReplicaId,
                    Guid.NewGuid());
            }
            else if (this.serviceContext is StatelessServiceContext)
            {
                this.listeningAddress = string.Format(
                    CultureInfo.InvariantCulture,
                    "{0}://+:{1}/{2}",
                    protocol,
                    port,
                    string.IsNullOrWhiteSpace(this.appRoot)
                        ? string.Empty
                        : this.appRoot.TrimEnd('/') + '/');
            }
            else
            {
                throw new InvalidOperationException();
            }

            this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);

            try
            {
                this.eventSource.Message("Starting web server on " + this.listeningAddress);

                this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));

                this.eventSource.Message("Listening on " + this.publishAddress);

                return Task.FromResult(this.publishAddress);
            }
            catch (Exception ex)
            {
                this.eventSource.Message("Web server failed to open endpoint {0}. {1}", this.endpointName, ex.ToString());

                this.StopWebServer();

                throw;
            }
        }

        public Task CloseAsync(CancellationToken cancellationToken)
        {
            this.eventSource.Message("Closing web server on endpoint {0}", this.endpointName);

            this.StopWebServer();

            return Task.FromResult(true);
        }

        public void Abort()
        {
            this.eventSource.Message("Aborting web server on endpoint {0}", this.endpointName);

            this.StopWebServer();
        }

        private void StopWebServer()
        {
            if (this.webApp != null)
            {
                try
                {
                    this.webApp.Dispose();
                }
                catch (ObjectDisposedException)
                {
                    // no-op
                }
            }
        }
    }
}
```

Agora que tem de colocar todas as peças no local, o seu projeto deve aspeto uma aplicação Web API normal com pontos de entrada fiável API de serviços e um anfitrião OWIN:


![Web API com pontos de entrada fiável API de serviços e anfitrião OWIN](media/service-fabric-reliable-services-communication-webapi/webapi-projectstructure.png)

## <a name="run-and-connect-through-a-web-browser"></a>Executar e ligue-se através de um browser web

Se ainda não o tiver feito, [configurar o seu ambiente de desenvolvimento](service-fabric-get-started.md).


Agora pode criar e implementar o seu serviço. Prima **F5** no Visual Studio para criar e implementar a aplicação. Na janela de eventos de diagnóstico, deverá ver uma mensagem que indica que o servidor web aberto no http://localhost:8281 /.


![Janela do Visual Studio eventos de diagnóstico](media/service-fabric-reliable-services-communication-webapi/webapi-diagnostics.png)

> [AZURE.NOTE] Se já tiver sido aberta a porta por outro processo, no seu computador, poderá ver um erro aqui. Isto indica que não foi possível abrir o serviço de escuta. Se for esse o caso, experimente utilizar uma porta diferente para a configuração de ponto final ServiceManifest.xml.


Assim que a execução do serviço, abra um browser e navegue até à [api/http://localhost:8281/valores](http://localhost:8281/api/values) para testá-la.

## <a name="scale-it-out"></a>Dimensioná-lo de saída

Dimensionamento saída aplicações web sem estado normalmente significa que adicionar mais máquinas e rodar um as aplicações web nos mesmos. Motor de orchestration do serviço ferro pode fazê-lo automaticamente sempre que novos nós são adicionados a um cluster. Quando cria ocorrências de um serviço sem estado, pode especificar o número de instâncias que pretende criar. Serviço ferro coloca esse número de instâncias em nós num cluster. E torna-se de que não criar mais do que uma instância em qualquer um nó. Também pode indicar ferro de serviço para criar sempre uma instância em todos os nós ao especificar **-1** para a contagem de instância. Isto garante que, sempre que adicionar nós para dimensionar saída seu cluster, uma instância do seu serviço sem estado será criada em nós de novos. Este valor é uma propriedade da instância do serviço, por isso está definido quando cria uma instância do serviço. Pode fazê-lo através do PowerShell:

```powershell

New-ServiceFabricService -ApplicationName "fabric:/WebServiceApplication" -ServiceName "fabric:/WebServiceApplication/WebService" -ServiceTypeName "WebServiceType" -Stateless -PartitionSchemeSingleton -InstanceCount -1

```

Pode também fazê-lo quando define um serviço de predefinição num projeto do Visual Studio sem estado de serviço:

```xml

<DefaultServices>
  <Service Name="WebService">
    <StatelessService ServiceTypeName="WebServiceType" InstanceCount="-1">
      <SingletonPartition />
    </StatelessService>
  </Service>
</DefaultServices>

```

Para mais informações sobre como criar a aplicação e instâncias do serviço, consulte o artigo [Implementar uma aplicação](service-fabric-deploy-remove-applications.md).

## <a name="next-steps"></a>Próximos passos

[Depurar a aplicação de serviço ferro utilizando o Visual Studio](service-fabric-debugging-your-application.md)
