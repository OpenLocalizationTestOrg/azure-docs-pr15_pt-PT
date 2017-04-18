<properties
    pageTitle="Adicionar as notificações push para a sua aplicação Universal Windows plataforma (UWP) | Aplicações móveis Azure"
    description="Saiba como utilizar aplicações do Azure aplicação serviço Mobile e o Azure notificação concentradores para enviar notificações push para a sua aplicação Universal Windows plataforma (UWP)."
    services="app-service\mobile,notification-hubs"
    documentationCenter="windows"
    authors="ysxu"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/12/2016"
    ms.author="yuaxu"/>

# <a name="add-push-notifications-to-your-windows-app"></a>Adicionar as notificações push para a sua aplicação do Windows

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

##<a name="overview"></a>Descrição geral

Neste tutorial, adicione as notificações push para o projeto [Iniciar do Windows rápidas](app-service-mobile-windows-store-dotnet-get-started.md) para que uma notificação de emissão é enviada para o dispositivo sempre que um registo é inserido.

Se não utilizar o projeto de servidor do guia de introdução do transferido, é necessário o pacote de extensão de notificações push. Consulte o artigo [trabalhar com o servidor de back-end .NET SDK para as aplicações móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

##<a name="configure-hub"></a>Configurar um concentrador de notificação

[AZURE.INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

##<a name="register-your-app-for-push-notifications"></a>Registe-se a sua aplicação para notificações push

Tem de submeter a aplicação da loja Windows, em seguida, configurar o seu projeto do servidor para integrar com serviços de notificação de Windows (WNS) para enviar push.

1. No Explorador de solução do Visual Studio, com o botão direito do projeto de aplicação UWP, clique em **loja** > **Associar aplicação com o arquivo de...**. 

    ![Associar a aplicação da loja Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/notification-hub-associate-uwp-app.png)
    
2. No assistente, clique em **seguinte**, inicie sessão com a sua conta Microsoft, escreva um nome para a sua aplicação na **reserva um novo nome de aplicação**, em seguida, clique em **reserva**.

3. Depois do registo de aplicação é criado com êxito, selecione o novo nome de aplicação, clique em **seguinte**e, em seguida, clique em **associar**. Esta ação adiciona as informações de registo de loja Windows necessárias manifesto da aplicação.  

7. Navegue para o [Windows Dev Center do](https://dev.windows.com/en-us/overview), iniciar sessão com a sua conta Microsoft, clique em novo registo de aplicação nas **minhas aplicações**, em seguida, expanda **Serviços** > **notificações de emissão**. 

8. Na página de **notificações de emissão** , clique em **site de serviços Live** em **Serviços do Microsoft Azure Mobile**.

9. Na página de registo, tome nota do valor em **segredos de aplicação** e do **Pacote SID**, que irá utilizar seguinte para configurar a sua aplicação móvel do back-end. 

    ![Associar a aplicação da loja Windows](./media/app-service-mobile-windows-store-dotnet-get-started-push/app-service-mobile-uwp-app-push-auth.png)

    > [AZURE.IMPORTANT] O segredo cliente e o pacote SID são as credenciais de segurança importantes. Não partilhar estes valores com qualquer pessoa ou distribuí-los com a sua aplicação. O **Id da aplicação** é utilizada com o segredo para configurar uma autenticação Account Microsoft.

##<a name="configure-the-backend-to-send-push-notifications"></a>Configurar o back-end para enviar notificações push

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]

##<a id="update-service"></a>Actualizar o servidor para enviar notificações push

Utilize o procedimento abaixo que corresponde ao seu tipo de projecto de back-end&mdash; [back-end do .NET](#dotnet) ou [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Projecto de back-end .NET

1. No Visual Studio, o project server com o botão direito e clique em **Gerir pacotes de NuGet**, procure Microsoft.Azure.NotificationHubs, em seguida, clique em **instalar**. Este procedimento instala a biblioteca do cliente concentradores de notificação.

2. Expanda **controladores**, abrir TodoItemController.cs e adicione o seguinte utilizando instruções:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;

3. Método de **PostTodoItem** , adicione o código seguinte depois da chamada para **InsertAsync**:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings =
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();

        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create the notification hub client.
        NotificationHubClient hub = NotificationHubClient
            .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Define a WNS payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">"
                                + item.Text + @"</text></binding></visual></toast>";
        try
        {
            // Send the push notification.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Este código indica o concentrador de notificação para enviar uma notificação de emissão depois de um novo item é inserção.

4. Volte a publicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end node.js

1. Se que ainda não o fez, [Transfira o projeto de guia de introdução](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, utiliza o [editor online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Substitua o código existente no ficheiro todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');

        var table = azureMobileApps.table();

        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK,
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');

        // Define the WNS payload that contains the new item Text.
        var payload = "<toast><visual><binding template=\ToastText01\><text id=\"1\">"
                                    + context.item.text + "</text></binding></visual></toast>";

        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a WNS native toast notification.
                    context.push.wns.sendToast(null, payload, function (error) {
                        if (error) {
                            logger.error('Error while sending push notification: ', error);
                        } else {
                            logger.info('Push notification sent successfully!');
                        }
                    });
                }
                // Don't forget to return the results from the context.execute()
                return results;
            })
            .catch(function (error) {
                logger.error('Error while running context.execute: ', error);
            });
        });

        module.exports = table;

    Isto envia uma notificação de alerta WNS que contém o item.text Quando um novo item de todo é inserido.

2. Quando a editar o ficheiro no seu computador local, volte a publicar o projeto de servidor.

##<a id="update-app"></a>Adicionar as notificações push para a sua aplicação

Em seguida, a aplicação tem de registar para as notificações push no arranque. Quando já tiver sido activada autenticação, certifique-se de que o utilizador sinais-in antes de tentar registe-se para as notificações push.

1. Abra o ficheiro de projeto **App.xaml.cs** e adicione o seguinte `using` declarações:

        using System.Threading.Tasks;
        using Windows.Networking.PushNotifications;

2. No mesmo ficheiro, adicione a definição de método **InitNotificationsAsync** seguinte para a classe de **aplicação** :

        private async Task InitNotificationsAsync()
        {
            // Get a channel URI from WNS.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register the channel URI with Notification Hubs.
            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }

    Este código obtém ChannelURI para a aplicação a partir de WNS e, em seguida, regista esse ChannelURI com a sua aplicação do aplicação do serviço móvel.

3. Na parte superior de processador de eventos de **OnLaunched** no **App.xaml.cs**, adicione a modificadora **assíncrona** para a definição do método e adicione a chamada seguinte para o novo método de **InitNotificationsAsync** , tal como no exemplo seguinte:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

            // ...
        }

    Isto garante que a ChannelURI curto está registado sempre que a aplicação é iniciada.

