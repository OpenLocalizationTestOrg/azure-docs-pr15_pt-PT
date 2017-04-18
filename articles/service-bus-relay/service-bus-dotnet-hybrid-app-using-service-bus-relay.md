<properties
    pageTitle="Aplicação de no-local/nuvem híbrido (.NET) | Microsoft Azure"
    description="Saiba como criar uma aplicação de híbrido no-local/nuvem do .NET utilizando o reencaminhamento de Azure Service Bus."
    services="service-bus"
    documentationCenter=".net"
    authors="sethmanheim"
    manager="timlt"
    editor=""/>

<tags
    ms.service="service-bus"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="09/16/2016"
    ms.author="sethm"/>

# <a name="net-on-premisescloud-hybrid-application-using-azure-service-bus-relay"></a>Aplicação do híbrido no-local/nuvem .NET utilizando Azure Service Bus reencaminhamento

## <a name="introduction"></a>Introdução

Este artigo descreve como criar uma aplicação de nuvem híbrida com o Microsoft Azure e Visual Studio. O tutorial assume que tem sem experiência prévia utilizando Azure. Em menos de 30 minutos, terá uma aplicação que utiliza vários recursos Azure para cima e a executar na nuvem.

Vai aprender:

-   Como criar ou adaptar um serviço web existente para o consumo por uma solução da web.
-   Como utilizar o serviço de reencaminhamento do Azure Service Bus para partilhar dados entre uma aplicação do Azure e um serviço web alojado noutro local.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="how-the-service-bus-relay-helps-with-hybrid-solutions"></a>Como o reencaminhamento de serviço Bus ajuda-o com as soluções híbrido

Soluções empresariais normalmente são compostos por uma combinação de código personalizado escrito para despachar necessidades de negócio novas e exclusivo e existente a funcionalidade fornecida pela soluções e sistemas de que já estão no local.

Arquitectura de solução está a começar a utilizar na nuvem para processamento mais fácil de requisitos de escala e reduzir os custos operacionais avançados. Ao fazê-lo, estes descobrir que elementos de serviço existente que gostaria de tirar partido como blocos modulares para as respetivas soluções estão dentro de firewall da empresa e terminar fácil chegar para acesso pela solução na nuvem. Muitos serviços internos não são incorporados ou alojados de forma a que podem ser expostos facilmente, na extremidade rede empresarial.

O reencaminhamento de serviço Bus foi concebido para o caso de utilização de serviços de web Windows Communication Foundation (WCF) existentes a demorar e tornar desses serviços em segurança acessíveis a soluções que residem fora do perímetro da empresa sem exigir alterações intrusivas à infraestrutura de rede da empresa. Desses serviços de reencaminhamento de serviço Bus ainda estão alojados no interior do seu ambiente existente, mas delegar a aguardar entrada sessões e dos pedidos de serviço Bus alojado na nuvem. Serviço Bus também protege desses serviços de acesso não autorizado utilizando a autenticação de [Assinatura de acesso partilhados](../service-bus-messaging/service-bus-sas-overview.md) (SA).

## <a name="solution-scenario"></a>Cenário de solução

Neste tutorial, irá criar um site ASP.NET permite-lhe ver uma lista de produtos na página de inventário do produto.

![][0]

O tutorial assume que tem informações do produto no sistema existente no local e utiliza o reencaminhamento de serviço Bus para alcançar para esse sistema. Isto é simulado por um serviço web que é executado numa aplicação de consola simples e é cópias por um conjunto de na memória dos produtos. Será capaz de executar esta aplicação de consola no seu computador e implementar o papel de web para Azure. Ao fazê-lo, irá ver como a função da web a ser executada no Centro de dados Azure irá facto efetuar a chamada para o seu computador, apesar do seu computador certamente residirão atrasado pelo menos uma firewall e uma camada de tradução (NAT) do endereço de rede.

Segue-se uma captura de ecrã da página inicial da aplicação web concluída.

