<properties
    pageTitle="Utilizar o serviço Bus resto tutorial reencaminhado mensagens | Microsoft Azure"
    description="Crie uma aplicação de anfitrião reencaminhamento simple serviço Bus expõe uma interface baseada em restantes."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="09/27/2016"
    ms.author="sethm" />

# <a name="service-bus-relay-rest-tutorial"></a>Tutorial do resto do serviço Bus reencaminhamento

Neste tutorial descreve como criar uma aplicação de anfitrião de serviço Bus simple que expõe uma interface baseada em restantes. RESTO permite um cliente da web, tal como um browser aceder a APIs Bus serviço através de pedidos de HTTP.

Neste tutorial utiliza os restantes Windows Communication Foundation (WCF) modelo de programação para construir um serviço de resto no serviço Bus. Para mais informações, consulte o artigo [WCF resto de programação modelo](https://msdn.microsoft.com/library/bb412169.aspx) e [Estruturar e implementar a serviços](https://msdn.microsoft.com/library/ms729746.aspx) na documentação WCF.

## <a name="step-1-create-a-service-namespace"></a>Passo 1: Criar um espaço de nomes de serviço

O primeiro passo é para criar um espaço de nomes e para obter uma chave de assinatura partilhadas do Access (SA). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através de Bus de serviço. Uma chave de SA é gerada automaticamente pelo sistema quando é criado um espaço de nomes de serviço. A combinação de espaço de nomes de serviço e chave SA fornece as credenciais para Bus de serviço para autenticar o acesso a uma aplicação.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="step-2-define-a-rest-based-wcf-service-contract-to-use-with-service-bus"></a>Passo 2: Defina um contrato de serviço WCF com base no resto para utilizar com o serviço Bus

Como com outros serviços de serviço Bus, quando cria um serviço de estilo resto, tem de definir o contrato. O contrato especifica que operações de anfitrião do suporta. Uma operação de serviço pode ser considerada como um método de serviço web. Há contratos que é criados através da definição de uma interface C++, c# ou Visual Basic. Cada método na interface de corresponde a uma operação de serviço específico. O atributo [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) terá de ser aplicado a cada interface e o atributo [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) terá de ser aplicado a cada operação. Se um método de uma interface que tem o [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) não tiver o [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), esse método não é apresentado. O código utilizado para estas tarefas é apresentado no exemplo siga o procedimento descrito.

A principal diferença entre um contrato de serviço Bus básico e um contrato resto estilo é a adição de uma propriedade para o [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx): [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx). Esta propriedade permite-lhe mapear um método na sua interface para um método no outro lado da interface. Neste caso, utilizamos [WebGetAttribute](https://msdn.microsoft.com/library/system.servicemodel.web.webgetattribute.aspx) para ligar um método de HTTP GET. Esta opção permite-Bus de serviço para com exatidão obter e interpretar enviados para a interface de comandos.

### <a name="to-create-a-service-bus-contract-with-an-interface"></a>Para criar um contrato de serviço Bus com uma interface

1. Abrir o Visual Studio como administrador: o programa no menu **Iniciar** com o botão direito e, em seguida, clique em **Executar como administrador**.

2. Crie um novo projeto de aplicação de consola. Clique no menu **ficheiro** e selecione **Novo**e, em seguida, selecione **projeto**. Na caixa de diálogo **Novo projeto** , clique em **Visual c#**, selecione o modelo de **Aplicação de consola** e o nome **ImageListener**. Utilize a predefinição de **localização**. Clique em **OK** para criar o projeto.

3. Para c# projeto, Visual Studio cria uma `Program.cs` ficheiro. Esta classe contém um esvaziar `Main()` método, necessário para um projecto de aplicação de consola para construir corretamente.

4. Adicione as referências às Bus de serviço e **System.ServiceModel.dll** ao projeto ao instalar o pacote de serviço Bus NuGet. Este pacote adiciona automaticamente as referências às bibliotecas do serviço Bus, bem como o WCF **ServiceModel**. No Explorador de solução, com o botão direito do projecto **ImageListener** e, em seguida, clique em **Gerir pacotes de NuGet**. Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de utilização.

5. Uma referência a **System.ServiceModel.Web.dll** explicitamente tem de adicionar ao projeto:

    um. No Explorador de solução, com o botão direito na pasta de **referências** na pasta de projeto e, em seguida, clique em **Adicionar referência**.

    b. Na caixa de diálogo **Adicionar referência** , clique no separador **estrutura** , no lado esquerdo e na caixa de **pesquisa** , escreva **System.ServiceModel.Web**. Selecione a caixa de verificação **System.ServiceModel.Web** , em seguida, clique em **OK**.

6. Adicione o seguinte `using` declarações na parte superior do ficheiro Program.cs.

    ```
    using System.ServiceModel;
    using System.ServiceModel.Channels;
    using System.ServiceModel.Web;
    using System.IO;
    ```

    [ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) é o espaço de nomes que permite o acesso de programação para funcionalidades básicas de WCF. Serviço Bus utiliza muitos dos objetos e atributos de WCF para definir contratos de serviço. Irá utilizar este espaço de nomes na maior parte das suas aplicações do serviço Bus reencaminhamento. Da mesma forma, [System.ServiceModel.Channels](https://msdn.microsoft.com/library/system.servicemodel.channels.aspx) ajuda-o a definir o canal, que é o objeto através da qual comunicar com o serviço Bus e o browser do cliente. Por fim, [System.ServiceModel.Web](https://msdn.microsoft.com/library/system.servicemodel.web.aspx) contém os tipos de que permitem-lhe criar aplicações baseadas na web.

7. Mudar o nome a `ImageListener` espaço de nomes para **Microsoft.ServiceBus.Samples**.

    ```
    namespace Microsoft.ServiceBus.Samples
    {
        ...
    ```

8. Directamente depois da Chaveta de abertura da declaração de espaço de nomes, definir uma nova interface denominada **IImageContract** e aplicar o atributo **ServiceContractAttribute** para a interface com um valor de `http://samples.microsoft.com/ServiceModel/Relay/`. O valor de espaço de nomes difere do espaço de nomes que utilizar em todo o âmbito do código. O valor de espaço de nomes é utilizado como um identificador exclusivo para este contrato e deve ter informações sobre a versão. Para obter mais informações, consulte [Serviço de controlo de versões](http://go.microsoft.com/fwlink/?LinkID=180498). Especificar o espaço de nomes explicitamente impede que o valor de espaço de nomes predefinido a ser adicionado ao nome do contrato.

    ```
    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/RESTTutorial1")]
    public interface IImageContract
    {
    }
    ```

9. Dentro do `IImageContract` interface, declarar um método para a operação única a `IImageContract` contrato expõe na interface e aplicar a `OperationContractAttribute` atributo para o método que pretende expor como parte do contrato de serviço Bus público.

    ```
    public interface IImageContract
    {
        [OperationContract]
        Stream GetImage();
    }
    ```

10. No atributo **OperationContract** , adicione o valor de **WebGet** .

    ```
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }
    ```

    Fazê-lo permite que o serviço Bus encaminhar HTTP GET os pedidos para `GetImage`e para traduzir os valores de retorno do `GetImage` para uma resposta de HTTP GETRESPONSE. Mais tarde no tutorial, irá utilizar um browser web para aceder a este método e para apresentar a imagem no browser.

11. Imediatamente a seguir a `IImageContract` definição, declarar um canal que ambos herda as `IImageContract` e `IClientChannel` interfaces.

    ```
    public interface IImageChannel : IImageContract, IClientChannel { }
    ```

    Um canal é o objeto WCF através do qual o serviço e o cliente passam informações umas às outras. Mais tarde, irá criar o canal na sua aplicação do anfitrião. Serviço Bus utiliza, em seguida, este canal para passar os pedidos de HTTP GET a partir do browser para a sua implementação **GetImage** . Serviço Bus também utiliza o canal para tirar o valor de retorno **GetImage** e traduzi-lo para um GETRESPONSE HTTP para o browser do cliente.

12. No menu de **Criar** , clique em **Criar solução** para confirmar a precisão do seu trabalho até ao momento.

### <a name="example"></a>Exemplo

O código seguinte mostra uma interface básica que define um contrato de serviço Bus.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "IImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## <a name="step-3-implement-a-rest-based-wcf-service-contract-to-use-service-bus"></a>Passo 3: Implementar um contrato de serviço WCF com base no resto para utilizar o serviço Bus

Criação de um serviço de estilo resto serviço Bus requer que crie primeiro o contrato, o que é definido utilizando uma interface. O passo seguinte é implementar a interface. Isto envolve a criação de uma classe denominada **ImageService** que implementa a interface de **IImageContract** definidos pelo utilizador. Depois de implementar o contrato, em seguida, configure a interface de utilização de um ficheiro de App. O ficheiro de configuração contém as informações necessárias para a aplicação, como o nome do serviço, o nome do contrato e o tipo de protocolo que é utilizado para comunicar com o serviço Bus. O código utilizado para estas tarefas é fornecido no exemplo siga o procedimento descrito.

Tal como acontece com os passos anteriores, existe muito pouca diferença entre um contrato de estilo de descanso e um contrato de serviço Bus básico de execução.

### <a name="to-implement-a-rest-style-service-bus-contract"></a>Para implementar um contrato Bus de serviço do resto de estilo

1. Crie uma nova classe denominada **ImageService** imediatamente a seguir a definição da interface do **IImageContract** . A classe de **ImageService** implementa a interface de **IImageContract** .

    ```
    class ImageService : IImageContract
    {
    }
    ```
    Semelhantes às outras implementações interface, pode implementar a definição num ficheiro diferente. No entanto, para neste tutorial, a aplicação é apresentado no mesmo ficheiro como a definição de interface e `Main()` método.

2. Aplica o atributo [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) para a classe de **IImageService** para indicar que a classe é uma aplicação de um contrato WCF.

    ```
    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
    }
    ```

    Tal como mencionado anteriormente, este espaço de nomes não é um espaço de nomes tradicional. Em vez disso, é parte da arquitetura WCF que identifica o contrato. Para mais informações, consulte o tópico de [Nomes de contrato de dados](https://msdn.microsoft.com/library/ms731045.aspx) na documentação WCF.

3. Adicione uma imagem. jpg ao seu projeto.  

    Esta é uma imagem que o serviço é apresentado no browser receção. Com o botão direito do projeto e, em seguida, clique em **Adicionar**. Em seguida, clique em **Item existente**. Utilize a caixa de diálogo **Adicionar Item existente** para navegar para um ficheiro. jpg adequado e, em seguida, clique em **Adicionar**.

    Quando adicionar o ficheiro, certifique-se de que **Todos os ficheiros** está selecionada na lista pendente junto ao **nome de ficheiro:** campo. Os restantes neste tutorial assume que o nome da imagem é "imagem. jpg". Se tiver um ficheiro diferente, terá de mudar o nome da imagem, ou altere o código do Adquirente na íntegra.

4. Para se certificar de que o serviço em execução pode localizar o ficheiro de imagem, no **Explorador de solução** com o botão direito no ficheiro de imagem, em seguida, clique em **Propriedades**. No painel de **Propriedades** , defina o **Copiar para directório de saída** para **copiar se mais recente**.

5. Adicionar uma referência para a assemblagem **System.Drawing.dll** ao projeto e adicione também o seguinte associado `using` declarações.  

    ```
    using System.Drawing;
    using System.Drawing.Imaging;
    using Microsoft.ServiceBus;
    using Microsoft.ServiceBus.Web;
    ```

6. Aula **ImageService** , adicione o construtor de seguinte que carrega o mapa de bits e prepara enviá-la para o browser do cliente.

    ```
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }
    }
    ```

7. Diretamente o seguir anterior ao código, adicione o seguinte método **GetImage** na classe **ImageService** para devolver uma mensagem HTTP que contém a imagem.

    ```
    public Stream GetImage()
    {
        MemoryStream stream = new MemoryStream();
        this.bitmap.Save(stream, ImageFormat.Jpeg);

        stream.Position = 0;
        WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

        return stream;
    }
    ```

    Esta implementação utiliza **MemoryStream não pode** para obter a imagem e prepará-lo para transmissão para o browser. Inicia a posição da cadeia em zero, declara o conteúdo da cadeia como jpeg e transmite em fluxo a informação.

8. No menu de **Criar** , clique em **Criar solução**.

### <a name="to-define-the-configuration-for-running-the-web-service-on-service-bus"></a>Para definir a configuração para executar o serviço web num serviço Bus

1. No **Explorador de soluções**, faça duplo clique **App** para o abrir no editor do Visual Studio.

    O ficheiro de **App** assemelha-se a um ficheiro de configuração de WCF e inclui o nome do serviço, ponto final (ou seja, a localização serviço Bus expõe para clientes e anfitriões comunicar com os outros) e enlace (o tipo do protocolo que é utilizado para comunicar). A diferença principal aqui é que o ponto final de serviço configuradas que se refere a um enlace [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) , que não faz parte do .NET Framework.

2. O `<system.serviceModel>` elemento XML é um elemento WCF que define um ou mais serviços. Aqui, é utilizado para definir o nome do serviço e o ponto final. Na parte inferior da `<system.serviceModel>` elemento (mas ainda dentro `<system.serviceModel>`), adicione uma `<bindings>` elemento que tem o seguinte conteúdo. Isto define enlaces utilizados na aplicação. Pode definir vários enlaces, mas para este tutorial estiver a definir apenas uma.

    ```
    <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
            <binding name="default">
                <security relayClientAuthenticationType="None" />
            </binding>
        </webHttpRelayBinding>
    </bindings>
    ```

    Este passo define um serviço Bus [WebHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.webhttprelaybinding.aspx) enlace com **relayClientAuthenticationType** configurada como **nenhuma**. Esta definição indica que um ponto final utilizando este enlace não requerem uma credencial de cliente.

3. Depois do `<bindings>` elemento, adicionar um `<services>` elemento. Semelhante aos enlaces, pode definir vários serviços num ficheiro de configuração de único. No entanto, para este tutorial, pode definir apenas uma.

    ```
    <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
            <endpoint name="RelayEndpoint"
                    contract="Microsoft.ServiceBus.Samples.IImageContract"
                    binding="webHttpRelayBinding"
                    bindingConfiguration="default"
                    behaviorConfiguration="sbTokenProvider"
                    address="" />
        </service>
    </services>
    ```

    Este passo configura um serviço que utiliza o predefinido definido anteriormente **webHttpRelayBinding**. Também utiliza predefinido **sbTokenProvider**, que é definido no próximo passo.

4. Depois do `<services>` elemento, criar uma `<behaviors>` elemento com o seguinte conteúdo, substituindo "SAS_KEY" com a chave de *Assinatura de acesso partilhados* (SA) obtido anteriormente a partir do [Azure portal][].

    ```
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedAccessSignature keyName="RootManageSharedAccessKey" key="SAS_KEY" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
            </endpointBehaviors>
            <serviceBehaviors>
                <behavior name="default">
                    <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
                </behavior>
            </serviceBehaviors>
    </behaviors>
    ```

5. Ainda na App, na `<appSettings>` elemento, substituir a ligação integral do valor de cadeia com a cadeia de ligação obtido anteriormente a partir do portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

6. No menu de **Criar** , clique em **Criar solução** para criar a solução inteira.

### <a name="example"></a>Exemplo

O código seguinte mostra a aplicação de serviço e contrato para um serviço baseado no resto que está em execução no serviço Bus utilizando o enlace **WebHttpRelayBinding** .

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{


    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

O exemplo seguinte mostra o ficheiro de App associado ao serviço.

```
<?xml version="1.0" encoding="utf-8"?>
<configuration>
    <startup> 
        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5.2"/>
    </startup>
    <system.serviceModel>
        <extensions>
            <!-- In this extension section we are introducing all known service bus extensions. User can remove the ones they don't need. -->
            <behaviorExtensions>
                <add name="connectionStatusBehavior"
                    type="Microsoft.ServiceBus.Configuration.ConnectionStatusElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="transportClientEndpointBehavior"
                    type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="serviceRegistrySettings"
                    type="Microsoft.ServiceBus.Configuration.ServiceRegistrySettingsElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </behaviorExtensions>
            <bindingElementExtensions>
                <add name="netMessagingTransport"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingTransportExtensionElement, Microsoft.ServiceBus,  Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="tcpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.TcpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="httpsRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.HttpsRelayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="onewayRelayTransport"
                    type="Microsoft.ServiceBus.Configuration.RelayedOnewayTransportElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingElementExtensions>
            <bindingExtensions>
                <add name="basicHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.BasicHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="webHttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WebHttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="ws2007HttpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.WS2007HttpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netOnewayRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetOnewayRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netEventRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetEventRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
                <add name="netMessagingBinding"
                    type="Microsoft.ServiceBus.Messaging.Configuration.NetMessagingBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
            </bindingExtensions>
        </extensions>
      <bindings>
        <!-- Application Binding -->
        <webHttpRelayBinding>
          <binding name="default">
            <security relayClientAuthenticationType="None" />
          </binding>
        </webHttpRelayBinding>
      </bindings>
      <services>
        <!-- Application Service -->
        <service name="Microsoft.ServiceBus.Samples.ImageService"
             behaviorConfiguration="default">
          <endpoint name="RelayEndpoint"
                  contract="Microsoft.ServiceBus.Samples.IImageContract"
                  binding="webHttpRelayBinding"
                  bindingConfiguration="default"
                  behaviorConfiguration="sbTokenProvider"
                  address="" />
        </service>
      </services>
      <behaviors>
        <endpointBehaviors>
          <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
              <tokenProvider>
                <sharedAccessSignature keyName="RootManageSharedAccessKey" key="[SAS_KEY]" />
              </tokenProvider>
            </transportClientEndpointBehavior>
          </behavior>
        </endpointBehaviors>
        <serviceBehaviors>
          <behavior name="default">
            <serviceDebug httpHelpPageEnabled="false" httpsHelpPageEnabled="false" />
          </behavior>
        </serviceBehaviors>
      </behaviors>
    </system.serviceModel>
    <appSettings>
        <!-- Service Bus specific app setings for messaging connections -->
        <add key="Microsoft.ServiceBus.ConnectionString"
            value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
</configuration>
```

## <a name="step-4-host-the-rest-based-wcf-service-to-use-service-bus"></a>Passo 4: Alojar o serviço WCF com base no resto para utilizar o serviço Bus

Este passo descreve como executar um serviço web utilizando uma aplicação de consola no serviço Bus. Uma lista completa de código escrito este passo é fornecida no exemplo siga o procedimento descrito.

### <a name="to-create-a-base-address-for-the-service"></a>Para criar um endereço base para o serviço

1. No `Main()` função da declaração, criar uma variável para armazenar o espaço de nomes do seu projeto Bus de serviço. Certifique-se substituir `yourNamespace` com o nome do espaço de nomes de serviço que criou anteriormente.

    ```
    string serviceNamespace = "yourNamespace";
    ```
    Serviço Bus utiliza o nome do espaço de nomes para criar um URI exclusivo.

2. Criar um `Uri` instância para o endereço base do serviço que é baseada no espaço de nomes.

    ```
    Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");
    ```

### <a name="to-create-and-configure-the-web-service-host"></a>Para criar e configurar o anfitrião do serviço web

- Criar o anfitrião do serviço web, utilizando o endereço URI criada anteriormente nesta secção.

    ```
    WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
    ```
    O anfitrião do serviço é o objeto WCF iniciar instância de aplicação do anfitrião. Este exemplo transmite-o tipo de anfitrião que pretende criar (uma **ImageService**) e o endereço que pretende que para expor a aplicação do anfitrião.

### <a name="to-run-the-web-service-host"></a>Para executar o anfitrião do serviço web

1. Abra o serviço.

    ```
    host.Open();
    ```
    O serviço está a ser executado.

2. Apresenta uma mensagem a indicar que o serviço está em execução e como parar o serviço.

    ```
    Console.WriteLine("Copy the following address into a browser to see the image: ");
    Console.WriteLine(address + "GetImage");
    Console.WriteLine();
    Console.WriteLine("Press [Enter] to exit");
    Console.ReadLine();
    ```

3. Quando tiver terminado, feche o anfitrião do serviço.

    ```
    host.Close();
    ```

## <a name="example"></a>Exemplo

O exemplo seguinte inclui o contrato de serviço e a aplicação a partir de passos anteriores no tutorial e aloja o serviço numa aplicação de consola. Compile o código seguinte para um ficheiro executável denominado ImageListener.exe.

```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.ServiceModel;
using System.ServiceModel.Channels;
using System.ServiceModel.Web;
using System.IO;
using System.Drawing;
using System.Drawing.Imaging;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Web;

namespace Microsoft.ServiceBus.Samples
{

    [ServiceContract(Name = "ImageContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IImageContract
    {
        [OperationContract, WebGet]
        Stream GetImage();
    }

    public interface IImageChannel : IImageContract, IClientChannel { }

    [ServiceBehavior(Name = "ImageService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class ImageService : IImageContract
    {
        const string imageFileName = "image.jpg";

        Image bitmap;

        public ImageService()
        {
            this.bitmap = Image.FromFile(imageFileName);
        }

        public Stream GetImage()
        {
            MemoryStream stream = new MemoryStream();
            this.bitmap.Save(stream, ImageFormat.Jpeg);

            stream.Position = 0;
            WebOperationContext.Current.OutgoingResponse.ContentType = "image/jpeg";

            return stream;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string serviceNamespace = "InsertServiceNamespaceHere";
            Uri address = ServiceBusEnvironment.CreateServiceUri("https", serviceNamespace, "Image");

            WebServiceHost host = new WebServiceHost(typeof(ImageService), address);
            host.Open();

            Console.WriteLine("Copy the following address into a browser to see the image: ");
            Console.WriteLine(address + "GetImage");
            Console.WriteLine();
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            host.Close();
        }
    }
}
```

### <a name="compiling-the-code"></a>A compilar o código

Depois de criar a solução, faça o seguinte para executar a aplicação:

1. Prima **F5**ou navegue até à localização do ficheiro executável (ImageListener\bin\Debug\ImageListener.exe) para executar o serviço. Manter a aplicação em execução, conforme necessário para efetuar o passo seguinte.

2. Copie e cole o endereço da linha de comandos do browser para ver a imagem.

3. Quando terminar, prima **Enter** na janela da linha de comandos para fechar a aplicação.

## <a name="next-steps"></a>Próximos passos

Agora que já tiver criado uma aplicação que utiliza o serviço de reencaminhamento de Bus de serviço, consulte os seguintes artigos para saber mais acerca das mensagens transmitido:

- [Azure Service Bus descrição geral da arquitectura](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md#relays)

- [Como utilizar o serviço de reencaminhamento de Bus](service-bus-dotnet-how-to-use-relay.md)

[Portal do Azure]: https://portal.azure.com