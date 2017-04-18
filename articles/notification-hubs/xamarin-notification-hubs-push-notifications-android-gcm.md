<properties
    pageTitle="Introdução ao concentradores de notificação para aplicações Xamarin.Android | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação de Xamarin Android."
    authors="ysxu"
    manager="erikre"
    editor=""
    services="notification-hubs"
    documentationCenter="xamarin"/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-xamarin-android"
    ms.devlang="dotnet"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-with-xamarin-for-android"></a>Introdução ao notificação concentradores com Xamarin para Android

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação de Xamarin.Android.
Crie uma aplicação de Xamarin.Android em branco que recebe as notificações push utilizando o Google Cloud mensagens (GCM). Quando tiver terminado, poderá utilizar o seu centro de notificação para difundir notificações push para todos os dispositivos de executar a sua aplicação. O código terminado está disponível na [aplicação NotificationHubs] [ GitHub] amostra.

Neste tutorial demonstra o cenário de difusão simple concentradores notificação ao utilizar.


## <a name="before-you-begin"></a>Antes de começar

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código para este tutorial completa pode ser encontrado no GitHub [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/Xamarin/GetStartedXamarinAndroid).



##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ Visual Studio com Xamarin no Windows ou Xamarin Studio no Mac OS X. concluída instruções de instalação estão no [programa de configuração e instalar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx).
+ Conta Google ativa
+ [Azure componente de mensagens]
+ [Google Cloud componente do cliente de mensagens]

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de notificação concentradores para Xamarin.Android aplicações.

> [AZURE.IMPORTANT] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A9C9624B5&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fpartner-xamarin-notification-hubs-android-get-started%2F).

##<a name="enable-google-cloud-messaging"></a>Ativar o Google Cloud mensagens

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-your-notification-hub"></a>Configurar o seu centro de notificação

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


<ol start="7">
<li><p>Clique no separador <b>Configurar</b> na parte superior, introduza o valor de <b>Chave API</b> obtidos na secção anterior e, em seguida, clique em <b>Guardar</b>.</p>
</li>
</ol>
&emsp;&emsp;![](./media/notification-hubs-android-get-started/notification-hub-configure-android.png)


O concentrador de notificação agora está configurado para funcionar com GCM e tiver as cadeias da ligação registar a sua aplicação para receber notificações e enviar as notificações push para ambas.

##<a name="connect-your-app-to-the-notification-hub"></a>Ligar a sua aplicação para o concentrador de notificação

###<a name="create-a-new-project"></a>Criar um novo projeto

1. No Xamarin Studio, clique em **Nova solução**, clique em **Aplicação Android**e clique em **seguinte**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project1.png)

2. Introduza o **Nome da aplicação** e **identificador**. Clique em **Plaforms de destino** que pretende de suporte e, em seguida, clique em **seguinte** e **Criar**.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-project2.png)


    Esta ação cria um novo projeto Android.

2. Abra as propriedades do projeto ao clicar o novo projeto na vista de solução e escolha das **Opções**. Selecione o item de **Aplicação Android** na secção **Criar** .

    Certifique-se de que a primeira letra do seu **nome do pacote** está em minúsculas.

    > [AZURE.IMPORTANT] A primeira letra do nome do pacote tem de estar em minúscula. Caso contrário, receberá erros manifesto das aplicações quando registar o seu **BroadcastReceiver** e **IntentFilter** para notificações push abaixo.

    ![](./media/partner-xamarin-notification-hubs-android-get-started/notification-hub--xamarin-android-app-options.png)


3. Opcionalmente, defina a **versão mínima Android** para outro nível API.

4. Opcionalmente, configure a **versão Android de destino** para a outra versão API que pretende de destino (tem de ser nível API 8 ou superior).

Clique em **OK** e feche a caixa de diálogo Opções do Project.


###<a name="add-the-required-components-to-your-project"></a>Adicionar os componentes necessários ao seu projeto

O Google Cloud mensagens cliente disponíveis no arquivo de componente de Xamarin simplifica o processo de suportar as notificações push no Xamarin.Android.

1. Botão direito do rato na pasta de componentes na aplicação Xamarin.Android e selecione **Obter mais componentes**.

2. Procure o componente de **Mensagens do Azure** e adicioná-lo ao projeto.

3. Procure o componente do **Cliente de mensagens do Google Cloud** e adicioná-lo ao projeto.