![][1]

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de poder começar a desenvolver aplicações Azure, obtenha as ferramentas e configurar o seu ambiente de desenvolvimento.

1.  Instale o SDK do Azure para .NET a partir da página [obter ferramentas e o SDK][] .

2.  Clique em **instalar o SDK** para a versão do Visual Studio estiver a utilizar. Os passos neste tutorial utilizam Visual Studio 2015.

4.  Quando lhe for pedido que execute ou guarde o programa de instalação, clique em **Executar**.

5.  Na **Web plataforma Installer**, clique em **instalar** e continuar com a instalação.

6.  Assim que a instalação estiver concluída, terá necessários para iniciar a aplicação de desenvolver tudo. O SDK inclui ferramentas que permitem-lhe facilmente desenvolver aplicações Azure no Visual Studio. Se não tiver o Visual Studio instalada, o SDK também instala o gratuito Visual Studio Express.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

Para começar a utilizar funcionalidades de serviço Bus no Azure, primeiro tem de criar um espaço de nomes de serviço. Um espaço de nomes fornece um âmbito contentor para endereçar recursos de serviço Bus na aplicação.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-an-on-premises-server"></a>Criar um servidor no local

Em primeiro lugar, irá criar um sistema de catálogo de produtos (fictício) no local. Vai ser bastante simple; Pode ver isto como que representa um sistema de catálogo de produtos real no local com uma superfície de serviço concluída que estamos a tentar integrar.

Este projeto é uma aplicação de consola do Visual Studio e utiliza o [pacote do Azure Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) para incluir as bibliotecas de serviço Bus e as definições de configuração.

### <a name="create-the-project"></a>Criar o projecto

1.  Utilizar privilégios de administrador, inicie o Microsoft Visual Studio. Para iniciar o Visual Studio com privilégios de administrador, botão direito do rato no ícone do programa **Visual Studio** e, em seguida, clique em **Executar como administrador**.

2.  No Visual Studio, no menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.

3.  A partir dos **Modelos instalados**, em **Visual c#**, clique em **Aplicação da consola**. Na caixa **nome** , escreva o nome **ProductsServer**:

    ![][11]

4.  Clique em **OK** para criar o projeto **ProductsServer** .

7.  Se já tiver instalado o Gestor de pacote NuGet para Visual Studio, avance para o próximo passo. Caso contrário, visite [NuGet][] e clique em [Instalar NuGet](http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c). Siga os pedidos para instalar o Gestor de pacote NuGet, em seguida, inicie novamente o Visual Studio.

7.  No Explorador de solução, com o botão direito do projecto **ProductsServer** , em seguida, clique em **Gerir pacotes de NuGet**.

8.  Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de utilização.

    ![][13]

    Tenha em atenção que os conjuntos de cliente necessário estão referenciados agora.

9.  Adicione uma nova classe para o contrato de produto. No Explorador de solução, com o botão direito do projecto **ProductsServer** e clique em **Adicionar**e, em seguida, clique em **classe**.

10. Na caixa **nome** , escreva o nome **ProductsContract.cs**. Em seguida, clique em **Adicionar**.

11. Na **ProductsContract.cs**, substitua a definição de espaço de nomes com o seguinte código, define o contrato do serviço.

    ```
    namespace ProductsServer
    {
        using System.Collections.Generic;
        using System.Runtime.Serialization;
        using System.ServiceModel;
    
        // Define the data contract for the service
        [DataContract]
        // Declare the serializable properties.
        public class ProductData
        {
            [DataMember]
            public string Id { get; set; }
            [DataMember]
            public string Name { get; set; }
            [DataMember]
            public string Quantity { get; set; }
        }
    
        // Define the service contract.
        [ServiceContract]
        interface IProducts
        {
            [OperationContract]
            IList<ProductData> GetProducts();
    
        }
    
        interface IProductsChannel : IProducts, IClientChannel
        {
        }
    }
    ```

