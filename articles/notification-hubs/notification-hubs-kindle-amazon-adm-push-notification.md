<properties
    pageTitle="Introdução ao Azure notificação concentradores para aplicações Kindle | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação de Kindle."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-kindle"
    ms.devlang="Java"
    ms.topic="hero-article"
    ms.date="06/29/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-for-kindle-apps"></a>Introdução ao concentradores de notificação para Kindle aplicações

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Este tutorial mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação de Kindle.
Crie uma aplicação vazia do Kindle que recebe as notificações push utilizando mensagens de dispositivo Amazon (ADM).

##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ Obter o SDK Android (Recomendamos partem do princípio de que irá utilizar Eclipse) a partir do <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android site</a>.
+ Siga os passos descritos na <a href="https://developer.amazon.com/appsandservices/resources/development-tools/ide-tools/tech-docs/01-setting-up-your-development-environment">Definição o seu ambiente de desenvolvimento de</a> configurar o seu ambiente de desenvolvimento para Kindle.

##<a name="add-a-new-app-to-the-developer-portal"></a>Adicionar uma nova aplicação para o portal de programador

1. Primeiro, crie uma aplicação no [developer portal para Amazon].

    ![][0]

2. Copie a **tecla de aplicação**.

    ![][1]

3. No portal do, clique no nome da sua aplicação e, em seguida, clique no separador **Dispositivo de mensagens** .

    ![][2]

4. Clique em **criar um novo perfil de segurança**e, em seguida, crie um novo perfil de segurança (por exemplo, de **perfil de segurança TestAdm**). Em seguida, clique em **Guardar**.

    ![][3]

5. Clique em **Perfis de segurança** para ver o perfil de segurança que acabou de criar. Copie os valores de **ID de cliente** e o **Segredo do cliente** para utilizar posteriormente.

    ![][4]

## <a name="create-an-api-key"></a>Criar uma chave de API

1. Abra uma linha de comandos com privilégios de administrador.
2. Navegue para a pasta de Android SDK.
3. Introduza o seguinte comando:

        keytool -list -v -alias androiddebugkey -keystore ./debug.keystore

    ![][5]

4.  A palavra-passe **keystore** , escreva **android**.

5.  Copie a identificação de **MD5** .
6.  Novamente no portal do programador, no separador **mensagens** , clique em **Android/Kindle** e introduza o nome do pacote para a sua aplicação (por exemplo, **com.sample.notificationhubtest**) e o valor de **MD5** e, em seguida, clique em **Gerar API chave**.

## <a name="add-credentials-to-the-hub"></a>Adicionar credenciais para o concentrador

No portal do, adicione o segredo cliente e o ID de cliente para o separador **Configurar** do seu centro de notificação.

## <a name="set-up-your-application"></a>Configurar a sua aplicação

> [AZURE.NOTE] Quando estiver a criar uma aplicação, utilize, pelo menos, API nível 17.

Adicione as bibliotecas ADM ao projeto Eclipse:

1. Para obter a biblioteca ADM, [Transferir o SDK]. Extrai o ficheiro de postal SDK.
2. Na Eclipse, com o botão direito do projeto e, em seguida, clique em **Propriedades**. Selecione **Java construir caminho** à esquerda e, em seguida, selecione o separador de **bibliotecas **na parte superior. Clique em **Adicionar Jar externas**e selecione o ficheiro `\SDK\Android\DeviceMessaging\lib\amazon-device-messaging-*.jar` do diretório na qual extraídas que o SDK Amazon.
3. Transferir o SDK Android NotificationHubs (ligação).
4. Deszipar o pacote e, em seguida, arraste o ficheiro `notification-hubs-sdk.jar` para o `libs` pasta no Eclipse.

Edite o manifesto de aplicação para suportar ADM:

1. Adicione o espaço de nomes de Amazon no elemento raiz manifesto:


        xmlns:amazon="http://schemas.amazon.com/apk/res/android"

2. Adicione permissões de como o primeiro elemento no elemento manifesto. Substitua **[Nome do seu pacote]** com o pacote que utilizou para criar a sua aplicação.

        <permission
         android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE"
         android:protectionLevel="signature" />

        <uses-permission android:name="android.permission.INTERNET"/>

        <uses-permission android:name="[YOUR PACKAGE NAME].permission.RECEIVE_ADM_MESSAGE" />

        <!-- This permission allows your app access to receive push notifications
        from ADM. -->
        <uses-permission android:name="com.amazon.device.messaging.permission.RECEIVE" />

        <!-- ADM uses WAKE_LOCK to keep the processor from sleeping when a message is received. -->
        <uses-permission android:name="android.permission.WAKE_LOCK" />

3. Inseri o elemento seguinte como o primeiro subordinado do elemento da aplicação. Não se esqueça de substituí-los **[Nome do seu serviço]** com o nome do seu processador de mensagem ADM que criou na secção seguinte (incluindo o pacote) e, substitua **[Nome do seu pacote]** com o nome do pacote com as quais que criou a sua aplicação.

        <amazon:enable-feature
              android:name="com.amazon.device.messaging"
                     android:required="true"/>
        <service
            android:name="[YOUR SERVICE NAME]"
            android:exported="false" />

        <receiver
            android:name="[YOUR SERVICE NAME]$Receiver" />

            <!-- This permission ensures that only ADM can send your app registration broadcasts. -->
            android:permission="com.amazon.device.messaging.permission.SEND" >

            <!-- To interact with ADM, your app must listen for the following intents. -->
            <intent-filter>
          <action android:name="com.amazon.device.messaging.intent.REGISTRATION" />
          <action android:name="com.amazon.device.messaging.intent.RECEIVE" />

          <!-- Replace the name in the category tag with your app's package name. -->
          <category android:name="[YOUR PACKAGE NAME]" />
            </intent-filter>
        </receiver>

