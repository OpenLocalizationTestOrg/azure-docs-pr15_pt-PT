<properties
    pageTitle="Enviar notificações push para aplicações do Chrome com Azure notificação concentradores | Microsoft Azure"
    description="Saiba como utilizar Azure notificação concentradores para enviar notificações push para uma aplicação do Chrome."
    services="notification-hubs"
    keywords="notificações push móveis, as notificações push, emissão notificação, as notificações push do chrome"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-chrome"
    ms.devlang="JavaScript"
    ms.topic="hero-article"
    ms.date="10/03/2016"
    ms.author="yuaxu"/>

# <a name="send-push-notifications-to-chrome-apps-with-azure-notification-hubs"></a>Enviar notificações push para aplicações do Chrome com concentradores de notificação do Azure

[AZURE.INCLUDE [notification-hubs-selector-get-started](../../includes/notification-hubs-selector-get-started.md)]

Este tópico mostra-lhe como utilizar o Azure notificação concentradores para enviar notificações push para uma aplicação do Chrome, que será apresentada dentro do contexto do browser Google Chrome. Neste tutorial, irá criar uma aplicação do Chrome que recebe as notificações push utilizando o [Google Cloud mensagens (GCM)](https://developers.google.com/cloud-messaging/). 

>[AZURE.NOTE] Para concluir este tutorial, tem de ter uma conta do Azure active. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo [Versão de avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fen-us%2Fdocumentation%2Farticles%notification-hubs-chrome-get-started%2F).

O tutorial orienta-o através destes passos básicos para ativar as notificações push:

* [Ativar o Google Cloud mensagens](#register)
* [Configurar o seu centro de notificação](#configure-hub)
* [Ligar a sua aplicação do Chrome com o concentrador de notificação](#connect-app)
* [Enviar uma notificação de emissão para a sua aplicação do Chrome](#send)
* [Funcionalidades adicionais e capacidades](#next-steps)

>[AZURE.NOTE] As notificações push do Chrome aplicação não estão genéricas no browser notificações - que são específicos para a expansão de browser do modelo (consulte [Descrição geral de aplicações do Chrome] para obter mais detalhes). Para além do browser de ambiente de trabalho, Chrome aplicações executar no mobile (Android e iOS) através de Apache Cordova. Consulte o artigo [As aplicações do Chrome no Mobile] para obter mais informações.

Configurar GCM e Azure notificação concentradores é idêntico ao configurar para Android, desde que foi preterida [Google Cloud mensagens para o Chrome] e o mesmo GCM suporta agora dispositivos Android e instâncias do Chrome.

##<a id="register"></a>Ativar o Google Cloud mensagens

1. Navegue para o Web site [Da consola do Google Cloud] , inicie sessão com as credenciais da conta Google e, em seguida, clique no botão **Criar projeto** . Forneça um adequado **Nome do projeto**e, em seguida, clique no botão **Criar** .

    ![Google Cloud Console - criar projeto][1]

2. Tome nota do **Número de projeto** na página de **projetos** para o projeto que acabou de criar. Irá utilizar este como o **ID do remetente GCM** na aplicação do Chrome para registar GCM.

    ![Consola do Google Cloud - número de projeto][2]

3. No painel esquerdo, clique em **APIs all & auth**e, em seguida, desloque-se para baixo e clique o botão de alternar para ativar o **Google Cloud mensagens para Android**. Não tem de ativar o **Google Cloud mensagens para o Chrome**.

    ![Consola do Google Cloud - chave do servidor][3]

4. No painel esquerdo, clique em **credenciais** > **Criar uma nova chave** > **Chave do servidor** > **Criar**.

    ![Consola do Google Cloud - credenciais][4]

5. Tome nota do servidor, de **API chave**. Irá configurar isto no seu centro de notificação em seguida, para ativá-la enviar notificações push para GCM.

    ![Consola do Google Cloud - chave API][5]

##<a id="configure-hub"></a>Configurar o seu centro de notificação

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]


&emsp;&emsp;6. na pá **Definições** , selecione **Serviços de notificação** e, em seguida, **Google (GCM)**. Introduza a chave de API e guardar.

&emsp;&emsp;![Notificação Azure concentradores - Google (GCM)](./media/notification-hubs-android-get-started/notification-hubs-gcm-api.png)

##<a id="connect-app"></a>Ligar a sua aplicação do Chrome com o concentrador de notificação

O concentrador de notificação agora está configurado para funcionar com GCM e tiver as cadeias de ligação para registar a sua aplicação para receber e enviar as notificações push. LK

###<a name="create-a-new-chrome-app"></a>Criar uma nova aplicação do Chrome

O exemplo abaixo é baseado no [Chrome aplicação GCM exemplo] e utiliza o caminho recomendado para criar uma aplicação do Chrome. Vamos irá realçar os passos especificamente relacionadas com a concentradores de notificação do Azure. 

>[AZURE.NOTE] Recomendamos que transferir a origem para esta aplicação Chrome a partir do [Chrome aplicação notificação concentrador de exemplo].

A aplicação do Chrome é criada através da JavaScript e pode utilizar qualquer um dos seus editores preferido word para criá-lo. Abaixo encontra o que esta aplicação Chrome terá o seguinte aspeto.

![Aplicação do Google Chrome][15]

1. Crie uma pasta e o nome `ChromePushApp`. Obviamente, o nome é arbitrário - se de que lhe atribuir um nome algo diferente, certifique-se de que substitua o caminho nos segmentos de código necessário.

2. Transfira a [biblioteca de crypto js] na pasta que criou no passo segundo. Esta pasta da biblioteca de irá conter duas subpastas: `components` e `rollups`.

3. Criar um `manifest.json` ficheiro. Todas as aplicações do Chrome de segurança abrange os por um ficheiro de manifesto que contém os metadados da aplicação e, a maior parte nenhumas, todas as permissões que são concedidas à aplicação quando o utilizador instala-lo.

        {
          "name": "NH-GCM Notifications",
          "description": "Chrome platform app.",
          "manifest_version": 2,
          "version": "0.1",
          "app": {
            "background": {
              "scripts": ["background.js"]
            }
          },
          "permissions": ["gcm", "storage", "notifications", "https://*.servicebus.windows.net/*"],
          "icons": { "128": "gcm_128.png" }
        }

    Aviso de `permissions` elemento, que especifica que esta aplicação Chrome poderão receber notificações push de GCM. -Lo também tem de especificar o URI de concentradores de notificação do Azure onde a aplicação do Chrome irá efetuar uma chamada de resto para registar.
    Nossa aplicação de exemplo também utiliza um ficheiro do ícone, `gcm_128.png`, que irá encontrar na origem de que é reutilizada do exemplo GCM original. Pode substituir os-lo para qualquer imagem que corresponde aos [critérios ícone](https://developer.chrome.com/apps/manifest/icons).

4. Criar um ficheiro denominado `background.js` com o seguinte código:

        // Returns a new notification ID used in the notification.
        function getNotificationId() {
          var id = Math.floor(Math.random() * 9007199254740992) + 1;
          return id.toString();
        }

        function messageReceived(message) {
          // A message is an object with a data property that
          // consists of key-value pairs.

          // Concatenate all key-value pairs to form a display string.
          var messageString = "";
          for (var key in message.data) {
            if (messageString != "")
              messageString += ", "
            messageString += key + ":" + message.data[key];
          }
          console.log("Message received: " + messageString);

          // Pop up a notification to show the GCM message.
          chrome.notifications.create(getNotificationId(), {
            title: 'GCM Message',
            iconUrl: 'gcm_128.png',
            type: 'basic',
            message: messageString
          }, function() {});
        }

        var registerWindowCreated = false;

        function firstTimeRegistration() {
          chrome.storage.local.get("registered", function(result) {

            registerWindowCreated = true;
            chrome.app.window.create(
              "register.html",
              {  width: 520,
                 height: 500,
                 frame: 'chrome'
              },
              function(appWin) {}
            );
          });
        }

        // Set up a listener for GCM message event.
        chrome.gcm.onMessage.addListener(messageReceived);

        // Set up listeners to trigger the first-time registration.
        chrome.runtime.onInstalled.addListener(firstTimeRegistration);
        chrome.runtime.onStartup.addListener(firstTimeRegistration);

    Este é o ficheiro que surge a janela da aplicação do Chrome HTML (**register.html**) e também define o processador **messageReceived** para processar a notificação de push recebidas.

5. Criar um ficheiro denominado `register.html` -Isto define a IU da aplicação Chrome. 

   >[AZURE.NOTE] Este exemplo utiliza **CryptoJS v3.1.2**. Se tiver transferido outra versão da biblioteca, certifique-se de que corretamente substituir a versão na `src` caminho.

        <html>

        <head>
        <title>GCM Registration</title>
        <script src="register.js"></script>
        <script src="CryptoJS v3.1.2/rollups/hmac-sha256.js"></script>
        <script src="CryptoJS v3.1.2/components/enc-base64-min.js"></script>
        </head>

        <body>

        Sender ID:<br/><input id="senderId" type="TEXT" size="20"><br/>
        <button id="registerWithGCM">Register with GCM</button>
        <br/>
        <br/>
        <br/>

        Notification Hub Name:<br/><input id="hubName" type="TEXT" style="width:400px"><br/><br/>
        Connection String:<br/><textarea id="connectionString" type="TEXT" style="width:400px;height:60px"></textarea>

        <br/>

        <button id="registerWithNH" disabled="true">Register with Azure Notification Hubs</button>

        <br/>
        <br/>

        <textarea id="console" type="TEXT" readonly style="width:500px;height:200px;background-color:#e5e5e5;padding:5px"></textarea>

        </body>

        </html>

6. Criar um ficheiro denominado `register.js` com o código abaixo. Este ficheiro Especifica o script atrás `register.html`. Aplicações do Chrome não permitir a execução de inline, pelo que tem de criar um script de segurança separada para a interface do utilizador.

        var registrationId = "";
        var hubName        = "", connectionString = "";
        var originalUri    = "", targetUri = "", endpoint = "", sasKeyName = "", sasKeyValue = "", sasToken = "";

        window.onload = function() {
           document.getElementById("registerWithGCM").onclick = registerWithGCM;  
           document.getElementById("registerWithNH").onclick = registerWithNH;
           updateLog("You have not registered yet. Please provider sender ID and register with GCM and then with  Notification Hubs.");
        }

        function updateLog(status) {
          currentStatus = document.getElementById("console").innerHTML;
          if (currentStatus != "") {
            currentStatus = currentStatus + "\n\n";
          }

          document.getElementById("console").innerHTML = currentStatus  + status;
        }

        function registerWithGCM() {
          var senderId = document.getElementById("senderId").value.trim();
          chrome.gcm.register([senderId], registerCallback);

          // Prevent register button from being clicked again before the registration finishes.
          document.getElementById("registerWithGCM").disabled = true;
        }

        function registerCallback(regId) {
          registrationId = regId;
          document.getElementById("registerWithGCM").disabled = false;

          if (chrome.runtime.lastError) {
            // When the registration fails, handle the error and retry the
            // registration later.
            updateLog("Registration failed: " + chrome.runtime.lastError.message);
            return;
          }

          updateLog("Registration with GCM succeeded.");
          document.getElementById("registerWithNH").disabled = false;

          // Mark that the first-time registration is done.
          chrome.storage.local.set({registered: true});
        }

        function registerWithNH() {
          hubName = document.getElementById("hubName").value.trim();
          connectionString = document.getElementById("connectionString").value.trim();
          splitConnectionString();
          generateSaSToken();
          sendNHRegistrationRequest();
        }

        // From http://msdn.microsoft.com/library/dn495627.aspx
        function splitConnectionString()
        {
          var parts = connectionString.split(';');
          if (parts.length != 3)
          throw "Error parsing connection string";

          parts.forEach(function(part) {
            if (part.indexOf('Endpoint') == 0) {
            endpoint = 'https' + part.substring(11);
            } else if (part.indexOf('SharedAccessKeyName') == 0) {
            sasKeyName = part.substring(20);
            } else if (part.indexOf('SharedAccessKey') == 0) {
            sasKeyValue = part.substring(16);
            }
          });

          originalUri = endpoint + hubName;
        }

        function generateSaSToken()
        {
          targetUri = encodeURIComponent(originalUri.toLowerCase()).toLowerCase();
          var expiresInMins = 10; // 10 minute expiration

          // Set expiration in seconds.
          var expireOnDate = new Date();
          expireOnDate.setMinutes(expireOnDate.getMinutes() + expiresInMins);
          var expires = Date.UTC(expireOnDate.getUTCFullYear(), expireOnDate
            .getUTCMonth(), expireOnDate.getUTCDate(), expireOnDate
            .getUTCHours(), expireOnDate.getUTCMinutes(), expireOnDate
            .getUTCSeconds()) / 1000;
          var tosign = targetUri + '\n' + expires;

          // Using CryptoJS.
          var signature = CryptoJS.HmacSHA256(tosign, sasKeyValue);
          var base64signature = signature.toString(CryptoJS.enc.Base64);
          var base64UriEncoded = encodeURIComponent(base64signature);

          // Construct authorization string.
          sasToken = "SharedAccessSignature sr=" + targetUri + "&sig="
                          + base64UriEncoded + "&se=" + expires + "&skn=" + sasKeyName;
        }

        function sendNHRegistrationRequest()
        {
          var registrationPayload =
          "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
          "<entry xmlns=\"http://www.w3.org/2005/Atom\">" +
              "<content type=\"application/xml\">" +
                  "<GcmRegistrationDescription xmlns:i=\"http://www.w3.org/2001/XMLSchema-instance\" xmlns=\"http://schemas.microsoft.com/netservices/2010/10/servicebus/connect\">" +
                      "<GcmRegistrationId>{GCMRegistrationId}</GcmRegistrationId>" +
                  "</GcmRegistrationDescription>" +
              "</content>" +
          "</entry>";

          // Update the payload with the registration ID obtained earlier.
          registrationPayload = registrationPayload.replace("{GCMRegistrationId}", registrationId);

          var url = originalUri + "/registrations/?api-version=2014-09";
          var client = new XMLHttpRequest();

          client.onload = function () {
            if (client.readyState == 4) {
              if (client.status == 200) {
                updateLog("Notification Hub Registration succesful!");
                updateLog(client.responseText);
              } else {
                updateLog("Notification Hub Registration did not succeed!");
                updateLog("HTTP Status: " + client.status + " : " + client.statusText);
                updateLog("HTTP Response: " + "\n" + client.responseText);
              }
            }
          };

          client.onerror = function () {
                updateLog("ERROR - Notification Hub Registration did not succeed!");
          }

          client.open("POST", url, true);
          client.setRequestHeader("Content-Type", "application/atom+xml;type=entry;charset=utf-8");
          client.setRequestHeader("Authorization", sasToken);
          client.setRequestHeader("x-ms-version", "2014-09");

          try {
              client.send(registrationPayload);
          }
          catch(err) {
              updateLog(err.message);
          }
        }

    O script acima tem os seguintes parâmetros de chaves:
    - **window.onLoad** define os eventos clique de botão de dois botões IU. Um regista com GCM e o outro utiliza o ID de registo que é devolvido após o registo com GCM para registar com concentradores de notificação do Azure.
    - **updateLog** é a função que lhe permite-nos processar capacidades de registo simples.
    - **registerWithGCM** é o primeira processador clique de botão, que torna o `chrome.gcm.register` a chamada para GCM para registar a instância atual do Chrome aplicação.
    - **registerCallback** é a função de chamada de retorno que obtém chamada quando a chamada de registo GCM devolve.
    - **registerWithNH** é o segundo processador clique de botão, que regista com concentradores de notificação. Obtém a mesma `hubName` e `connectionString` (que o utilizador tiver especificado) e crafts a chamada de notificação concentradores registo REST API.
    - **splitConnectionString** e **generateSaSToken** são os assistentes que representam execução de um processo de criação token SA, que deve ser utilizada em todas as chamadas REST API JavaScript. Para mais informações, consulte o artigo [Conceitos comuns](http://msdn.microsoft.com/library/dn495627.aspx).
    - **sendNHRegistrationRequest** é a função que forneça uma resto HTTP chamar a concentradores de notificação do Azure.
    - **registrationPayload** define a carga útil XML de registo. Para mais informações, consulte o artigo [Criar registo NH REST API]. Poderemos actualizar o ID de registo no mesmo com o que recebemos GCM.
    - **cliente** é uma instância da **XMLHttpRequest** que utilizamos para fazer o pedido de HTTP POST. Tenha em atenção que estamos a atualizar o `Authorization` cabeçalho com `sasToken`. Conclusão com êxito desta chamada registará esta instância de aplicação do Chrome com concentradores de notificação do Azure.


A estrutura de pastas geral para este projeto deve um aspeto semelhante ao seguinte:     ![Google Chrome aplicação - estrutura de pastas][21]

###<a name="set-up-and-test-your-chrome-app"></a>Configurar e testar a sua aplicação do Chrome

1. Abra o browser Chrome. Abrir **as extensões de Chrome** e ativar o **modo de programador**.

    ![Google Chrome - ativar o modo de programador][16]

2. Clique em **carregar a extensão descompactado** e navegue para a pasta onde criou os ficheiros. Opcionalmente, também pode utilizar as **aplicações do Chrome e extensões de programador ferramenta**. Esta ferramenta é uma aplicação do Chrome em si (instalado a partir da loja de Web Chrome) e fornece capacidades de depuração avançadas para o desenvolvimento de aplicação do Chrome.

    ![Google Chrome - carregar a extensão descompactado][17]

3. Se a aplicação do Chrome é criada sem erros, em seguida, verá a aplicação do Chrome aparecer.

    ![Google Chrome - visualização de aplicação do Chrome][18]

4. Introduza o **Número de projeto** que recebeu anteriormente a partir da **Consola do Google na nuvem** como o ID do remetente e clique em **Registe-se com GCM**. Tem a ver a mensagem **registo com GCM foi concluída com êxito.**

    ![Google Chrome - personalização de aplicação do Chrome][19]

5. Introduza o **Nome do concentrador de notificação** e **DefaultListenSharedAccessSignature** obtidos a partir do portal anterior e, clique em **Registe-se com concentrador de notificação do Azure**. Tem a ver a mensagem **notificação concentrador de tipos de registo com êxito!** e os detalhes de resposta de registo, que contém o registo do Azure notificação concentradores ID.

    ![Google Chrome - especificar os detalhes do concentrador de notificação][20]  

##<a name="send"></a>Enviar uma notificação para a sua aplicação do Chrome

Para fins de teste, enviaremos as notificações push Chrome utilizando um .NET da consola de aplicação. 

>[AZURE.NOTE] Pode enviar notificações push com notificação concentradores a partir de qualquer back-end através do nosso pública <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST interface</a>. Consulte o artigo nosso [portal de documentação](https://azure.microsoft.com/documentation/services/notification-hubs/) para obter mais exemplos em diferentes plataformas.

1. No Visual Studio, no menu **ficheiro** , selecione **Novo** e, em seguida, o **Project**. Em **Visual c#**, clique em **Windows** e a **Aplicação de consola**e, em seguida, clique em **OK**.  Esta ação cria um novo projeto de aplicação de consola.

2. No menu **Ferramentas** , clique em **Gestor de pacotes de biblioteca** e, em seguida, **Consola do Gestor de pacote**. Esta ação apresentará a consola do Gestor de pacote.

3. Na janela da consola, execute o seguinte comando:

        Install-Package Microsoft.Azure.NotificationHubs

    Esta ação adiciona uma referência para o SDK do Azure Service Bus com o <a href="http://nuget.org/packages/  WindowsAzure.ServiceBus/">pacote de WindowsAzure.ServiceBus NuGet</a>.

4. Abrir `Program.cs` e adicione o seguinte `using` declaração:

        using Microsoft.Azure.NotificationHubs;

5. No `Program` classe, adicione o seguinte método:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            String message = "{\"data\":{\"message\":\"Hello Chrome from Azure Notification Hubs\"}}";
            await hub.SendGcmNativeNotificationAsync(message);
        }

    Certifique-se substituir o `<hub name>` marcador de posição com o nome do concentrador de notificação que é apresentado no [portal](https://portal.azure.com) no seu pá concentrador de notificação. Além disso, substitua o marcador de posição de cadeia de ligação com a cadeia de ligação denominada `DefaultFullSharedAccessSignature` que obteve na secção de configuração do concentrador de notificação.

    >[AZURE.NOTE] Certifique-se de que utiliza a cadeia de ligação com o acesso **completo** , não o access **ouvir** . A cadeia de ligação de acesso **ouvir** não conceder permissões para enviar notificações push.

5. Adicionar as seguintes chamadas na `Main` método:

         SendNotificationAsync();
         Console.ReadLine();
         
6. Certifique-se de que está a ser executado Chrome e executar a aplicação da consola.

7. Deverá visualizar a seguinte notificação pop-up no seu ambiente de trabalho.

    ![Google Chrome - notificação][13]

8. Também pode ver as suas notificações ao utilizar a janela de notificações do Chrome na barra de tarefas (no Windows) quando Chrome está em execução.

    ![Google Chrome - lista de notificações][14]

>[AZURE.NOTE] Não tem de ter a execução de aplicação do Chrome ou abrir no browser (apesar do browser Chrome propriamente dito tem de estar em execução). Também obter uma vista consolidada de todas as notificações na janela do Chrome notificações.

## <a name="next-steps"> </a>Passos seguintes

Saiba mais sobre concentradores de notificação na [Descrição geral de concentradores de notificação].

Para utilizadores específicos de destino, consulte o tutorial [Azure notificação concentradores notificar utilizadores] . 

Se pretender segmentar os seus utilizadores por grupos de interesse, pode seguir o tutorial [Azure notificação concentradores força de notícias] .

<!-- Images. -->
[1]: ./media/notification-hubs-chrome-get-started/GoogleConsoleCreateProject.PNG
[2]: ./media/notification-hubs-chrome-get-started/GoogleProjectNumber.png
[3]: ./media/notification-hubs-chrome-get-started/EnableGCM.png
[4]: ./media/notification-hubs-chrome-get-started/CreateServerKey.png
[5]: ./media/notification-hubs-chrome-get-started/ServerKey.png
[6]: ./media/notification-hubs-chrome-get-started/CreateNH.png
[7]: ./media/notification-hubs-chrome-get-started/NHNamespace.png
[8]: ./media/notification-hubs-chrome-get-started/NamespaceConfigure.png
[9]: ./media/notification-hubs-chrome-get-started/NHConfigure.png
[10]: ./media/notification-hubs-chrome-get-started/NHConfigureGCM.png
[11]: ./media/notification-hubs-chrome-get-started/NHDashboard.png
[12]: ./media/notification-hubs-chrome-get-started/NHConnString.png
[13]: ./media/notification-hubs-chrome-get-started/ChromeNotification.png
[14]: ./media/notification-hubs-chrome-get-started/ChromeNotificationWindow.png
[15]: ./media/notification-hubs-chrome-get-started/ChromeApp.png
[16]: ./media/notification-hubs-chrome-get-started/ChromeExtensions.png
[17]: ./media/notification-hubs-chrome-get-started/ChromeLoadExtension.png
[18]: ./media/notification-hubs-chrome-get-started/ChromeAppLoaded.png
[19]: ./media/notification-hubs-chrome-get-started/ChromeAppGCM.png
[20]: ./media/notification-hubs-chrome-get-started/ChromeAppNH.png
[21]: ./media/notification-hubs-chrome-get-started/FinalFolderView.png

<!-- URLs. -->
[Exemplo de concentrador de notificação de aplicação Chrome]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToChromeApps
[Consola do Google Cloud]: http://cloud.google.com/console
[Azure Classic Portal]: https://manage.windowsazure.com/
[Descrição geral de concentradores de notificação]: notification-hubs-push-notification-overview.md
[Descrição geral de aplicações do Chrome]: https://developer.chrome.com/apps/about_apps
[Exemplo de aplicação GCM do Chrome]: https://github.com/GoogleChrome/chrome-app-samples/tree/master/samples/gcm-notifications
[Installable Web Apps]: https://developers.google.com/chrome/apps/docs/
[Aplicações do Chrome no Mobile]: https://developer.chrome.com/apps/chrome_apps_on_mobile
[Criar o registo NH REST API]: http://msdn.microsoft.com/library/azure/dn223265.aspx
[biblioteca de Crypto js]: http://code.google.com/p/crypto-js/
[GCM with Chrome Apps]: https://developer.chrome.com/apps/cloudMessaging
[Google Cloud mensagens para o Chrome]: https://developer.chrome.com/apps/cloudMessagingV1
[Notificação Azure concentradores notificam os utilizadores]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Notícias de última hora de notificação concentradores Azure]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
