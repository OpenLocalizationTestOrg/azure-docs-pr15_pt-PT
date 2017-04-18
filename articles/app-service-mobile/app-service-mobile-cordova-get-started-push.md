<properties
    pageTitle="Adicionar as notificações Push Apache Cordova aplicação com aplicações móveis do Azure | Azure de aplicação de serviço"
    description="Saiba como utilizar aplicações do Azure Mobile para enviar notificações push para a sua aplicação Apache Cordova."
    services="app-service\mobile"
    documentationCenter="javascript"
    manager="erikre"
    editor=""
    authors="ysxu"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-html"
    ms.devlang="javascript"
    ms.topic="article"
    ms.date="10/01/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-apache-cordova-app"></a>Adicionar as notificações push para a sua aplicação Apache Cordova

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial, adicione as notificações push ao projeto [Apache Cordova rápida iniciar] para que uma notificação de emissão é enviada para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, é necessário o pacote de extensão de notificações push. Consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="prerequisites"></a>Pré-requisitos

Neste tutorial abrange uma aplicação de Apache Cordova desenvolvida com o Visual Studio 2015 que é executada no emulador Android Google, dispositivo Windows, dispositivo Android e num dispositivo iOS.

Para concluir este tutorial, é necessário:

* Um PC com [Visual Studio Comunidade 2015] ou versões posteriores.
* [Ferramentas do visual Studio para Apache Cordova].
* Uma [conta Azure ativa](https://azure.microsoft.com/pricing/free-trial/).
* Um projecto [Cordova Apache guia] concluído.
* (Android) Uma [conta do Google] com um endereço de e-mail verificado.
* (iOS) Uma associação do programa de programador do Apple e num dispositivo iOS (iOS Simulator não suporta push).
* (Windows) Uma conta de programador da loja Windows e um dispositivo Windows 10.

##<a name="configure-hub"></a>Configurar um concentrador de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

[Veja um vídeo que mostra os passos nesta secção](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-3-Create-azure-notification-hub)

##<a name="update-the-server-project-to-send-push-notifications"></a>Actualizar o servidor de projecto para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

##<a name="add-push-to-app"></a>Modificar a sua aplicação Cordova para receber notificações push

Deve certificar-se de que o projeto de aplicação Apache Cordova está pronto a processar as notificações push ao instalar o plug-in do Cordova push plus quaisquer serviços push específicos da plataforma.

#### <a name="update-the-cordova-version-in-your-project"></a>Atualize a versão de Cordova no projeto.

Recomendamos que Atualize o projecto do cliente para Cordova 6.1.1 se o seu projeto está configurado com uma versão anterior. Para atualizar o project, botão direito do rato config para abrir o estruturador de configuração. Selecione o separador plataformas e selecione 6.1.1 na caixa de texto **Cordova clip** .

Selecione **Criar**, em seguida, **Criar solução** para atualizar o projeto.

#### <a name="install-the-push-plugin"></a>Instalar o plug-in push

Aplicações de Apache Cordova não vierem processam capacidades de dispositivo ou rede.  Estas capacidades são fornecidas pelo plug-ins que são publicadas [npm](https://www.npmjs.com/) ou GitHub.  O `phonegap-plugin-push` Plug-in é utilizada para processar as notificações push rede.

Pode instalar o plug-in push de uma das seguintes formas:

**A partir de comandos:**

Execute o seguinte comando:

    cordova plugin add phonegap-plugin-push

**A partir do Visual Studio:**

1.  No Explorador de solução, abra o `config.xml` ficheiro clique em **Plug-ins** > **personalizadas**, selecione **Git** como a origem da instalação, em seguida, introduza `https://github.com/phonegap/phonegap-plugin-push` como a origem.

    ![](./media/app-service-mobile-cordova-get-started-push/add-push-plugin.png)

2.  Clique na seta junto a origem da instalação.

3. No **SENDER_ID**, se já tiver um ID do projecto numéricos para o projeto de consola de programador do Google, pode adicioná-lo aqui. Caso contrário, introduza um valor de marcador de posição, como 777777, e se está a filtrar Android pode atualizar este valor no Config mais tarde.

4. Clique em **Adicionar**.

Agora está instalado o plug-in push.

####<a name="install-the-device-plugin"></a>Instalar o plug-in do dispositivo

Siga o mesmo procedimento que utilizou para instalar o plug-in push, mas irá encontrar o plug-in do dispositivo na lista de plug-ins Core (clique em **Plug-ins** > **Core** para localizá-lo). Precisa deste plug-in para obter o nome de plataforma (`device.platform`).

#### <a name="register-your-device-for-push-on-start-up"></a>Registe-se o seu dispositivo, para push no arranque

Inicialmente, podemos irá incluir algum código mínimo para Android. Mais tarde, irá oferecemos algumas modificações pequenas para executar o iOS ou o Windows 10.

1. Adicione uma chamada para **registerForPushNotifications** durante a chamada de retorno para o processo de início de sessão ou na parte inferior do método **onDeviceReady** :

        // Login to the service.
        client.login('google')
            .then(function () {
                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                    // Added to register for push notifications.
                registerForPushNotifications();

            }, handleError);

    Este exemplo mostra que entra em contacto **registerForPushNotifications** após ser bem sucedida de autenticação, que é recomendado quando utilizar as notificações push e autenticação na sua aplicação.

2. Adicione o novo método de **registerForPushNotifications** da seguinte forma:

        // Register for Push Notifications. Requires that phonegap-plugin-push be installed.
        var pushRegistration = null;
        function registerForPushNotifications() {
          pushRegistration = PushNotification.init({
              android: { senderID: 'Your_Project_ID' },
              ios: { alert: 'true', badge: 'true', sound: 'true' },
              wns: {}
          });

        // Handle the registration event.
        pushRegistration.on('registration', function (data) {
          // Get the native platform of the device.
          var platform = device.platform;
          // Get the handle returned during registration.
          var handle = data.registrationId;
          // Set the device-specific message template.
          if (platform == 'android' || platform == 'Android') {
              // Register for GCM notifications.
              client.push.register('gcm', handle, {
                  mytemplate: { body: { data: { message: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'iOS') {
              // Register for notifications.            
              client.push.register('apns', handle, {
                  mytemplate: { body: { aps: { alert: "{$(messageParam)}" } } }
              });
          } else if (device.platform === 'windows') {
              // Register for WNS notifications.
              client.push.register('wns', handle, {
                  myTemplate: {
                      body: '<toast><visual><binding template="ToastText01"><text id="1">$(messageParam)</text></binding></visual></toast>',
                      headers: { 'X-WNS-Type': 'wns/toast' } }
              });
          }
        });

        pushRegistration.on('notification', function (data, d2) {
          alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
        }

3. (Android) No código acima, substituir `Your_Project_ID` com o valor numérico ID o project para a sua aplicação a partir da [Consola de programador do Google].

## <a name="optional-configure-and-run-the-app-on-android"></a>(Opcional) Configurar e executar a aplicação no Android

Conclua nesta secção para ativar as notificações push para Android.

####<a name="enable-gcm"></a>Ativar Firebase na nuvem mensagens

Uma vez que recomendamos está a filtrar a plataforma do Google Android inicialmente, tem de ativar o serviço de nuvem mensagens Firebase. Da mesma forma, se o foram alvo dispositivos do Microsoft Windows, teria de activar o suporte WNS.

[AZURE.INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

####<a name="configure-backend"></a>Configurar o aplicação Mobile back-end para enviar pedidos de push utilizando FCM

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

####<a name="configure-your-cordova-app-for-android"></a>Configurar a sua aplicação Cordova para Android

Na sua aplicação Cordova, abra config e substituir `Your_Project_ID` com o valor numérico ID o project para a sua aplicação a partir da [Consola de programador do Google].

        <plugin name="phonegap-plugin-push" version="1.7.1" src="https://github.com/phonegap/phonegap-plugin-push.git">
            <variable name="SENDER_ID" value="Your_Project_ID" />
        </plugin>

Abra index.js e atualizar o código para utilizar o seu ID de projeto numéricos.

        pushRegistration = PushNotification.init({
            android: { senderID: 'Your_Project_ID' },
            ios: { alert: 'true', badge: 'true', sound: 'true' },
            wns: {}
        });

####<a name="configure-device"></a>Configurar o seu dispositivo Android para depuração USB

Antes de poder implementar a aplicação para o seu dispositivo Android, tem de ativar a depuração de USB.  Execute os seguintes passos no seu telemóvel Android:

1. Aceda a **Definições** > **sobre telefone**, em seguida, toque em **número de compilação** até que o modo de programador está ativado (cerca de 7 vezes).

2. Novamente na **Definições** > **Opções de programador** ativar a **depuração de USB**, em seguida, ligue o seu telemóvel Android para o desenvolvimento PC com um cabo USB.

Testámos isto utilizar um dispositivo de X Google Nexus 5 a executar o Android 6.0 (caramelo).  No entanto, as técnicas serem comuns qualquer moderno lançamento Android.

#### <a name="install-google-play-services"></a>Instale os serviços Google Play

O plug-in push baseia-se no Android serviços do Google Play para notificações push.  

1.  No **Visual Studio**, clique em **Ferramentas** > **Android** > **Android SDK Gestor**, expanda a pasta **Extras** e marque a caixa para se certificar de que cada um dos seguintes SDK está instalada.
    * Android 2.3 ou superior
    * Revisão Google repositório 27 ou superior
    * Serviços do Google Play 9.0.2 ou superior

2.  Clique em **Instalar pacotes** e aguardar para concluir a instalação.

Atuais necessário bibliotecas estão listadas na [documentação de instalação do plug-in-phonegap-push].

#### <a name="test-push-notifications-in-the-app-on-android"></a>Teste as notificações push na aplicação no Android

Pode as notificações push do agora teste ao executar a aplicação e inserir itens na tabela TodoItem. Pode fazê-lo a partir do mesmo dispositivo ou de um segundo dispositivo, desde que estiver a utilizar o mesmo back-end. Teste a sua aplicação Cordova a plataforma Android de uma das seguintes formas:

- **Num dispositivo físico:**  
Anexe o seu dispositivo Android com o seu computador de desenvolvimento com um cabo USB.  Em vez de **Emulador Android Google**, selecione o **dispositivo**. Visual Studio irá implementar a aplicação para o dispositivo e executá-la.  Em seguida, pode interagir com a aplicação no dispositivo.  
Melhorar a sua experiência de desenvolvimento.  Ecrã de partilha de aplicações como [Mobizen] pode ajudá-lo a desenvolver uma aplicação Android por a projetar ecrã Android para um browser no seu PC.

- **Num emulador Android:**  
Existem passos de configuração adicionais necessários ao usar uma emulador.

    Certifique-se de que está a implementar ou depuração num dispositivo virtual que tenha APIs Google definir como alvo, conforme apresentado abaixo no Gestor de dispositivo Virtual Android (AVD).

    ![](./media/app-service-mobile-cordova-get-started-push/google-apis-avd-settings.png)

    Se pretender utilizar uma alternativa mais rápida x86 emulador, [instale o controlador HAXM](https://taco.visualstudio.com/en-us/docs/run-app-apache/#HAXM) e configurar o emulador utilizá-la.

    Adicionar uma conta do Google para o dispositivo Android ao clicar em **aplicações** > **Definições** > **Adicionar conta**, em seguida, siga os pedidos para adicionar um Google existente de conta para o dispositivo (Recomendamos utilizando uma conta existente em vez de criar um novo).

    ![](./media/app-service-mobile-cordova-get-started-push/add-google-account.png)

    Executar a aplicação de fazer como antes e insira um novo item de todo. Desta vez, é apresentado um ícone de notificação na área de notificação. Pode abrir o nível de notificação para ver o texto completo da notificação.

    ![](./media/app-service-mobile-cordova-get-started-push/android-notifications.png)

##<a name="optional-configure-and-run-on-ios"></a>(Opcional) Configurar e executar o IOS

Esta secção destina-se a executar o projeto Cordova em dispositivos iOS. Pode ignorar esta secção se não estiver a trabalhar com dispositivos iOS.

####<a name="install-and-run-the-ios-remotebuild-agent-on-a-mac-or-cloud-service"></a>Instalar e executar o agente de remotebuild iOS num serviço Mac ou na nuvem

Antes de poder executar uma aplicação Cordova IOS utilizando o Visual Studio, aceda a [iOS guia de configuração](http://taco.visualstudio.com/en-us/docs/ios-guide/) para instalar e executar o agente de remotebuild os passos.

Certifique-se de que pode criar a aplicação para iOS. Os passos no guia de configuração são necessários para construir para iOS a partir do Visual Studio. Se não possui um Mac, pode criar para iOS com o agente de remotebuild num serviço como o MacInCloud. Para obter mais informações, consulte o artigo [executar a sua aplicação iOS na nuvem](http://taco.visualstudio.com/en-us/docs/build_ios_cloud/).

>[AZURE.NOTE] XCode 7 ou superior é necessário para utilizar o plug-in push IOS.

####<a name="find-the-id-to-use-as-your-app-id"></a>Localizar o ID a utilizar como o seu ID de aplicação

Antes de registar a sua aplicação para as notificações push, Config aberta na sua aplicação Cordova, localize o `id` valor no elemento de widget atributo e copiá-lo para utilizar posteriormente. No seguinte XML, o ID é `io.cordova.myapp7777777`.

        <widget defaultlocale="en-US" id="io.cordova.myapp7777777"
        version="1.0.0" windows-packageVersion="1.1.0.0" xmlns="http://www.w3.org/ns/widgets"
            xmlns:cdv="http://cordova.apache.org/ns/1.0" xmlns:vs="http://schemas.microsoft.com/appx/2014/htmlapps">

Utilize mais tarde, este identificador quando cria um ID de aplicação no portal de programador da Apple. (Se criar um ID de aplicação diferente no portal do programador e pretende utilizá-la, terá de tirar alguns passos adicionais mais tarde neste tutorial para alterar este ID no Config. O ID no elemento de widget tem de corresponder o ID da aplicação no portal do programador.)

####<a name="register-the-app-for-push-notifications-on-apples-developer-portal"></a>Registe-se a aplicação para as notificações push no portal de programador da Apple

[AZURE.INCLUDE [Enable Apple Push Notifications](../../includes/enable-apple-push-notifications.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-5-Set-up-apns-for-push)

####<a name="configure-azure-to-send-push-notifications"></a>Configurar o Azure para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-apns-configure-push](../../includes/app-service-mobile-apns-configure-push.md)]

####<a name="verify-that-your-app-id-matches-your-cordova-app"></a>Verificar se o seu ID de aplicação corresponde à sua aplicação Cordova

Se o ID da aplicação que criou na sua conta de programador do Apple já corresponder o ID do elemento widget no Config, pode ignorar este passo. No entanto, se não corresponderem aos IDs de, siga os passos seguintes:

1. Elimine a pasta de plataformas a partir do seu projeto.

2. Elimine a pasta de plug-ins do seu projeto.

3. Elimine a pasta node_modules a partir do seu projeto.

4. Atualize o atributo de id do elemento widget no config para utilizar o ID da aplicação que criou na sua conta de programador da Apple.

5. Reconstrua o seu projeto.

#####<a name="test-push-notifications-in-your-ios-app"></a>Teste as notificações push na sua aplicação iOS

1. No Visual Studio, certifique-se de que **iOS** é selecionado como o destino da implementação e, em seguida, selecione o **dispositivo** para executar no seu dispositivo iOS ligada.

    Pode executar num dispositivo iOS ligado ao PC utilizando o iTunes. O iOS Simulator não suporta as notificações push.

2. Prima o botão **Executar** ou **F5** no Visual Studio para criar o projecto e iniciar a aplicação num dispositivo iOS, em seguida, clique em **OK** para aceitar as notificações push.

    >[AZURE.NOTE] Explicitamente tem de aceitar as notificações push da sua aplicação. Este pedido só ocorre a primeira vez que a aplicação é executado.

3. Na aplicação, escreva uma tarefa e, em seguida, clique no sinal de adição (+) ícone.

4. Certifique-se de que uma notificação for recebida, em seguida, clique em OK para dispensar a notificação.

##<a name="optional-configure-and-run-on-windows"></a>(Opcional) Configurar e executar o Windows

Esta secção destina-se a executar o projeto de aplicação Apache Cordova em dispositivos Windows 10 (o plug-in do PhoneGap push é suportado no Windows 10). Pode ignorar esta secção se não estiver a trabalhar com dispositivos com Windows.

####<a name="register-your-windows-app-for-push-notifications-with-wns"></a>Registe-se a aplicação do Windows para as notificações push com WNS

Para utilizar as opções de arquivo no Visual Studio, selecione um alvo de Windows a partir da lista de plataformas de solução, como o **Windows x64** ou o **Windows x86** (evite **AnyCPU do Windows** para notificações push).

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]

[Veja um vídeo que mostra os passos semelhantes](https://channel9.msdn.com/series/Azure-connected-services-with-Cordova/Azure-connected-services-task-6-Set-up-wns-for-push)

####<a name="configure-the-notification-hub-for-wns"></a>Configurar o concentrador de notificação para WNS

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

####<a name="configure-your-cordova-app-to-support-windows-push-notifications"></a>Configurar a sua aplicação Cordova para suportar as notificações push do Windows

Abrir o estruturador de configuração (botão direito do rato no Config e selecione **Estruturador de vista**), selecione o separador do **Windows** e selecione o **Windows 10** em **Versão de destino do Windows**.

>[AZURE.NOTE] Se estiver a utilizar uma versão Cordova antes de Cordova 5.1.1 (6.1.1 recomendado), também tem de definir o sinalizador capazes de alerta para true no Config.

Para suportar push notificações no seu predefinido (depuração) constrói, build.json abrir ficheiro. Copie a configuração de "lançamento" para a configuração de depuração.

        "windows": {
            "release": {
                "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
                "publisherId": "CN=yourpublisherID"
            }
        }

Após a atualização, o código precedente deve ter este aspeto.

    "windows": {
        "release": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            },
        "debug": {
            "packageCertificateKeyFile": "res\\native\\windows\\CordovaApp.pfx",
            "publisherId": "CN=yourpublisherID"
            }
        }

Criar a aplicação e verifique se tem sem erros. Aplicação cliente deverá agora registe-se para as notificações a partir da aplicação Mobile back-end. Repita esta secção para cada projeto Windows na sua solução.

####<a name="test-push-notifications-in-your-windows-app"></a>Teste as notificações push na sua aplicação do Windows

No Visual Studio, certifique-se de que está selecionada uma plataforma Windows como o destino da implementação, como o **Windows x64** ou **Windows x86**. Para executar a aplicação num PC Windows 10 alojamento Visual Studio, escolha **Computador Local**.

Prima o botão executar para criar o projecto e iniciar a aplicação.

Na aplicação, escreva um nome para uma nova todoitem e, em seguida, clique no sinal de adição (+) ícone para adicioná-lo.

Certifique-se de que é recebida uma notificação quando o item é adicionado.

##<a name="next-steps"></a>Próximos passos

* Leia sobre [Concentradores de notificação] para saber mais sobre as notificações push.
* Se ainda não o tiver feito, continue a iniciação pela [Adição de autenticação] para a sua aplicação Apache Cordova.

Saiba como utilizar os SDK.

* [Apache Cordova SDK]
* [Servidor de ASP.NET SDK]
* [Servidor de node.js SDK]

<!-- URLs -->
[Adicionar autenticação]: app-service-mobile-cordova-get-started-users.md
[Guia de introdução do Apache Cordova]: app-service-mobile-cordova-get-started.md
[authentication]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Conta do Google]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Consola de programador do Google]: https://console.developers.google.com/home/dashboard
[documentação de instalação do plug-in-phonegap-push]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Comunidade do Visual Studio 2015]: http://www.visualstudio.com/
[Ferramentas do Visual Studio para Apache Cordova]: https://www.visualstudio.com/en-us/features/cordova-vs.aspx
[Notificação concentradores]: ../notification-hubs/notification-hubs-push-notification-overview.md
[Apache Cordova SDK]: app-service-mobile-cordova-how-to-use-client-library.md
[Servidor de ASP.NET SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Servidor de node.js SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