12. Na Program.cs, substitua a definição de espaço de nomes com o código seguinte, que adiciona o serviço de perfil e o anfitrião do mesmo.

    ```
    namespace ProductsServer
    {
        using System;
        using System.Linq;
        using System.Collections.Generic;
        using System.ServiceModel;
    
        // Implement the IProducts interface.
        class ProductsService : IProducts
        {
    
            // Populate array of products for display on website
            ProductData[] products =
                new []
                    {
                        new ProductData{ Id = "1", Name = "Rock",
                                         Quantity = "1"},
                        new ProductData{ Id = "2", Name = "Paper",
                                         Quantity = "3"},
                        new ProductData{ Id = "3", Name = "Scissors",
                                         Quantity = "5"},
                        new ProductData{ Id = "4", Name = "Well",
                                         Quantity = "2500"},
                    };
    
            // Display a message in the service console application
            // when the list of products is retrieved.
            public IList<ProductData> GetProducts()
            {
                Console.WriteLine("GetProducts called.");
                return products;
            }
    
        }
    
        class Program
        {
            // Define the Main() function in the service application.
            static void Main(string[] args)
            {
                var sh = new ServiceHost(typeof(ProductsService));
                sh.Open();
    
                Console.WriteLine("Press ENTER to close");
                Console.ReadLine();
    
                sh.Close();
            }
        }
    }
    ```

13. No Explorador de solução, faça duplo clique no ficheiro de **App** para abri-lo no editor do Visual Studio. Na parte inferior da ** &lt;sistema. ServiceModel&gt; ** elemento (mas ainda dentro &lt;sistema. ServiceModel&gt;), adicione o seguinte código XML. Certifique-se de que substitua *yourServiceNamespace* com o nome do seu espaço de nomes e *yourKey* a chave de SA que anteriormente obtidos a partir do portal:

    ```
    <system.serviceModel>
    ...
      <services>
         <service name="ProductsServer.ProductsService">
           <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts" behaviorConfiguration="products"/>
         </service>
      </services>
      <behaviors>
         <endpointBehaviors>
           <behavior name="products">
             <transportClientEndpointBehavior>
                <tokenProvider>
                   <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
             </transportClientEndpointBehavior>
           </behavior>
         </endpointBehaviors>
      </behaviors>
    </system.serviceModel>
    ```
14. Ainda na App, na ** &lt;appSettings&gt; ** elemento, substituir a ligação de valor de cadeia com a cadeia de ligação obtido anteriormente a partir do portal. 

    ```
    <appSettings>
    <!-- Service Bus specific app settings for messaging connections -->
    <add key="Microsoft.ServiceBus.ConnectionString"
           value="Endpoint=sb://yourNamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=yourKey"/>
    </appSettings>
    ```

14. Prima **Ctrl + Shift + B** ou a partir do menu **construir** , clique em **Criar solução** para construir a aplicação e verificar a precisão do seu trabalho até ao momento.

## <a name="create-an-aspnet-application"></a>Criar uma aplicação do ASP.NET

Nesta secção irá criar uma aplicação do ASP.NET simple que apresenta os dados obtidos a partir do seu serviço de produto.

### <a name="create-the-project"></a>Criar o projecto

1.  Certifique-se de que o Visual Studio está em execução com privilégios de administrador.

2.  No Visual Studio, no menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.

3.  A partir dos **Modelos instalados**, em **Visual c#**, clique em **Aplicação Web do ASP.NET**. Nome do projeto **ProductsPortal**. Em seguida, clique em **OK**.

    ![][15]

4.  Na lista **Selecione um modelo** , clique em **MVC**. 

6.  Selecione a caixa do **anfitrião na nuvem**.

    ![][16]

5. Clique no botão **Alterar autenticação** . Na caixa de diálogo **Alterar autenticação** , clique em **Sem autenticação**e, em seguida, clique em **OK**. Para este tutorial, está a implementar uma aplicação que não necessita de um início de sessão do utilizador.

    ![][18]

