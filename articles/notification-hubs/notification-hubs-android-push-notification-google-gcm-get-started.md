<properties
    pageTitle="Enviar notificações push para Android com o Azure notificação concentradores | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para as notificações push para dispositivos Android."
    services="notification-hubs"
    documentationCenter="android"
    keywords="notificações push, notificação de emissão, notificação de emissão android"
    authors="ysxu"
    manager="erikre"
    editor=""/>
<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-android"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.date="07/05/2016"
    ms.author="yuaxu"/>

# <a name="sending-push-notifications-to-android-with-azure-notification-hubs"></a>Enviar notificações push para Android com o Azure notificação concentradores

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

> [AZURE.IMPORTANT] Este tópico demonstra notificações push com Google Cloud mensagens (GCM). Se estiver a utilizar o Firebase nuvem mensagens (FCM da Google), consulte o artigo [enviar as notificações push para Android com o concentradores de notificação do Azure e FCM](notification-hubs-android-push-notification-google-fcm-get-started.md).

Este tutorial mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do Android.
Crie uma aplicação vazia Android que recebe as notificações push utilizando o Google Cloud mensagens (GCM).

[AZURE.INCLUDE [notification-hubs-hero-slug](../../includes/notification-hubs-hero-slug.md)]

O código para este tutorial completa pode ser transferido das GitHub [aqui](https://github.com/Azure/azure-notificationhubs-samples/tree/master/Android/GetStarted).


##<a name="prerequisites"></a>Pré-requisitos

> [AZURE.IMPORTANT] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-android-get-started).

Para além de uma conta Azure active mencionada acima, este tutorial apenas requer a versão mais recente do [Android Studio](http://go.microsoft.com/fwlink/?LinkId=389797).

Concluir este tutorial é um pré-requisito para todos os outros tutoriais de notificação concentradores para aplicações do Android.

##<a name="creating-a-project-that-supports-google-cloud-messaging"></a>Criar um projeto que suporta mensagens do Google Cloud

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]


##<a name="configure-a-new-notification-hub"></a>Configurar um novo concentrador de notificação