###<a name="set-up-notification-hubs-in-your-project"></a>Configurar o concentradores notificação no seu projeto

1. Reuna as seguintes informações para o seu centro de aplicação e notificação Android:

    - **GoogleProjectNumber**: obter este valor de número de projeto da descrição geral da sua aplicação no Portal de programador do Google. Efetuou uma nota anterior deste valor quando criou a aplicação no portal.
    - **Ouvir cadeia de ligação**: no dashboard de no [Portal clássica Azure], clique em **cadeias de ligação da vista**. Copie a cadeia de ligação *DefaultListenSharedAccessSignature* para este valor.
    - **Nome do concentrador**: Este é o nome do seu Centro a partir do [Azure clássica Portal]. Por exemplo, *mynotificationhub2*.

    Criar uma classe **Constants.cs** para o seu projeto Xamarin e definir os seguintes valores constantes na aula. Substitua os marcadores de posição com os valores.

        public static class Constants
        {
            public const string SenderID = "<GoogleProjectNumber>"; // Google API Project Number
            public const string ListenConnectionString = "<Listen connection string>";
            public const string NotificationHubName = "<hub name>";
        }

2. Adicione o seguinte utilizando instruções para **MainActivity.cs**:

        using Android.Util;
        using Gcm.Client;

3. Adicionar uma variável de instância para o `MainActivity` escolares que serão utilizada para mostrar uma caixa de diálogo alerta quando a aplicação estiver em execução:

        public static MainActivity instance;


3. Crie o seguinte método a classe de **MainActivity** :

        private void RegisterWithGCM()
        {
            // Check to ensure everything's set up right
            GcmClient.CheckDevice(this);
            GcmClient.CheckManifest(this);

            // Register for push notifications
            Log.Info("MainActivity", "Registering...");
            GcmClient.Register(this, Constants.SenderID);
        }

4. No `OnCreate` método de **MainActivity.cs**, iniciar a `instance` variável e adicionar uma chamada para `RegisterWithGCM`:

        protected override void OnCreate (Bundle bundle)
        {
            instance = this;

            base.OnCreate (bundle);

            // Set your view from the "main" layout resource
            SetContentView (Resource.Layout.Main);

            // Get your button from the layout resource,
            // and attach an event to it
            Button button = FindViewById<Button> (Resource.Id.myButton);

            RegisterWithGCM();
        }


4. Crie uma nova classe, **MyBroadcastReceiver**.

    > [AZURE.NOTE] Vamos irá guiá-lo através da criação de uma classe **BroadcastReceiver** de raiz abaixo. No entanto, uma alternativa rápida para a criação de manualmente **MyBroadcastReceiver.cs** é referir-se para o ficheiro de **GcmService.cs** localizado no projeto Xamarin.Android exemplo incluído com as [amostras NotificationHubs][GitHub]. Duplicar **GcmService.cs** e alterar os nomes de classe podem ser um ótimo local para começar, assim.

5. Adicione o seguinte utilizando instruções para **MyBroadcastReceiver.cs** (se referir ao assemblagem que adicionou anteriormente e o componente):

        using System.Collections.Generic;
        using System.Text;
        using Android.App;
        using Android.Content;
        using Android.Util;
        using Gcm.Client;
        using WindowsAzure.Messaging;

5. No **MyBroadcastReceiver.cs**, adicione os seguintes pedidos de permissão entre as declarações de **utilização** e a declaração de **espaço de nomes** :

        [assembly: Permission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "@PACKAGE_NAME@.permission.C2D_MESSAGE")]
        [assembly: UsesPermission(Name = "com.google.android.c2dm.permission.RECEIVE")]

        //GET_ACCOUNTS is needed only for Android versions 4.0.3 and below
        [assembly: UsesPermission(Name = "android.permission.GET_ACCOUNTS")]
        [assembly: UsesPermission(Name = "android.permission.INTERNET")]
        [assembly: UsesPermission(Name = "android.permission.WAKE_LOCK")]

