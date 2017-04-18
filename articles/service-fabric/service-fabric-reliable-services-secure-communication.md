<properties
   pageTitle="Ajudar a comunicação segura nos serviços do serviço ferro | Microsoft Azure"
   description="Descrição geral de como para o ajudar a proteger as comunicações para os serviços fiáveis que está a executar num cluster Azure Service ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="suchiagicha"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="required"
   ms.date="07/06/2016"
   ms.author="suchiagicha"/>

# <a name="help-secure-communication-for-services-in-azure-service-fabric"></a>Comunicação segura de ajuda nos serviços do Azure Service ferro

Segurança é um dos aspectos mais importantes de comunicação. A arquitetura de aplicação de serviços fiáveis fornece alguns pilhas de comunicação pré-concebidas e ferramentas que pode utilizar para melhorar a segurança. Este artigo irá falar sobre como melhorar a segurança quando estiver a utilizar o remoto do serviço e na pilha de comunicações do Windows Communication Foundation (WCF).

## <a name="help-secure-a-service-when-youre-using-service-remoting"></a>Ajudar a proteger um serviço quando estiver a utilizar o remoto de serviço

Iremos utilizar um [exemplo](service-fabric-reliable-services-communication-remoting.md) existente que explica como configurar a comunicação remota para serviços fiáveis. Para ajudar a proteger um serviço quando estiver a utilizar o remoto do serviço, siga estes passos:

1. Criar uma interface, `IHelloWorldStateful`, que define os métodos que irão estar disponíveis para uma chamada de procedimento remoto no seu serviço. Irá utilizar o seu serviço `FabricTransportServiceRemotingListener`, que é declarado na `Microsoft.ServiceFabric.Services.Remoting.FabricTransport.Runtime` espaço de nomes. Este é um `ICommunicationListener` pós-implementação que fornece capacidades de comunicação remota.

    ```csharp
    public interface IHelloWorldStateful : IService
    {
        Task<string> GetHelloWorld();
    }

    internal class HelloWorldStateful : StatefulService, IHelloWorldStateful
    {
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]{
                    new ServiceReplicaListener(
                        (context) => new FabricTransportServiceRemotingListener(context,this))};
        }

        public Task<string> GetHelloWorld()
        {
            return Task.FromResult("Hello World!");
        }
    }
    ```