[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. na pá **Definições** , selecione **Serviços de notificação** e, em seguida, **Google (GCM)**. Introduza a chave de API e clique em **Guardar**.

&emsp;&emsp;![Notificação Azure concentradores - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

O concentrador de notificação agora está configurado para funcionar com GCM e tiver as cadeias da ligação registar a sua aplicação para receber e enviar as notificações push para ambas.

##<a id="connecting-app"></a>Ligar a sua aplicação para o concentrador de notificação

###<a name="create-a-new-android-project"></a>Criar um novo projeto Android

1. No Android Studio, inicie um novo projeto de Android Studio.

    ![Android Studio - novo projeto][13]

2. Selecione fator de formulário de **telemóvel e Tablet** e o **Mínimo SDK** , que pretende de suporte. Em seguida, clique em **seguinte**.

    ![Android Studio - fluxo de trabalho de criação de projeto][14]

3. Escolher **Atividade vazia** para a atividade principal, clique em **seguinte**e, em seguida, clique em **Concluir**.

###<a name="add-google-play-services-to-the-project"></a>Adicionar serviços do Google Play ao projeto

[AZURE.INCLUDE [Add Play Services](../../includes/notification-hubs-android-studio-add-google-play-services.md)]

###<a name="adding-azure-notification-hubs-libraries"></a>Adicionar bibliotecas concentradores de notificação do Azure


1. No `Build.Gradle` ficheiro para a **aplicação**, adicione as seguintes linhas na secção **dependências** .

        compile 'com.microsoft.azure:notification-hubs-android-sdk:0.4@aar'
        compile 'com.microsoft.azure:azure-notifications-handler:1.0.1@aar'

2. Adicione o repositório seguinte após a secção **dependências** .

        repositories {
            maven {
                url "http://dl.bintray.com/microsoftazuremobile/SDK"
            }
        }

### <a name="updating-the-androidmanifestxml"></a>Atualizar a AndroidManifest.xml.


1. Para suportar GCM, podemos tem de implementar um serviço de escuta ID da instância no nosso código que é utilizado para [obter tokens de registo](https://developers.google.com/cloud-messaging/android/client#sample-register) com a [API de ID de instância da Google](https://developers.google.com/instance-id/). Neste tutorial podemos será dar um nome a classe `MyInstanceIDService`. 
 
    Adicionar a seguinte definição do serviço para o ficheiro AndroidManifest.xml, interior a `<application>` etiqueta. Substituir o `<your package>` marcador de posição com o seu nome de pacote real mostrada na parte superior da `AndroidManifest.xml` ficheiro.

        <service android:name="<your package>.MyInstanceIDService" android:exported="false">
            <intent-filter>
                <action android:name="com.google.android.gms.iid.InstanceID"/>
            </intent-filter>
        </service>


2. Assim que recebemos a nossa token de registo GCM a partir da API do ID de instância, vamos utilizá-lo para [registar o concentrador de notificação do Azure](notification-hubs-push-notification-registration-management.md). Irá suportamos este registo no fundo utilizando um `IntentService` denominada `RegistrationIntentService`. Este serviço também será responsável por [atualizar a nossa token de registo GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens).
 
    Adicionar a seguinte definição do serviço para o ficheiro AndroidManifest.xml, interior a `<application>` etiqueta. Substituir o `<your package>` marcador de posição com o seu nome de pacote real mostrada na parte superior da `AndroidManifest.xml` ficheiro. 

        <service
            android:name="<your package>.RegistrationIntentService"
            android:exported="false">
        </service>



3. Recomendamos também definirá um destinatário para receber notificações. Adicionar a seguinte definição recetor para o ficheiro AndroidManifest.xml, interior a `<application>` etiqueta. Substituir o `<your package>` marcador de posição com o seu nome de pacote real mostrada na parte superior da `AndroidManifest.xml` ficheiro.

        <receiver android:name="com.microsoft.windowsazure.notifications.NotificationsBroadcastReceiver"
            android:permission="com.google.android.c2dm.permission.SEND">
            <intent-filter>
                <action android:name="com.google.android.c2dm.intent.RECEIVE" />
                <category android:name="<your package name>" />
            </intent-filter>
        </receiver>



4. Adicione o seguinte GCM necessária relacionados com permissões abaixo a `</application>` etiqueta. Certifique-se substituir `<your package>` com o nome do pacote mostrado na parte superior da `AndroidManifest.xml` ficheiro.

    Para mais informações sobre estas permissões, consulte o artigo [Configurar uma aplicação de cliente GCM para Android](https://developers.google.com/cloud-messaging/android/client#manifest).

        <uses-permission android:name="android.permission.INTERNET"/>
        <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
        <uses-permission android:name="android.permission.WAKE_LOCK"/>
        <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />

        <permission android:name="<your package>.permission.C2D_MESSAGE" android:protectionLevel="signature" />
        <uses-permission android:name="<your package>.permission.C2D_MESSAGE"/>


### <a name="adding-code"></a>Adicionar código


1. Na vista de projeto, expanda **aplicação** > **src** > **principal** > **java**. A pasta de pacote em **java**com o botão direito, clique em **Novo**e, em seguida, clique em **Classe Java**. Adicionar uma nova com o nome de classe `NotificationSettings`. 

    ![Android Studio - nova classe Java][6]

    Certifique-se atualizar a estes três marcadores de posição no seguinte código para o `NotificationSettings` classe:
    * **SenderId**: O número de projeto obtidos anterior na [Consola do Google Cloud](http://cloud.google.com/console).
    * **HubListenConnectionString**: A cadeia de ligação **DefaultListenAccessSignature** para o seu centro. Pode copiar essa cadeia de ligação ao clicar em **Políticas de acesso** no pá **Definições** do seu centro no [Portal do Azure].
    * **HubName**: Utilize o nome do seu centro de notificação que aparece no pá concentrador no [Portal do Azure].

    `NotificationSettings`código:

        public class NotificationSettings {
            public static String SenderId = "<Your project number>";
            public static String HubName = "<Your HubName>";
            public static String HubListenConnectionString = "<Your default listen connection string>";
        }

2. Utilizando os passos acima, adicionar outra nova classe denominada `MyInstanceIDService`. Este será nossa implementação do serviço escuta ID da instância.

    O código para esta classe ligarem nosso `IntentService` para [atualizar o token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) em segundo plano.

        import android.content.Intent;
        import android.util.Log;
        import com.google.android.gms.iid.InstanceIDListenerService;
        
        
        public class MyInstanceIDService extends InstanceIDListenerService {
        
            private static final String TAG = "MyInstanceIDService";
        
            @Override
            public void onTokenRefresh() {
        
                Log.i(TAG, "Refreshing GCM Registration Token");
        
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        };


3. Adicionar outra nova classe ao seu projeto com o nome, `RegistrationIntentService`. Este será da execução do nosso `IntentService` que processará [atualizar o token GCM](https://developers.google.com/instance-id/guides/android-implementation#refresh_tokens) e [Registar com o concentrador de notificação](notification-hubs-push-notification-registration-management.md).

    Utilize o seguinte código para esta classe.

        import android.app.IntentService;
        import android.content.Intent;
        import android.content.SharedPreferences;
        import android.preference.PreferenceManager;
        import android.util.Log;
        
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.google.android.gms.iid.InstanceID;
        import com.microsoft.windowsazure.messaging.NotificationHub;
        
        public class RegistrationIntentService extends IntentService {
        
            private static final String TAG = "RegIntentService";
        
            private NotificationHub hub;
        
            public RegistrationIntentService() {
                super(TAG);
            }
        
            @Override
            protected void onHandleIntent(Intent intent) {      
                SharedPreferences sharedPreferences = PreferenceManager.getDefaultSharedPreferences(this);
                String resultString = null;
                String regID = null;
        
                try {
                    InstanceID instanceID = InstanceID.getInstance(this);
                    String token = instanceID.getToken(NotificationSettings.SenderId,
                            GoogleCloudMessaging.INSTANCE_ID_SCOPE);        
                    Log.i(TAG, "Got GCM Registration Token: " + token);
        
                    // Storing the registration id that indicates whether the generated token has been
                    // sent to your server. If it is not stored, send the token to your server,
                    // otherwise your server should have already received the token.
                    if ((regID=sharedPreferences.getString("registrationID", null)) == null) {      
                        NotificationHub hub = new NotificationHub(NotificationSettings.HubName,
                                NotificationSettings.HubListenConnectionString, this);
                        Log.i(TAG, "Attempting to register with NH using token : " + token);

                        regID = hub.register(token).getRegistrationId();

                        // If you want to use tags...
                        // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                        // regID = hub.register(token, "tag1", "tag2").getRegistrationId();

                        resultString = "Registered Successfully - RegId : " + regID;
                        Log.i(TAG, resultString);       
                        sharedPreferences.edit().putString("registrationID", regID ).apply();
                    } else {
                        resultString = "Previously Registered Successfully - RegId : " + regID;
                    }
                } catch (Exception e) {
                    Log.e(TAG, resultString="Failed to complete token refresh", e);
                    // If an exception happens while fetching the new token or updating our registration data
                    // on a third-party server, this ensures that we'll attempt the update at a later time.
                }
        
                // Notify UI that registration has completed.
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(resultString);
                }
            }
        }


        
4. No seu `MainActivity` classe, adicione o seguinte `import` declarações acima da declaração de classe.

        import com.google.android.gms.common.ConnectionResult;
        import com.google.android.gms.common.GoogleApiAvailability;
        import com.google.android.gms.gcm.*;
        import com.microsoft.windowsazure.notifications.NotificationsManager;
        import android.util.Log;
        import android.widget.TextView;
        import android.widget.Toast;

5. Adicione os seguintes membros privado na parte superior a classe de. Vamos utilizar estes [verifica a disponibilidade de serviços do Google Play conforme recomendado pelo Google](https://developers.google.com/android/guides/setup#ensure_devices_have_the_google_play_services_apk).

        public static MainActivity mainActivity;
        public static Boolean isVisible = false;    
        private GoogleCloudMessaging gcm;
        private static final int PLAY_SERVICES_RESOLUTION_REQUEST = 9000;

6. No seu `MainActivity` classe, adicione o seguinte método a disponibilidade dos serviços do Google Play. 

        /**
         * Check the device to make sure it has the Google Play Services APK. If
         * it doesn't, display a dialog that allows users to download the APK from
         * the Google Play Store or enable it in the device's system settings.
         */
        private boolean checkPlayServices() {
            GoogleApiAvailability apiAvailability = GoogleApiAvailability.getInstance();
            int resultCode = apiAvailability.isGooglePlayServicesAvailable(this);
            if (resultCode != ConnectionResult.SUCCESS) {
                if (apiAvailability.isUserResolvableError(resultCode)) {
                    apiAvailability.getErrorDialog(this, resultCode, PLAY_SERVICES_RESOLUTION_REQUEST)
                            .show();
                } else {
                    Log.i(TAG, "This device is not supported by Google Play Services.");
                    ToastNotify("This device is not supported by Google Play Services.");
                    finish();
                }
                return false;
            }
            return true;
        }


7. No seu `MainActivity` classe, adicione o seguinte código que irá dar entrada para serviços do Google Play antes de telefonar seu `IntentService` para obter o registo GCM token e registe-se com o seu centro de notificação.

        public void registerWithNotificationHubs()
        {
            Log.i(TAG, " Registering with Notification Hubs");
    
            if (checkPlayServices()) {
                // Start IntentService to register this application with GCM.
                Intent intent = new Intent(this, RegistrationIntentService.class);
                startService(intent);
            }
        }


8. No `OnCreate` método a `MainActivity` classe, adicione o seguinte código para iniciar o processo de registo notificadas aquando da atividade.

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            mainActivity = this;
            NotificationsManager.handleNotifications(this, NotificationSettings.SenderId, MyHandler.class);
            registerWithNotificationHubs();
        }


9. Adicionar estes métodos adicionais para o `MainActivity` para verificar o estado de estado e relatório da aplicação na sua aplicação.

        @Override
        protected void onStart() {
            super.onStart();
            isVisible = true;
        }
    
        @Override
        protected void onPause() {
            super.onPause();
            isVisible = false;
        }
    
        @Override
        protected void onResume() {
            super.onResume();
            isVisible = true;
        }
    
        @Override
        protected void onStop() {
            super.onStop();
            isVisible = false;
        }
    
        public void ToastNotify(final String notificationMessage) {
            runOnUiThread(new Runnable() {
                @Override
                public void run() {
                    Toast.makeText(MainActivity.this, notificationMessage, Toast.LENGTH_LONG).show();
                    TextView helloText = (TextView) findViewById(R.id.text_hello);
                    helloText.setText(notificationMessage);
                }
            });
        }


10. O `ToastNotify` método utiliza o *"Olá mundo"* `TextView` controlo para comunicar o estado e notificações de forma persistente na aplicação. No seu esquema activity_main.xml, adicione o seguinte id para que o controlo.

        android:id="@+id/text_hello"

11. Seguinte que vamos adicionar uma subclasse para os nossos recetor definido na AndroidManifest.xml. Adicionar outra nova classe ao seu projeto com o nome `MyHandler`.

12. Adicione as seguintes instruções de importar no topo da `MyHandler.java`:

        import android.app.NotificationManager;
        import android.app.PendingIntent;
        import android.content.Context;
        import android.content.Intent;
        import android.os.Bundle;
        import android.support.v4.app.NotificationCompat;
        import com.microsoft.windowsazure.notifications.NotificationsHandler;

13. Adicione o seguinte código para o `MyHandler` escolares tornando uma subclasse de `com.microsoft.windowsazure.notifications.NotificationsHandler`.

    Este código substitui o `OnReceive` método, para que a alça de irá reportar notificações que são recebidas. O processador também envia a notificação de push para o Gestor de notificação Android utilizando o `sendNotification()` método. O `sendNotification()` método deve ser executado quando a aplicação não está em execução e uma notificação for recebida.

        public class MyHandler extends NotificationsHandler {
            public static final int NOTIFICATION_ID = 1;
            private NotificationManager mNotificationManager;
            NotificationCompat.Builder builder;
            Context ctx;
        
            @Override
            public void onReceive(Context context, Bundle bundle) {
                ctx = context;
                String nhMessage = bundle.getString("message");
                sendNotification(nhMessage);
                if (MainActivity.isVisible) {
                    MainActivity.mainActivity.ToastNotify(nhMessage);
                }
            }
        
            private void sendNotification(String msg) {
        
                Intent intent = new Intent(ctx, MainActivity.class);
                intent.addFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        
                mNotificationManager = (NotificationManager)
                        ctx.getSystemService(Context.NOTIFICATION_SERVICE);
        
                PendingIntent contentIntent = PendingIntent.getActivity(ctx, 0,
                        intent, PendingIntent.FLAG_ONE_SHOT);
        
                Uri defaultSoundUri = RingtoneManager.getDefaultUri(RingtoneManager.TYPE_NOTIFICATION);
                NotificationCompat.Builder mBuilder =
                        new NotificationCompat.Builder(ctx)
                                .setSmallIcon(R.mipmap.ic_launcher)
                                .setContentTitle("Notification Hub Demo")
                                .setStyle(new NotificationCompat.BigTextStyle()
                                        .bigText(msg))
                                .setSound(defaultSoundUri)
                                .setContentText(msg);
        
                mBuilder.setContentIntent(contentIntent);
                mNotificationManager.notify(NOTIFICATION_ID, mBuilder.build());
            }
        }


14. No Android Studio na barra de menus, clique em **Criar** > **Recompilar projetos** para se certificar de que não há erros estão presentes no seu código.

##<a name="sending-push-notifications"></a>Enviar notificações push

Pode testar a receber notificações push na sua aplicação enviando-os através de do [Portal do Azure] - aspeto para a secção de **resolução de problemas** no pá concentrador, conforme apresentado abaixo.

![Enviar notificação Azure concentradores - teste](./media/notification-hubs-android-get-started/notification-hubs-test-send.png)

[AZURE.INCLUDE [notification-hubs-sending-notifications-from-the-portal](../../includes/notification-hubs-sending-notifications-from-the-portal.md)]

## <a name="optional-send-push-notifications-directly-from-the-app"></a>(Opcional) Enviar notificações push diretamente a partir da aplicação

Normalmente, terá de enviar notificações utilizando um servidor de back-end. Para alguns casos, poderá pretender possam enviar notificações push diretamente a partir da aplicação de cliente. Esta secção explica como enviar notificações a partir do cliente utilizar a [API REST do Azure notificação concentrador](https://msdn.microsoft.com/library/azure/dn223264.aspx).

1. Na vista de projeto do Android Studio, expanda **aplicação** > **src** > **principal** > **resoluções** > **esquema**. Abrir o `activity_main.xml` ficheiro de esquema e clique no **texto** do separador para atualizar o conteúdo de texto do ficheiro. Atualizá-lo com o código abaixo, que adiciona novos `Button` e `EditText` controlos para enviar mensagens de notificação de push para o concentrador de notificação. Adicionar apenas antes deste código na parte inferior, `</RelativeLayout>`.

        <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="@string/send_button"
        android:id="@+id/sendbutton"
        android:layout_centerVertical="true"
        android:layout_centerHorizontal="true"
        android:onClick="sendNotificationButtonOnClick" />

        <EditText
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/editTextNotificationMessage"
        android:layout_above="@+id/sendbutton"
        android:layout_centerHorizontal="true"
        android:layout_marginBottom="42dp"
        android:hint="@string/notification_message_hint" />

2. Na vista de projeto do Android Studio, expanda **aplicação** > **src** > **principal** > **resoluções** > **valores**. Abrir o `strings.xml` ficheiro e adicionar os valores de cadeia que são referenciados pela nova `Button` e `EditText` controlos. Adicioná-los na parte inferior do ficheiro, basta antes `</resources>`.

        <string name="send_button">Send Notification</string>
        <string name="notification_message_hint">Enter notification message text</string>


3. No seu `NotificationSetting.java` de ficheiros, adicione a seguinte definição para o `NotificationSettings` escolares.

    Actualização `HubFullAccess` com a cadeia de ligação **DefaultFullSharedAccessSignature** para o seu centro. Nesta cadeia de ligação pode ser copiada a partir do [Portal do Azure] ao clicar em **Políticas de acesso** no pá **Definições** para o seu centro de notificação.

        public static String HubFullAccess = "<Enter Your DefaultFullSharedAccess Connection string>";

4. No seu `MainActivity.java` de ficheiros, adicione o seguinte `import` declarações acima a `MainActivity` escolares.

        import java.io.BufferedOutputStream;
        import java.io.BufferedReader;
        import java.io.InputStreamReader;
        import java.io.OutputStream;
        import java.net.HttpURLConnection;
        import java.net.URL;
        import java.net.URLEncoder;
        import javax.crypto.Mac;
        import javax.crypto.spec.SecretKeySpec;
        import android.util.Base64;
        import android.view.View;
        import android.widget.EditText;

6. No seu `MainActivity.java` de ficheiros, adicione os seguintes membros na parte superior da `MainActivity` escolares.  

        private String HubEndpoint = null;
        private String HubSasKeyName = null;
        private String HubSasKeyValue = null;

6. Tem de criar um token de Software de assinatura do Access (SA) para autenticar um pedido de mensagem para enviar mensagens para o seu centro de notificação. Isto é feito analisar os dados chaves da cadeia de ligação e, em seguida, criando token SA, tal como mencionado na referência REST API [Conceitos comuns](http://msdn.microsoft.com/library/azure/dn495627.aspx) . O código seguinte é um exemplo da implementação.

    No `MainActivity.java`, adicione o seguinte método para o `MainActivity` escolares para analisar a cadeia de ligação.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx
         * to parse the connection string so a SaS authentication token can be
         * constructed.
         *
         * @param connectionString This must be the DefaultFullSharedAccess connection
         *                         string for this example.
         */
        private void ParseConnectionString(String connectionString)
        {
            String[] parts = connectionString.split(";");
            if (parts.length != 3)
                throw new RuntimeException("Error parsing connection string: "
                        + connectionString);
    
            for (int i = 0; i < parts.length; i++) {
                if (parts[i].startsWith("Endpoint")) {
                    this.HubEndpoint = "https" + parts[i].substring(11);
                } else if (parts[i].startsWith("SharedAccessKeyName")) {
                    this.HubSasKeyName = parts[i].substring(20);
                } else if (parts[i].startsWith("SharedAccessKey")) {
                    this.HubSasKeyValue = parts[i].substring(16);
                }
            }
        }


7. No `MainActivity.java`, adicione o seguinte método para o `MainActivity` escolares para criar um token de autenticação SA.

        /**
         * Example code from http://msdn.microsoft.com/library/azure/dn495627.aspx to
         * construct a SaS token from the access key to authenticate a request.
         *
         * @param uri The unencoded resource URI string for this operation. The resource
         *            URI is the full URI of the Service Bus resource to which access is
         *            claimed. For example,
         *            "http://<namespace>.servicebus.windows.net/<hubName>"
         */
        private String generateSasToken(String uri) {
    
            String targetUri;
            String token = null;
            try {
                targetUri = URLEncoder
                        .encode(uri.toString().toLowerCase(), "UTF-8")
                        .toLowerCase();
    
                long expiresOnDate = System.currentTimeMillis();
                int expiresInMins = 60; // 1 hour
                expiresOnDate += expiresInMins * 60 * 1000;
                long expires = expiresOnDate / 1000;
                String toSign = targetUri + "\n" + expires;
    
                // Get an hmac_sha1 key from the raw key bytes
                byte[] keyBytes = HubSasKeyValue.getBytes("UTF-8");
                SecretKeySpec signingKey = new SecretKeySpec(keyBytes, "HmacSHA256");
    
                // Get an hmac_sha1 Mac instance and initialize with the signing key
                Mac mac = Mac.getInstance("HmacSHA256");
                mac.init(signingKey);
    
                // Compute the hmac on input data bytes
                byte[] rawHmac = mac.doFinal(toSign.getBytes("UTF-8"));
    
                // Using android.util.Base64 for Android Studio instead of
                // Apache commons codec
                String signature = URLEncoder.encode(
                        Base64.encodeToString(rawHmac, Base64.NO_WRAP).toString(), "UTF-8");
    
                // Construct authorization string
                token = "SharedAccessSignature sr=" + targetUri + "&sig="
                        + signature + "&se=" + expires + "&skn=" + HubSasKeyName;
            } catch (Exception e) {
                if (isVisible) {
                    ToastNotify("Exception Generating SaS : " + e.getMessage().toString());
                }
            }
    
            return token;
        }



8. No `MainActivity.java`, adicione o seguinte método para o `MainActivity` escolares para processar o clique de botão **Enviar notificação** e enviar a mensagem de notificação push ao concentrador ao utilizar a API REST incorporados.

        /**
         * Send Notification button click handler. This method parses the
         * DefaultFullSharedAccess connection string and generates a SaS token. The
         * token is added to the Authorization header on the POST request to the
         * notification hub. The text in the editTextNotificationMessage control
         * is added as the JSON body for the request to add a GCM message to the hub.
         *
         * @param v
         */
        public void sendNotificationButtonOnClick(View v) {
            EditText notificationText = (EditText) findViewById(R.id.editTextNotificationMessage);
            final String json = "{\"data\":{\"message\":\"" + notificationText.getText().toString() + "\"}}";
    
            new Thread()
            {
                public void run()
                {
                    try
                    {
                        // Based on reference documentation...
                        // http://msdn.microsoft.com/library/azure/dn223273.aspx
                        ParseConnectionString(NotificationSettings.HubFullAccess);
                        URL url = new URL(HubEndpoint + NotificationSettings.HubName +
                                "/messages/?api-version=2015-01");
    
                        HttpURLConnection urlConnection = (HttpURLConnection)url.openConnection();
    
                        try {
                            // POST request
                            urlConnection.setDoOutput(true);
    
                            // Authenticate the POST request with the SaS token
                            urlConnection.setRequestProperty("Authorization", 
                                generateSasToken(url.toString()));
    
                            // Notification format should be GCM
                            urlConnection.setRequestProperty("ServiceBusNotification-Format", "gcm");
    
                            // Include any tags
                            // Example below targets 3 specific tags
                            // Refer to : https://azure.microsoft.com/en-us/documentation/articles/notification-hubs-routing-tag-expressions/
                            // urlConnection.setRequestProperty("ServiceBusNotification-Tags", 
                            //      "tag1 || tag2 || tag3");
    
                            // Send notification message
                            urlConnection.setFixedLengthStreamingMode(json.length());
                            OutputStream bodyStream = new BufferedOutputStream(urlConnection.getOutputStream());
                            bodyStream.write(json.getBytes());
                            bodyStream.close();
    
                            // Get reponse
                            urlConnection.connect();
                            int responseCode = urlConnection.getResponseCode();
                            if ((responseCode != 200) && (responseCode != 201)) {
                                BufferedReader br = new BufferedReader(new InputStreamReader((urlConnection.getErrorStream())));
                                String line;
                                StringBuilder builder = new StringBuilder("Send Notification returned " +
                                        responseCode + " : ")  ;
                                while ((line = br.readLine()) != null) {
                                    builder.append(line);
                                }

                                ToastNotify(builder.toString());
                            }
                        } finally {
                            urlConnection.disconnect();
                        }
                    }
                    catch(Exception e)
                    {
                        if (isVisible) {
                            ToastNotify("Exception Sending Notification : " + e.getMessage().toString());
                        }
                    }
                }
            }.start();
        }



##<a name="testing-your-app"></a>Testar a sua aplicação

####<a name="push-notifications-in-the-emulator"></a>Notificações de emissão o emulador

Se pretender testar as notificações push dentro de uma emulador, certifique-se de que a sua imagem emulador suporta o nível de API do Google que escolheu para a sua aplicação. Se a sua imagem não suporta nativo APIs da Google, ficará com o **serviço\_não\_disponível** exceção.

Para além de acima, certifique-se de que adicionou a conta do Google ao seu emulador em execução em **Definições** > **contas**. Caso contrário, tenta registar GCM pode resultar no **autenticação\_falhou** exceção.

####<a name="running-the-application"></a>Executar a aplicação

1. Executar a aplicação e repare que o ID de registo é comunicado para um registo com êxito.

    ![Testes no Android - registo de canal][18]

2. Introduza uma mensagem de notificação para ser enviada para todos os dispositivos Android que tem registado com o concentrador.

    ![Testes no Android - enviar uma mensagem][19]

3. Prima **Enviar notificação**. Todos os dispositivos que tem a aplicação em execução irão mostrar uma `AlertDialog` instância com a mensagem de notificação push. Dispositivos que não tem a aplicação em execução, mas que estavam anteriormente registados para notificações push irão receber uma notificação no Gestor de notificação Android. Aqueles podem ser visualizadas ao percorrer para baixo a partir do canto superior esquerdo.

    ![Testes no Android - as notificações][21]

##<a name="next-steps"></a>Próximos passos

Recomendamos que o tutorial [Concentradores de notificação de utilização para as notificações push aos utilizadores] como o passo seguinte. -Lo irá mostrar-lhe como enviar notificações a partir de um ASP.NET back-end, utilizar etiquetas para utilizadores específicos de destino.

Se pretender segmentar os seus utilizadores por grupos de interesse, consulte o artigo o tutorial [Concentradores de notificação de utilização para enviar notícias de última hora] .

Para obter mais informações gerais sobre concentradores de notificação, consulte o nosso [Notificação concentradores orientações].

<!-- Images. -->
[6]: ./media/notification-hubs-android-get-started/notification-hub-android-new-class.png

[12]: ./media/notification-hubs-android-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-new-project.png
[14]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-choose-form-factor.png
[15]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app4.png
[16]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app5.png
[17]: ./media/notification-hubs-android-get-started/notification-hub-create-android-app6.png

[18]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-registered.png
[19]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-set-message.png

[20]: ./media/notification-hubs-android-get-started/notification-hub-create-console-app.png
[21]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-received-message.png
[22]: ./media/notification-hubs-android-get-started/notification-hub-scheduler1.png
[23]: ./media/notification-hubs-android-get-started/notification-hub-scheduler2.png
[29]: ./media/mobile-services-android-get-started-push/mobile-eclipse-import-Play-library.png

[30]: ./media/notification-hubs-android-get-started/notification-hubs-debug-hub-gcm.png

[31]: ./media/notification-hubs-android-get-started/notification-hubs-android-studio-add-ui.png


<!-- URLs. -->
[Get started with push notifications in Mobile Services]: ../mobile-services-javascript-backend-android-get-started-push.md  
[Mobile Services Android SDK]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Referencing a library project]: http://go.microsoft.com/fwlink/?LinkId=389800
[Azure Classic Portal]: https://manage.windowsazure.com/
[Notificação concentradores orientações]: http://msdn.microsoft.com/library/jj927170.aspx
[Utilizar concentradores de notificação para as notificações push para os utilizadores]: notification-hubs-aspnet-backend-gcm-android-push-to-user-google-notification.md
[Utilizar concentradores de notificação para enviar notícias de última hora]: notification-hubs-aspnet-backend-android-xplat-segmented-gcm-push-notification.md
[Portal do Azure]: https://portal.azure.com