6.  Na secção **Do Microsoft Azure** da caixa de diálogo **Novo projeto de ASP.NET** , certifique-se de que a **alojar na nuvem** está selecionada e que a **Aplicação de serviço** está selecionada na lista pendente.

    ![][19]

7. Clique em **OK**. 

8. Agora tem de configurar Azure recursos para uma nova aplicação web. Siga todos os passos na secção [Configurar Azure recursos para uma nova aplicação web](../app-service-web/web-sites-dotnet-get-started.md#configure-azure-resources-for-a-new-web-app). Em seguida, regresse a este tutorial e continue para o passo seguinte.

5.  No Explorador de solução, com o botão direito **modelos** e, em seguida, clique em **Adicionar**, em seguida, clique em **classe**. Na caixa **nome** , escreva o nome **Product.cs**. Em seguida, clique em **Adicionar**.

    ![][17]

### <a name="modify-the-web-application"></a>Modificar a aplicação web

1.  No ficheiro Product.cs no Visual Studio, substitua a definição de espaço de nomes existente com o código seguinte.

    ```
    // Declare properties for the products inventory.
    namespace ProductsWeb.Models
    {
        public class Product
        {
            public string Id { get; set; }
            public string Name { get; set; }
            public string Quantity { get; set; }
        }
    }
    ```

2.  No Explorador de solução, expanda a pasta de **controladores** , em seguida, faça duplo clique no ficheiro de **HomeController.cs** para abri-lo no Visual Studio.

3. Na **HomeController.cs**, substitua a definição de espaço de nomes existente com o código seguinte.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Collections.Generic;
        using System.Web.Mvc;
        using Models;
    
        public class HomeController : Controller
        {
            // Return a view of the products inventory.
            public ActionResult Index(string Identifier, string ProductName)
            {
                var products = new List<Product>
                    {new Product {Id = Identifier, Name = ProductName}};
                return View(products);
            }
         }
    }
    ```

3.  No Explorador de solução, expanda a pasta Views\Shared, em seguida, faça duplo clique em **_Layout.cshtml** para o abrir no editor do Visual Studio.

5.  Altere todas as ocorrências da **Minha aplicação do ASP.NET** para **produtos da construções de betão**.

6. Remova as ligações de **casa**, **sobre**e o **contacto** . No exemplo seguinte, elimine o código realçado.

    ![][41]

7.  No Explorador de solução, expanda a pasta Views\Home, em seguida, faça duplo clique em **Index.cshtml** para o abrir no editor do Visual Studio.
    Substitua o seguinte código de todo o conteúdo do ficheiro.

    ```
    @model IEnumerable<ProductsWeb.Models.Product>
    
    @{
            ViewBag.Title = "Index";
    }
    
    <h2>Prod Inventory</h2>
    
    <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                  <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>
    
    @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>
    }
    
    </table>
    ```

9.  Para verificar a precisão do seu trabalho até ao momento, pode premir **Ctrl + Shift + B** para construir o projeto.


### <a name="run-the-app-locally"></a>Executar a aplicação de localmente

Execute a aplicação para verificar que funciona.

1.  Certifique-se de que **ProductsPortal** é o projeto ativo. Botão direito do rato no nome do projeto no Explorador de solução e selecione **Como projeto de arranque**.
2.  No Visual Studio, prima F5.
3.  A aplicação deverá aparecer em execução num browser.

    ![][21]

## <a name="put-the-pieces-together"></a>Juntar as peças

O passo seguinte é ligar o servidor de produtos no local com a aplicação do ASP.NET.

1.  Se ainda não esteja aberto, no Visual Studio, abra novamente o projeto **ProductsPortal** que criou na secção [criar uma aplicação do ASP.NET](#create-an-aspnet-application) .

2.  Semelhante para o passo na secção "Criar um servidor no local", adicione o pacote de NuGet para as referências de projeto. No Explorador de solução, com o botão direito do projecto **ProductsPortal** , em seguida, clique em **Gerir pacotes de NuGet**.

3.  Procurar "Serviço Bus" e selecione o item do **Microsoft Azure Service Bus** . Em seguida, concluir a instalação e feche esta caixa de diálogo.

4.  No Explorador de solução, com o botão direito do projecto **ProductsPortal** , em seguida, clique em **Adicionar**, em seguida, **Item existente**.

5.  Navegue para o ficheiro de **ProductsContract.cs** do projeto de consola **ProductsServer** . Clique em para realçar ProductsContract.cs. Clique na seta para baixo junto a **Adicionar**e, em seguida, clique em **Adicionar como ligação**.

    ![][24]

6.  Agora, abra o ficheiro **HomeController.cs** no editor do Visual Studio e substituir a definição de espaço de nomes com o código seguinte. Certifique-se de que substitua *yourServiceNamespace* o nome do seu serviço espaço de nomes e *yourKey* com a sua chave de SA. Isto permitirá que o cliente para chamar o serviço no local, a devolver o resultado da chamada.

    ```
    namespace ProductsWeb.Controllers
    {
        using System.Linq;
        using System.ServiceModel;
        using System.Web.Mvc;
        using Microsoft.ServiceBus;
        using Models;
        using ProductsServer;
    
        public class HomeController : Controller
        {
            // Declare the channel factory.
            static ChannelFactory<IProductsChannel> channelFactory;
    
            static HomeController()
            {
                // Create shared access signature token credentials for authentication.
                channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(),
                    "sb://yourServiceNamespace.servicebus.windows.net/products");
                channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior {
                    TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                        "RootManageSharedAccessKey", "yourKey") });
            }
    
            public ActionResult Index()
            {
                using (IProductsChannel channel = channelFactory.CreateChannel())
                {
                    // Return a view of the products inventory.
                    return this.View(from prod in channel.GetProducts()
                                     select
                                         new Product { Id = prod.Id, Name = prod.Name,
                                             Quantity = prod.Quantity });
                }
            }
        }
    }
    ```

7.  No Explorador de solução, com o botão direito a solução **ProductsPortal** (tornar-se de que a solução, não o projeto com o botão direito). Clique em **Adicionar**e, em seguida, clique em **Projeto existente**.

8.  Navegue para o projeto **ProductsServer** , em seguida, faça duplo clique sobre o ficheiro da solução **ProductsServer.csproj** para adicioná-lo.

9.  Tem de executar **ProductsServer** para mostrar os dados no **ProductsPortal**. No Explorador de solução, com o botão direito a solução **ProductsPortal** e clique em **Propriedades**. É apresentada a caixa de diálogo de **Páginas de propriedades** .

10. No lado esquerdo, clique em **Projeto de arranque**. No lado direito, clique em **vários projectos de arranque**. Certifique-se de que **ProductsServer** e **ProductsPortal** aparecem, por essa ordem, com **Iniciar** predefinir a ação para ambos.

      ![][25]

11. Ainda na caixa de diálogo **Propriedades** , clique em **Dependências de projecto** no lado esquerdo.

12. Na lista de **projetos** , clique em **ProductsServer**. Certifique-se de que está **ProductsPortal** **não** selecionada.

14. Na lista de **projetos** , clique em **ProductsPortal**. Certifique-se de que **ProductsServer** está selecionada. 

    ![][26]

15. Clique em **OK** na caixa de diálogo **Páginas de propriedades** .

## <a name="run-the-project-locally"></a>Execute o projecto localmente

Para testar a aplicação localmente, no Visual Studio prima **F5**. O servidor no local (**ProductsServer**) deverá começar primeiro, em seguida, a aplicação **ProductsPortal** deverá começar numa janela do browser. Desta vez, verá que inventário do produto lista dados obtidos a partir do sistema de no local de serviço do produto.

![][10]

Na página **ProductsPortal** , prima a **Atualizar** . Sempre que atualizar a página, verá a aplicação de servidor apresentar uma mensagem quando `GetProducts()` a partir do **ProductsServer** chama-se.

Feche ambas as aplicações antes de prosseguir para o passo seguinte.

## <a name="deploy-the-productsportal-project-to-an-azure-web-app"></a>Implementar o projeto ProductsPortal para uma aplicação web do Azure

O passo seguinte é converter o frontend **ProductsPortal** para uma aplicação Azure web. Em primeiro lugar, implemente o projeto **ProductsPortal** , todos os passos na secção [Implementar o projeto web para a aplicação Azure web](../app-service-web/web-sites-dotnet-get-started.md#deploy-the-web-project-to-the-azure-web-app)a seguir. Depois de implementação estiver concluída, regresse a este tutorial e continue para o passo seguinte.

> [AZURE.NOTE] Poderá ver uma mensagem de erro na janela do browser quando o projecto da web **ProductsPortal** é iniciado automaticamente depois da implementação. Esta é esperada e ocorre porque a aplicação **ProductsServer** não estiver em execução ainda.

Copie o URL da aplicação web implementado, tal como terá do URL no próximo passo. Também pode obter este URL a partir da janela de atividade de serviço de aplicação do Azure no Visual Studio:

![][9] 

### <a name="set-productsportal-as-web-app"></a>Definir ProductsPortal como web app

Antes de executar a aplicação na nuvem, tem de garantir que **ProductsPortal** é iniciada a partir do Visual Studio como uma aplicação web.

1. No Visual Studio, com o botão direito do projecto **ProjectsPortal** e, em seguida, clique em **Propriedades**.

3. Na coluna da esquerda, clique em **Web**.

5. Na secção de **Ação iniciar** , clique no botão **Começar URL** e, na caixa de texto, introduza o URL para a sua aplicação web anteriormente implementado; Por exemplo, `http://productsportal1234567890.azurewebsites.net/`.

    ![][27]

