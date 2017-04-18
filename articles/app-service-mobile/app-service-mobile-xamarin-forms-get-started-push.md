<properties
    pageTitle="Adicionar as notificações push para a sua aplicação Xamarin.Forms | Microsoft Azure"
    description="Saiba como utilizar os serviços Azure para enviar notificações push plataforma com várias as suas aplicações Xamarin.Forms."
    services="app-service\mobile"
    documentationCenter="xamarin"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-xamarinforms-app"></a>Adicionar as notificações push para a sua aplicação Xamarin.Forms

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Descrição geral

Neste tutorial, adicione push notificações para todos os projetos resultaram a partir do [Xamarin.Forms rápida iniciar](app-service-mobile-xamarin-forms-get-started.md) para que seja enviada uma notificação de emissão para todos os clientes em diferentes plataformas, sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, é necessário o pacote de extensão de notificações push. Consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="prerequisites"></a>Pré-requisitos

* Para iOS, terá de um [programa de programador do Apple associação](https://developer.apple.com/programs/ios/) e um dispositivo iOS física porque o [iOS simulator não suporta as notificações push](https://developer.apple.com/library/ios/documentation/IDEs/Conceptual/iOS_Simulator_Guide/TestingontheiOSSimulator.html).

##<a name="configure-hub"></a>Configurar um concentrador de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="update-the-server-project-to-send-push-notifications"></a>Actualizar o servidor de projecto para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


##<a name="optional-configure-and-run-the-android-project"></a>(Opcional) Configurar e executar o projecto Android

Conclua nesta secção para ativar as notificações push para o projeto Xamarin.Forms Droid para Android.


###<a name="enable-firebase-cloud-messaging-fcm"></a>Ativar a nuvem Firebase mensagens (FCM)

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

###<a name="configure-the-mobile-app-backend-to-send-push-requests-using-fcm"></a>Configurar o aplicação Mobile back-end para enviar pedidos de push utilizando FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

###<a name="add-push-notifications-to-the-android-project"></a>Adicionar as notificações push para o projeto Android

Com o back-end configurado com FCM, podemos pode adicionar componentes códigos para o cliente para registar FCM, registe-se para as notificações push com concentradores de notificação do Azure através da aplicação móvel back-end e receber notificações.

1. No **Droid** project, com o botão direito na pasta de **componentes** , clique em **Obter mais componentes...**, procure o componente do **Cliente de mensagens do Google Cloud** e adicioná-lo ao projeto. Este componente suporta as notificações push para um projeto Xamarin Android.


2. Abra o ficheiro de projeto MainActivity.cs e adicione o seguinte utilizando instrução na parte superior do ficheiro:

        using Gcm.Client;

3. Adicione o seguinte código para o método **OnCreate** depois da chamada para **LoadApplication**:

        try
        {
            // Check to ensure everything's setup right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            System.Diagnostics.Debug.WriteLine("Registering...");
            GcmClient.Register(this, PushHandlerBroadcastReceiver.SENDER_IDS);
        }
        catch (Java.Net.MalformedURLException)
        {
            CreateAndShowDialog("There was an error creating the client. Verify the URL.", "Error");
        }
        catch (Exception e)
        {
            CreateAndShowDialog(e.Message, "Error");
        }


4. Adicione um novo método de helper **CreateAndShowDialog** , da seguinte forma:

        private void CreateAndShowDialog(String message, String title)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);

            builder.SetMessage (message);
            builder.SetTitle (title);
            builder.Create().Show ();
        }


5. Adicione o seguinte código para a classe de **MainActivity** :

        // Create a new instance field for this activity.
        static MainActivity instance = null;

        // Return the current activity instance.
        public static MainActivity CurrentActivity
        {
            get
            {
                return instance;
            }
        }

    Isto expõe a instância atual do **MainActivity** , de modo que podem ser executados no tópico de IU principal.

6. Iniciar o `instance`, variável no início do método **OnCreate** , da seguinte forma.

        // Set the current instance of MainActivity.
        instance = this;

2. Adicionar um novo ficheiro de classe ao projeto **Droid** denominado `GcmService.cs`e certifique-se as seguintes instruções de **utilizar** estão presentes na parte superior do ficheiro:

        using Android.App;
        using Android.Content;
        using Android.Media;
        using Android.Support.V4.App;
        using Android.Util;
        using Gcm.Client;
        using Microsoft.WindowsAzure.MobileServices;
        using Newtonsoft.Json.Linq;
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Text;