6. Na **MyBroadcastReceiver.cs**, altere a classe de **MyBroadcastReceiver** para fazer corresponder o seguinte procedimento:

        [BroadcastReceiver(Permission=Gcm.Client.Constants.PERMISSION_GCM_INTENTS)]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_MESSAGE },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_REGISTRATION_CALLBACK },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        [IntentFilter(new string[] { Gcm.Client.Constants.INTENT_FROM_GCM_LIBRARY_RETRY },
            Categories = new string[] { "@PACKAGE_NAME@" })]
        public class MyBroadcastReceiver : GcmBroadcastReceiverBase<PushHandlerService>
        {
            public static string[] SENDER_IDS = new string[] { Constants.SenderID };

            public const string TAG = "MyBroadcastReceiver-GCM";
        }

7. Adicione outra classe **MyBroadcastReceiver.cs** denominada **PushHandlerService**, que deriva da **GcmServiceBase**. Certifique-se aplicar o atributo de **serviço** para a classe de:

        [Service] // Must use the service tag
        public class PushHandlerService : GcmServiceBase
        {
            public static string RegistrationID { get; private set; }
            private NotificationHub Hub { get; set; }

            public PushHandlerService() : base(Constants.SenderID)
            {
                Log.Info(MyBroadcastReceiver.TAG, "PushHandlerService() constructor");
            }
        }


8. **GcmServiceBase** implementa métodos **OnRegistered()**, **OnUnRegistered()**, **OnMessage()**, **OnRecoverableError()**e **OnError()**. Os nossos classe de implementação **PushHandlerService** tem de substituir estes métodos e estes métodos irão despoletado na resposta para interagir com o concentrador de notificação.


9. Substitua o método de **OnRegistered()** **PushHandlerService** utilizando o seguinte código:

        protected override void OnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Registered: " + registrationId);
            RegistrationID = registrationId;

            createNotification("PushHandlerService-GCM Registered...",
                                "The device has been Registered!");

            Hub = new NotificationHub(Constants.NotificationHubName, Constants.ListenConnectionString,
                                        context);
            try
            {
                Hub.UnregisterAll(registrationId);
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }

            //var tags = new List<string>() { "falcons" }; // create tags if you want
            var tags = new List<string>() {};

            try
            {
                var hubRegistration = Hub.Register(registrationId, tags.ToArray());
            }
            catch (Exception ex)
            {
                Log.Error(MyBroadcastReceiver.TAG, ex.Message);
            }
        }

    > [AZURE.NOTE] No código **OnRegistered()** acima, deverá nota a capacidade de especificar etiquetas para se registar no canais mensagens específicos.

10. Substitua o método de **OnMessage** **PushHandlerService** utilizando o seguinte código:

        protected override void OnMessage(Context context, Intent intent)
        {
            Log.Info(MyBroadcastReceiver.TAG, "GCM Message Received!");

            var msg = new StringBuilder();

            if (intent != null && intent.Extras != null)
            {
                foreach (var key in intent.Extras.KeySet())
                    msg.AppendLine(key + "=" + intent.Extras.Get(key).ToString());
            }

            string messageText = intent.Extras.GetString("message");
            if (!string.IsNullOrEmpty (messageText))
            {
                createNotification ("New hub message!", messageText);
            }
            else
            {
                createNotification ("Unknown message details", msg.ToString ());
            }
        }

