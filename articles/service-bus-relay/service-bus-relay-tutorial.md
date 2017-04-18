<properties 
    pageTitle="Tutorial do serviço Bus reencaminhamento | Microsoft Azure"
    description="Crie um cliente de serviço Bus aplicação e serviço utilizando o serviço Bus reencaminhamento."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-tutorial"></a>Tutorial do serviço Bus reencaminhamento

Neste tutorial descreve como criar uma aplicação de cliente de serviço Bus simple e o serviço utilizando as capacidades de "reencaminhamento" Bus de serviço. Para obter um tutorial correspondente que utiliza Bus serviço [juntamente mensagens](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consulte o artigo o [Serviço Bus controlada mensagens .NET Tutorial](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Trabalho através deste tutorial dá-lhe uma compreensão dos passos que são necessários para criar uma aplicação de cliente e o serviço do serviço Bus. Como os respetivos homólogos WCF, um serviço é uma construção expõe pontos finais de uma ou mais, cada um dos quais expõe uma ou mais operações de serviço. O ponto final de um serviço Especifica um endereço onde o serviço pode ser encontrado, um enlace que contém as informações que um cliente tem de comunicar com o serviço e um contrato que define a funcionalidade fornecida pelo serviço aos seus clientes. A diferença entre um WCF e um serviço de serviço Bus principal é que o ponto final é apresentado quando se clica na nuvem em vez de localmente no seu computador.

Depois de trabalhar através de sequência de tópicos neste tutorial, terá um serviço em execução e um cliente que pode invocar operações do serviço. O primeiro tópico descreve como configurar uma conta. Os passos seguintes descrevem como definir um serviço que utiliza um contrato, como implementar o serviço e como configurar o serviço no código. Estes também descrevem como alojar e executar o serviço. O serviço que é criado está alojado personalizada e o serviço de cliente e executar no mesmo computador. Pode configurar o serviço utilizando código ou um ficheiro de configuração.

Os três passos finais descrevem como criar uma aplicação de cliente, configure a aplicação de cliente e criar e utilizam um cliente que pode aceder à funcionalidade do anfitrião.

Todos os tópicos nesta secção partem do princípio de que está a utilizar o Visual Studio como o ambiente de desenvolvimento.

## <a name="sign-up-for-an-account"></a>Inscrever-se para uma conta

O primeiro passo é para criar um espaço de nomes e para obter uma chave de assinatura partilhadas do Access (SA). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através de Bus de serviço. Uma chave de SA é gerada automaticamente pelo sistema quando é criado um espaço de nomes de serviço. A combinação de espaço de nomes de serviço e chave SA fornece as credenciais para Bus de serviço para autenticar o acesso a uma aplicação.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="define-a-wcf-service-contract-to-use-with-service-bus"></a>Definir um contrato de serviço WCF para utilizar com o serviço Bus

O contrato de serviço Especifica que operações o serviço suporta a (a terminologia de serviços Web para métodos ou funções). Há contratos que é criados através da definição de uma interface C++, c# ou Visual Basic. Cada método na interface de corresponde a uma operação de serviço específico. Cada interface tem de ter o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) que lhe foi aplicado e cada operação tem de ter o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) aplicado ao mesmo. Se um método de uma interface que tenha o atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) , esse método não é apresentado. O código para estas tarefas é fornecido no exemplo siga o procedimento descrito. Para um debate maior de contratos e serviços, consulte o artigo [Estruturar e implementar a serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação WCF.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para criar um contrato de serviço Bus com uma interface

1. Abrir o Visual Studio como administrador ao clicar o programa no menu **Iniciar** e selecionar **Executar como administrador**.

2. Crie um novo projeto de aplicação de consola. Clique no menu **ficheiro** e selecione **Novo**e, em seguida, clique em **projeto**. Na caixa de diálogo **Novo projeto** , clique em **Visual c#** (se **Visual c#** não aparecer, procure em **Idiomas de outros**). Clique no modelo de **Aplicação de consola** e o nome **EchoService**. Clique em **OK** para criar o projeto.

    ![][2]

3. Instale o pacote de serviço Bus NuGet. Este pacote adiciona automaticamente as referências às bibliotecas do serviço Bus, bem como o WCF **ServiceModel**. [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o espaço de nomes que lhe permita aceder através de programação as funcionalidades básicas de WCF. Serviço Bus utiliza muitos dos objetos e atributos de WCF para definir contratos de serviço.

    No Explorador de solução, a solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet para solução**. Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus`. Certifique-se de que o nome do projeto está seleccionado na caixa de **versões** . Clique em **instalar**e aceite os termos de utilização.

    ![][3]

3. No Explorador de solução, faça duplo clique no ficheiro de Program.cs para abri-lo no editor, se ainda não estiver aberta.

4. Adicione o seguinte utilizando as instruções na parte superior do ficheiro:

    ```
    using System.ServiceModel;
    using Microsoft.ServiceBus;
    ```

1. Altere o nome do espaço de nomes a partir do seu nome de **EchoService** predefinido para **Microsoft.ServiceBus.Samples**.

    >[AZURE.IMPORTANT] Neste tutorial, utiliza c# espaço de nomes **Microsoft.ServiceBus.Samples**, que é o espaço de nomes do tipo de contrato gerido que é utilizado no ficheiro de configuração no passo [configurar o cliente WCF](#configure-the-wcf-client) . Pode especificar qualquer espaço de nomes que pretende quando constrói este amostra; No entanto, o tutorial não funcionar, a menos que, em seguida, modificar os espaços de nomes do contrato e, por conseguinte, do serviço no ficheiro de configuração da aplicação. O espaço de nomes especificado no ficheiro de App tem de ser o mesmo que o espaço de nomes especificado nos seus ficheiros c#.

1. Imediatamente a seguir a `Microsoft.ServiceBus.Samples` declaração de espaço de nomes, mas no espaço de nomes, definir uma nova interface denominada `IEchoContract` e aplicar a `ServiceContractAttribute` atributo para a interface com um valor de espaço de nomes de **http://samples.microsoft.com/ServiceModel/Relay/**. O valor de espaço de nomes difere do espaço de nomes que utilizar em todo o âmbito do código. Em vez disso, espaço de nomes é utilizado o valor como um identificador exclusivo para este contrato. Especificar o espaço de nomes explicitamente impede que o valor de espaço de nomes predefinido a ser adicionado ao nome do contrato.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
    }
    ```

    >[AZURE.NOTE] Normalmente, o espaço de nomes do contrato de serviço contém um esquema de nomenclatura que inclui informações sobre a versão. Incluindo informações sobre a versão do espaço de nomes do contrato de serviço permite que os serviços identificar principais alterações ao definir um novo contrato com um novo espaço de nomes e expor-lo num novo ponto final. Desta forma, clientes podem continuar a utilizar o contrato de serviço antigo sem ter de ser atualizados. Informações sobre a versão pode ter uma data ou um número de compilação. Para obter mais informações, consulte [Serviço de controlo de versões](http://go.microsoft.com/fwlink/?LinkID=180498). Para efeitos neste tutorial, o esquema de nomes do espaço de nomes de contrato de serviço não contém informações sobre a versão.

1. Dentro do `IEchoContract` interface, declarar um método para a operação única a `IEchoContract` contrato expõe na interface e aplicar a `OperationContractAttribute` atributo para o método que pretende expor como parte do contrato de serviço Bus público.

    ```
    [OperationContract]
    string Echo(string text);
    ```

1. Imediatamente a seguir a `IEchoContract` interface definição, declarar um canal que ambos herda `IEchoContract` e obtenha o também a `IClientChannel` interface, como é mostrado aqui:

    ```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

    Um canal é o objeto WCF através do qual o anfitrião e o cliente passam informações umas às outras. Mais tarde, irá escrever código contra o canal para aplicar o eco informações entre as duas aplicações.

1. No menu de **Criar** , clique em **Criar solução** ou prima **Ctrl + Shift + B** para confirmar a precisão do seu trabalho até ao momento.

### <a name="example"></a>Exemplo

O código seguinte mostra uma interface básica que define um contrato de serviço Bus.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Agora que a interface é criada, pode implementar a interface.

## <a name="implement-the-wcf-contract-to-use-service-bus"></a>Implementar o contrato WCF para utilizar o serviço Bus

Criação de uma retransmissão de serviço Bus requer que cria o contrato, o que é definido utilizando uma interface pela primeira vez. Para obter mais informações sobre como criar a interface, consulte o passo anterior. O passo seguinte é implementar a interface. Isto envolve a criação de uma classe denominada `EchoService` que implementa definidas pelo utilizador `IEchoContract` interface. Depois de implementar a interface de, em seguida, configure a interface utilizando um ficheiro de configuração da App. O ficheiro de configuração contém as informações necessárias para a aplicação, como o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o serviço Bus. O código utilizado para estas tarefas é fornecido no exemplo siga o procedimento descrito. Para um debate mais geral sobre como implementar um contrato de serviço, consulte o artigo [Implementar contratos](https://msdn.microsoft.com/library/ms733764.aspx) na documentação WCF.

1. Criar uma nova com o nome de classe `EchoService` imediatamente a seguir a definição da `IEchoContract` interface. O `EchoService` classe implementa o `IEchoContract` interface. 

    ```
    class EchoService : IEchoContract
    {
    }
    ```
    
    Semelhantes às outras implementações interface, pode implementar a definição num ficheiro diferente. No entanto, para este tutorial, a aplicação está localizada no mesmo ficheiro como a definição de interface e o `Main` método.

1. Aplicar o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) para o `IEchoContract` interface. O atributo especifica o nome do serviço e o espaço de nomes. Depois de fazê-lo, o `EchoService` classe é apresentada da seguinte forma:

    ```
    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
    }
    ```

1. Implementar a `Echo` método definido na `IEchoContract` interface na `EchoService` escolares. 

    ```
    public string Echo(string text)
    {
        Console.WriteLine("Echoing: {0}", text);
        return text;
    }
    ```

1. Clique em **Criar**e, em seguida, clique em **Criar solução** para confirmar a precisão do seu trabalho.

### <a name="to-define-the-configuration-for-the-service-host"></a>Para definir a configuração para o anfitrião do serviço

1. O ficheiro de configuração é muito semelhante a um ficheiro de configuração de WCF. Incluir o nome do serviço, ponto final (ou seja, a localização serviço Bus expõe para clientes e anfitriões comunicar com os outros) e o enlace (o tipo do protocolo que é utilizado para comunicar). A diferença principal é que este ponto final de serviço configuradas que se refere a um enlace [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) , que não faz parte do .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) é um dos enlaces definidos pelo serviço Bus.

1. No **Explorador de soluções**, faça duplo clique no ficheiro de App para abri-lo no editor do Visual Studio.

2. No `<appSettings>` elemento, substituir os marcadores de posição com o nome do seu espaço de nomes de serviço e as associações de segurança de chave que copiou num passo anterior. 

1. Dentro de `<system.serviceModel>` etiquetas, adicionar um `<services>` elemento. Pode definir várias aplicações de serviço Bus num ficheiro de configuração de único. No entanto, este tutorial define apenas uma.
 
    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <services>

        </services>
      </system.serviceModel>
    </configuration>
    ```

1. Dentro de `<services>` elemento, adicionar um `<service>` elemento para definir o nome do serviço.

    ```
    <service name="Microsoft.ServiceBus.Samples.EchoService">
    </service>
    ```

1. Dentro de `<service>` elemento, definir a localização do contrato de ponto final e o tipo de encadernação para o ponto final.

    ```
    <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
    ```

    O ponto final define onde o cliente irá procurar a aplicação do anfitrião. Mais tarde, o tutorial utiliza este passo para criar um URI que expõe totalmente o anfitrião através do serviço Bus. O enlace declara que recomendamos está a utilizar TCP como o protocolo para comunicar com Bus de serviço.

1. No menu de **Criar** , clique em **Criar solução** para confirmar a precisão do seu trabalho.

### <a name="example"></a>Exemplo

O código seguinte mostra a execução do contrato de serviço.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

O código seguinte mostra o formato básico do ficheiro App associado com o anfitrião do serviço.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="host-and-run-a-basic-web-service-to-register-with-service-bus"></a>Alojar e executar um serviço Web básico para registar com Bus de serviço

Este passo descreve como executar um serviço de serviço Bus básico.

### <a name="to-create-the-service-bus-credentials"></a>Para criar as credenciais de serviço Bus

1. No `Main()`, criar duas variáveis na qual pretende armazenar o espaço de nomes e as associações de segurança de chave que são lidos a partir da janela de consola.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS key: ");
    string sasKey = Console.ReadLine();
    ```

    Mais tarde, a tecla SA será utilizada para aceder ao seu projeto Bus de serviço. O espaço de nomes é transmitido como um parâmetro para `CreateServiceUri` para criar um serviço URI.

4. Utilizar um objeto de [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx) , declare que vai estar a utilizar uma chave de SA como o tipo de credenciais. Adicione o seguinte código diretamente a seguir ao código adicionado no último passo.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço

1. Seguir o código que adicionou no último passo, criar uma `Uri` instância para o endereço base do serviço. Este URI Especifica o esquema de Bus de serviço, o espaço de nomes e o caminho da interface de serviço.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

    "sb" é uma abreviatura para o esquema de serviço Bus e indica que estamos a utilizar TCP como o protocolo. Isto também anteriormente foi indicado no ficheiro de configuração, quando [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) foi especificada como o enlace.
    
    Neste tutorial, o URI é `sb://putServiceNamespaceHere.windows.net/EchoService`.

### <a name="to-create-and-configure-the-service-host"></a>Para criar e configurar o anfitrião do serviço

1. Defina o modo de conectividade para `AutoDetect`.

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

    O modo de conectividade descreve o protocolo o serviço utiliza para comunicar com o serviço Bus; HTTP ou TCP. Utilizar a predefinição `AutoDetect`, o serviço tenta ligar ao serviço Bus ao longo do TCP se estiver disponível e HTTP se TCP não estiver disponível. Especifica que difere o protocolo o serviço de nota para a comunicação de cliente. Esse protocolo é determinado pela enlace utilizado. Por exemplo, um serviço pode utilizar o enlace [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx) , que especifica que respectivo ponto final (apresentado quando se clica no serviço Bus) comunica com clientes através de HTTP. Desse serviço mesmo poderia especificar **ConnectivityMode.AutoDetect** para que o serviço comunica com o serviço Bus ao longo do TCP.

1. Criar o anfitrião do serviço, utilizar o URI criada anteriormente nesta secção.

    ```
    ServiceHost host = new ServiceHost(typeof(EchoService), address);
    ```

    O anfitrião do serviço é o objeto WCF iniciar instância o serviço. Aqui, ser efetuada com-o tipo de serviço que pretende criar (uma `EchoService` tipo) e também para o endereço que pretende que para expor o serviço.

1. Na parte superior do ficheiro Program.cs, adicione as referências às [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) e [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

    ```
    using System.ServiceModel.Description;
    using Microsoft.ServiceBus.Description;
    ```

1. Novamente na `Main()`, configure o ponto final para activar o acesso do público.

    ```
    IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
    ```

    Este passo informa Bus de serviço que a aplicação pode ser encontrada publicamente examinar a ATOM Bus serviço feed para o seu projeto. Se definir **DiscoveryType** para **privado**, um cliente seria ainda conseguir aceder ao serviço. No entanto, o serviço seria não será apresentada quando procura o espaço de nomes de serviço Bus. Em vez disso, o cliente teria de conhecer o caminho de ponto final prévia.

1. Aplicam-se as credenciais do serviço para os pontos finais de serviço definidos no ficheiro App:

    ```
    foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
    {
        endpoint.Behaviors.Add(serviceRegistrySettings);
        endpoint.Behaviors.Add(sasCredential);
    }
    ```

    Tal como indicado no passo anterior, poderia ter declarado vários serviços e os pontos finais no ficheiro de configuração. Se tinha, este código seria percorrer o ficheiro de configuração e a pesquisa para cada ponto final para o qual-deve ser aplicada as suas credenciais. No entanto, neste tutorial, o ficheiro de configuração tem apenas um ponto final.

### <a name="to-open-the-service-host"></a>Para abrir o anfitrião do serviço

1. Abra o serviço.

    ```
    host.Open();
    ```

1. Informe o utilizador que o serviço está a ser executado e explicam como encerrar o serviço.

    ```
    Console.WriteLine("Service address: " + address);
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

1. Quando tiver terminado, feche o anfitrião do serviço.

    ```
    host.Close();
    ```

1. Prima **Ctrl + Shift + B** para construir o projeto.

### <a name="example"></a>Exemplo

O exemplo seguinte inclui o contrato de serviço e a aplicação a partir de passos anteriores no tutorial e aloja o serviço numa aplicação de consola. Compile o seguinte para um ficheiro executável denominado EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## <a name="create-a-wcf-client-for-the-service-contract"></a>Criar um cliente WCF para o contrato de serviço

O passo seguinte é criar uma aplicação de cliente do serviço Bus básica e definir o contrato de serviços que irá implementar nos passos posteriores. Nota que muitos destes passos que se pareça com os passos utilizados para criar um serviço: que define um contrato, um App de edição de ficheiros, com as credenciais para ligar ao serviço Bus e assim sucessivamente. O código utilizado para estas tarefas é fornecido no exemplo siga o procedimento descrito.

1. Crie um novo projeto a solução do Visual Studio atual para o cliente ao efetuar o seguinte:
    1. No Explorador de solução, na mesma solução que contém o serviço, com o botão direito a solução atual (e não o projecto) e clique em **Adicionar**. Em seguida, clique em **Novo projeto**.
    2. Na caixa de diálogo **Adicionar novo projeto** , clique em **Visual c#** (se **Visual c#** não aparecer, procure em **Idiomas de outras**), selecione o modelo de **Aplicação de consola** e o nome **EchoClient**.
    3. Clique em **OK**.
<br />

1. No Explorador de solução, duplo clique no ficheiro de Program.cs no projeto **EchoClient** para o abrir no editor, se ainda não estiver aberta.

1. Alterar o nome do espaço de nomes a partir do seu nome de grupo predefinido `EchoClient` para `Microsoft.ServiceBus.Samples`.

1. Instale o [pacote de serviço Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus). No Explorador de solução, com o botão direito do projecto **EchoClient** e, em seguida, clique em **Gerir pacotes de NuGet**. Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de utilização.

    ![][3]

1. Adicionar um `using` declaração do espaço de nomes [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) no ficheiro Program.cs. 

    ```
    using System.ServiceModel;
    ```

1. Adicione a definição do contrato de serviço para o espaço de nomes, conforme mostrado no seguinte exemplo. Note que esta definição é idêntica à definição utilizada no projeto **serviço** . Deve adicionar este código na parte superior da `Microsoft.ServiceBus.Samples` espaço de nomes.

    ```
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }
    ```

1. Prima **Ctrl + Shift + B** para construir o cliente.

### <a name="example"></a>Exemplo

O código seguinte mostra o estado atual do ficheiro Program.cs no projeto EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="configure-the-wcf-client"></a>Configurar o cliente WCF

Neste passo, crie um ficheiro de App para uma aplicação de cliente básico que acede ao serviço criado anteriormente neste tutorial. Este ficheiro App define o contrato, o encadernação e o nome do ponto final. O código utilizado para estas tarefas é fornecido no exemplo siga o procedimento descrito.

1. No Explorador de solução, no **EchoClient** project, faça duplo clique **App** para abrir o ficheiro no editor do Visual Studio.

2. No `<appSettings>` elemento, substituir os marcadores de posição com o nome do seu espaço de nomes de serviço e as associações de segurança de chave que copiou num passo anterior.

1. Dentro do elemento ServiceModel, adicionar um `<client>` elemento.

    ```
    <?xmlversion="1.0"encoding="utf-8"?>
    <configuration>
      <system.serviceModel>
        <client>
        </client>
      </system.serviceModel>
    </configuration>
    ```

    Este passo declara que estiver a definir uma aplicação de cliente WCF de estilo.

1. Dentro de `client` elemento, defina o nome, contrato e tipo de ligação para o ponto final.

    ```
    <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IEchoContract"
                    binding="netTcpRelayBinding"/>
    ```

    Este passo define o nome do ponto final, o contrato definido no serviço e o facto de que a aplicação de cliente utiliza TCP para comunicar com Bus de serviço. O nome do ponto final é utilizado no próximo passo para criar uma ligação esta configuração de ponto final com o serviço de URI.

1. Clique em **ficheiro**, em seguida, **Guardar tudo**.

## <a name="example"></a>Exemplo

O código seguinte mostra o ficheiro App para o cliente eco.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## <a name="implement-the-wcf-client-to-call-service-bus"></a>Implementar o cliente WCF para chamadas Bus de serviço

Neste passo, implementar uma aplicação de cliente básico que acede ao serviço que criou anteriormente neste tutorial. Semelhante ao serviço, o cliente executa muitas das mesmas operações para aceder ao serviço Bus:

1. Define o modo de conectividade.
1. Cria o URI que localiza o serviço anfitrião.
1. Define as credenciais de segurança.
1. Aplica-se as credenciais para a ligação.
1. Abre-se a ligação.
1. Executa as tarefas específicas da aplicação.
1. Fecha a ligação.

No entanto, uma das principais diferenças é que a aplicação de cliente utiliza um canal para ligar ao serviço Bus, Considerando que o serviço utiliza uma chamada para **ServiceHost**. O código utilizado para estas tarefas é fornecido no exemplo siga o procedimento descrito.

### <a name="to-implement-a-client-application"></a>Para implementar uma aplicação de cliente

1. Defina o modo de conectividade a **deteção automática**. Adicione o seguinte código dentro de `Main()` método da aplicação **EchoClient** .

    ```
    ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
    ```

1. Defina variáveis para os valores para o espaço de nomes de serviço e a chave de SA que são lidos a partir da consola colocar em espera.

    ```
    Console.Write("Your Service Namespace: ");
    string serviceNamespace = Console.ReadLine();
    Console.Write("Your SAS Key: ");
    string sasKey = Console.ReadLine();
    ```

1. Crie o URI que define a localização de anfitrião do no seu projeto Bus de serviço.

    ```
    Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
    ```

1. Crie o objeto de credenciais para o ponto final de espaço de nomes de serviço.

    ```
    TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
    sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
    ```

1. Crie a fábrica de canal carrega a configuração descrita no ficheiro App.

    ```
    ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
    ```

    Uma fábrica de canal é um objeto WCF que cria um canal através da qual comunicam aplicações de serviço e de cliente.

1. Aplica as credenciais de Bus de serviço.

    ```
    channelFactory.Endpoint.Behaviors.Add(sasCredential);
    ```

1. Crie e abra o canal para o serviço.

    ```
    IEchoChannel channel = channelFactory.CreateChannel();
    channel.Open();
    ```

1. Escreva a interface de utilizador básica e a funcionalidade para o eco.

    ```
    Console.WriteLine("Enter text to echo (or [Enter] to exit):");
    string input = Console.ReadLine();
    while (input != String.Empty)
    {
        try
        {
            Console.WriteLine("Server echoed: {0}", channel.Echo(input));
        }
        catch (Exception e)
        {
            Console.WriteLine("Error: " + e.Message);
        }
        input = Console.ReadLine();
    }
    ```

    Repare que o código utiliza a instância do objeto canal como um proxy do serviço.

1. Feche o canal e, feche a fábrica de.

    ```
    channel.Close();
    channelFactory.Close();
    ```

## <a name="to-run-the-applications"></a>Para executar as aplicações

1. Prima **Ctrl + Shift + B** para criar a solução. Isso cria o projeto de cliente e o projeto de serviço que criou nos passos anteriores.

2. Antes de executar a aplicação de cliente, tem de se certificar de que a aplicação de serviço está em execução. No Explorador de solução no Visual Studio, com o botão direito a solução **EchoService** , em seguida, clique em **Propriedades**.

3. Na caixa de diálogo de propriedades de solução, clique em **Projeto de arranque**, em seguida, clique no botão de **vários projetos de arranque** . Certifique-se de **que echoservice** aparece em primeiro lugar, na lista. 

4. Defina a caixa de **ação** para a **EchoService** e o **EchoClient** projetos para **Iniciar**.

    ![][5]

5. Clique em **dependências de projeto**. Na caixa de **projetos** , selecione **EchoClient**. Na caixa **depende da** , certifique-se de **que echoservice** está selecionada.

    ![][6]

6. Clique em **OK** para fechar a caixa de diálogo **Propriedades** .

7. Prima **F5** para executar ambos os projetos.

8. Ambas as janelas de consola abra e pedir-lhe o nome do espaço de nomes. O serviço tem de ser executado primeiro, por isso, na janela da consola **EchoService** , introduza o espaço de nomes e, em seguida, prima **Enter**.

9. Em seguida, lhe for pedido para a sua chave de SA. Introduza a chave de SA e prima ENTER.

    Eis a saída de exemplo a partir da janela de consola. Tenha em atenção que os valores fornecidos seguem-se por exemplo efeitos apenas.

    `Your Service Namespace: myNamespace`
    `Your SAS Key: <SAS key value>`

    A aplicação de serviço imprime na janela da consola do endereço no qual ouvir, conforme visto no seguinte exemplo.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/`
    `Press [Enter] to exit`
    
10. Na janela da consola **EchoClient** , introduza as mesmas informações que introduziu anteriormente para a aplicação de serviço. Siga os passos anteriores para introduzir o mesmo espaço de nomes de serviço e valores chave SA para a aplicação de cliente.

11. Depois de introduzir estes valores, o cliente é aberto um canal para o serviço e solicita-lhe que introduza algum texto conforme visto no seguinte exemplo de saída da consola.

    `Enter text to echo (or [Enter] to exit):` 

    Introduza algum texto enviar para a aplicação de serviço e prima Enter. Este texto é enviado para o serviço através da operação de serviço eco e é apresentada na janela de consola do serviço, tal como a saída de exemplo seguinte.

    `Echoing: My sample text`

    A aplicação de cliente recebe o valor de retorno da `Echo` operação, que é o texto original e imprime-lo à sua janela consola. Segue-se saída de exemplo a partir da janela de consola do cliente.

    `Server echoed: My sample text`

12. Pode continuar a enviar mensagens de texto a partir do cliente para o serviço desta maneira. Quando terminar, prima Enter nas janelas de consola de cliente e do serviço para terminar ambas as aplicações.

## <a name="example"></a>Exemplo

O exemplo seguinte mostra como criar uma aplicação de cliente, como as operações do serviço de chamadas e como fechar o cliente depois de terminar a chamada de operação.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## <a name="next-steps"></a>Próximos passos

Neste tutorial mostrava como construir um cliente de serviço Bus aplicação e serviço utilizando as capacidades de "reencaminhamento" Bus de serviço. Para obter um tutorial semelhante que utiliza Bus de serviço de [mensagens](../service-bus-messaging/service-bus-messaging-overview.md#Brokered-messaging), consulte o artigo o [Serviço Bus controlada mensagens .NET Tutorial](../service-bus-messaging/service-bus-brokered-tutorial-dotnet.md).

Para saber mais sobre Bus de serviço, consulte os tópicos seguintes.

- [Descrição geral do serviço Bus messaging](../service-bus-messaging/service-bus-messaging-overview.md)
- [Conceitos básicos da Bus de serviço](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md)
- [Arquitetura de Bus de serviço](../service-bus-messaging/service-bus-architecture.md)

[Azure classic portal]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png