6. No menu **ficheiro** no Visual Studio, clique em **Guardar tudo**.

7. No menu de compilação no Visual Studio, clique em **Reconstruir solução**.

## <a name="run-the-application"></a>Executar a aplicação

2.  Prima F5 para criar e executar a aplicação. O servidor no local (a aplicação de consola **ProductsServer** ) deverá começar primeiro, em seguida, a aplicação **ProductsPortal** deverá começar numa janela do browser, conforme apresentado na seguinte captura de ecrã. Aviso novamente inventário do produto listas de dados obtidos a partir do serviço de produto no local sistema e apresenta esses dados no web app. Verifique se o URL para se certificar de que **ProductsPortal** está em execução na nuvem, como uma aplicação Azure web. 

    ![][1]

    > [AZURE.IMPORTANT] A aplicação da consola de **ProductsServer** tem de ser conseguir servir os dados para a aplicação **ProductsPortal** e em execução. Se o browser apresenta um erro, aguarde alguns segundos mais para **ProductsServer** carregar e apresentar a seguinte mensagem. Em seguida, prima **Atualizar** no browser.

    ![][37]

3. Novamente no browser, prima **Atualizar** na página **ProductsPortal** . Sempre que atualizar a página, verá a aplicação de servidor apresentar uma mensagem quando `GetProducts()` a partir do **ProductsServer** chama-se.

    ![][38]

## <a name="next-steps"></a>Próximos passos  

Para saber mais sobre Bus de serviço, consulte os seguintes recursos:  

* [Serviço Azure Bus][sbwacom]  
* [Como utilizar o serviço Bus filas][sbwacomqhowto]  


  [0]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obter ferramentas e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920
  [NuGet]: http://nuget.org
  
  [11]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [16]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [17]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.png
  [18]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-5.png
  [19]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-6.png
  [9]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-9.png
  [10]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App3.png

  [21]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [24]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [25]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [26]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [27]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-web-8.png
  
  [36]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [37]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [38]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/hy-service2.png
  [41]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [43]: ./media/service-bus-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png


  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

