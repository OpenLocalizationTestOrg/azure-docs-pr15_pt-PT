<properties
    pageTitle="Arquitetura de emissão de notificação concentradores - Enterprise"
    description="Orientações sobre a utilização do Azure notificação concentradores num ambiente empresarial"
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/29/2016" 
    ms.author="yuaxu"/>

# <a name="enterprise-push-architectural-guidance"></a>Orientações arquitetura de push Enterprise

Empresas hoje são gradualmente mover concreto aplicações móveis para ambos os respetivos utilizadores finais (externo) ou para os trabalhadores (internos). Possuem back-end sistemas existentes no local-lo de ser mainframes ou algumas aplicações LoB que devem ser integradas na arquitetura de aplicação móvel. Este guia irá falar sobre a melhor forma de fazer esta integração recomendar solução possível para cenários comuns.

É um requisito frequente para envio de notificações push para os utilizadores através da sua aplicação móvel quando ocorre um evento de interesse nos sistemas de back-end. Por exemplo um cliente de identificação bancária que tenha a aplicação de banca o banco no seu iPhone pretende ser notificado quando um débito é tornado acima de um determinado montante da sua conta ou de um cenário de intranet local onde pretende ser notificado quando receber um pedido de aprovação um empregado do departamento financeiro que tenha uma aplicação de aprovação de orçamento no seu Windows Phone.

Processamento de aprovação de conta bancária ou é provável que ser realizadas em algumas sistema de back-end que tem de iniciar uma push ao utilizador. Poderão existir vários esses sistemas de back-end que tem todos os construir o mesmo tipo de lógica para implementar push quando uma notificação de accionadores um evento. A complexidade aqui reside no integrar vários sistemas de back-end juntamente com um sistema de push única onde os utilizadores finais podem subscreveu o diferentes notificações e até mesmo poderá várias aplicações móveis, por exemplo, no caso de aplicações móveis intranet onde uma aplicação móvel poderá querer receber notificações de vários esses sistemas de back-end. Os sistemas de back-end não conhece ou saber da push semântica/tecnologia para que uma solução comum aqui tradicionais foi apresentar um componente de pesquisa os sistemas de back-end para qualquer eventos de interesse e que é o responsável pela enviar as mensagens de push para o cliente.
Aqui será falarmos sobre uma solução ainda melhor utilizando Azure Service Bus - modelo de tópico/subscrição que irá reduzir a complexidade ao efetuar a solução dimensionáveis.

Eis a arquitetura geral da solução (GRG com várias aplicações móveis mas igualmente aplicáveis quando existe apenas uma aplicação móvel)

## <a name="architecture"></a>Arquitetura

![][1]

A peça chave neste diagrama arquitetura é Azure Service Bus que fornece um modelo (mais informações sobre-lo no [serviço Bus Pub/Sub de programação]) de programação tópicos/subscrições. No auscultador, que neste caso, é móvel back-end (normalmente [Serviço móvel do Azure], que irá iniciar uma push para as aplicações móveis) não receber mensagens diretamente a partir dos sistemas de back-end mas em vez disso, temos uma camada de produção intermédia fornecida pela [Azure Service Bus] que permite aos back-end móvel receber mensagens de um ou mais sistemas de back-end. Um tópico de Bus serviço tem de ser criada para cada um dos sistemas back-end, por exemplo, conta, HR, finanças que que mostra basicamente são "tópicos" de interesse que iniciará mensagens sejam enviadas como notificação push. Os sistemas de back-end irão enviar mensagens para estes tópicos. Mobile back-end pode subscrever um ou mais tópicos através da criação de uma subscrição de Bus de serviço. Isto irá dão back-end móvel para receber uma notificação a partir do sistema de back-end correspondente. Back-end móvel continua a ouvir para mensagens no respetivas subscrições e assim que recebe uma mensagem, desativa novamente e envia-a como notificação a sua concentrador de notificação. Concentradores de notificação, em seguida, eventualmente entrega a mensagem para a aplicação móvel. Por isso, para resumir os componentes chave, temos mais:

1. Sistemas de back-end (sistemas LoB/legado)
    - Cria serviço Bus tópico
    - Envia a mensagem
2. Móvel back-end
    - Cria a subscrição do serviço
    - Recebe a mensagem (a partir do sistema de back-end)
    - Envia uma notificação para clientes (através do concentrador de notificação do Azure)
3. Aplicação móvel
    - Notificação de visualização e recebe

###<a name="benefits"></a>Benefícios:

1. Desacoplamento entre o recetor (serviço móvel do aplicação/através do Centro de notificação) e o remetente (sistemas de back-end) permite que os sistemas de back-end adicionais a ser integrados com o alterar mínima.
2. Isto torna também o cenário de várias aplicações móveis ser capaz de receber eventos de um ou mais sistemas de back-end.  