9. Adicione os seguintes pedidos de permissão na parte superior do ficheiro, após as declarações de **utilizar** e antes da declaração de **espaço de nomes** .

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]
        //GET_ACCOUNTS is only needed for android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]

10. Adicione a definição de classe seguinte para o espaço de nomes. 

        [BroadcastReceiver(Permission = Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK }, Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY }, Categories = new string[] { "@PACKAGE_NAME@" })]
        public class PushHandlerBroadcastReceiver : GcmBroadcastReceiverBase<GcmService>
        {
            public static string[] SENDER_IDS = new string[] { "<PROJECT_NUMBER>" };
        }

    >[AZURE.NOTE]Substitua **< PROJECT_NUMBER >** com o seu número de projeto indicados anteriormente.   

11. Substitua a classe de **GcmService** vazia com o código seguinte, que utiliza no auscultador de difusão novo:

         [Service]
         public class GcmService : GcmServiceBase
         {
             public static string RegistrationID { get; private set; }

             public GcmService()
                 : base(PushHandlerBroadcastReceiver.SENDER_IDS){}
         }


12. Adicione o seguinte código para a classe de **GcmService** que substitui o processador de eventos **OnRegistered** e implemente um método de **Registar** .

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose("PushHandlerBroadcastReceiver", "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            var push = TodoItemManager.DefaultManager.CurrentClient.GetPush();

            MainActivity.CurrentActivity.RunOnUiThread(() => Register(push, null));
        }

        public async void Register(Microsoft.WindowsAzure.MobileServices.Push push, IEnumerable<string> tags)
        {
            try
            {
                const string templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";

                JObject templates = new JObject();
                templates["genericMessage"] = new JObject
                {
                    {"body", templateBodyGCM}
                };

                await push.RegisterAsync(RegistrationID, templates);
                Log.Info("Push Installation Id", push.InstallationId.ToString());
            }
            catch (Exception ex)
            {
                System.Diagnostics.Debug.WriteLine(ex.Message);
                Debugger.Break();
            }
        }

        Note that this code uses the `messageParam` parameter in the template registration. 

13. Adicione o seguinte código que implementa **OnMessage**: 

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info("PushHandlerBroadcastReceiver", "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            //Store the message
            var prefs = GetSharedPreferences(context.PackageName, FileCreationMode.Private);
            var edit = prefs.Edit();
            edit.PutString("last_msg", msg.ToString());
            edit.Commit();

            string message = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty(message))
            {
                createNotification("New todo item!", "Todo item: " + message);
                return;
            }

            string msg2 = intent.Extras.GetString("msg");
            if (!string.IsNullOrEmpty(msg2))
            {
                createNotification("New hub message!", msg2);
                return;
            }

            createNotification("Unknown message details", msg.ToString());
        }

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show ui
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Use Notification Builder
            NotificationCompat.Builder builder = new NotificationCompat.Builder(this);

            //Create the notification
            //we use the pending intent, passing our ui intent over which will get called
            //when the notification is tapped.
            var notification = builder.SetContentIntent(PendingIntent.GetActivity(this, 0, uiIntent, 0))
                    .SetSmallIcon(Android.Resource.Drawable.SymActionEmail)
                    .SetTicker(title)
                    .SetContentTitle(title)
                    .SetContentText(desc)

                    //Set the notification sound
                    .SetSound(RingtoneManager.GetDefaultUri(RingtoneType.Notification))

                    //Auto cancel will remove the notification once the user touches it
                    .SetAutoCancel(true).Build();

            //Show the notification
            notificationManager.Notify(1, notification);
        }

    Isto trata receber notificações e enviá-los para o Gestor de notificação para ser apresentado.

14. **GcmServiceBase** requer que implementar os métodos de processador **OnUnRegistered** e **AoOcorrerErro** , que pode efetuar da seguinte forma:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "Unregistered RegisterationId : " + registrationId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error("PushHandlerBroadcastReceiver", "GCM Error: " + errorId);
        }

Agora, está pronto teste as notificações push na aplicação em execução no dispositivo Android ou o emulador.

###<a name="test-push-notifications-in-your-android-app"></a>Teste as notificações push na sua aplicação Android

