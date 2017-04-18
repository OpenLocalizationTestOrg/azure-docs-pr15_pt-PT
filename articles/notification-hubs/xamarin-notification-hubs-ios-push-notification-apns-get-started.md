<properties
    pageTitle="iOS as notificações de emissão com concentradores de notificação para as aplicações de Xamarin | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do iOS Xamarin."
    services="notification-hubs"
    keywords="notificações, push mensagens, de emissão de IOS notificações de emissão, mensagem de emissão"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="ios-push-notifications-with-notification-hubs-for-xamarin-apps"></a>iOS as notificações de emissão com concentradores de notificação para as aplicações de Xamarin

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral
> [AZURE.IMPORTANT] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-ios-get-started).

Este tutorial mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do iOS.
Crie uma aplicação de Xamarin.iOS em branco que recebe as notificações push utilizando o [Serviço de notificações Push da Apple (APNs)](https://developer.apple.com/library/ios/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html). Quando tiver terminado, poderá utilizar o seu centro de notificação para difundir notificações push para todos os dispositivos de executar a sua aplicação. O código terminado está disponível na [aplicação NotificationHubs] [ GitHub] amostra.

Neste tutorial demonstra o cenário de difusão de mensagem push simples com concentradores de notificação.

##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ [Xcode 6.0][Install Xcode]
+ Um iOS 7.0 (ou versão posterior) dispositivo compatível
+ iOS associação do programa de programador
+ [Xamarin Studio]

   > [AZURE.NOTE] Devido a requisitos de configuração de notificações, de emissão iOS tem implementar e testar a aplicação de exemplo num dispositivo iOS física (iPhone ou iPad) em vez de no simulator.

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de notificação concentradores para Xamarin iOS aplicações.


[AZURE.INCLUDE [Notification Hubs Enable Apple Push Notifications](../../includes/notification-hubs-enable-apple-push-notifications.md)]


##<a name="configure-your-notification-hub"></a>Configurar o seu centro de notificação

Esta secção apresenta-lhe através da criação de um concentrador de notificação de novo e configurar a autenticação com APNS utilizando o certificado de push **. p12** que criou. Se pretender utilizar um concentrador de notificação que já criou, pode avançar para o passo 5.

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li>
<p>Como queremos configurar a ligação de APNS, no Portal do Azure, abra as definições de notificação concentrador, ande clique nos <b>Serviços de notificação</b>e, em seguida, clique no item de <b>Apple (APNS)</b> na lista. Uma vez feito, clique em <b>Carregar certificado</b> e selecione o certificado <b>. p12</b> que exportou versões anteriores, assim como a palavra-passe para o certificado.</p>
<p>Certifique-se selecionar o modo <b>Sandbox</b> , uma vez que irá enviar mensagens de push num ambiente de desenvolvimento. Utilize a definição de <b>produção</b> apenas se pretender enviar notificações push para os utilizadores que já adquiriu a sua aplicação a partir da loja.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-apns.png)

&emsp;&emsp;![](./media/notification-hubs-ios-get-started/notification-hubs-sandbox.png)


O concentrador de notificação agora está configurado para funcionar com APNS e tiver as cadeias de ligação para registar a sua aplicação e enviar as notificações push.


##<a name="connect-your-app-to-the-notification-hub"></a>Ligar a sua aplicação para o concentrador de notificação

#### <a name="create-a-new-project"></a>Criar um novo projeto

1. No Xamarin Studio, criar um novo projeto do iOS e selecione a **Unified API** > **Única aplicação da vista de** modelo.

    ![Xamarin Studio - tipo de aplicação selecione][31]

2. Adicione uma referência para o componente de mensagens do Azure. Na vista de solução, com o botão direito na pasta de **componentes** para o seu projeto e selecione **Obter mais componentes**. Procure o componente de **Mensagens do Azure** e adicione o componente ao seu projeto.

3. No **AppDelegate.cs**, adicione o seguinte utilizando instrução:

        using WindowsAzure.Messaging;

4. Declare uma instância da **SBNotificationHub**:

        private SBNotificationHub Hub { get; set; }

5. Crie uma classe **Constants.cs** com variáveis que se seguem:

        // Azure app-specific connection string and hub path
        public const string ConnectionString = "<Azure connection string>";
        public const string NotificationHubPath = "<Azure hub path>";


6. No **AppDelegate.cs**, atualize **FinishedLaunching()** para fazer corresponder o seguinte procedimento:

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            if (UIDevice.CurrentDevice.CheckSystemVersion (8, 0)) {
                var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                       UIUserNotificationType.Alert | UIUserNotificationType.Badge | UIUserNotificationType.Sound,
                       new NSSet ());

                UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
                UIApplication.SharedApplication.RegisterForRemoteNotifications ();
            } else {
                UIRemoteNotificationType notificationTypes = UIRemoteNotificationType.Alert | UIRemoteNotificationType.Badge | UIRemoteNotificationType.Sound;
                UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (notificationTypes);
            }

            return true;
        }