4. Reconstrua projeto UWP aplicação. A aplicação está agora pronta para receber notificações de alerta.

##<a id="test"></a>Teste as notificações push na sua aplicação

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]


##<a id="more"></a>Próximos passos

Saiba mais sobre as notificações push:

* [Como utilizar o cliente gerido para as aplicações móveis do Azure](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications)  
Modelos de dar-lhe flexibilidade de poder enviar em diferentes plataformas puxa e puxa localizadas. Saiba como registar modelos.

* [Diagnosticar problemas de notificações push](../notification-hubs/notification-hubs-push-notification-fixer.md)  
Existem várias razões porque é que o notificações poderão obter capituladas ou não terminem em dispositivos. Este tópico mostra-lhe como analisar e descobrir a causa de raiz de falhas de notificações push. 

Considere continuar para um dos seguintes tutoriais:

+ [Adicionar autenticação para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-users.md)  
  Saiba como autenticar os utilizadores da sua aplicação com um fornecedor de identidade.

+ [Ativar a sincronização offline para a sua aplicação](app-service-mobile-windows-store-dotnet-get-started-offline-data.md)  
  Saiba como adicionar a sua aplicação utilizando um back-end aplicação Mobile suporte offline. Sincronização offline permite que os utilizadores finais interagir com uma aplicação móvel&mdash;visualizar, adicionar ou modificar dados&mdash;mesmo quando não existe nenhuma ligação de rede.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

