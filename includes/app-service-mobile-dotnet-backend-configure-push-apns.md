
+ **Back-end do .NET (c#)**:    
    1. No Visual Studio, o project server com o botão direito e clique em **Gerir pacotes de NuGet**, procure `Microsoft.Azure.NotificationHubs`, em seguida, clique em **instalar**. Este procedimento instala a biblioteca de notificação concentradores para envio de notificações a partir do seu back-end.

    2. No projeto do Visual Studio o back-end, abra **controladores** > **TodoItemController.cs**. Na parte superior do ficheiro, adicione o seguinte `using` declaração:

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
    
                // iOS payload
                var appleNotificationPayload = "{\"aps\":{\"alert\":\"" + item.Text + "\"}}";
    
                try
                {
                    // Send the push notification and log the results.
                    var result = await hub.SendAppleNativeNotificationAsync(appleNotificationPayload);
    
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

+ **Back-end de Node.js** : 
   
    1. Se que ainda não o fez, [Transfira o projeto de guia de introdução](app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart) ou, caso contrário, utiliza o [editor online no portal do Azure](app-service-mobile-node-backend-how-to-use-server-sdk.md#online-editor). 
    
    2. Substitua o script de tabela todoitem.js com o seguinte código:


            var azureMobileApps = require('azure-mobile-apps'),
                promises = require('azure-mobile-apps/src/utilities/promises'),
                logger = require('azure-mobile-apps/src/logger');
            
            var table = azureMobileApps.table();
            
            // When adding record, send a push notification via APNS
            table.insert(function (context) {
                // For details of the Notification Hubs JavaScript SDK, 
                // see http://aka.ms/nodejshubs
                logger.info('Running TodoItem.insert');
                
                // Create a payload that contains the new item Text.
                var payload = "{\"aps\":{\"alert\":\"" + context.item.text + "\"}}";
                
                // Execute the insert; Push as a post-execute action when results are returned as a Promise.
                return context.execute()
                    .then(function (results) {
                        // Only do the push if configured
                        if (context.push) {
                            context.push.apns.send(null, payload, function (error) {
                                if (error) {
                                    logger.error('Error while sending push notification: ', error);
                                } else {
                                    logger.info('Push notification sent successfully!');
                                }
                            });
                        }
                        return results;
                    })
                    .catch(function (error) {
                        logger.error('Error while running context.execute: ', error);
                    });
            });
            
            module.exports = table;

    2. Quando a editar o ficheiro no seu computador local, volte a publicar o projeto de servidor.