7. Substitua o método de **RegisteredForRemoteNotifications()** no **AppDelegate.cs**:

        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            Hub = new SBNotificationHub(Constants.ConnectionString, Constants.NotificationHubPath);

            Hub.UnregisterAllAsync (deviceToken, (error) => {
                if (error != null)
                {
                    Console.WriteLine("Error calling Unregister: {0}", error.ToString());
                    return;
                }

                NSSet tags = null; // create tags if you want
                Hub.RegisterNativeAsync(deviceToken, tags, (errorCallback) => {
                    if (errorCallback != null)
                        Console.WriteLine("RegisterNativeAsync error: " + errorCallback.ToString());
                });
            });
        }

8. Substitua o método de **ReceivedRemoteNotification()** no **AppDelegate.cs**:

        public override void ReceivedRemoteNotification(UIApplication application, NSDictionary userInfo)
        {
            ProcessNotification(userInfo, false);
        }

9. Crie o seguinte método **ProcessNotification()** **AppDelegate.cs**:

        void ProcessNotification(NSDictionary options, bool fromFinishedLaunching)
        {
            // Check to see if the dictionary has the aps key.  This is the notification payload you would have sent
            if (null != options && options.ContainsKey(new NSString("aps")))
            {
                //Get the aps dictionary
                NSDictionary aps = options.ObjectForKey(new NSString("aps")) as NSDictionary;

                string alert = string.Empty;

                //Extract the alert text
                // NOTE: If you're using the simple alert by just specifying
                // "  aps:{alert:"alert msg here"}  ", this will work fine.
                // But if you're using a complex alert with Localization keys, etc.,
                // your "alert" object from the aps dictionary will be another NSDictionary.
                // Basically the JSON gets dumped right into a NSDictionary,
                // so keep that in mind.
                if (aps.ContainsKey(new NSString("alert")))
                    alert = (aps [new NSString("alert")] as NSString).ToString();

                //If this came from the ReceivedRemoteNotification while the app was running,
                // we of course need to manually process things like the sound, badge, and alert.
                if (!fromFinishedLaunching)
                {
                    //Manually show an alert
                    if (!string.IsNullOrEmpty(alert))
                    {
                        UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                        avAlert.Show();
                    }
                }
            }
        }

    > [AZURE.NOTE] Pode escolher substituir **FailedToRegisterForRemoteNotifications()** a lidar com situações como sem ligação à rede. O que é particularmente importante onde o utilizador poderá iniciar a aplicação em modo offline (por exemplo, um avião) e pretende lidar com push mensagens cenários específicos para a sua aplicação.


10. Execute a aplicação no seu dispositivo.


## <a name="sending-push-notifications"></a>Enviar notificações Push


Pode testar a receber notificações push na sua aplicação ao enviar notificações no [Portal do Azure] através da **Testar enviar** capacidade do conjunto de ferramentas de **resolução de problemas** , para a direita na página de concentrador de notificação, conforme mostrado no ecrã abaixo.

![](./media/notification-hubs-ios-get-started/notification-hubs-test-send.png)

Notificações push normalmente são enviadas através de um serviço de back-end como serviços Mobile ou ASP.NET através de uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de push se uma biblioteca não está disponível no seu cenário. 

Neste tutorial, iremos mantenha-a simples e apenas demonstram testar a sua aplicação cliente ao enviar notificações utilizando o .NET SDK para concentradores notificação numa aplicação de consola em vez de um serviço de back-end. Recomendamos que o tutorial [Concentradores de notificação de utilização para as notificações push aos utilizadores](notification-hubs-aspnet-backend-ios-apple-apns-notification.md) como o próximo passo para o envio de notificações a partir de um back-end ASP.NET. No entanto, as seguintes abordagens podem ser utilizadas para o envio de notificações:

