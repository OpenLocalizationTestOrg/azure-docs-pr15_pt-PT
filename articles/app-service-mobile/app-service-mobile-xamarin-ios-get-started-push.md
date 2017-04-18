<properties
    pageTitle="Adicionar as notificações push para a sua aplicação Xamarin.iOS com o serviço de aplicação do Azure"
    description="Saiba como utilizar a aplicação de serviço de Azure para enviar notificações push para a sua aplicação Xamarin.iOS"
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinios-app"></a>Adicionar as notificações push para a sua aplicação Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Descrição geral

Neste tutorial, adicione as notificações push ao projeto [Xamarin.iOS rápida iniciar](app-service-mobile-xamarin-ios-get-started.md) para que uma notificação de emissão é enviada para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, é necessário o pacote de extensão de notificações push. Consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="prerequisites"></a>Pré-requisitos

* Conclua o tutorial [Xamarin.iOS guia de introdução](app-service-mobile-xamarin-ios-get-started.md) .

* Um dispositivo iOS física. Notificações push não são suportadas pelo simulator iOS.

##<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registe-se a aplicação para as notificações push no portal de programador da Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

##<a name="configure-your-mobile-app-to-send-push-notifications"></a>Configurar a sua aplicação Mobile para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Actualizar o servidor de projecto para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="configure-your-xamarinios-project"></a>Configurar o seu projeto Xamarin.iOS

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]

##<a name="add-push-notifications-to-your-app"></a>Adicionar as notificações push para a sua aplicação

1. No **QSTodoService**, adicione a propriedade seguinte para que **AppDelegate** poderem adquirir o cliente móvel:

            public MobileServiceClient GetClient {
            get
            {
                return client;
            }
            private set
            {
                client = value;
            }
        }

1. Adicione o seguinte `using` declaração para a parte superior do ficheiro **AppDelegate.cs** .

        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;

2. No **AppDelegate**, substitui o evento **FinishedLaunching** :

        public override bool FinishedLaunching(UIApplication application, NSDictionary launchOptions)
        {
            // registers for push for iOS8
            var settings = UIUserNotificationSettings.GetSettingsForTypes(
                UIUserNotificationType.Alert
                | UIUserNotificationType.Badge
                | UIUserNotificationType.Sound,
                new NSSet());

            UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
            UIApplication.SharedApplication.RegisterForRemoteNotifications();

            return true;
        }

3. No mesmo ficheiro, substitui o evento **RegisteredForRemoteNotifications** . Este código está a registar para uma notificação de modelo simples que será enviada através de todas as plataformas suportadas pelo servidor.

    Para mais informações sobre modelos com concentradores de notificação, consulte o artigo [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).


        public override void RegisteredForRemoteNotifications(UIApplication application, NSData deviceToken)
        {
            MobileServiceClient client = QSTodoService.DefaultService.GetClient;

            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyAPNS}
            };

            // Register for push with your mobile app
            var push = client.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }


4. Em seguida, substitui o evento **DidReceivedRemoteNotification** :

        public override void DidReceiveRemoteNotification (UIApplication application, NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps [new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

Agora, a aplicação é atualizada para suportar as notificações push.

## <a name="test"></a>Teste as notificações push na sua aplicação

1. Prima o botão **Executar** para criar o projecto e iniciar a aplicação num dispositivo com iOS, em seguida, clique em **OK** para aceitar as notificações push.

    > [AZURE.NOTE] Explicitamente tem de aceitar as notificações push da sua aplicação. Este pedido só ocorre a primeira vez que a aplicação é executado.

2. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição (**+**) ícone.

3. Certifique-se de que uma notificação for recebida, em seguida, clique em **OK** para dispensar a notificação.

4. Repita o passo 2 e imediatamente fecha a aplicação, em seguida, certifique-se de que é apresentada uma notificação.

Neste tutorial foi concluído com êxito.

<!-- Images. -->

<!-- URLs. -->



