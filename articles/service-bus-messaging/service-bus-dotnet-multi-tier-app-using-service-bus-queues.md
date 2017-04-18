<properties
    pageTitle="Aplicação de várias camada .NET | Microsoft Azure"
    description="Um tutorial .NET que o ajuda a desenvolver uma aplicação de várias camada no Azure utiliza filas Bus de serviço para comunicar entre camadas."
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
    ms.topic="get-started-article"
    ms.date="09/01/2016"
    ms.author="sethm"/>

# <a name="net-multi-tier-application-using-azure-service-bus-queues"></a>Aplicação de várias camada .NET utilizando filas Bus de serviço do Azure

## <a name="introduction"></a>Introdução

Desenvolver para o Microsoft Azure é fácil de utilizar o Visual Studio e o SDK do Azure gratuito para .NET. Neste tutorial explica os passos para criar uma aplicação que utiliza vários recursos Azure em execução no seu ambiente local. Os passos partem do pressuposto de que tem sem experiência prévia utilizando Azure.

Irá obter o seguinte procedimento:

-   Como ativar o seu computador para o desenvolvimento Azure com uma única transferência e instalação.
-   Como utilizar o Visual Studio desenvolver para o Azure.
-   Como criar uma aplicação de várias camada no Azure utilizando funções web e trabalhador.
-   Como a comunicação entre camadas utilizando serviço Bus filas.

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

