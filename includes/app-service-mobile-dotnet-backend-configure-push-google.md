Utilize o procedimento que corresponde ao seu tipo de projecto de back-end&mdash; [back-end de .NET](#dotnet) ou [back-end de Node.js](#nodejs).

### <a name="dotnet"></a>Projecto de back-end .NET

1. No Visual Studio, o project server com o botão direito e clique em **Gerir pacotes de NuGet**, procure `Microsoft.Azure.NotificationHubs`, em seguida, clique em **instalar**. Este procedimento instala a biblioteca do cliente concentradores de notificação.

2. Na pasta controladores, abra TodoItemController.cs e adicione o seguinte `using` declarações:

        using Microsoft.Azure.Mobile.Server.Config;
        using Microsoft.Azure.NotificationHubs;

3. Substituir o `PostTodoItem` método com o seguinte código:  

      
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
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

            // Android payload
            var androidNotificationPayload = "{ \"data\" : {\"message\":\"" + item.Text + "\"}}";

            try
            {
                // Send the push notification and log the results.
                var result = await hub.SendGcmNativeNotificationAsync(androidNotificationPayload);

                // Write the success result to the logs.
                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                // Write the failure result to the logs.
                config.Services.GetTraceWriter()
                    .Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

4. Volte a publicar o projeto de servidor.

### <a name="nodejs"></a>Projeto de back-end node.js

1. Se que ainda não o fez, [Transfira o projeto de guia de introdução](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, utiliza o [editor online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor).
 
1. Substitua o código existente no ficheiro todoitem.js com o seguinte:

        var azureMobileApps = require('azure-mobile-apps'),
        promises = require('azure-mobile-apps/src/utilities/promises'),
        logger = require('azure-mobile-apps/src/logger');
        
        var table = azureMobileApps.table();
        
        table.insert(function (context) {
        // For more information about the Notification Hubs JavaScript SDK, 
        // see http://aka.ms/nodejshubs
        logger.info('Running TodoItem.insert');
        
        // Define the GCM payload.
        var payload = {
            "data": {
                "message": context.item.text
            }
        };   
        
        // Execute the insert.  The insert returns the results as a Promise,
        // Do the push as a post-execute action within the promise flow.
        return context.execute()
            .then(function (results) {
                // Only do the push if configured
                if (context.push) {
                    // Send a GCM native notification.
                    context.push.gcm.send(null, payload, function (error) {
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

    Isto envia uma notificação de GCM que contém o item.text Quando um novo item de todo é inserido. 

2. Quando a editar o ficheiro no seu computador local, volte a publicar o projeto de servidor. 
