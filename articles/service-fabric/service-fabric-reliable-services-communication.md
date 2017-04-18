<properties
   pageTitle="Descrição geral de comunicação de serviços fiável | Microsoft Azure"
   description="Descrição geral do modelo de comunicação fiável serviços, incluindo abrir listeners sobre os serviços, resolver os pontos finais e comunicar entre os serviços."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="BharatNarasimman"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="how-to-use-the-reliable-services-communication-apis"></a>Como utilizar a comunicação de serviços fiáveis APIs

Azure Service ferro como uma plataforma é completamente desconhecido sobre a comunicação entre os serviços. Todos os protocolos e pilhas são aceitáveis, a partir do UDP para HTTP. O utilizador é o Programador de serviço para escolher como os serviços devem comunicar. A arquitetura de aplicação de serviços fiáveis fornece pilhas de comunicação incorporados, bem como APIs que pode utilizar para criar o seu componentes de comunicação personalizado. 

## <a name="set-up-service-communication"></a>Configurar a comunicação de serviço

A API serviços fiável utiliza uma interface simple para a comunicação de serviço. Para abrir um ponto final do seu serviço, basta implemente esta interface:

```csharp

public interface ICommunicationListener
{
    Task<string> OpenAsync(CancellationToken cancellationToken);

    Task CloseAsync(CancellationToken cancellationToken);

    void Abort();
}

```

Em seguida, pode adicionar a implementação de escuta de comunicação, devolvendo-lo numa substituição de método de classe baseado no serviço.

Para serviços de estado:

```csharp
class MyStatelessService : StatelessService
{
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
        ...
    }
    ...
}
```

Para os serviços com estado:

```csharp
class MyStatefulService : StatefulService
{
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        ...
    }
    ...
}
```

Em ambos os casos, devolver uma coleção de listeners. Isto permite que o seu serviço a escutar vários pontos finais potencialmente utilizar protocolos diferentes, utilizando vários listeners. Por exemplo, poderá ter uma escuta HTTP e uma escuta WebSocket em separado. Cada escuta obtém um nome e a coleção de resultante da *nome: endereço* pares é representado como um objeto JSON quando um cliente pede os endereços de espera para uma partição ou uma instância do serviço.

No serviço de estado, a substituição devolve um conjunto de ServiceInstanceListeners. Um ServiceInstanceListener contém uma função para criar um ICommunicationListener e atribui-lhe um nome. Para os serviços com estado, a substituição devolve um conjunto de ServiceReplicaListeners. Este é um pouco diferentes a partir do homólogo sem estado, uma vez que um ServiceReplicaListener tem uma opção para abrir uma ICommunicationListener em réplicas secundárias. Não só pode utilizar vários listeners de comunicação num serviço, mas também pode especificar quais listeners aceitam pedidos em réplicas secundários e os que escutam apenas réplicas principais.

Por exemplo, pode ter uma ServiceRemotingListener que entra em chamadas RPC apenas em réplicas principais e os pedidos de uma segunda, personalizada escuta do feed de leitura em réplicas secundárias através de HTTP:

```csharp
protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
{
    return new[]
    {
        new ServiceReplicaListener(context =>
            new MyCustomHttpListener(context),
            "HTTPReadonlyEndpoint",
            true),

        new ServiceReplicaListener(context =>
            this.CreateServiceRemotingListener(context),
            "rpcPrimaryEndpoint",
            false)
    };
}
```

> [AZURE.NOTE] Quando criar múltiplas listeners para um serviço, cada escuta **deve** ser fornecidas um nome exclusivo.

Por fim, descrevem os pontos finais que são necessários para o serviço no [serviço manifesto](service-fabric-application-model.md) na secção pontos finais.

```xml
<Resources>
    <Endpoints>
      <Endpoint Name="WebServiceEndpoint" Protocol="http" Port="80" />
      <Endpoint Name="OtherServiceEndpoint" Protocol="tcp" Port="8505" />
    <Endpoints>
</Resources>

```