## <a name="create-your-adm-message-handler"></a>Criar o seu processador de mensagem ADM

1. Criar uma nova classe herda `com.amazon.device.messaging.ADMMessageHandlerBase` e o nome `MyADMMessageHandler`, conforme apresentado na figura seguinte:

    ![][6]

2. Adicione o seguinte `import` declarações:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.support.v4.app.NotificationCompat;
        import com.amazon.device.messaging.ADMMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub

3. Adicione o seguinte código de aula que criou. Lembre-se substituí-los a cadeia de ligação de nome e concentrador (ouvir):

        public static final int NOTIFICATION_ID = 1;
        private NotificationManager mNotificationManager;
        NotificationCompat.Builder builder;
        private static NotificationHub hub;
        public static NotificationHub getNotificationHub(Context context) {
            Log.v("com.wa.hellokindlefire", "getNotificationHub");
            if (hub == null) {
                hub = new NotificationHub("[hub name]", "[listen connection string]", context);
            }
            return hub;
        }

        public MyADMMessageHandler() {
                super("MyADMMessageHandler");
            }

            public static class Receiver extends ADMMessageReceiver
            {
                public Receiver()
                {
                    super(MyADMMessageHandler.class);
                }
            }

            private void sendNotification(String msg) {
                Context ctx = getApplicationContext();

             mNotificationManager = (NotificationManager)
                    ctx.getSystemService(Context.NOTIFICATION_SERVICE);

            PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                new Intent(ctx, MainActivity.class), 0);

            NotificationCompat.Builder mBuilder =
                new NotificationCompat.Builder(ctx)
                .setSmallIcon(R.mipmap.ic_launcher)
                .setContentTitle("Notification Hub Demo")
                .setStyle(new NotificationCompat.BigTextStyle()
                         .bigText(msg))
                .setContentText(msg);

            mBuilder.setContentIntent(contentIntent);
            mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
        }

4. Adicione o seguinte código para o `OnMessage()` método:

        String nhMessage = intent.getExtras().getString("msg");
        sendNotification(nhMessage);

5. Adicione o seguinte código para o `OnRegistered` método:

            try {
        getNotificationHub(getApplicationContext()).register(registrationId);
            } catch (Exception e) {
        Log.e("[your package name]", "Fail onRegister: " + e.getMessage(), e);
            }

6.  Adicione o seguinte código para o `OnUnregistered` método:

            try {
                getNotificationHub(getApplicationContext()).unregister();
            } catch (Exception e) {
                Log.e("[your package name]", "Fail onUnregister: " + e.getMessage(), e);
            }

7. No `MainActivity` método, adicione a seguinte instrução importar:

        import com.amazon.device.messaging.ADM;

8. Adicione o seguinte código no final da `OnCreate` método:

        final ADM adm = new ADM(this);
        if (adm.getRegistrationId() == null)
        {
           adm.startRegister();
        } else {
            new AsyncTask() {
                  @Override
                  protected Object doInBackground(Object... params) {
                     try {                       MyADMMessageHandler.getNotificationHub(getApplicationContext()).register(adm.getRegistrationId());
                     } catch (Exception e) {
                         Log.e("com.wa.hellokindlefire", "Failed registration with hub", e);
                         return e;
                     }
                     return null;
                 }
               }.execute(null, null, null);
        }

## <a name="add-your-api-key-to-your-app"></a>Adicionar a sua chave de API para a sua aplicação

1. No Eclipse, crie um novo ficheiro chamado **api_key.txt** nos activos diretório do seu projeto.
2. Abra o ficheiro e copie a chave de API gerado no portal de programador do Amazon.

## <a name="run-the-app"></a>Executar a aplicação

1. Inicie o emulador.
2. No emulador, percorra a partir da parte superior e clique em **Definições**e, em seguida, clique na **minha conta** e registe-se com uma conta de Amazon válida.
3. Eclipse, execute a aplicação.

> [AZURE.NOTE] Se ocorrer um problema, verifique a hora da emulador (ou dispositivo). O valor de hora deve ser preciso. Para alterar a hora do emulador Kindle, pode executar o seguinte comando a partir do seu diretório de ferramentas de plataforma Android SDK:

        adb shell  date -s "yyyymmdd.hhmmss"

## <a name="send-a-message"></a>Enviar uma mensagem

Para enviar uma mensagem ao utilizar .NET:

        static void Main(string[] args)
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("[conn string]", "[hub name]");

            hub.SendAdmNativeNotificationAsync("{\"data\":{\"msg\" : \"Hello from .NET!\"}}").Wait();
        }

![][7]

<!-- URLs. -->
[Developer portal para Amazon]: https://developer.amazon.com/home.html
[Transferir o SDK]: https://developer.amazon.com/public/resources/development-tools/sdk

[0]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal1.png
[1]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal2.png
[2]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal3.png
[3]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal4.png
[4]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-portal5.png
[5]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-cmd-window.png
[6]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-new-java-class.png
[7]: ./media/notification-hubs-kindle-get-started/notification-hub-kindle-notification.png