11. Adicione os seguintes métodos **createNotification** e **dialogNotify** a **PushHandlerService** para notificando os utilizadores quando for recebida uma notificação.

    >[AZURE.NOTE] Estrutura de notificação na versão Android 5.0 e versões posterior representa um desvio significativo das versões anteriores. Se testar esta situação no Android 5.0 ou posterior, a aplicação terá de estar em execução para receber a notificação. Para mais informações, consulte o artigo [Notificações Android](http://go.microsoft.com/fwlink/?LinkId=615880).

        void createNotification(string title, string desc)
        {
            //Create notification
            var notificationManager = GetSystemService(Context.NotificationService) as NotificationManager;

            //Create an intent to show UI
            var uiIntent = new Intent(this, typeof(MainActivity));

            //Create the notification
            var notification = new Notification(Android.Resource.Drawable.SymActionEmail, title);

            //Auto-cancel will remove the notification once the user touches it
            notification.Flags = NotificationFlags.AutoCancel;

            //Set the notification info
            //we use the pending intent, passing our ui intent over, which will get called
            //when the notification is tapped.
            notification.SetLatestEventInfo(this, title, desc, PendingIntent.GetActivity(this, 0, uiIntent, 0));

            //Show the notification
            notificationManager.Notify(1, notification);
            dialogNotify (title, desc);
        }

        protected void dialogNotify(String title, String message)
        {

            MainActivity.instance.RunOnUiThread(() => {
                AlertDialog.Builder dlg = new AlertDialog.Builder(MainActivity.instance);
                AlertDialog alert = dlg.Create();
                alert.SetTitle(title);
                alert.SetButton("Ok", delegate {
                    alert.Dismiss();
                });
                alert.SetMessage(message);
                alert.Show();
            });
        }


12. Substitui abstratos membros **OnUnRegistered()**, **OnRecoverableError()**e **OnError()** para que o seu código compila:

        protected override void OnUnRegistered(Context context, string registrationId)
        {
            Log.Verbose(MyBroadcastReceiver.TAG, "GCM Unregistered: " + registrationId);

            createNotification("GCM Unregistered...", "The device has been unregistered!");
        }

        protected override bool OnRecoverableError(Context context, string errorId)
        {
            Log.Warn(MyBroadcastReceiver.TAG, "Recoverable Error: " + errorId);

            return base.OnRecoverableError (context, errorId);
        }

        protected override void OnError(Context context, string errorId)
        {
            Log.Error(MyBroadcastReceiver.TAG, "GCM Error: " + errorId);
        }


##<a name="run-your-app-in-the-emulator"></a>Executar a aplicação no emulador

Se executar esta aplicação no emulador, certifique-se de que utiliza um Android Virtual dispositivo (AVD) que suporte APIs do Google.

> [AZURE.IMPORTANT] Para receberem notificações push, tem de configurar uma conta do Google no seu dispositivo Android Virtual. (No emulador, navegue até **Definições** e clique em **Adicionar conta**). Além disso, certifique-se de que o emulador está ligado à Internet.

>[AZURE.NOTE] Estrutura de notificação na versão Android 5.0 e versões posterior representa um desvio significativo das versões anteriores. Para mais informações, consulte o artigo [Notificações Android](http://go.microsoft.com/fwlink/?LinkId=615880).


1. Em **Ferramentas**, clique em **Abrir o Gestor de emulador Android**, selecione o seu dispositivo e, em seguida, clique em **Editar**.

    ![][18]

2. Selecione o **Google APIs** no **destino**e, em seguida, clique em **OK**.

    ![][19]

3. Na barra de ferramentas superior, clique em **Executar**e, em seguida, selecione a sua aplicação. Isto inicia o emulador e executa a aplicação.

  A aplicação obtém os *atributos registrationId* GCM e regista com o concentrador de notificação.

##<a name="send-notifications-from-your-backend"></a>Enviar notificações a partir do seu back-end


Pode testar a receber notificações na sua aplicação ao enviar notificações no [Portal clássica Azure] através do separador depuração no centro do notificação, conforme mostrado no ecrã abaixo.

![][30]


Notificações push normalmente são enviadas num serviço como o serviços Mobile ou ASP.NET back-end através de uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não está disponível para o seu back-end.

Eis uma lista de algumas tutoriais que poderá pretender rever para o envio de notificações:

- ASP.NET: Consulte o artigo [Utilizar concentradores de notificação para as notificações push para os utilizadores].
- Azure notificação concentradores Java SDK: veja [como utilizar concentradores de notificação de Java](notification-hubs-java-push-notification-tutorial.md) para o envio de notificações a partir do Java. Este foi testado no Eclipse para o desenvolvimento Android.
- PHP: Consulte o artigo [como utilizar concentradores de notificação de PHP](notification-hubs-php-push-notification-tutorial.md).


Nas subsecções seguintes do tutorial, enviar notificações ao utilizar uma aplicação de consola do .NET e utilizando um serviço móvel através de um script nó.

####<a name="optional-send-notifications-by-using-a-net-app"></a>(Opcional) Enviar notificações ao utilizar uma aplicação .NET

Nesta secção, enviaremos notificações ao utilizar uma aplicação de consola do .NET

1. Crie uma nova aplicação de consola do Visual c#:

    ![][20]

2. No Visual Studio, clique em **Ferramentas**, clique em **Gestor de pacote NuGet**e, em seguida, clique em **Consola do Gestor de pacote**.

    Esta ação apresentará consola do Gestor de pacotes no Visual Studio.

3. Na janela da consola do Gestor de pacote, definir **predefinidos do project** para o novo projeto de aplicação de consola e, em seguida, na janela da consola, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência para o SDK do Azure notificação concentradores utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote de Microsoft.Azure.Notification concentradores NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

4. Abra o ficheiro Program.cs e adicione o seguinte `using` declaração:

        using Microsoft.Azure.NotificationHubs;

5. No seu `Program` classe, adicione o seguinte método. Atualize o texto do marcador de posição com o seu nome de cadeia e concentrador de tipos de ligação de *DefaultFullSharedAccessSignature* a partir do [Azure clássica Portal].

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            await hub.SendGcmNativeNotificationAsync("{ \"data\" : {\"message\":\"Hello from Azure!\"}}");
        }

6. Adicione as seguintes linhas no seu método de **principais** :

         SendNotificationAsync();
         Console.ReadLine();

7. Prima a tecla F5 para executar a aplicação. Deverá receber uma notificação na aplicação.

    ![][21]

####<a name="optional-send-notifications-by-using-a-mobile-service"></a>(Opcional) Enviar notificações ao utilizar um serviço móvel

1. Siga a [começar a trabalhar com serviços Mobile].

1. Inicie sessão no [Portal clássica do Azure]e, selecione o seu serviço móvel.

2. Selecione o separador **Programador** , no canto superior.

    ![][22]

3. Criar uma nova tarefa agendada, insira um nome e selecione **a pedido**.

    ![][23]

4. Quando a tarefa é criada, clique no nome da tarefa. Em seguida, clique no separador de **Script** na barra superior.

5. Inserir o seguinte script dentro de uma função programador. Certifique-se substituir os marcadores de posição com o seu nome de concentrador de notificação e a cadeia de ligação para *DefaultFullSharedAccessSignature* que obteve anterior. Clique em **Guardar**.

        var azure = require('azure');
        var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string>');
        notificationHubService.gcm.send(null,'{"data":{"message" : "Hello from Mobile Services!"}}',
          function (error)
          {
            if (!error) {
               console.warn("Notification successful");
            }
            else
            {
              console.warn("Notification failed" + error);
            }
          }
        );

6. Clique em **Executar uma vez** na barra inferior. Deverá receber uma notificação de alerta.

##<a name="next-steps"></a>Próximos passos

Neste exemplo simples, difundida notificações para todos os seus dispositivos Android. Para utilizadores específicos de destino, consulte o tutorial [Concentradores de notificação de utilização para as notificações push para os utilizadores]. Se pretender segmentar os seus utilizadores por grupos de interesse, pode ser lido [Concentradores de notificação de utilização para enviar notícias de última hora]. Saiba mais sobre como utilizar concentradores de notificação na [Notificação concentradores orientação] e, a [Notificação concentradores instruções para Android].

<!-- Anchors. -->
[Enable Google Cloud Messaging]: #register
[Configure your Notification Hub]: #configure-hub
[Connecting your app to the Notification Hub]: #connecting-app
[Run your app with the emulator]: #run-app
[Send notifications from your back-end]: #send
[Next steps]:#next-steps

<!-- Images. -->

[11]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-configure-android.png

[13]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app1.png
[15]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-xamarin-android-app3.png

[18]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app7.png
[19]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-android-app8.png

[20]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-android-toast.png
[22]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hub-scheduler2.png

[30]: ./media/partner-xamarin-notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Começar a trabalhar com serviços Mobile]: /develop/mobile/tutorials/get-started-xamarin-android/#create-new-service
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js

[Azure Portal clássico]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notificação concentradores orientações]: http://msdn.microsoft.com/library/jj927170.aspx
[Notificação concentradores instruções para Android]: http://msdn.microsoft.com/library/dn282661.aspx

[Utilizar concentradores de notificação para as notificações push para os utilizadores]: /manage/services/notification-hubs/notify-users-aspnet
[Utilizar concentradores de notificação para enviar notícias de última hora]: /manage/services/notification-hubs/breaking-news-dotnet
[GCMClient Component page]: http://components.xamarin.com/view/GCMClient
[Xamarin.NotificationHub GitHub page]: https://github.com/SaschaDittmann/Xamarin.NotificationHub
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331329
[Google Cloud componente do cliente de mensagens]: http://components.xamarin.com/view/GCMClient/
[Azure componente de mensagens]: http://components.xamarin.com/view/azure-messaging