A escuta de comunicação pode aceder aos recursos de ponto final que atribuído à mesma a partir do `CodePackageActivationContext` na `ServiceContext`. A escuta, em seguida, pode começar a ouvir para pedidos de quando for aberto.

```csharp
var codePackageActivationContext = serviceContext.CodePackageActivationContext;
var port = codePackageActivationContext.GetEndpoint("ServiceEndpoint").Port;

```

> [AZURE.NOTE] Recursos de ponto final são comuns para o pacote de todo o serviço e são atribuídos ao serviço ferro quando o pacote de serviço está ativado. Várias réplicas do serviço alojadas no mesmo ServiceHost podem partilhar a mesma porta. Isto significa que a escuta de comunicação deve suportar a partilha de portas. O caminho recomendado de este procedimento é a escutar de comunicação utilizar a partição ID e ID de réplica de instância quando gera o endereço de ouvir.

### <a name="service-address-registration"></a>Registo do endereço de serviço

Um serviço de sistema denominado o *Serviço de nomenclatura* é executada no serviço ferro clusters. O serviço de nomenclatura é uma entidade de registo para serviços e os respetivos endereços de que cada instância ou réplica do serviço está a escutar. Quando o `OpenAsync` método de um `ICommunicationListener` for concluída, sua devolução valor obtém registado no serviço de atribuição de nomes. Este valor devolvido que obtém publicado no serviço de nomenclatura é uma cadeia cujo valor pode ser qualquer coisa de todo. Este valor de cadeia é o que os clientes Verão pedem para um endereço para o serviço do serviço de atribuição de nomes.

```csharp
public Task<string> OpenAsync(CancellationToken cancellationToken)
{
    EndpointResourceDescription serviceEndpoint = serviceContext.CodePackageActivationContext.GetEndpoint("ServiceEndpoint");
    int port = serviceEndpoint.Port;

    this.listeningAddress = string.Format(
                CultureInfo.InvariantCulture,
                "http://+:{0}/",
                port);
                        
    this.publishAddress = this.listeningAddress.Replace("+", FabricRuntime.GetNodeContext().IPAddressOrFQDN);
            
    this.webApp = WebApp.Start(this.listeningAddress, appBuilder => this.startup.Invoke(appBuilder));
    
    // the string returned here will be published in the Naming Service.
    return Task.FromResult(this.publishAddress);
}
```

Serviço ferro fornece APIs que permite aos clientes e outros serviços, em seguida, pergunte para este endereço, nome do serviço. Isto é importante porque o endereço de serviço não é estático. Serviços são movidos à volta do cluster para fins de equilíbrio e disponibilidade de recursos. Este é o mecanismo que permite que os clientes resolver o endereço escutar para um serviço.

> [AZURE.NOTE] Para uma guia passo a passo completa de como escrever uma `ICommunicationListener`, consulte [Serviços Web API do serviço de ferro com OWIN personalizada alojamento](service-fabric-reliable-services-communication-webapi.md)

## <a name="communicating-with-a-service"></a>Comunicar com um serviço
API serviços fiável fornece as seguintes bibliotecas para escrever clientes comunicam com os serviços.

### <a name="service-endpoint-resolution"></a>Resolução de ponto final de serviço
É o primeiro passo para comunicação com um serviço resolver um endereço de ponto final da partição ou instância do serviço que pretende falar. O `ServicePartitionResolver` utilitário classe é uma primitiva básica que ajuda a determinar o ponto final de um serviço o tempo de execução de clientes. Na terminologia ferro de serviço, o processo de determinar o ponto final de um serviço está designado a *resolução de ponto final de serviço*.

Para ligar aos serviços dentro de um cluster `ServicePartitionResolver` podem ser criados utilizando as predefinições. Esta é a utilização recomendada para a maior parte das situações:

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();
```

Para ligar aos serviços num cluster de diferentes, um `ServicePartitionResolver` podem ser criadas com um conjunto de pontos finais de gateway cluster. Note que os pontos finais de gateway são os pontos finais apenas diferentes para ligar para o mesmo cluster. Por exemplo:

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver("mycluster.cloudapp.azure.com:19000", "mycluster.cloudapp.azure.com:19001");
```

