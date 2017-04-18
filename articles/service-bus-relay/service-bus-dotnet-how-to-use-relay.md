<properties
    pageTitle="Como utilizar o reencaminhamento de serviço Bus com .NET | Microsoft Azure"
    description="Saiba como utilizar o serviço de reencaminhamento Azure Service Bus para ligar duas aplicações alojadas em diferentes localizações."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="sethm"/>


# <a name="how-to-use-the-azure-service-bus-relay-service"></a>Como utilizar o serviço de reencaminhamento de Bus de serviço do Azure

Este artigo descreve como utilizar o serviço de reencaminhamento de Bus de serviço. Os exemplos são gravados no c# e utilizam o Windows Communication Foundation (WCF) API com extensões contidas na assemblagem Bus de serviço. Para mais informações sobre o reencaminhamento de Bus de serviço, consulte a descrição geral do [serviço Bus reencaminhado mensagens](service-bus-relay-overview.md) .

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="what-is-the-service-bus-relay"></a>O que é o reencaminhamento de Bus de serviço?

O serviço do [serviço Bus *reencaminhamento* ](service-bus-relay-overview.md) permite-lhe criar aplicações híbridas que são executados num centro de dados do Azure e no seu ambiente de empresa no local. O reencaminhamento de serviço Bus facilita a esta, permitindo-lhe para expor segura serviços do Windows Communication Foundation (WCF) que se encontram dentro de uma rede de empresa da empresa para a nuvem pública, sem ter de abrir uma ligação de firewall ou exigir alterações intrusivas à infraestrutura de rede da empresa.

![Conceitos de reencaminhamento](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

O reencaminhamento de serviço Bus permite-lhe para alojar serviços WCF dentro do ambiente existente do enterprise. Em seguida, pode delegar a escutar receção sessões e os pedidos para estes serviços WCF ao serviço do serviço Bus em execução no interior do Azure. Permite-lhe expor estes serviços para o código de aplicação em execução no Azure, ou para os trabalhadores móveis ou ambientes de parceiro da extranet. Serviço Bus permite-lhe segura controlar quem pode aceder a estes serviços num nível de extensivamente. Fornece uma maneira de poderosa e segura para expor os dados a partir do seu soluções empresariais existente e funcionalidade da aplicação e tirar partido do mesmo a partir da nuvem.

Este artigo demonstra como utilizar o reencaminhamento de serviço Bus para criar um serviço web WCF, exposto utilizando um enlace de canal TCP, que implementa uma conversação segura entre duas partes.

## <a name="create-a-service-namespace"></a>Criar um espaço de nomes de serviço

Para começar a utilizar o reencaminhamento de serviço Bus no Azure, primeiro tem de criar um espaço de nomes. Um espaço de nomes fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação.

Para criar um espaço de nomes de serviço:

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="get-the-service-bus-nuget-package"></a>Obter o pacote de serviço Bus NuGet

O [pacote de serviço Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus) é a forma mais fácil para obter a API Bus do serviço e para configurar a aplicação de todas as dependências de Bus de serviço. Para instalar o pacote de NuGet no seu projeto, faça o seguinte:

1.  No Explorador de solução, **referências**de contexto, em seguida, clique em **Gerir pacotes de NuGet**.
2.  Procurar "Serviço Bus" e selecione o item do **Microsoft Azure Service Bus** . Clique em **instalar** para concluir a instalação, em seguida, feche a caixa de diálogo seguintes:

    ![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)

## <a name="use-service-bus-to-expose-and-consume-a-soap-web-service-with-tcp"></a>Utilizar o serviço Bus para expor e consuma um serviço web SOAP com TCP

Para expor um serviço web de WCF SOAP existente para consumo externo, terá de fazer alterações para enlaces do serviço e endereços. Isto pode exigir alterações ao seu ficheiro de configuração ou poderá necessitar de alterações de código, dependendo de como ter configurar e configurado o seus serviços WCF. Tenha em atenção que WCF permite-lhe têm os pontos finais de rede vários sobre o serviço do mesmo, pelo que pode manter os pontos finais da internos existentes ao adicionar os pontos finais de Bus de serviço para o acesso externo ao mesmo tempo.

Nesta tarefa, irá criar um serviço WCF simple e adicionar-lhe uma escuta Bus de serviço. Este exercício assume alguma familiaridade com Visual Studio e, consequentemente, não percorra todos os detalhes da criação de um projeto. Em vez disso, foca-se no código.

Antes de iniciar estes passos, conclua o procedimento seguinte para configurar o seu ambiente:

1.  No Visual Studio, crie uma aplicação de consola que contém dois projetos, "Cliente" e "Serviço", a solução.
2.  Adicione o pacote do Microsoft Azure Service Bus NuGet para ambos os projetos. Este pacote adiciona todas as referências de assemblagem necessárias para os seus projetos.

### <a name="how-to-create-the-service"></a>Como criar o serviço

Primeiro, crie o próprio serviço. Qualquer serviço WCF consiste em pelo menos três partes distintas:

-   Definição de um contrato que descreve o que as mensagens são trocadas e o que são operações para ser chamado.
-   Implementação do contrato referido.
-   Anfitrião que aloja o serviço WCF e expõe vários pontos finais.

Os exemplos de código nesta secção endereço de cada um destes componentes.

O contrato define uma única operação, `AddNumbers`, que adiciona dois números e devolve o resultado. O `IProblemSolverChannel` interface permite que o cliente para gerir mais facilmente o tempo de vida de proxy. Criar uma interface como é considerada prática recomendada. É uma boa ideia para colocar esta definição de contrato para um ficheiro separado para que pode referenciar esse ficheiro a partir do seu "Cliente" e a "Serviço" projetos, mas também pode copiar o código para ambos os projetos.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Com o contrato no local, a implementação é comum.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### <a name="configure-a-service-host-programmatically"></a>Configurar um anfitrião de serviço através de programação

Com o contrato e implementação no local, agora pode alojar o serviço. Alojamento ocorre dentro de um objeto de [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx) , tratará de gestão de instâncias do serviço e aloja os pontos finais que ouvir para mensagens. O código seguinte configura o serviço com um ponto final de local regular e um ponto final de serviço Bus para ilustrar o aspeto, lado a lado, pontos finais internas e externas. Substitua o *espaço de nomes* de cadeia com o seu nome de espaço de nomes e *yourKey* a chave de SA obtido no passo anterior do programa de configuração.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "<yourKey>")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

