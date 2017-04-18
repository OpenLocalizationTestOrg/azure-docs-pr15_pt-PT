<properties
    pageTitle="Introdução ao Azure Cativação móvel para Xamarin.iOS"
    description="Saiba como utilizar Azure Mobile Cativação com as notificações de emissão e de análise Xamarin.iOS aplicações para."
    services="mobile-engagement"
    documentationCenter="xamarin"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="get-started-with-azure-mobile-engagement-for-xamarinios-apps"></a>Introdução ao Azure Cativação móvel para as aplicações de Xamarin.iOS

[AZURE.INCLUDE [Hero tutorial switcher](../../includes/mobile-engagement-hero-tutorial-switcher.md)]

Este tópico mostra-lhe como utilizar o Azure Mobile Cativação para compreender a utilização da aplicação e enviar as notificações push aos utilizadores segmentados numa aplicação Xamarin.iOS.
Neste tutorial, criar uma aplicação de Xamarin.iOS em branco que recolhe dados básico e recebe as notificações push utilizando o sistema de notificações Push (APNS) da Apple.

Neste tutorial requer o seguinte:

+ [Xamarin Studio](http://xamarin.com/studio). Também pode utilizar o Visual Studio com Xamarin mas este tutorial utiliza Xamarin Studio. Para obter instruções de instalação, consulte o artigo [configurar e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). 
+ [Cativação móvel Xamarin SDK](https://www.nuget.org/packages/Microsoft.Azure.Engagement.Xamarin/)

> [AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fmobile-engagement-xamarin-ios-get-started).

##<a id="setup-azme"></a>Configuração móvel Cativação para a sua aplicação iOS

[AZURE.INCLUDE [Create Mobile Engagement App in Portal](../../includes/mobile-engagement-create-app-in-portal-new.md)]

##<a id="connecting-app"></a>Ligar a sua aplicação ao Mobile Cativação back-end

Neste tutorial apresenta uma "integração básica" qual é o mínimas necessárias para recolher dados e enviar uma notificação de emissão do conjunto.

Vamos irá criar uma aplicação básica com Xamarin para demonstrar a integração:

###<a name="create-a-new-xamarinios-project"></a>Criar um novo projeto de Xamarin.iOS

1. Inicie o Xamarin Studio. Aceda a **ficheiro** -> **Novo** -> **solução** 

    ![][1]

2. Selecione **Única vista de aplicação**, certifique-se que o idioma selecionado é **c#** e, em seguida, clique em **seguinte**.

    ![][2]

3. Preencha o **Nome da aplicação** e o **Identificador de organização** e, em seguida, clique em **seguinte**. 

    ![][3]

    > [AZURE.IMPORTANT] Certifique-se de que ao perfil publicação que acaba por ser utilizado para implementar a sua aplicação iOS está a utilizar um ID da aplicação que corresponda exatamente com o identificador de pacote aqui, tem. 

4. Atualize o **Nome do projeto**, o **Nome da solução** e a **localização** , se necessário e clique em **Criar**.

    ![][4]
 
Xamarin Studio irá criar a aplicação de demonstração na qual podemos integrar o Mobile Cativação. 

###<a name="connect-your-app-to-mobile-engagement-backend"></a>Ligar a sua aplicação ao back-end de Cativação Mobile

1. Clique com o botão direito na pasta de **pacotes** nas janelas de solução e selecione **Adicionar pacotes...**

    ![][5]

2. Procure o **Microsoft Azure Mobile Cativação Xamarin SDK** e adicioná-lo a sua solução.  

    ![][6]
   
3. Abra **AppDelegate.cs** e adicione o seguinte utilizando instrução:

        using Microsoft.Azure.Engagement.Xamarin;

4. Método de **FinishedLaunching** , adicione o seguinte para iniciar a ligação com back-end de Cativação Mobile. Certifique-se adicionar o seu **ConnectionString**. Este código também utiliza fictícios **NotificationIcon** que é adicionado pelo SDK Cativação Mobile que pretende substituir. 

        EngagementConfiguration config = new EngagementConfiguration {
                        ConnectionString = "YourConnectionStringFromAzurePortal",
                        NotificationIcon = UIImage.FromBundle("close")
                    };
        EngagementAgent.Init (config);

##<a id="monitor"></a>Activar a monitorização em tempo real

Para poder começar a enviar dados e garantindo que os utilizadores não estão ativos, terá de enviar, pelo menos, um ecrã para o back-end Cativação Mobile.

1. Abra **ViewController.cs** e adicione o seguinte utilizando instrução:

        using Microsoft.Azure.Engagement.Xamarin;

2. Substituir a classe a partir do qual `ViewController` herda `UIViewController` para `EngagementViewController`. 

##<a id="monitor"></a>Ligar a aplicação com monitorização em tempo real

[AZURE.INCLUDE [Connect app with real-time monitoring](../../includes/mobile-engagement-connect-app-with-monitor.md)]

##<a id="integrate-push"></a>Ativar as notificações push e mensagens de na aplicação

Cativação Mobile permite-lhe interagir com os seus utilizadores e ALCANÇAR com as notificações push e mensagens no contexto das campanhas de na aplicação. Este módulo chama alcance no portal do Cativação Mobile.
As secções seguintes configurar a sua aplicação para recebê-las.

### <a name="modify-your-application-delegate"></a>Modificar o seu delegado de aplicação

1. Abra o **AppDelegate.cs** e adicione o seguinte utilizando instrução:

        using System; 

2. Agora dentro de `FinishedLaunching` método, adicione o seguinte para se registar no mensagens push depois de`EngagementAgent.init(...)`

        if (UIDevice.CurrentDevice.CheckSystemVersion(8,0))
        {
            var pushSettings = UIUserNotificationSettings.GetSettingsForTypes (
                (UIUserNotificationType.Badge |
                    UIUserNotificationType.Sound |
                    UIUserNotificationType.Alert),
                null);
            UIApplication.SharedApplication.RegisterUserNotificationSettings (pushSettings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications ();
        }
        else
        {
            UIApplication.SharedApplication.RegisterForRemoteNotificationTypes (
                UIRemoteNotificationType.Badge |
                UIRemoteNotificationType.Sound |
                UIRemoteNotificationType.Alert);
        }

3. Por fim - atualizar ou adicionar seguintes métodos:

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, 
            Action<UIBackgroundFetchResult> completionHandler)
        {
            EngagementAgent.ApplicationDidReceiveRemoteNotification(userInfo, completionHandler);
        }

        public override void RegisteredForRemoteNotifications (UIApplication application, NSData deviceToken)
        {
            // Register device token on Engagement
            EngagementAgent.RegisterDeviceToken(deviceToken);
        }

        public override void FailedToRegisterForRemoteNotifications(UIApplication application, NSError error)
        {
            Console.WriteLine("Failed to register for remote notifications: Error '{0}'", error);
        }

4. No ficheiro **Info.plist** da solução, confirme que o **Identificador de pacote** corresponde com o **ID da aplicação** de que tem no seu perfil de aprovisionamento no Centro de Dev Center do Apple. 

    ![][7]

5. No mesmo ficheiro **Info.plist** , certifique-se de que tem selecionada a **Ativar modos de fundo** e **Notificações remoto**. 

    ![][8]

6. Execute a aplicação no dispositivo que tiver associadas com este perfil de publicação. 

[AZURE.INCLUDE [mobile-engagement-ios-send-push-push](../../includes/mobile-engagement-ios-send-push.md)]

<!-- Images. -->
[1]: ./media/mobile-engagement-xamarin-ios-get-started/new-solution.png
[2]: ./media/mobile-engagement-xamarin-ios-get-started/app-type.png
[3]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-name.png
[4]: ./media/mobile-engagement-xamarin-ios-get-started/configure-project-confirm.png
[5]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget.png
[6]: ./media/mobile-engagement-xamarin-ios-get-started/add-nuget-azme.png
[7]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-confirm-bundle.png
[8]: ./media/mobile-engagement-xamarin-ios-get-started/info-plist-configure-push.png