Em alternativa, `ServicePartitionResolver` pode ser dada uma função para criar um `FabricClient` para utilização interna: 
 
```csharp
public delegate FabricClient CreateFabricClientDelegate();
```

`FabricClient`é o objeto que é utilizado para comunicar com o cluster de serviço ferro das várias operações de gestão no cluster. Isto é útil quando quiser ter mais controlo sobre como `ServicePartitionResolver` interage com o seu cluster. `FabricClient`executa a cache internamente e é geralmente dispendioso criar, pelo que é importante reutilizar `FabricClient` instâncias quanto possíveis. 

```csharp
ServicePartitionResolver resolver = new  ServicePartitionResolver(() => CreateMyFabricClient());
```

Um método de resolver, em seguida, é utilizado para obter o endereço de um serviço ou uma partição de serviço para os serviços com partições.

```csharp
ServicePartitionResolver resolver = ServicePartitionResolver.GetDefault();

ResolvedServicePartition partition =
    await resolver.ResolveAsync(new Uri("fabric:/MyApp/MyService"), new ServicePartitionKey(), cancellationToken);
```

Um endereço de serviço pode ser resolvido facilmente utilizando um `ServicePartitionResolver`, mas mais trabalho é necessário para garantir que o endereço resolvido pode ser utilizado corretamente. O cliente será necessário detetar se a tentativa de ligação falhou devido a um erro breves e pode ser repetida (por exemplo, serviço movido ou está temporariamente indisponível), ou um erro permanente (por exemplo, serviço foi eliminado ou o recurso pedido já não existe). Instâncias do serviço ou réplicas podem deslocar-se de nó nó em qualquer altura por diversas razões. O endereço de serviço resolvido através de `ServicePartitionResolver` poderão estar obsoletas por hora seu código do cliente tenta ligar. Nesse caso novamente o cliente terá de resolver novamente o endereço. Fornecer anterior `ResolvedServicePartition` indica que o solucionador necessita de volte a tentar em vez de simplesmente obter um endereço em cache.

Normalmente, o código do cliente não precisa de trabalhar com o `ServicePartitionResolver` diretamente. É criado e passou para fábricas de cliente de comunicação na API do fiável serviços. As fábricas utilização interna a resolução para gerar um objeto de cliente que pode ser utilizado para comunicar com os serviços.

### <a name="communication-clients-and-factories"></a>Clientes de comunicação e fábricas

Biblioteca de fábrica do mesmo de comunicação implementa um padrão de repetição falhas de processamento típicas que facilita a repetir a ligações para os pontos finais de serviço resolvido. A biblioteca de fábrica do mesmo fornece o mecanismo repetir, enquanto que forneça os processadores de erro.

`ICommunicationClientFactory`Define a interface base implementada por uma fábrica de cliente de comunicação que produz os clientes que podem falar para um serviço de serviço ferro. A aplicação da CommunicationClientFactory depende da pilha de comunicação utilizada pelo serviço serviço ferro onde pretende que o cliente comunicar. A API serviços fiável fornece uma `CommunicationClientFactoryBase<TCommunicationClient>`. Este procedimento fornece uma implementação base da `ICommunicationClientFactory` interface e executa tarefas que são comuns a todos os pilhas de comunicação. (Estas tarefas incluem a utilizar um `ServicePartitionResolver` para determinar o ponto final de serviço). Os clientes normalmente implementam a classe de CommunicationClientFactoryBase abstratos para processar lógica que seja específica para a pilha de comunicação.

O cliente de comunicação basta recebe um endereço e utiliza-o para ligar a um serviço. O cliente pode utilizar o protocolo que pretende.

```csharp
class MyCommunicationClient : ICommunicationClient
{
    public ResolvedServiceEndpoint Endpoint { get; set; }

    public string ListenerName { get; set; }

    public ResolvedServicePartition ResolvedServicePartition { get; set; }
}
```

