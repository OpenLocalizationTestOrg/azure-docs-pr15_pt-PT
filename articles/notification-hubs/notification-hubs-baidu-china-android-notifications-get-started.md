<properties
    pageTitle="Introdução ao Azure notificação concentradores utilizando Baidu | Microsoft Azure"
    description="Neste tutorial, saiba como utilizar o Azure notificação concentradores para as notificações push para dispositivos Android utilizando Baidu."
    services="notification-hubs"
    documentationCenter="android"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.devlang="java"
    ms.topic="hero-article"
    ms.tgt_pltfrm="mobile-baidu"
    ms.workload="mobile"
    ms.date="08/19/2016"
    ms.author="yuaxu"/>

# <a name="get-started-with-notification-hubs-using-baidu"></a>Introdução ao concentradores notificação utilizando Baidu

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

##<a name="overview"></a>Descrição geral

Push de nuvem Baidu é um serviço de nuvem chinês que pode utilizar para enviar notificações push para dispositivos móveis. Este serviço é particularmente útil na China, onde fornecer notificações push a Android é complexa devido a presença de arquivos de aplicação diferente e serviços de emissão, para além da disponibilidade de dispositivos Android que normalmente não estiver ligado a GCM (Google Cloud mensagens).

##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial requer o seguinte:

+ Android SDK (Recomendamos partem do princípio de que irá utilizar Eclipse), que pode transferir a partir do <a href="http://go.microsoft.com/fwlink/?LinkId=389797">Android site</a>
+ [SDK Android de serviços móveis]
+ [Baidu Push Android SDK]

>[AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%2Fnotification-hubs-baidu-get-started%2F).


##<a name="create-a-baidu-account"></a>Criar uma conta de Baidu

Para utilizar Baidu, tem de ter uma conta de Baidu. Se já tiver uma, inicie a sessão [portal de Baidu] e avançar para o próximo passo. Caso contrário, consulte as instruções abaixo sobre como criar uma conta de Baidu.  

1. Aceda ao [portal de Baidu] e clique na ligação**登录**(**início de sessão**). Clique em**立即注册**para iniciar o processo de registo de conta.

    ![][1]

2. Introduza os detalhes necessários — telefone/correio eletrónico do código de endereço, palavra-passe e verificação — e clique em **início de sessão no**.

    ![][2]

3. Será enviado uma mensagem de e-mail para o endereço de e-mail que introduziu com uma ligação para ativar a sua conta Baidu.

    ![][3]

4. Inicie sessão na sua conta de e-mail, abra o mail de ativação Baidu e clique na ligação de ativação para ativar a sua conta Baidu.

    ![][4]

Assim que tiver uma conta de Baidu ativada, inicie a sessão [portal de Baidu].

##<a name="register-as-a-baidu-developer"></a>Registe-se como um programador Baidu

1. Assim que tiver sessão iniciada [portal de Baidu], clique em**更多 >>** (**mais**).

    ![][5]

2. Deslocar para baixo na secção**站长与开发者服务 (Webmaster e serviços de programador)** e clique em**百度开放云平台**(**Baidu abrir plataforma na nuvem**).

    ![][6]

3. Na página seguinte, clique em**开发者服务**(**Programador serviços**) no canto superior direito.

    ![][7]

4. Na página seguinte, clique em**注册开发者**(**Os programadores registado**) a partir do menu no canto superior direito.

    ![][8]

5. Introduza o seu nome, uma descrição e um número de telemóvel para receber uma mensagem de texto de verificação e, em seguida, clique em**送验证码**(**Código de verificação Enviar**). Tenha em atenção que para números de telefone internacionais, tem de incluir o indicativo do país entre parênteses. Por exemplo, para um número de Portugal, vai ser **(1) 1234567890**.

    ![][9]

6. Em seguida, deverá receber uma mensagem de texto com um número de verificação, conforme mostrado no exemplo seguinte:

    ![][10]

