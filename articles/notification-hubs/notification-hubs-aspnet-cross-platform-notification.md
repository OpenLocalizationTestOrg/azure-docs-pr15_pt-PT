<properties
    pageTitle="Enviar notificações em diferentes plataformas aos utilizadores com notificação concentradores (ASP.NET)"
    description="Saiba como utilizar modelos de notificação concentradores para enviar um pedido de única, uma notificação de plataforma desconhecido destina-se todas as plataformas."
    services="notification-hubs"
    documentationCenter=""
    authors="ysxu"
    manager="erikre"
    editor=""/>

<tags
    ms.service="notification-hubs"
    ms.workload="mobile"
    ms.tgt_pltfrm="mobile-windows"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="10/03/2016" 
    ms.author="yuaxu"/>

# <a name="send-cross-platform-notifications-to-users-with-notification-hubs"></a>Enviar em diferentes plataformas notificações para utilizadores com notificação concentradores


Anterior [Notificar utilizadores com notificação concentradores]tutorial, aprendeu como notificações de emissão para todos os dispositivos registados por um utilizador autenticado específico. Desse tutorial, vários pedidos foram necessário para enviar uma notificação para cada plataforma de cliente suportadas. Notificação concentradores suporta modelos, que permitem-lhe especificar a forma como pretende receber notificações de um dispositivo específico. Isto simplifica o envio de notificações em diferentes plataformas. Este tópico demonstra como tirar partido dos modelos para enviar um pedido de única, uma notificação de plataforma desconhecido destina-se todas as plataformas. Para obter informações sobre modelos mais detalhadas, consulte o artigo [Descrição geral do Azure notificação concentradores][Templates].

> [AZURE.NOTE] Notificação concentradores permite que um dispositivo registar a vários modelos com a mesma etiqueta. Neste caso, uma mensagem a receber filtragem essa etiqueta resulta em várias notificações emitidas para o dispositivo, um para cada modelo. Permite-lhe apresentar a mesma mensagem em várias notificações visuais, tal como ambos como um distintivo e como numa notificação de alerta numa aplicação da loja Windows.

Conclua os passos seguintes para enviar notificações em diferentes plataformas utilização de modelos:

1. No Explorador de solução no Visual Studio, expanda a pasta de **controladores** , em seguida, abra o ficheiro RegisterController.cs.

2. Localize o bloco de código do método de **mensagem** que cria uma nova substituir de registo de `switch` conteúdo com o seguinte código:

        switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":
                toastTemplate = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"message\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }

    Este código chama o método de específicos da plataforma para criar um registo de modelo em vez de um registo nativo. Não precisam de ser modificados registos existentes porque registos de modelo derivam registos nativos.

3. Controlador de **notificações** , substitua o método de **sendNotification** com o seguinte código:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);
        }

    Este código envia uma notificação a todas as plataformas ao mesmo tempo e sem ter de especificar uma carga útil nativa. Notificação de concentradores constrói e oferece a carga de útil correta para todos os dispositivos com o valor fornecido _etiqueta_ , conforme especificado nos modelos registados.

4. Voltar a publicar o projeto de back-end WebApi.

5. Execute novamente a aplicação de cliente e verifique o registo com êxito.

6. (Opcional) Implementar a aplicação de cliente para um dispositivo de segundo, em seguida, execute a aplicação.

    Tenha em atenção que é apresentada uma notificação em cada dispositivo.

## <a name="next-steps"></a>Próximos passos

Agora que concluiu este tutorial, saiba mais sobre como notificação concentradores e modelos nestes tópicos:

+ **[Utilizar concentradores de notificação para enviar notícias de última hora]** <br/>Demonstra outro cenário para utilizar modelos

+  **[Descrição geral de concentradores notificação Azure][Templates]**<br/>Tópico de descrição geral tem mais informações detalhadas sobre modelos.


<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Push to users ASP.NET]: /manage/services/notification-hubs/notify-users-aspnet
[Push to users Mobile Services]: /manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express for Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Utilizar concentradores de notificação para enviar notícias de última hora]: notification-hubs-windows-notification-dotnet-push-xplat-segmented-wns.md
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Notificar os utilizadores com notificação concentradores]: notification-hubs-aspnet-backend-windows-dotnet-wns-notification.md
[Templates]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub How to for Windows Store]: http://msdn.microsoft.com/library/windowsazure/jj927172.aspx