## <a name="sample"></a>Exemplo:

###<a name="prerequisites"></a>Pré-requisitos
Devem concluir os seguintes tutoriais para se familiarizar com os conceitos, bem como criação comuns e os passos de configuração:

1. A [programação de serviço Bus Pub/Sub] - Isto explica os detalhes de trabalhar com o serviço Bus tópicos/subscrições, como criar um espaço de nomes para conter tópicos/subscrições, como enviar e receber mensagens de-los.
2. [Notificação concentradores - Windows Universal tutorial] - Isto explica como configurar uma aplicação da loja Windows e utilizar concentradores de notificação para registar e, em seguida, receber notificações.

###<a name="sample-code"></a>Código de exemplo

O código de exemplo completo está disponível na [Notificação concentrador amostras]. Dividida em três componentes:

1. **EnterprisePushBackendSystem**

    um. Este projeto utiliza o pacote de Nuget *WindowsAzure.ServiceBus* e é baseado no [serviço Bus Pub/Sub de programação].

    b. Esta é uma simple c# consola aplicação para simular um sistema de LoB que inicia a mensagem para ser entregue a aplicação móvel.

        static void Main(string[] args)
        {
            string connectionString =
                CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the topic where we will send notifications
            CreateTopic(connectionString);

            // Send message
            SendMessage(connectionString);
        }

    c. `CreateTopic`é utilizada para criar o tópico de serviço Bus onde enviaremos mensagens.

        public static void CreateTopic(string connectionString)
        {
            // Create the topic if it does not exist already

            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.TopicExists(sampleTopic))
            {
                namespaceManager.CreateTopic(sampleTopic);
            }
        }

    d. `SendMessage`é utilizada para enviar as mensagens a este tópico Bus de serviço. Aqui vamos estão simplesmente a enviar um conjunto de mensagens aleatórios para o tópico periodicamente da amostra. Normalmente haverá um sistema de back-end que irá enviar mensagens quando ocorre um evento.

        public static void SendMessage(string connectionString)
        {
            TopicClient client =
                TopicClient.CreateFromConnectionString(connectionString, sampleTopic);

            // Sends random messages every 10 seconds to the topic
            string[] messages =
            {
                "Employee Id '{0}' has joined.",
                "Employee Id '{0}' has left.",
                "Employee Id '{0}' has switched to a different team."
            };

            while (true)
            {
                Random rnd = new Random();
                string employeeId = rnd.Next(10000, 99999).ToString();
                string notification = String.Format(messages[rnd.Next(0,messages.Length)], employeeId);

                // Send Notification
                BrokeredMessage message = new BrokeredMessage(notification);
                client.Send(message);

                Console.WriteLine("{0} Message sent - '{1}'", DateTime.Now, notification);

                System.Threading.Thread.Sleep(new TimeSpan(0, 0, 10));
            }
        }

