Nesta secção, pode atualizar o código no seu projeto de back-end aplicações Mobile existente para enviar uma notificação de emissão sempre que é adicionado um novo item. Isto é recorrendo a funcionalidade de [modelo dos](../articles/notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) concentradores de notificação, permitindo-puxa em diferentes plataformas. Vários clientes estão registados para as notificações push utilizar modelos e um único push universal pode aceder a todas as plataformas de cliente.

Escolha o procedimento abaixo que corresponde ao seu tipo de projecto de back-end&mdash; [back-end do .NET](#dotnet) ou [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Projecto de back-end .NET
1. No Visual Studio, o project server com o botão direito e clique em **Gerir pacotes de NuGet**, procure `Microsoft.Azure.NotificationHubs`, em seguida, clique em **instalar**. Este procedimento instala a biblioteca de notificação concentradores para envio de notificações a partir do seu back-end.

3. No server project, abrir **controladores** > **TodoItemController.cs**e adicione o seguinte utilizando instruções:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;
        using Microsoft.Azure.Mobile.Server.Config;
    

2. Método de **PostTodoItem** , adicione o código seguinte depois da chamada para **InsertAsync**:  

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

        // Sending the message so that all template registrations that contain "messageParam"
        // will receive the notifications. This includes APNS, GCM, WNS, and MPNS template registrations.
        Dictionary<string,string> templateParams = new Dictionary<string,string>();
        templateParams["messageParam"] = item.Text + " was added to the list.";

        try
        {
            // Send the push notification and log the results.
            var result = await hub.SendTemplateNotificationAsync(templateParams);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Isto envia uma notificação de modelo que contém o item. Texto quando um novo item é inserido.

4. Volte a publicar o projeto de servidor. 

### <a name="nodejs"></a>Projeto de back-end node.js

1. Se que ainda não o fez, [Transfira o projeto de back-end guia de introdução](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, utiliza o [editor online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).

2. Substitua o código existente na todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
    
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the template payload.
        var payload = '{"messageParam": "' + context.item.text + '" }';  
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a template notification.
                    context.push.send(null, payload, function (error) {
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

    Isto envia uma notificação de modelo que contém o item.text Quando um novo item é inserido.

2. Quando a editar o ficheiro no seu computador local, volte a publicar o projeto de servidor.