Os dois primeiros passos são necessários apenas durante o teste num emulador.

1. Certifique-se de que está a implementar ou depuração num dispositivo virtual que tenha APIs Google definir como alvo, conforme apresentado abaixo no Gestor de dispositivo Virtual Android (AVD).

2. Adicionar uma conta do Google para o dispositivo Android ao clicar em **aplicações** > **Definições** > **Adicionar conta**, em seguida, siga os pedidos para utilizar Adicionar uma conta existente do Google para o dispositivo para criar um novo.

1. No Visual Studio ou Xamarin Studio, clique com o botão direito do rato em projeto **Droid** e clique em **Definir como projeto de arranque**.

2. Prima o botão **Executar** para criar o projecto e iniciar a aplicação no seu dispositivo Android ou emulador.

3. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição (**+**) ícone.

4. Certifique-se de que o é recebida uma notificação quando um item é adicionado.


##<a name="optional-configure-and-run-the-ios-project"></a>(Opcional) Configurar e executar o iOS projecto

Esta secção destina-se a executar o iOS Xamarin projeto para dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]


####<a name="configure-the-notification-hub-for-apns"></a>Configurar o concentrador de notificação para APNS

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

Seguinte irá configurar a definição de projeto iOS no Xamarin Studio ou Visual Studio.

[AZURE.INCLUDE [app-service-mobile-xamarin-ios-configure-project](../../includes/app-service-mobile-xamarin-ios-configure-project.md)]


####<a name="add-push-notifications-to-your-ios-app"></a>Adicionar as notificações push para a sua aplicação iOS

1. No **iOS** project, abra AppDelegate.cs adicionar a seguinte instrução **utilizando** para a parte superior do ficheiro código.

        using Newtonsoft.Json.Linq;

4. Aula **AppDelegate** , adicione uma substituição do evento **RegisteredForRemoteNotifications** para se registar no notificações:

        public override void RegisteredForRemoteNotifications(UIApplication application, 
            NSData deviceToken)
        {
            const string templateBodyAPNS = "{\"aps\":{\"alert\":\"$(messageParam)\"}}";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
                {
                  {"body", templateBodyAPNS}
                };

            // Register for push with your mobile app
            Push push = TodoItemManager.DefaultManager.CurrentClient.GetPush();
            push.RegisterAsync(deviceToken, templates);
        }

5. No **AppDelegate**, adicione também a seguinte substituição **DidReceivedRemoteNotification** processador de eventos:

        public override void DidReceiveRemoteNotification(UIApplication application, 
            NSDictionary userInfo, Action<UIBackgroundFetchResult> completionHandler)
        {
            NSDictionary aps = userInfo.ObjectForKey(new NSString("aps")) as NSDictionary;

            string alert = string.Empty;
            if (aps.ContainsKey(new NSString("alert")))
                alert = (aps[new NSString("alert")] as NSString).ToString();

            //show alert
            if (!string.IsNullOrEmpty(alert))
            {
                UIAlertView avAlert = new UIAlertView("Notification", alert, null, "OK", null);
                avAlert.Show();
            }
        }

    Este método processa as notificações de recebidas enquanto a aplicação estiver em execução.

2. Aula **AppDelegate** , adicione o seguinte código para o método de **FinishedLaunching** : 

        // Register for push notifications.
        var settings = UIUserNotificationSettings.GetSettingsForTypes(
            UIUserNotificationType.Alert
            | UIUserNotificationType.Badge
            | UIUserNotificationType.Sound,
            new NSSet());

        UIApplication.SharedApplication.RegisterUserNotificationSettings(settings);
        UIApplication.SharedApplication.RegisterForRemoteNotifications();

    Isto permite que suporte para notificações remotos e os pedidos de emissão registo.

Agora, a aplicação é atualizada para suportar as notificações push.

####<a name="test-push-notifications-in-your-ios-app"></a>Teste as notificações push na sua aplicação iOS

1. Clique com o botão direito do rato em projeto iOS e clique em **Definir como StartPp projeto**.

2. Prima o botão **Executar** ou **F5** no Visual Studio para criar o projecto e iniciar a aplicação num dispositivo iOS, em seguida, clique em **OK** para aceitar as notificações push.

    > [AZURE.NOTE] Explicitamente tem de aceitar as notificações push da sua aplicação. Este pedido só ocorre a primeira vez que a aplicação é executado.

3. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição (**+**) ícone.

4. Certifique-se de que uma notificação for recebida, em seguida, clique em **OK** para dispensar a notificação.


##<a name="optional-configure-and-run-the-windows-projects"></a>(Opcional) Configurar e executar os Windows projectos

Esta secção destina-se a executar o Xamarin.Forms WinApp e projetos de WinPhone81 para dispositivos com Windows. Estes passos também suportam projetos Universal Windows plataforma (UWP). Pode ignorar esta secção se não estiver a trabalhar com dispositivos com Windows.


####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registe-se a aplicação do Windows para as notificações push com WNS

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


####<a name="configure-the-notification-hub-for-wns"></a>Configurar o concentrador de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


####<a name="add-push-notifications-to-your-windows-app"></a>Adicionar as notificações push para a sua aplicação do Windows

1. No Visual Studio, abra **App.xaml.cs** num projeto do Windows e adicione as seguintes instruções de **utilizar** .

        using Newtonsoft.Json.Linq;
        using Microsoft.WindowsAzure.MobileServices;
        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;
        using <your_TodoItemManager_portable_class_namespace>;

    Substituir `<your_TodoItemManager_portable_class_namespace>` com espaço de nomes do seu projeto portátil, que contém o `TodoItemManager` escolares.
 

2. No App.xaml.cs adicione o seguinte método **InitNotificationsAsync** : 

        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            const string templateBodyWNS = 
                "<toast><visual><binding template=\"ToastText01\"><text id=\"1\">$(messageParam)</text></binding></visual></toast>";

            JObject headers = new JObject();
            headers["X-WNS-Type"] = "wns/toast";

            JObject templates = new JObject();
            templates["genericMessage"] = new JObject
            {
                {"body", templateBodyWNS},
                {"headers", headers} // Needed for WNS.
            };

            await TodoItemManager.DefaultManager.CurrentClient.GetPush()
                .RegisterAsync(channel.Uri, templates);
        }

    Este método obtém o canal de notificações push e regista um modelo para receber notificações de modelo a partir do seu centro de notificação. Uma notificação de modelo que suporta *messageParam* será enviada para este cliente.

3. No App.xaml.cs, atualizar a definição de método de processador de eventos de **OnLaunched** adicionando o `async` modificadora, em seguida, adicione a linha seguinte de código no final do método: 

        await InitNotificationsAsync();

    Isto torna-se de que o registo de notificações push é criado ou atualizado sempre que a aplicação é iniciada. É importante fazer isto para garantir que o canal de push WNS está sempre ativo.  

4. No Explorador de solução para Visual Studio, abra o ficheiro **Package.appxmanifest** e defina **Capazes de alerta** para **Sim** em **notificações**.

5. Criar a aplicação e certifique-se que tem sem erros.  Aplicação cliente deverá agora registe-se para as notificações de modelo de aplicação Mobile back-end. Repita esta secção para cada projeto Windows na sua solução.


####<a name="test-push-notifications-in-your-windows-app"></a>Teste as notificações push na sua aplicação do Windows

1. No Visual Studio, clique com o botão direito do rato num projeto do Windows e clique em **Definir como projeto de arranque**.

2. Prima o botão **Executar** para criar o projecto e iniciar a aplicação.

3. Na aplicação, escreva um nome para uma nova todoitem e, em seguida, clique no sinal de adição (**+**) ícone para adicioná-lo.

4. Certifique-se de que é recebida uma notificação quando o item é adicionado.

##<a name="next-steps"></a>Próximos passos

Saiba mais sobre as notificações push:

* [Diagnosticar problemas de notificações push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Existem várias razões porque é que o notificações poderão obter capituladas ou não terminem em dispositivos. Este tópico mostra-lhe como analisar e descobrir a causa de raiz de falhas de notificações push. 

Considere continuar para um dos seguintes tutoriais:

* [Adicionar autenticação para a sua aplicação](app-service-mobile-xamarin-forms-get-started-users.md)  
Saiba como autenticar os utilizadores da sua aplicação com um fornecedor de identidade.

* [Ativar a sincronização offline para a sua aplicação](app-service-mobile-xamarin-forms-get-started-offline-data.md)  
  Saiba como adicionar a sua aplicação utilizando um back-end aplicação Mobile suporte offline. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.

<!-- Images. -->

<!-- URLs. -->
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333