No exemplo, crie dois pontos finais são sobre a aplicação de contrato mesmo. Uma é local e um é projetado através do serviço Bus. Principais diferenças entre as mesmas são enlaces; [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) para o um local e [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) para o ponto final de serviço Bus e os endereços. O ponto final local tem um endereço de rede local com uma porta distinto. O ponto final de serviço Bus tem um endereço de ponto final composto da cadeia `sb`, o nome do seu espaço de nomes e o caminho "solver." Isto resulta no URI `sb://[serviceNamespace].servicebus.windows.net/solver`, identificar o ponto final de serviço como um ponto final de serviço Bus TCP com um nome DNS externo completamente qualificado. Se colocar o código de substituir marcadores de posição para o `Main` função da aplicação de **serviço** , terá de um serviço funcional. Se pretender que o seu serviço para ouvir exclusivamente no serviço Bus, remova a declaração de ponto final local.

### <a name="configure-a-service-host-in-the-appconfig-file"></a>Configurar um anfitrião do serviço no ficheiro App

Também pode configurar o anfitrião do utilizar o ficheiro de App. O serviço de alojamento código neste caso, é apresentado no seguinte exemplo.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Mover as definições de ponto final para o ficheiro App. O pacote de NuGet já adicionou um intervalo de definições para o ficheiro de App, que são as extensões de configuração necessária para Bus de serviço. O exemplo seguinte, o que é o equivalente exato do código anterior, deverá aparecer diretamente abaixo do elemento **ServiceModel** . Este exemplo de código assume que o espaço de nomes do projeto c# se chamar o **serviço**.
Substitua os marcadores de posição com o seu espaço de nomes de serviço de serviço Bus e chave SA.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Após efetuar estas alterações, o serviço é iniciado como estava antes, mas com dois pontos finais direto: um local e ouvir uma na nuvem.

### <a name="create-the-client"></a>Criar o cliente

#### <a name="configure-a-client-programmatically"></a>Configurar um cliente através de programação

Consumir o serviço, pode construir um cliente WCF utilizando um objeto de [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx) . Serviço Bus utiliza um modelo de segurança baseada em tokens implementado utilizando SA. A classe de [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) representa um fornecedor de tokens de segurança com métodos de fábrica do mesmo incorporados que devolvem alguns fornecedores de tokens conhecidos. O exemplo seguinte utiliza o método de [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) para processar aquisição do token SA adequado. O nome e a chave são aqueles obtidos a partir do portal conforme descrito na secção anterior.

Em primeiro lugar, referência ou copiar a `IProblemSolver` contrato código de serviço para o seu projeto do cliente.

Em seguida, substitua o código na `Main` método do cliente, substituindo novamente o texto do marcador de posição com o seu espaço de nomes de serviço Bus e chave SA.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","<yourKey>") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Agora pode criar o cliente e o serviço, executá-las (executar o serviço pela primeira vez) e o cliente liga para o serviço e imprime **9**. Pode executar o cliente e servidor em computadores diferentes, até mesmo em redes e comunicação continuará a funcionar. Também pode executar o código do cliente na nuvem ou localmente.

#### <a name="configure-a-client-in-the-appconfig-file"></a>Configurar um cliente no ficheiro App

O código seguinte mostra como configurar o cliente de utilizar o ficheiro de App.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Mover as definições de ponto final para o ficheiro App. O exemplo seguinte, o que é o mesmo que o código listado anteriormente, deverá aparecer diretamente abaixo do elemento **ServiceModel** . Aqui, como antes, tem de substituir os marcadores de posição com o seu espaço de nomes de serviço Bus e chave SA.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="<yourKey>" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu os princípios básicos para o serviço de reencaminhamento de serviço Bus, siga estas ligações para mais informações.

- [Serviço Bus reencaminhado descrição geral de mensagens](service-bus-relay-overview.md)
- [Azure Service Bus descrição geral da arquitectura](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- Transfira amostras Bus de serviço a partir do [Azure amostras][] ou ver um [Descrição geral das amostras Bus de serviço][].

  [Shared Access Signature Authentication with Service Bus]: ../service-bus-messaging/service-bus-shared-access-signature-authentication.md
  [Amostras Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [Descrição geral das amostras do serviço Bus]: ../service-bus-messaging/service-bus-samples.md