2. Adicione as definições de escuta e as credenciais de segurança.

    Certifique-se de que o certificado que pretende utilizar para ajudar a proteger a sua comunicação do serviço está instalado em todos os nós no cluster. Existem duas formas que pode fornecer as definições de escuta e as credenciais de segurança:

    1. Forneça-lhes diretamente no código do serviço:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            FabricTransportListenerSettings listenerSettings = new FabricTransportListenerSettings
            {
                MaxMessageSize = 10000000,
                SecurityCredentials = GetSecurityCredentials()
            };
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(context,this,listenerSettings))
            };
        }

        private static SecurityCredentials GetSecurityCredentials()
        {
            // Provide certificate details.
            var x509Credentials = new X509Credentials
            {
                FindType = X509FindType.FindByThumbprint,
                FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
                StoreLocation = StoreLocation.LocalMachine,
                StoreName = "My",
                ProtectionLevel = ProtectionLevel.EncryptAndSign
            };
            x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");
            return x509Credentials;
        }
        ```
    2. Forneça-lhes utilizando um [pacote de config](service-fabric-application-model.md):

        Adicionar um `TransportSettings` secção no ficheiro settings.xml.

        ```xml
        <!--Section name should always end with "TransportSettings".-->
        <!--Here we are using a prefix "HelloWorldStateful".-->
        <Section Name="HelloWorldStatefulTransportSettings">
            <Parameter Name="MaxMessageSize" Value="10000000" />
            <Parameter Name="SecurityCredentialsType" Value="X509" />
            <Parameter Name="CertificateFindType" Value="FindByThumbprint" />
            <Parameter Name="CertificateFindValue" Value="4FEF3950642138446CC364A396E1E881DB76B48C" />
            <Parameter Name="CertificateStoreLocation" Value="LocalMachine" />
            <Parameter Name="CertificateStoreName" Value="My" />
            <Parameter Name="CertificateProtectionLevel" Value="EncryptAndSign" />
            <Parameter Name="CertificateRemoteCommonNames" Value="ServiceFabric-Test-Cert" />
        </Section>
        ```

        Neste caso, a `CreateServiceReplicaListeners` método terá o seguinte aspeto:

        ```csharp
        protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
        {
            return new[]
            {
                new ServiceReplicaListener(
                    (context) => new FabricTransportServiceRemotingListener(
                        context,this,FabricTransportListenerSettings.LoadFrom("HelloWorldStateful")))
            };
        }
        ```

         Se adicionar um `TransportSettings` secção no ficheiro settings.xml sem qualquer prefixo `FabricTransportListenerSettings` irá carregar todas as definições desta secção por predefinição.

         ```xml
         <!--"TransportSettings" section without any prefix.-->
         <Section Name="TransportSettings">
             ...
         </Section>
         ```
         Neste caso, a `CreateServiceReplicaListeners` método terá o seguinte aspeto:

         ```csharp
         protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
         {
             return new[]
             {
                 return new[]{
                         new ServiceReplicaListener(
                             (context) => new FabricTransportServiceRemotingListener(context,this))};
             };
         }
         ```

3. Quando chamar métodos num serviço protegido utilizando a pilha de comunicação remota, em vez de utilizar o `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxy` escolares para criar um proxy de serviço, utilize `Microsoft.ServiceFabric.Services.Remoting.Client.ServiceProxyFactory`. Passar no `FabricTransportSettings`, que contém `SecurityCredentials`.

    ```csharp

    var x509Credentials = new X509Credentials
    {
        FindType = X509FindType.FindByThumbprint,
        FindValue = "4FEF3950642138446CC364A396E1E881DB76B48C",
        StoreLocation = StoreLocation.LocalMachine,
        StoreName = "My",
        ProtectionLevel = ProtectionLevel.EncryptAndSign
    };
    x509Credentials.RemoteCommonNames.Add("ServiceFabric-Test-Cert");

    FabricTransportSettings transportSettings = new FabricTransportSettings
    {
        SecurityCredentials = x509Credentials,
    };

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(transportSettings));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o código do cliente estiver em execução como parte de um serviço, pode carregar `FabricTransportSettings` do ficheiro settings.xml. Crie uma secção de TransportSettings é semelhante ao código de serviço, conforme mostrado anterior. Efetue as seguintes alterações para o código do cliente:

    ```csharp

    ServiceProxyFactory serviceProxyFactory = new ServiceProxyFactory(
        (c) => new FabricTransportServiceRemotingClientFactory(FabricTransportSettings.LoadFrom("TransportSettingsPrefix")));

    IHelloWorldStateful client = serviceProxyFactory.CreateServiceProxy<IHelloWorldStateful>(
        new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

    Se o cliente não está em execução como parte de um serviço, pode criar um ficheiro de client_name.settings.xml na mesma localização onde está o client_name.exe. Em seguida, crie uma secção de TransportSettings desse ficheiro.

    Semelhante ao serviço, se adicionar um `TransportSettings` secção sem qualquer prefixo no cliente settings.xml/client_name.settings.xml, `FabricTransportSettings` irá carregar todas as definições desta secção por predefinição.

    Nesse caso, o código anterior ainda mais é simplificado:  

    ```csharp

    IHelloWorldStateful client = ServiceProxy.Create<IHelloWorldStateful>(
                 new Uri("fabric:/MyApplication/MyHelloWorldService"));

    string message = await client.GetHelloWorld();

    ```

## <a name="help-secure-a-service-when-youre-using-a-wcf-based-communication-stack"></a>Ajudar a proteger um serviço quando estiver a utilizar uma pilha de comunicação com base em WCF

Iremos utilizar um [exemplo](service-fabric-reliable-services-communication-wcf.md) existente que explica como configurar uma pilha de comunicação com base em WCF para serviços fiáveis. Para ajudar a proteger um serviço quando estiver a utilizar uma pilha de comunicação com base em WCF, siga estes passos:

1. O serviço, terá de ajudar a proteger a escuta de comunicação WCF (`WcfCommunicationListener`) que criar. Para fazer isto, modificar o `CreateServiceReplicaListeners` método.

    ```csharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
        return new[]
        {
            new ServiceReplicaListener(
                this.CreateWcfCommunicationListener)
        };
    }

    private WcfCommunicationListener<ICalculator> CreateWcfCommunicationListener(StatefulServiceContext context)
    {
       var wcfCommunicationListener = new WcfCommunicationListener<ICalculator>(
            serviceContext:context,
            wcfServiceObject:this,
            // For this example, we will be using NetTcpBinding.
            listenerBinding: GetNetTcpBinding(),
            endpointResourceName:"WcfServiceEndpoint");

        // Add certificate details in the ServiceHost credentials.
        wcfCommunicationListener.ServiceHost.Credentials.ServiceCertificate.SetCertificate(
            StoreLocation.LocalMachine,
            StoreName.My,
            X509FindType.FindByThumbprint,
            "9DC906B169DC4FAFFD1697AC781E806790749D2F");
        return wcfCommunicationListener;
    }

    private static NetTcpBinding GetNetTcpBinding()
    {
        NetTcpBinding b = new NetTcpBinding(SecurityMode.TransportWithMessageCredential);
        b.Security.Message.ClientCredentialType = MessageCredentialType.Certificate;
        return b;
    }
    ```

2. No cliente, o `WcfCommunicationClient` permanecem inalterados escolares que criou no [exemplo](service-fabric-reliable-services-communication-wcf.md) anterior. Mas é necessário substituir o `CreateClientAsync` método de `WcfCommunicationClientFactory`:

    ```csharp
    public class SecureWcfCommunicationClientFactory<TServiceContract> : WcfCommunicationClientFactory<TServiceContract> where TServiceContract : class
    {
        private readonly Binding clientBinding;
        private readonly object callbackObject;
        public SecureWcfCommunicationClientFactory(
            Binding clientBinding,
            IEnumerable<IExceptionHandler> exceptionHandlers = null,
            IServicePartitionResolver servicePartitionResolver = null,
            string traceId = null,
            object callback = null)
            : base(clientBinding, exceptionHandlers, servicePartitionResolver,traceId,callback)
        {
            this.clientBinding = clientBinding;
            this.callbackObject = callback;
        }

        protected override Task<WcfCommunicationClient<TServiceContract>> CreateClientAsync(string endpoint, CancellationToken cancellationToken)
        {
            var endpointAddress = new EndpointAddress(new Uri(endpoint));
            ChannelFactory<TServiceContract> channelFactory;
            if (this.callbackObject != null)
            {
                channelFactory = new DuplexChannelFactory<TServiceContract>(
                this.callbackObject,
                this.clientBinding,
                endpointAddress);
            }
            else
            {
                channelFactory = new ChannelFactory<TServiceContract>(this.clientBinding, endpointAddress);
            }
            // Add certificate details to the ChannelFactory credentials.
            // These credentials will be used by the clients created by
            // SecureWcfCommunicationClientFactory.  
            channelFactory.Credentials.ClientCertificate.SetCertificate(
                StoreLocation.LocalMachine,
                StoreName.My,
                X509FindType.FindByThumbprint,
                "9DC906B169DC4FAFFD1697AC781E806790749D2F");
            var channel = channelFactory.CreateChannel();
            var clientChannel = ((IClientChannel)channel);
            clientChannel.OperationTimeout = this.clientBinding.ReceiveTimeout;
            return Task.FromResult(this.CreateWcfCommunicationClient(channel));
        }
    }
    ```

    Utilizar `SecureWcfCommunicationClientFactory` para criar um cliente de comunicação WCF (`WcfCommunicationClient`). Utilize o cliente para invocar métodos de serviço.

    ```csharp
    IServicePartitionResolver partitionResolver = ServicePartitionResolver.GetDefault();

    var wcfClientFactory = new SecureWcfCommunicationClientFactory<ICalculator>(clientBinding: GetNetTcpBinding(), servicePartitionResolver: partitionResolver);

    var calculatorServiceCommunicationClient =  new WcfCommunicationClient(
        wcfClientFactory,
        ServiceUri,
        ServicePartitionKey.Singleton);

    var result = calculatorServiceCommunicationClient.InvokeWithRetryAsync(
        client => client.Channel.Add(2, 3)).Result;
    ```

## <a name="next-steps"></a>Próximos passos

* [Web API com OWIN nos serviços do fiáveis](service-fabric-reliable-services-communication-webapi.md)