* **Interface do resto**: pode suportar notificações push em nenhuma plataforma de back-end utilizando a [interface de descanso](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure notificação concentradores .NET SDK**: no Nuget pacote Gestor de para Visual Studio, executar o [Pacote de instalação Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [como utilizar concentradores de notificação de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

**Aplicações móveis**: para um exemplo sobre como enviar notificações a partir de um back-end de aplicações móveis do Azure aplicação serviço que está integrado com o concentradores de notificação, consulte o artigo [Adicionar as notificações push para a sua aplicação móvel](../app-service-mobile/app-service-mobile-ios-get-started-push.md).

* **Java / PHP**: para um exemplo sobre como enviar notificações push utilizando as API REST, consulte o artigo "Como utilizar concentradores de notificação de Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).


####<a name="optional-send-push-notifications-from-a-net-console-app"></a>(Opcional) Enviar notificações Push através de uma aplicação de consola do .NET

Nesta secção, enviaremos notificações push ao utilizar uma aplicação de consola do .NET simple. Para efeitos neste exemplo, vamos irá mudar para um ambiente de desenvolvimento do Windows que tem o Visual Studio já instalada.

1. No Visual Studio, crie uma nova aplicação de consola do Visual c#:

    ![Visual Studio - criar uma nova aplicação de consola][213]

2. No Visual Studio, clique em **Ferramentas**, clique em **Gestor de pacote NuGet**e, em seguida, clique em **Consola do Gestor de pacote**.

    Consola do Gestor de pacote deverão aparecer ancorada para o fim da sua área de trabalho do Visual Studio.

3. Na janela da consola do Gestor de pacote, definir **predefinidos do project** para o novo projeto de aplicação de consola e, em seguida, na janela da consola, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência para o SDK do Azure notificação concentradores utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote de Microsoft.Azure.Notification concentradores NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)


4. Abrir o `Program.cs` ficheiro e adicione o seguinte `using` instrução, garantindo que pode utilizamos Azure classes e funções dentro da sua aula principal:

        using Microsoft.Azure.NotificationHubs;

3. No seu `Program` classe, adicione o seguinte método (não se esqueça substituir a **cadeia de ligação** e o **nome de concentrador**):

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var alert = "{\"aps\":{\"alert\":\"Hello from .NET!\"}}";
            await hub.SendAppleNativeNotificationAsync(alert);
        }

4. Adicione as seguintes linhas no seu `Main` método:

         SendNotificationAsync();
         Console.ReadLine();

5. Prima a tecla F5 para executar a aplicação. Em segundos, deverá ver uma notificação de emissão aparecem no seu dispositivo. Se estiver a utilizar uma rede de dados móveis ou Wi-Fi, certifique-se de que tem uma ligação à internet ativa no dispositivo.

Pode encontrar os possíveis payloads na Apple no [Local e guia de programação de notificação de notificações Push].

####<a name="optional-send-notifications-from-a-mobile-service"></a>(Opcional) Enviar notificações a partir de um serviço móvel

Nesta secção, enviaremos notificações push utilizar um serviço móvel através de um script nó.

Para enviar uma notificação ao utilizar um serviço móvel, siga a [começar a trabalhar com serviços Mobile]e, em seguida:

1. Inicie sessão no [Portal clássica do Azure]e, selecione o seu serviço móvel.

2. Selecione o separador **Programador** , no canto superior.

    ![Portal clássica Azure - Programador][215]

3. Criar uma nova tarefa agendada, insira um nome e selecione **a pedido**.

    ![Azure clássica Portal - criar nova tarefa][216]

4. Quando a tarefa é criada, clique no nome da tarefa. Em seguida, clique no separador de **Script** na barra superior.

5. Inserir o seguinte script dentro de uma função programador. Certifique-se substituir os marcadores de posição com o seu nome de concentrador de notificação e a cadeia de ligação para *DefaultFullSharedAccessSignature* que obteve anterior. Clique em **Guardar**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<Hubname>', '<SAS Full access >');
        notificationHubService.apns.send(
            null,
            {"aps":
                {
                "alert": "Hello from Mobile Services!"
                }
            },
            function (error)
            {
                if (!error) {
                    console.warn("Notification successful");
                }
            }
        );


6. Clique em **Executar uma vez** na barra inferior. Deverá receber um alerta no seu dispositivo.

##<a name="next-steps"></a>Próximos passos

Neste exemplo simples, difundida notificações push para todos os seus dispositivos iOS. Para utilizadores específicos de destino, consulte o tutorial [Concentradores de notificação de utilização para as notificações push para os utilizadores]. Se pretender segmentar os seus utilizadores por grupos de interesse, pode ser lido [Concentradores de notificação de utilização para enviar notícias de última hora]. Saiba mais sobre como utilizar concentradores de notificação na [Notificação concentradores orientação] e, a [Notificação concentradores instruções para iOS].


<!-- Images. -->

[213]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-console-app.png

[215]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler1.png
[216]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-scheduler2.png


[31]: ./media/partner-xamarin-notification-hubs-ios-get-started/notification-hub-create-ios-app.png




<!-- URLs. -->
[Mobile Services iOS SDK]: http://go.microsoft.com/fwLink/?LinkID=266533
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253

[Começar a trabalhar com serviços Mobile]: /develop/mobile/tutorials/get-started-xamarin-ios
[Azure Portal clássico]: https://manage.windowsazure.com/
[Notificação concentradores orientações]: http://msdn.microsoft.com/library/jj927170.aspx
[Notificação concentradores instruções para iOS]: http://msdn.microsoft.com/library/jj927168.aspx
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[iOS Provisioning Portal]: http://go.microsoft.com/fwlink/p/?LinkId=272456

[Utilizar concentradores de notificação para as notificações push para os utilizadores]: /manage/services/notification-hubs/notify-users-aspnet
[Utilizar concentradores de notificação para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet

[Local e guia de programação de notificações de emissão]: http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/Chapters/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW1
[Apple Push Notification Service]: http://go.microsoft.com/fwlink/p/?LinkId=272584

[Azure Mobile Services Component]: http://components.xamarin.com/view/azure-mobile-services/
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Xamarin Studio]: http://xamarin.com/download
[WindowsAzure.Messaging]: https://github.com/infosupport/WindowsAzure.Messaging.iOS
[Portal do Azure]: https://portal.azure.com