7. Introduza o número de verificação a partir da mensagem no**验证码**(**código de confirmação**).

8. Por fim, execute o registo de programador aceitar o contrato de Baidu e clicando em**提交**(**Submeter**). Irá ver a seguinte página concluído com êxito de registo:

    ![][11]

##<a name="create-a-baidu-cloud-push-project"></a>Criar um projeto de push Baidu na nuvem

Quando cria um projeto de push Baidu na nuvem, recebe o seu ID de aplicação, chave API e chave secreta.

1. Assim que tiver sessão iniciada [portal de Baidu], clique em**更多 >>** (**mais**).

    ![][5]

2. Deslocar para baixo na secção**站长与开发者服务**(**Webmaster e nos serviços do programador**) e clique em**百度开放云平台**(**Baidu abrir plataforma na nuvem**).

    ![][6]

3. Na página seguinte, clique em**开发者服务**(**Programador serviços**) no canto superior direito.

    ![][7]

4. Na página seguinte, clique em**云推送**(**Na nuvem emissão**) a partir da secção**云服务**(**Serviços em nuvem**).

    ![][12]

5. Assim que estiver um programador registado, irá ver**管理控制台**(**Consola de gestão**) no menu de superior. Clique em**开发者服务管理**(**os programadores de gestão do serviço**).

    ![][13]

6. Na página seguinte, clique em**创建工程**(**Criar projeto**).

    ![][14]

7. Introduza um nome da aplicação e clique em**创建**(**Criar**).

    ![][15]

8. Durante a criação de um projeto de push Baidu na nuvem do bem sucedida, irá ver uma página com **AppID**, **API chave**e **Chave secreta**. Tome nota da chave de API e chave secreta, que utilizamos mais tarde.

    ![][16]

9. Configure o projecto para as notificações push ao clicar em**云推送**(**Na nuvem Push**) no painel da esquerda.

    ![][31]

10. Na página seguinte, clique no botão**推送设置**(**definições de emissão**).

    ![][32]  

11. Na página configuração, adicione o nome do pacote que que vai ser utilizar no seu projeto Android no campo**应用包名**(**pacote de aplicação**) e, em seguida, clique em**保存设置**(**Guardar**).  

    ![][33]

Irá ver a**保存成功!** (**guardado com êxito!**) mensagem.

##<a name="configure-your-notification-hub"></a>Configurar o seu centro de notificação

1. Inicie sessão no [Portal clássica do Azure]e, em seguida, clique em **+ Novo** na parte inferior do ecrã.

2. Clique em **Serviços de aplicação**, clique em **Serviço Bus**, clique em **Centro de notificação**e, em seguida, clique em **Criar rápida**.

3. Forneça um nome para o **Centro de notificação**, selecione a **região** e o **espaço de nomes** onde vai ser criado neste concentrador de notificação e, em seguida, clique em **criar um concentrador de notificação de novo**.  

    ![][17]

4. Clique no espaço de nomes na qual o seu centro de notificação que criou e, em seguida, clique em **Concentradores de notificação** na parte superior.

    ![][18]

5. Selecione o concentrador de notificação que criou e, em seguida, clique em **Configurar** a partir do menu superior.

    ![][19]

6. Desloque para baixo até à secção **definições de notificação de baidu** e introduza a chave de API e chave secreta, que obteve a partir da consola de Baidu anteriormente para o seu projeto de push Baidu na nuvem. Clique em **Guardar**.

    ![][20]

7. Clique no separador **Dashboard** na parte superior para o concentrador de notificação e, em seguida, clique em **Cadeia de ligação da vista**.

    ![][21]

8. Tome nota das **DefaultListenSharedAccessSignature** e **DefaultFullSharedAccessSignature** a partir da janela **informações de ligação de acesso** .

    ![][22]

##<a name="connect-your-app-to-the-notification-hub"></a>Ligar a sua aplicação para o concentrador de notificação