2. **ReceiveAndSendNotification**

    um. Este projeto utiliza os pacotes *WindowsAzure.ServiceBus* e *Microsoft.Web.WebJobs.Publish* Nuget e é baseado no [serviço Bus Pub/Sub de programação].

    b. Este é outra c# consola aplicação que recomendamos executará como um [Azure WebJob] uma vez que este tenha que ser executado continuamente para ouvir para mensagens a partir dos sistemas LoB/back-end. Este será parte do seu telemóvel back-end.

        static void Main(string[] args)
        {
            string connectionString =
                     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

            // Create the subscription which will receive messages
            CreateSubscription(connectionString);

            // Receive message
            ReceiveMessageAndSendNotification(connectionString);
        }

    c. `CreateSubscription`é utilizada para criar uma subscrição do serviço Bus para o tópico onde o sistema de back-end irá enviar mensagens. Consoante o cenário de negócio, este componente irá criar um ou mais subscrições para tópicos correspondentes (por exemplo, algumas podem estar a receber mensagens do sistema de recursos humanos, algumas a partir do sistema de juros e assim sucessivamente)

        static void CreateSubscription(string connectionString)
        {
            // Create the subscription if it does not exist already
            var namespaceManager =
                NamespaceManager.CreateFromConnectionString(connectionString);

            if (!namespaceManager.SubscriptionExists(sampleTopic, sampleSubscription))
            {
                namespaceManager.CreateSubscription(sampleTopic, sampleSubscription);
            }
        }

    d. ReceiveMessageAndSendNotification é utilizada para Leia a mensagem a partir do tópico utilizar a sua subscrição e se a leitura for bem sucedida, em seguida, crie uma notificação (no cenário de exemplo numa notificação de alerta nativo do Windows) sejam enviadas para a aplicação móvel com o Azure notificação concentradores.

        static void ReceiveMessageAndSendNotification(string connectionString)
        {
            // Initialize the Notification Hub
            string hubConnectionString = CloudConfigurationManager.GetSetting
                    ("Microsoft.NotificationHub.ConnectionString");
            hub = NotificationHubClient.CreateClientFromConnectionString
                    (hubConnectionString, "enterprisepushservicehub");

            SubscriptionClient Client =
                SubscriptionClient.CreateFromConnectionString
                        (connectionString, sampleTopic, sampleSubscription);

            Client.Receive();

            // Continuously process messages received from the subscription
            while (true)
            {
                BrokeredMessage message = Client.Receive();
                var toastMessage = @"<toast><visual><binding template=""ToastText01""><text id=""1"">{messagepayload}</text></binding></visual></toast>";

                if (message != null)
                {
                    try
                    {
                        Console.WriteLine(message.MessageId);
                        Console.WriteLine(message.SequenceNumber);
                        string messageBody = message.GetBody<string>();
                        Console.WriteLine("Body: " + messageBody + "\n");

                        toastMessage = toastMessage.Replace("{messagepayload}", messageBody);
                        SendNotificationAsync(toastMessage);

                        // Remove message from subscription
                        message.Complete();
                    }
                    catch (Exception)
                    {
                        // Indicate a problem, unlock message in subscription
                        message.Abandon();
                    }
                }
            }
        }
        static async void SendNotificationAsync(string message)
        {
            await hub.SendWindowsNativeNotificationAsync(message);
        }

    "e". Para publicar tal como um **WebJob**, clique com o botão direito do rato sobre a solução no Visual Studio e selecione **Publicar como WebJob**

    ![][2]

    f. Selecione o seu perfil de publicação e criar um novo site de Azure se não existir já qual será o anfitrião este WebJob e assim que tiver o Web site, em seguida, **Publicar**.

    ![][3]

    g. Configure a tarefa de ser "Executar continuamente" para que quando inicia sessão no [Portal clássica Azure] deverá ver algo semelhante ao seguinte:

    ![][4]


3. **EnterprisePushMobileApp**

    um. Esta é uma aplicação da loja Windows que vai receber notificações de alerta de WebJob em execução como parte do seu telemóvel back-end e apresentá-la. Se baseia-se a [Notificação concentradores - tutorial Universal do Windows].  

    b. Certifique-se de que a aplicação estiver ativada para receber notificações de alerta.

    c. Certifique-se de que os controladores de notificação seguinte código de registo que está a ser denomina-se na aplicação de arranque (depois de substituir a *HubName* e *DefaultListenSharedAccessSignature*:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("[HubName]", "[DefaultListenSharedAccessSignature]");
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

### <a name="running-sample"></a>Exemplo a ser executado:

1. Certifique-se de que o seu WebJob está em execução com êxito e agendado para "Executar continuamente".
2. Execute **EnterprisePushMobileApp** que irá iniciar a aplicação da loja Windows.
3. Executar a aplicação de consola **EnterprisePushBackendSystem** que irá simular LoB back-end e começar a enviar mensagens e deverá ver notificações de alerta aparecem como o seguinte:

    ![][5]

4. As mensagens foram enviadas originalmente para tópicos de serviço Bus qual foi a ser monitorizadas subscrições do serviço Bus no seu trabalho Web. Assim que uma mensagem foi recebida, uma notificação foi criada e enviada para a aplicação móvel. Pode percorrer os registos de WebJob para confirmar o processamento quando acede à ligação de registos no [Azure clássica Portal] para a tarefa da Web:

    ![][6]

<!-- Images -->
[1]: ./media/notification-hubs-enterprise-push-architecture/architecture.png
[2]: ./media/notification-hubs-enterprise-push-architecture/WebJobsContextMenu.png
[3]: ./media/notification-hubs-enterprise-push-architecture/PublishAsWebJob.png
[4]: ./media/notification-hubs-enterprise-push-architecture/WebJob.png
[5]: ./media/notification-hubs-enterprise-push-architecture/Notifications.png
[6]: ./media/notification-hubs-enterprise-push-architecture/WebJobsLog.png

<!-- Links -->
[Exemplos de concentrador de notificação]: https://github.com/Azure/azure-notificationhubs-samples
[Serviço móvel do Azure]: http://azure.microsoft.com/documentation/services/mobile-services/
[Serviço Azure Bus]: http://azure.microsoft.com/documentation/articles/fundamentals-service-bus-hybrid-solutions/
[Programação do serviço Bus Pub/Sub]: http://azure.microsoft.com/documentation/articles/service-bus-dotnet-how-to-use-topics-subscriptions/
[Azure WebJob]: http://azure.microsoft.com/documentation/articles/web-sites-create-web-jobs/
[Notificação concentradores - tutorial Universal do Windows]: http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Azure Portal clássico]: https://manage.windowsazure.com/