Neste tutorial vai criar e executar a aplicação de várias camada num serviço em nuvem Azure. O front-end será uma função de web do ASP.NET MVC e o back-end será uma função de trabalho que utiliza uma fila Bus de serviço. Pode criar a aplicação de várias camada mesmo com o front-end como um projecto da web que é implementado para um Web site Azure em vez de um serviço na nuvem. Para obter instruções sobre o que fazer de forma diferente no front-end Web site Azure, consulte a secção [os passos seguintes](#nextsteps) . Também pode experimentar o tutorial de [aplicação do .NET híbrido no-local/na nuvem](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md) .

A seguinte captura de ecrã mostra a aplicação completa.

![][0]

## <a name="scenario-overview-inter-role-communication"></a>Descrição geral de cenário: função dependências entre comunicação

Para submeter uma ordem de processamento, o componente de IU front-end, a ser executada em função web, tem interagir com a lógica camada em execução na função de trabalho. Este exemplo utiliza Bus serviço controlada mensagens para a comunicação entre as camadas.

Através de mensagens juntamente entre a web e camadas meio decouples dois componentes. Contrariamente ao direta mensagens (ou seja, TCP ou HTTP), a camada web não ligar para a camada diretamente; em vez disso-emite unidades de trabalho, como mensagens, para Bus de serviço, que mantém-los sujeito até a camada está pronta para consumir e processá-las.

Serviço Bus fornece duas entidades para suportar mensagens juntamente: filas e tópicos. Com filas, todas as mensagens enviadas para a fila são consumida por um único destinatário. Tópicos de suporte o padrão de subscrever/publicar em que cada mensagem publicada é disponibilizada para uma subscrição registada com o tópico. Cada subscrição logicamente mantém a sua própria fila de mensagens. Também podem ser configuradas subscrições com regras de filtro restringir o conjunto de mensagens passada para a fila de subscrição para aqueles que correspondam ao filtro. O exemplo seguinte utiliza serviço Bus filas.

![][1]

Este mecanismo de comunicação tem várias vantagens em relação direta mensagens:

-   **Desacoplamento temporal.** Com o comportamento assíncrono padrão de mensagens, produtor e consumidores não é necessário onlinehttps ao mesmo tempo. Serviço Bus sujeito armazena as mensagens até à parte consome está pronta a recebê-las. Permite que os componentes da aplicação distribuída para ser desligada, quer imediatamente, por exemplo, para a manutenção ou devido a uma falha de sistema do componente, sem que afetam o sistema como um todo. Além disso, a aplicação consome apenas poderá ter de ficar online durante determinadas horas do dia.

-   **Carrega o nivelamento.** Em muitas aplicações, carga do sistema varia ao longo do tempo, enquanto o tempo de processamento necessário para cada unidade de trabalho é normalmente constante. Produtor de mensagem intermediating e consumidores de com uma fila significa que a aplicação consome (o trabalhador) só necessita de ser aprovisionada para acomodar carga média em vez de carga de pico. Profundidade da fila cresce e contratos os variações de carga recebida. Este procedimento guarda diretamente dinheiro em termos a quantidade de infraestrutura necessária para a carga de aplicação de serviço.

-   **Balanceamento de carga.** Como carregar aumentos, podem ser adicionados mais processos de trabalho lerem fila de espera. Cada mensagem é processada por apenas um dos processos de trabalho. Além disso, este balanceamento de carga com base em solicitação permite uma utilização ideal da máquinas trabalho mesmo se as máquinas trabalhador diferem em termos de processamento power, como estes puxa mensagens a sua própria velocidade máxima. Este padrão é frequentemente designado pelo padrão de *competir consumidor* .

    ![][2]

As secções seguintes descrevem o código que implementa esta arquitectura.

## <a name="set-up-the-development-environment"></a>Configurar o ambiente de desenvolvimento

Antes de poder começar a desenvolver aplicações Azure, obtenha as ferramentas e configurar o seu ambiente de desenvolvimento.

1.  Instale o SDK do Azure para .NET em [obter ferramentas e SDK][].

2.  Clique em **instalar o SDK** para a versão do Visual Studio estiver a utilizar. Os passos neste tutorial utilizam Visual Studio 2015.

4.  Quando lhe for pedido que execute ou guarde o programa de instalação, clique em **Executar**.

5.  Na **Web plataforma Installer**, clique em **instalar** e continuar com a instalação.

6.  Assim que a instalação estiver concluída, terá necessários para iniciar a aplicação de desenvolver tudo. O SDK inclui ferramentas que permitem-lhe facilmente desenvolver aplicações Azure no Visual Studio. Se não tiver o Visual Studio instalada, o SDK também instala o gratuito Visual Studio Express.

## <a name="create-a-namespace"></a>Criar um espaço de nomes

O passo seguinte é criar um espaço de nomes de serviço e obter uma chave de assinatura partilhadas do Access (SA). Um espaço de nomes fornece um limite de aplicação para cada aplicação exposta através de Bus de serviço. Uma chave de SA é gerada pelo sistema quando é criado um espaço de nomes. A combinação de espaço de nomes e chave SA fornece as credenciais para Bus de serviço para autenticar o acesso a uma aplicação.

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## <a name="create-a-web-role"></a>Criar uma função da web

Nesta secção, construa o front-end da sua aplicação. Primeiro, crie as páginas que apresenta a sua aplicação.
Após esta ação, adicione o código que submete itens para uma fila Bus de serviço e apresenta informações de estado sobre fila de espera.

### <a name="create-the-project"></a>Criar o projecto

1.  Utilizar privilégios de administrador, inicie o Microsoft Visual Studio. Para iniciar o Visual Studio com privilégios de administrador, botão direito do rato no ícone do programa **Visual Studio** e, em seguida, clique em **Executar como administrador**. O emulador cluster Azure, mais tarde outros fabricantes referido neste artigo, necessita que o Visual Studio ser iniciado com privilégios de administrador.

    No Visual Studio, no menu **ficheiro** , clique em **Novo**e, em seguida, clique em **projeto**.

2.  A partir dos **Modelos instalados**, em **Visual c#**, clique em **nuvem** e, em seguida, clique em **Serviço em nuvem Azure**. Nome do projeto **MultiTierApp**. Em seguida, clique em **OK**.

    ![][9]

3.  A partir do **.NET Framework 4,5** funções, faça duplo clique em **Funções de Web do ASP.NET**.

    ![][10]

4.  Paire o cursor sobre **WebRole1** em **solução do serviço em nuvem Azure**, clique no ícone do lápis e mude o nome a função de web para **FrontendWebRole**. Em seguida, clique em **OK**. (Certifique-se de que introduzir "Frontend" com um minúscula '"e","não"FrontEnd".)

    ![][11]

5.  Caixa de diálogo **Novo projeto de ASP.NET** , na lista **Selecione um modelo** , clique em **MVC**.

    ![][12]

6. Ainda na caixa de diálogo **Novo projeto de ASP.NET** , clique no botão **Alterar autenticação** . Na caixa de diálogo **Alterar autenticação** , clique em **Sem autenticação**e, em seguida, clique em **OK**. Para este tutorial, está a implementar uma aplicação que não necessita de um início de sessão do utilizador.

    ![][16]

7. Novamente na caixa de diálogo **Novo projeto de ASP.NET** , clique em **OK** para criar o projeto.

6.  No **Explorador de solução**, no **FrontendWebRole** project, **referências**de contexto, em seguida, clique em **Gerir pacotes de NuGet**.

7.  Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus`. Clique em **instalar**e aceite os termos de utilização.

    ![][13]

    Note que os conjuntos de cliente necessário agora referenciados e algumas novos ficheiros de código foram adicionados.

9.  No **Explorador de soluções**, com o botão direito **modelos** e clique em **Adicionar**, em seguida, clique em **classe**. Na caixa **nome** , escreva o nome **OnlineOrder.cs**. Em seguida, clique em **Adicionar**.

### <a name="write-the-code-for-your-web-role"></a>Escrever o código para o seu perfil da web

Nesta secção, crie as várias páginas que apresenta a sua aplicação.

1.  No ficheiro OnlineOrder.cs no Visual Studio, substitua a definição de espaço de nomes existente com o seguinte código:

    ```
    namespace FrontendWebRole.Models
    {
        public class OnlineOrder
        {
            public string Customer { get; set; }
            public string Product { get; set; }
        }
    }
    ```

2.  No **Explorador de soluções**, faça duplo clique **Controllers\HomeController.cs**. Adicione as seguintes instruções de **utilizar** na parte superior do ficheiro para incluir os espaços de nomes para o modelo que acabou de criar, bem como Bus de serviço.

    ```
    using FrontendWebRole.Models;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    ```

3.  Também no ficheiro HomeController.cs no Visual Studio, substitua a definição de espaço de nomes existente com o código seguinte. Este código contém métodos de acordo com a apresentação dos itens para a fila.

    ```
    namespace FrontendWebRole.Controllers
    {
        public class HomeController : Controller
        {
            public ActionResult Index()
            {
                // Simply redirect to Submit, since Submit will serve as the
                // front page of this application.
                return RedirectToAction("Submit");
            }
    
            public ActionResult About()
            {
                return View();
            }
    
            // GET: /Home/Submit.
            // Controller method for a view you will create for the submission
            // form.
            public ActionResult Submit()
            {
                // Will put code for displaying queue message count here.
    
                return View();
            }
    
            // POST: /Home/Submit.
            // Controller method for handling submissions from the submission
            // form.
            [HttpPost]
            // Attribute to help prevent cross-site scripting attacks and
            // cross-site request forgery.  
            [ValidateAntiForgeryToken]
            public ActionResult Submit(OnlineOrder order)
            {
                if (ModelState.IsValid)
                {
                    // Will put code for submitting to queue here.
    
                    return RedirectToAction("Submit");
                }
                else
                {
                    return View(order);
                }
            }
        }
    }
    ```

4.  No menu **construir** , clique em **Criar solução** para testar a precisão do seu trabalho até ao momento.

5.  Agora, crie a vista para o `Submit()` método que criou anteriormente. Botão direito do rato dentro de `Submit()` método (sobrecarga das `Submit()` que não tem parâmetros) e, em seguida, selecione **Adicionar vista**.

    ![][14]

6.  É apresentada uma caixa de diálogo para criar a vista. Na lista de **modelo** , selecione **Criar**. Na lista de **classe do modelo** , clique na classe de **OnlineOrder** .

    ![][15]

7.  Clique em **Adicionar**.

8.  Agora, altere o nome apresentado da sua aplicação. No **Explorador de soluções**, faça duplo clique sobre a **Views\Shared\\_Layout.cshtml** ficheiro para o abrir no editor do Visual Studio.

9.  Substitua todas as ocorrências da **Minha aplicação do ASP.NET** com **produtos do construções de betão**.

10. Remova as ligações de **casa**, **sobre**e o **contacto** . Elimine o código realçado:

    ![][28]

11. Por fim, modificar a página de submissão para incluir algumas informações sobre fila de espera. No **Explorador de soluções**, faça duplo clique no ficheiro de **Views\Home\Submit.cshtml** para abri-lo no editor do Visual Studio. Adicione a linha seguinte depois de `<h2>Submit</h2>`. Por agora, o `ViewBag.MessageCount` estiver em branco. Irão povoá-la mais tarde.

    ```
    <p>Current number of orders in queue waiting to be processed: @ViewBag.MessageCount</p>
    ```

12. Agora tem implementado a interface do utilizador. Pode premir **F5** para executar a sua aplicação e confirme que parece conforme esperado.

    ![][17]

### <a name="write-the-code-for-submitting-items-to-a-service-bus-queue"></a>Escrever o código para submeter itens para uma fila Bus de serviço

Agora, adicione o código para submeter itens para uma fila. Primeiro, crie uma classe que contém as informações de ligação do serviço Bus fila de espera. Em seguida, iniciar a sua ligação de Global.aspx.cs. Por fim, atualizar o código de submissão que criou anteriormente no HomeController.cs realmente Submeter itens para uma fila Bus de serviço.

1.  No **Explorador de soluções**, com o botão direito **FrontendWebRole** (botão direito do rato no project, não a função). Clique em **Adicionar**e, em seguida, clique em **escolares**.

2.  Nome a classe **QueueConnector.cs**. Clique em **Adicionar** para criar a classe.

3.  Agora, adicione o código que contém as informações de ligação e inicia a ligação para uma fila Bus de serviço. Substituir de todo o conteúdo da QueueConnector.cs com o seguinte código e introduza valores para `your Service Bus namespace` (o seu nome de espaço de nomes) e `yourKey`, qual é a **chave primária** obtido anteriormente a partir do portal Azure.

    ```
    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.ServiceBus;
    
    namespace FrontendWebRole
    {
        public static class QueueConnector
        {
            // Thread-safe. Recommended that you cache rather than recreating it
            // on every request.
            public static QueueClient OrdersQueueClient;
    
            // Obtain these values from the portal.
            public const string Namespace = "your Service Bus namespace";
    
            // The name of your queue.
            public const string QueueName = "OrdersQueue";
    
            public static NamespaceManager CreateNamespaceManager()
            {
                // Create the namespace manager which gives you access to
                // management operations.
                var uri = ServiceBusEnvironment.CreateServiceUri(
                    "sb", Namespace, String.Empty);
                var tP = TokenProvider.CreateSharedAccessSignatureTokenProvider(
                    "RootManageSharedAccessKey", "yourKey");
                return new NamespaceManager(uri, tP);
            }
    
            public static void Initialize()
            {
                // Using Http to be friendly with outbound firewalls.
                ServiceBusEnvironment.SystemConnectivity.Mode =
                    ConnectivityMode.Http;
    
                // Create the namespace manager which gives you access to
                // management operations.
                var namespaceManager = CreateNamespaceManager();
    
                // Create the queue if it does not exist already.
                if (!namespaceManager.QueueExists(QueueName))
                {
                    namespaceManager.CreateQueue(QueueName);
                }
    
                // Get a client to the queue.
                var messagingFactory = MessagingFactory.Create(
                    namespaceManager.Address,
                    namespaceManager.Settings.TokenProvider);
                OrdersQueueClient = messagingFactory.CreateQueueClient(
                    "OrdersQueue");
            }
        }
    }
    ```

4.  Agora, certifique-se de que o seu método de **inicialização** obtém a chamada. No **Explorador de soluções**, faça duplo clique **Global.asax\Global.asax.cs**.

5.  Adicione a linha seguinte de código no final do método **Application_Start** .

    ```
    FrontendWebRole.QueueConnector.Initialize();
    ```

6.  Por fim, atualize o código de web que criou anteriormente, para apresentar itens para a fila. No **Explorador de soluções**, faça duplo clique **Controllers\HomeController.cs**.

7.  Atualizar a `Submit()` método (sobrecarga que não tem parâmetros) da seguinte forma para obter a contagem de mensagem para a fila.

    ```
    public ActionResult Submit()
    {
        // Get a NamespaceManager which allows you to perform management and
        // diagnostic operations on your Service Bus queues.
        var namespaceManager = QueueConnector.CreateNamespaceManager();
    
        // Get the queue, and obtain the message count.
        var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
        ViewBag.MessageCount = queue.MessageCount;
    
        return View();
    }
    ```

8.  Atualizar a `Submit(OnlineOrder order)` método (a sobrecarga assume um parâmetro) da seguinte forma para submeter a informação da encomenda para a fila.

    ```
    public ActionResult Submit(OnlineOrder order)
    {
        if (ModelState.IsValid)
        {
            // Create a message from the order.
            var message = new BrokeredMessage(order);
    
            // Submit the order.
            QueueConnector.OrdersQueueClient.Send(message);
            return RedirectToAction("Submit");
        }
        else
        {
            return View(order);
        }
    }
    ```

9.  Agora, pode executar a aplicação novamente. Sempre que submeter uma ordem, aumenta a contagem de mensagens.

    ![][18]

## <a name="create-the-worker-role"></a>Criar a função de trabalho

Agora irá criar a função de trabalho que processa as submissões de encomenda. Este exemplo utiliza o modelo de projeto do Visual Studio **Trabalhador função com o serviço Bus fila** . Já obtida as credenciais necessárias a partir do portal.

1. Certifique-se de que tem ligados Visual Studio à sua conta Azure.

2.  No Visual Studio, no **Explorador de solução** com o botão direito na pasta de **funções** em projeto **MultiTierApp** .

3.  Clique em **Adicionar**e, em seguida, clique em **Novo projeto de função de trabalho**. É apresentada a caixa de diálogo **Adicionar novo projeto de função** .

    ![][26]

4.  Na caixa de diálogo **Adicionar novo projeto de função** , clique em **Funções de trabalho com fila de Bus de serviço**.

    ![][23]

5.  Na caixa **nome** , nome do projeto **OrderProcessingRole**. Em seguida, clique em **Adicionar**.

6.  Copie a cadeia de ligação que obteve no passo 9 da secção "Criar um espaço de nomes de serviço Bus" para a área de transferência.

7.  No **Explorador de soluções**, com o botão direito **OrderProcessingRole** que criou no passo 5 (Certifique-se de que contexto **OrderProcessingRole** em **funções**e não a classe). Em seguida, clique em **Propriedades**.

8.  No separador **Definições** da caixa de diálogo **Propriedades** , clique dentro da caixa de **valor** para **Microsoft.ServiceBus.ConnectionString**e, em seguida, cole o valor de ponto final que copiou no passo 6.

    ![][25]

9.  Crie uma classe **OnlineOrder** para representar as encomendas, processá-los a partir de fila de espera. Pode reutilizar uma classe que já criadas. No **Explorador de soluções**, com o botão direito a classe de **OrderProcessingRole** (botão direito do rato no ícone de classe e não a função). Clique em **Adicionar**e, em seguida, clique em **Item existente**.

10. Navegue para a subpasta **FrontendWebRole\Models**e, em seguida, faça duplo clique em **OnlineOrder.cs** para adicioná-lo para este projeto.

11. Na **WorkerRole.cs**, altere o valor da variável **NomeFila** de `"ProcessingQueue"` para `"OrdersQueue"` conforme mostrado no seguinte código.

    ```
    // The name of your queue.
    const string QueueName = "OrdersQueue";
    ```

12. Adicione o seguinte utilizando instrução na parte superior do ficheiro WorkerRole.cs.

    ```
    using FrontendWebRole.Models;
    ```

13. No `Run()` funcionar, interior a `OnMessage()` de chamadas, substitua os conteúdos do `try` cláusula com o código seguinte.

    ```
    Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
    // View the message as an OnlineOrder.
    OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
    Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
    receivedMessage.Complete();
    ```

14. Concluiu a aplicação. Pode testar a aplicação completa do clicando com o botão direito do projecto MultiTierApp no Explorador de soluções, selecionando **Definir como projeto de arranque**e, em seguida, prima F5. Tenha em atenção que a contagem de mensagem não é incrementado, uma vez que a função de trabalho processa itens a partir de fila de espera e marca-los como concluída. Pode ver os resultados do rastreio ao seu cargo trabalhador visualizando Azure calcular emulador IU. Pode fazê-lo ao clicar no ícone de emulador na área de notificação da barra de tarefas e selecionar **Mostrar IU emulador calcular**.

    ![][19]

    ![][20]

## <a name="next-steps"></a>Próximos passos  

Para saber mais sobre Bus de serviço, consulte os seguintes recursos:  

* [Serviço Azure Bus][sbmsdn]  
* [Página de serviço do serviço Bus][sbwacom]  
* [Como utilizar o serviço Bus filas][sbwacomqhowto]  

Para saber mais sobre cenários de várias camados, consulte o artigo:  

* [Aplicação de várias camada .NET através do armazenamento de tabelas, filas e Blobs][mutitierstorage]  

  [0]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [1]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [Obter ferramentas e SDK]: http://go.microsoft.com/fwlink/?LinkId=271920


  [GetSetting]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.getsetting.aspx
  [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]: https://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudconfigurationmanager.aspx
  [NamespaceMananger]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx

  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx

  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx

  [EventHubClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventhubclient.aspx

  [9]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.png
  [10]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [11]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [12]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [13]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [14]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [15]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [16]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-14.png
  [17]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [18]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png

  [19]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [20]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [23]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [25]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [26]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [28]: ./media/service-bus-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png

  [sbmsdn]: http://msdn.microsoft.com/library/azure/ee732537.aspx  
  [sbwacom]: /documentation/services/service-bus/  
  [sbwacomqhowto]: service-bus-dotnet-get-started-with-queues.md  
  [mutitierstorage]: https://code.msdn.microsoft.com/Windows-Azure-Multi-Tier-eadceb36
  