1. Em Eclipse TSA, criar um novo projeto Android (**ficheiro** > **Novo** > **Android de aplicação de projeto**).

    ![][23]

2. Introduza um **Nome da aplicação** e certifique-se de que a versão de **Mínimo obrigatório SDK** está definida para **API 16: Android 4.1**.

    ![][24]

3. Clique em **seguinte** e continue a seguir o assistente até que é apresentada a janela de **Atividade de criar** . Certifique-se de que a **Atividade em branco** está selecionada e, por fim, selecione **Concluir** para criar uma nova aplicação Android.

    ![][25]

4. Certifique-se de que o **Project construir destino** está definida corretamente.

    ![][26]

5. Transferir o ficheiro de notificação-concentradores-0.4.jar a partir do separador **ficheiros** do [Notificação-concentradores-Android-SDK no Bintray](https://bintray.com/microsoftazuremobile/SDK/Notification-Hubs-Android-SDK/0.4). Adicione o ficheiro para a pasta de **efectuar** do seu projeto Eclipse e atualizar a pasta de *efectuar* .

6. Transferir e descomprimir o ficheiro o [Baidu emissão SDK Android], abra a pasta de **efectuar** e, em seguida, copie o ficheiro de para caixa de **pushservice x.y.z** e o **armeabi** & **mips** pastas na pasta **efectuar** da sua aplicação Android.

7. Abra o ficheiro **AndroidManifest.xml** do seu projeto Android e adicione as permissões que são necessários para o SDK Baidu.

        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.READ_PHONE_STATE" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.RECEIVE_BOOT_COMPLETED" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.VIBRATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.DISABLE_KEYGUARD" />
        <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.ACCESS_DOWNLOAD_MANAGER" />
        <uses-permission android:name="android.permission.DOWNLOAD_WITHOUT_NOTIFICATION" />

8. Adicione a propriedade **nome do android:** a sua **aplicação** elemento **AndroidManifest.xml**, substituindo *yourprojectname* (por exemplo, **com.example.BaiduTest**). Certifique-se de que este nome do projeto corresponde àquele que configurou na consola do Baidu.

        <application android:name="yourprojectname.DemoApplication"

9. Adicione a seguinte configuração dentro do elemento de aplicação após a **. MainActivity** elemento de atividade, substituindo *yourprojectname* (por exemplo, **com.example.BaiduTest**):

        <receiver android:name="yourprojectname.MyPushMessageReceiver">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.MESSAGE" />
                <action android:name="com.baidu.android.pushservice.action.RECEIVE" />
                <action android:name="com.baidu.android.pushservice.action.notification.CLICK" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.PushServiceReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="android.intent.action.BOOT_COMPLETED" />
                <action android:name="android.net.conn.CONNECTIVITY_CHANGE" />
                <action android:name="com.baidu.android.pushservice.action.notification.SHOW" />
            </intent-filter>
        </receiver>

        <receiver android:name="com.baidu.android.pushservice.RegistrationReceiver"
            android:process=":bdservice_v1">
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.METHOD" />
                <action android:name="com.baidu.android.pushservice.action.BIND_SYNC" />
            </intent-filter>
            <intent-filter>
                <action android:name="android.intent.action.PACKAGE_REMOVED"/>
                <data android:scheme="package" />
            </intent-filter>
        </receiver>

        <service
            android:name="com.baidu.android.pushservice.PushService"
            android:exported="true"
            android:process=":bdservice_v1"  >
            <intent-filter>
                <action android:name="com.baidu.android.pushservice.action.PUSH_SERVICE" />
            </intent-filter>
        </service>

9. Adicione uma nova classe denominada **ConfigurationSettings.java** ao projeto.

    ![][28]

    ![][29]

10. Adicione o seguinte código para o mesmo:

        public class ConfigurationSettings {
                public static String API_KEY = "...";
                public static String NotificationHubName = "...";
                public static String NotificationHubConnectionString = "...";
            }

    Defina o valor de **API_KEY** com que recuperou do Baidu nuvem projeto versões anteriores, **NotificationHubName** com o seu nome de concentrador de notificação a partir do Portal de clássica do Azure e **NotificationHubConnectionString** com DefaultListenSharedAccessSignature a partir do Portal de clássica Azure.

11. Adicionar uma nova classe denominada **DemoApplication.java**e adicionar-lhe o seguinte código:

        import com.baidu.frontia.FrontiaApplication;

        public class DemoApplication extends FrontiaApplication {
            @Override
            public void onCreate() {
                super.onCreate();
            }
        }

12. Adicionar outra nova classe denominada **MyPushMessageReceiver.java**e adicionar o código abaixo à mesma. Esta é a classe que processa as notificações push que são recebidas do servidor de push Baidu.

        import java.util.List;
        import android.content.Context;
        import android.os.AsyncTask;
        import android.util.Log;
        import com.baidu.frontia.api.FrontiaPushMessageReceiver;
        import com.microsoft.windowsazure.messaging.NotificationHub;

        public class MyPushMessageReceiver extends FrontiaPushMessageReceiver {
            /** TAG to Log */
            public static NotificationHub hub = null;
            public static String mChannelId, mUserId;
            public static final String TAG = MyPushMessageReceiver.class
                    .getSimpleName();

            @Override
            public void onBind(Context context, int errorCode, String appid,
                    String userId, String channelId, String requestId) {
                String responseString = "onBind errorCode=" + errorCode + " appid="
                        + appid + " userId=" + userId + " channelId=" + channelId
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
                mChannelId = channelId;
                mUserId = userId;

                try {
                 if (hub == null) {
                        hub = new NotificationHub(
                                ConfigurationSettings.NotificationHubName,
                                ConfigurationSettings.NotificationHubConnectionString,
                                context);
                        Log.i(TAG, "Notification hub initialized");
                    }
                } catch (Exception e) {
                   Log.e(TAG, e.getMessage());
                }

                registerWithNotificationHubs();
            }

            private void registerWithNotificationHubs() {
               new AsyncTask<Void, Void, Void>() {
                  @Override
                  protected Void doInBackground(Void... params) {
                     try {
                         hub.registerBaidu(mUserId, mChannelId);
                         Log.i(TAG, "Registered with Notification Hub - '"
                                + ConfigurationSettings.NotificationHubName + "'"
                                + " with UserId - '"
                                + mUserId + "' and Channel Id - '"
                                + mChannelId + "'");
                     } catch (Exception e) {
                         Log.e(TAG, e.getMessage());
                     }
                     return null;
                 }
               }.execute(null, null, null);
            }

            @Override
            public void onSetTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onSetTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onDelTags(Context context, int errorCode,
                    List<String> sucessTags, List<String> failTags, String requestId) {
                String responseString = "onDelTags errorCode=" + errorCode
                        + " sucessTags=" + sucessTags + " failTags=" + failTags
                        + " requestId=" + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onListTags(Context context, int errorCode, List<String> tags,
                    String requestId) {
                String responseString = "onListTags errorCode=" + errorCode + " tags="
                        + tags;
                Log.d(TAG, responseString);
            }

            @Override
            public void onUnbind(Context context, int errorCode, String requestId) {
                String responseString = "onUnbind errorCode=" + errorCode
                        + " requestId = " + requestId;
                Log.d(TAG, responseString);
            }

            @Override
            public void onNotificationClicked(Context context, String title,
                    String description, String customContentString) {
                String notifyString = "title=\"" + title + "\" description=\""
                        + description + "\" customContent=" + customContentString;
                Log.d(TAG, notifyString);
            }

            @Override
            public void onMessage(Context context, String message,
                    String customContentString) {
                String messageString = "message=\"" + message + "\" customContentString=" + customContentString;
                Log.d(TAG, messageString);
            }
        }

13. Abra o **MainActivity.java**e adicione o seguinte para o método de **onCreate** :

            PushManager.startWork(getApplicationContext(),
                    PushConstants.LOGIN_TYPE_API_KEY, ConfigurationSettings.API_KEY);

14. Abra as seguintes declarações de importação na parte superior:

            import com.baidu.android.pushservice.PushConstants;
            import com.baidu.android.pushservice.PushManager;

##<a name="send-notifications-to-your-app"></a>Enviar notificações para a sua aplicação


Pode rapidamente testar receber notificações na sua aplicação ao enviar notificações no [Portal do Azure](https://portal.azure.com/) utilizando o botão **Testar enviar** no centro do notificação, conforme mostrado no ecrã abaixo.

![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-test-send-wns.png)

Notificações push normalmente são enviadas num serviço de back-end como serviços Mobile ou ASP.NET através de uma biblioteca compatível. Também pode utilizar a API REST diretamente para enviar mensagens de notificação se uma biblioteca não está disponível para back-end.

Neste tutorial, iremos mantenha-a simples e apenas demonstram testar a sua aplicação cliente ao enviar notificações utilizando o .NET SDK para concentradores notificação numa aplicação de consola em vez de um serviço de back-end. Recomendamos que o tutorial [Concentradores de notificação de utilização para as notificações push aos utilizadores](notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md) como o próximo passo para o envio de notificações a partir de um back-end ASP.NET. No entanto, as seguintes abordagens podem ser utilizadas para o envio de notificações:

* **Interface do resto**: pode suportar notificação qualquer plataforma de back-end utilizando a [interface de descanso](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx).

* **Microsoft Azure notificação concentradores .NET SDK**: no Nuget pacote Gestor de para Visual Studio, executar o [Pacote de instalação Microsoft.Azure.NotificationHubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/).

* **Node.js** : [como utilizar concentradores de notificação de Node.js](notification-hubs-nodejs-push-notification-tutorial.md).

* **Aplicações móveis**: para um exemplo sobre como enviar notificações a partir de um back-end de aplicações móveis do Azure aplicação serviço que está integrado com o concentradores de notificação, consulte o artigo [Adicionar as notificações push para a sua aplicação móvel](../app-service-mobile/app-service-mobile-windows-store-dotnet-get-started-push.md).

* **Java / PHP**: para um exemplo sobre como enviar notificações ao utilizar a API REST, consulte o artigo "Como utilizar concentradores de notificação de Java/PHP" ([Java](notification-hubs-java-push-notification-tutorial.md) | [PHP](notification-hubs-php-push-notification-tutorial.md)).

##<a name="optional-send-notifications-from-a-net-console-app"></a>(Opcional) Envie notificações a partir de uma aplicação de consola do .NET.

Nesta secção, mostramos-enviar uma notificação de utilizar uma aplicação de consola do .NET.

1. Crie uma nova aplicação de consola do Visual c#:

    ![][30]

2. Na janela da consola do Gestor de pacote, definir **predefinidos do project** para o novo projeto de aplicação de consola e, em seguida, na janela da consola, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência para o SDK do Azure notificação concentradores utilizando o <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">pacote de Microsoft.Azure.Notification concentradores NuGet</a>.

    ![](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-package-manager.png)

3. Abra o ficheiro **Program.cs** e adicione o seguinte utilizando instrução:

        using Microsoft.Azure.NotificationHubs;

4. No seu `Program` classe, adicione o seguinte método e substituir *DefaultFullSharedAccessSignatureSASConnectionString* e *NotificationHubName* com os valores que possui.

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("DefaultFullSharedAccessSignatureSASConnectionString", "NotificationHubName");
            string message = "{\"title\":\"((Notification title))\",\"description\":\"Hello from Azure\"}";
            var result = await hub.SendBaiduNativeNotificationAsync(message);
        }

5. Adicione as seguintes linhas no seu método de **principais** :

         SendNotificationAsync();
         Console.ReadLine();

##<a name="test-your-app"></a>Testar a sua aplicação

Para testar esta aplicação com um telemóvel real, basta ligar o telefone ao seu computador utilizando um cabo USB. Este procedimento carrega sua aplicação para o telefone anexado.

Para testar esta aplicação com o emulador, na barra de ferramentas superior a Eclipse, clique em **Executar**e, em seguida, selecione a sua aplicação. Isto inicia o emulador e, em seguida, carrega e executa a aplicação.

A aplicação obtém a 'ID de utilizador' e 'channelId' a partir do serviço de notificações Baidu Push e regista com o concentrador de notificação.

Para enviar uma notificação de teste, que pode utilizar o separador de depuração do Portal clássica do Azure. Se já criado a aplicação da consola do .NET para Visual Studio, basta premir a tecla F5 no Visual Studio para executar a aplicação. A aplicação irá enviar uma notificação que irá aparecer na área de notificação superior do seu dispositivo ou emulador.


<!-- Images. -->
[1]: ./media/notification-hubs-baidu-get-started/BaiduRegistration.png
[2]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationInput.png
[3]: ./media/notification-hubs-baidu-get-started/BaiduConfirmation.png
[4]: ./media/notification-hubs-baidu-get-started/BaiduActivationEmail.png
[5]: ./media/notification-hubs-baidu-get-started/BaiduRegistrationMore.png
[6]: ./media/notification-hubs-baidu-get-started/BaiduOpenCloudPlatform.png
[7]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperServices.png
[8]: ./media/notification-hubs-baidu-get-started/BaiduDeveloperRegistration.png
[9]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationInput.png
[10]: ./media/notification-hubs-baidu-get-started/BaiduDevRegistrationConfirmation.png
[11]: ./media/notification-hubs-baidu-get-started/BaiduDevConfirmationFinal.png
[12]: ./media/notification-hubs-baidu-get-started/BaiduCloudPush.png
[13]: ./media/notification-hubs-baidu-get-started/BaiduDevSvcMgmt.png
[14]: ./media/notification-hubs-baidu-get-started/BaiduCreateProject.png
[15]: ./media/notification-hubs-baidu-get-started/BaiduCreateProjectInput.png
[16]: ./media/notification-hubs-baidu-get-started/BaiduProjectKeys.png
[17]: ./media/notification-hubs-baidu-get-started/AzureNHCreation.png
[18]: ./media/notification-hubs-baidu-get-started/NotificationHubs.png
[19]: ./media/notification-hubs-baidu-get-started/NotificationHubsConfigure.png
[20]: ./media/notification-hubs-baidu-get-started/NotificationHubBaiduConfigure.png
[21]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionStringView.png
[22]: ./media/notification-hubs-baidu-get-started/NotificationHubsConnectionString.png
[23]: ./media/notification-hubs-baidu-get-started/EclipseNewProject.png
[24]: ./media/notification-hubs-baidu-get-started/EclipseProjectCreation.png
[25]: ./media/notification-hubs-baidu-get-started/EclipseBlankActivity.png
[26]: ./media/notification-hubs-baidu-get-started/EclipseProjectBuildProperty.png
[27]: ./media/notification-hubs-baidu-get-started/EclipseBaiduReferences.png
[28]: ./media/notification-hubs-baidu-get-started/EclipseNewClass.png
[29]: ./media/notification-hubs-baidu-get-started/EclipseConfigSettingsClass.png
[30]: ./media/notification-hubs-baidu-get-started/ConsoleProject.png
[31]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig1.png
[32]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig2.png
[33]: ./media/notification-hubs-baidu-get-started/BaiduPushConfig3.png

<!-- URLs. -->
[SDK Android de serviços móveis]: https://go.microsoft.com/fwLink/?LinkID=280126&clcid=0x409
[Baidu Push Android SDK]: http://developer.baidu.com/wiki/index.php?title=docs/cplat/push/sdk/clientsdk
[Azure Portal clássico]: https://manage.windowsazure.com/
[Baidu portal]: http://www.baidu.com/