A fábrica de cliente é principalmente responsável pela criação clientes de comunicação. Para clientes que não pretenda manter uma ligação persistente, tal como um cliente de HTTP, a fábrica de só precisa de criar e devolver o cliente. Outros protocolos de mantêm uma ligação persistente, tal como alguns protocolos binários, também devem ser validados pela fábrica para determinar se ou não a ligação precisa de ser recriadas.  

```csharp
public class MyCommunicationClientFactory : CommunicationClientFactoryBase<MyCommunicationClient>
{
    protected override void AbortClient(MyCommunicationClient client)
    {
    }

    protected override Task<MyCommunicationClient> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
    {
    }

    protected override bool ValidateClient(MyCommunicationClient clientChannel)
    {
    }

    protected override bool ValidateClient(string endpoint, MyCommunicationClient client)
    {
    }
}
```

Por fim, um processador de exceção é responsáveis para determinar que ação a tomar quando ocorre uma exceção. Exceções são categorizadas em **retriable** e **que não sejam retriable**. 

 - **Não retriable** exceções simplesmente novamente obtenham devolvidas novamente para o autor da chamada. 
 - Exceções **Retriable** ainda mais são categorizadas em **breves** e **não transitório**.
  - Exceções **breves** são aqueles que podem ser repetidas simplesmente sem resolver novamente o endereço de ponto final de serviço. Estes irão incluir breves problemas de rede ou respostas de erro de serviço que não seja aqueles que indicar que o endereço de ponto final de serviço não existe. 
  - Exceções **transitória não** são aqueles que requerem o endereço de ponto final de serviço para ser resolvido novamente. Estes incluem exceções que indicam que não foi possível alcançar o ponto final de serviço, indicar o serviço foi movido para um nó diferente. 

O `TryHandleException` faz com que uma decisão sobre uma determinado exceção. Se este **não sabe** o que decisões para tornar sobre uma exceção, deve devolver **Falso**. Se- **a saber** o que fazer com que a decisão,-deve definir o resultado em conformidade e devolver **Verdadeiro**.
 
```csharp
class MyExceptionHandler : IExceptionHandler
{
    public bool TryHandleException(ExceptionInformation exceptionInformation, OperationRetrySettings retrySettings, out ExceptionHandlingResult result)
    {
        // if exceptionInformation.Exception is known and is transient (can be retried without re-resolving)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, true, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;


        // if exceptionInformation.Exception is known and is not transient (indicates a new service endpoint address must be resolved)
        result = new ExceptionHandlingRetryResult(exceptionInformation.Exception, false, retrySettings, retrySettings.DefaultMaxRetryCount);
        return true;

        // if exceptionInformation.Exception is unknown (let the next IExceptionHandler attempt to handle it)
        result = null;
        return false;
    }
}
```
### <a name="putting-it-all-together"></a>Reunir tudo
Com um `ICommunicationClient`, `ICommunicationClientFactory`, e `IExceptionHandler` compilado à volta de um protocolo de comunicação, um `ServicePartitionClient` é é moldado-lo em todos os conjunto e fornece o ciclo de resolução de endereço partição falhas de processamento e service à volta destes componentes.

```csharp
private MyCommunicationClientFactory myCommunicationClientFactory;
private Uri myServiceUri;

var myServicePartitionClient = new ServicePartitionClient<MyCommunicationClient>(
    this.myCommunicationClientFactory,
    this.myServiceUri,
    myPartitionKey);

var result = await myServicePartitionClient.InvokeWithRetryAsync(async (client) =>
   {
      // Communicate with the service using the client.
   },
   CancellationToken.None);

```

## <a name="next-steps"></a>Próximos passos
 - Ver um exemplo de comunicação de HTTP entre serviços num [projeto de exemplo no GitHUb](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/WordCount).

 - [Chamadas de procedimento remoto com remoto serviços fiáveis](service-fabric-reliable-services-communication-remoting.md)

 - [Web API que utiliza OWIN nos serviços do fiável](service-fabric-reliable-services-communication-webapi.md)

 - [Comunicação WCF, utilizando os serviços fiável](service-fabric-reliable-services-communication-wcf.md)
