<properties
    pageTitle="Ligação do Azure concentrador de notificação de funções | Microsoft Azure"
    description="Compreenda como utilizar encadernação concentrador de notificação do Azure nas funções Azure."
    services="functions"
    documentationCenter="na"
    authors="wesmc7777"
    manager="erikre"
    editor=""
    tags=""
    keywords="funções de Azure, funções, evento processamento, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="10/27/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-notification-hub-output-binding"></a>Ligação de saída do Azure concentrador de notificação de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e código concentrador de notificação do Azure enlaces Azure funções. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

As funções podem enviar notificações push utilizar um concentrador de notificação do Azure configurados com algumas linhas de código. No entanto, o concentrador de notificação do Azure tem de ser configurado para a plataforma notificações serviços (PNS) que pretende utilizar. Para mais informações sobre a configuração de um concentrador de notificação do Azure e desenvolver um aplicações de cliente que registe-se para receber notificações, consulte o artigo [Introdução ao concentradores de notificação](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md) e clique na plataforma de cliente de destino na parte superior.

As notificações que envia podem ser notificações nativas ou notificações de modelo. Notificações nativas alvo uma plataforma de notificação específico, tal como está configurado na `platform` propriedade da ligação de saída. Uma notificação de modelo pode ser utilizada para múltiplas plataformas de destino.   

## <a name="notification-hub-output-binding-properties"></a>Propriedades de enlace de saída de concentrador de notificação

O ficheiro function.json fornece as seguintes propriedades:

- `name`: Nome variável utilizado no código de função da mensagem de notificação concentrador.
- `type`: tem de estar definida para *"notificationHub"*.
- `tagExpression`: Expressões etiqueta permitem-lhe especificar que sejam entregues notificações de um conjunto de dispositivos que tem registou para receber notificações que correspondem a expressão de etiqueta.  Para mais informações, consulte o artigo [Encaminhamento e etiqueta expressões](../notification-hubs/notification-hubs-tags-segment-push-message.md).
- `hubName`: Nome do recurso concentrador notificação no portal do Azure.
- `connection`: Esta cadeia de ligação tem de ser uma cadeia de ligação de **Definição da aplicação** definida para o valor de *DefaultFullSharedAccessSignature* para o seu centro de notificação.
- `direction`: tem de estar definida para *"saída"*. 
- `platform`: A propriedade de plataforma indica a plataforma de notificação os destinos de notificação. Tem de ser um dos seguintes valores:
    - `template`: Esta é a plataforma de predefinição se a propriedade de plataforma for omitida de ligação de saída. Notificações de modelo podem ser utilizadas para qualquer plataforma configurada no Centro de notificação do Azure de destino. Para mais informações sobre como utilizar modelos no geral para enviar notificações de plataforma cruzada com um concentrador de notificação do Azure, consulte o artigo [modelos](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md).
    - `apns`: Serviço de notificações Push da Apple. Para obter mais informações sobre como configurar o concentrador de notificação para APNS e receber a notificação numa aplicação de cliente, consulte o artigo [enviar as notificações push para iOS com concentradores de notificação do Azure](../notification-hubs/notification-hubs-ios-apple-push-notification-apns-get-started.md) 
    - `adm`: [Amazon dispositivo mensagens](https://developer.amazon.com/device-messaging). Para obter mais informações sobre como configurar o concentrador de notificação para ADM e receber a notificação de numa aplicação Kindle, consulte o artigo [Introdução ao concentradores de notificação para as aplicações do Kindle](../notification-hubs/notification-hubs-kindle-amazon-adm-push-notification.md) 
    - `gcm`: [Google Cloud mensagens](https://developers.google.com/cloud-messaging/). Firebase nuvem mensagens, que é a nova versão do GCM, também é suportada. Para obter mais informações sobre como configurar o concentrador de notificação para GCM/FCM e receber a notificação de numa aplicação cliente Android, consulte o artigo [enviar as notificações push para Android com o Azure notificação concentradores](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md)
    - `wns`: [Serviços de notificação do Windows emissão](https://msdn.microsoft.com/en-us/windows/uwp/controls-and-patterns/tiles-and-notifications-windows-push-notification-services--wns--overview) filtragem de plataformas do Windows. Windows Phone 8.1 e posterior também é suportada pelo WNS. Para obter mais informações sobre como configurar o concentrador de notificação para WNS e receber a notificação de numa aplicação Universal Windows plataforma (UWP), consulte o artigo [Introdução à notificação concentradores para Windows Universal plataforma de aplicações](../notification-hubs/notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
    - `mpns`: [Serviço de notificações Push da Microsoft](https://msdn.microsoft.com/en-us/library/windows/apps/ff402558.aspx). Suporta esta plataforma Windows Phone 8 e plataformas anteriores do Windows Phone. Para obter mais informações sobre como configurar o concentrador de notificação para MPNS e receber a notificação numa aplicação do Windows Phone, consulte o artigo [enviar as notificações push com Azure notificação concentradores no Windows Phone](../notification-hubs/notification-hubs-windows-mobile-push-notifications-mpns.md)
 
Exemplo function.json:

    {
      "bindings": [
        {
          "name": "notification",
          "type": "notificationHub",
          "tagExpression": "",
          "hubName": "my-notification-hub",
          "connection": "MyHubConnectionString",
          "platform": "gcm",
          "direction": "out"
        }
      ],
      "disabled": false
    }

## <a name="notification-hub-connection-string-setup"></a>Configuração de cadeia de ligação de concentrador de notificação

Para utilizar uma saída do concentrador de notificação encadernação, tem de configurar a cadeia de ligação para o concentrador. Isto pode ser feito no separador *integrar* ao selecionar o seu centro de notificação ou criar um novo. 

Pode adicionar também manualmente uma cadeia de ligação para um concentrador existente ao adicionar uma cadeia de ligação para o *DefaultFullSharedAccessSignature* ao seu centro de notificação. Nesta cadeia de ligação fornece a sua permissão de acesso de função para enviar mensagens de notificação. O valor de cadeia de ligação *DefaultFullSharedAccessSignature* pode ser acedido a partir do botão de **teclas** na pá principal do seu recurso de concentrador de notificação no portal do Azure. Para adicionar manualmente uma cadeia de ligação para o seu centro, utilize os seguintes passos: 

1. No pá a **aplicação de função** do Azure portal, clique em **as definições da aplicação função > aceda a definições de aplicação de serviço**.

2. Na pá **Definições** , clique em **Definições da aplicação**.

3. Desloque para baixo para a secção de **cadeias de ligação** e adicionar uma entrada com nome para o valor de *DefaultFullSharedAccessSignature* para o seu centro de notificação. Altere o tipo de **escala**.
4. Referenciar o seu nome de cadeia de ligação nas ligações de saída. Semelhante a **MyHubConnectionString** utilizadas no exemplo acima.

## <a name="native-notification-examples"></a>Exemplos de notificação nativo

#### <a name="apns-native-notifications-with-c-queue-triggers"></a>Notificações de nativas APNS com c# fila Accionadores

Este exemplo mostra como utilizar tipos de definidas na [Biblioteca do Microsoft Azure notificação concentradores](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de APNS nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native APNS notification is ...
        // { "aps": { "alert": "notification message" }}  

        log.Info($"Sending APNS notification of a new user");   
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string apnsNotificationPayload = "{\"aps\": {\"alert\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{apnsNotificationPayload}");
        await notification.AddAsync(new AppleNotification(apnsNotificationPayload));        
    }

#### <a name="gcm-native-notifications-with-c-queue-triggers"></a>Notificações de nativas GCM com c# fila Accionadores

Este exemplo mostra como utilizar tipos de definidas na [Biblioteca do Microsoft Azure notificação concentradores](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de GCM nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The JSON format for a native GCM notification is ...
        // { "data": { "message": "notification message" }}  

        log.Info($"Sending GCM notification of a new user");    
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string gcmNotificationPayload = "{\"data\": {\"message\": \"A new user wants to be added (" + 
                                            user.name + ")\" }}";
        log.Info($"{gcmNotificationPayload}");
        await notification.AddAsync(new GcmNotification(gcmNotificationPayload));       
    }

#### <a name="wns-native-notifications-with-c-queue-triggers"></a>Notificações de nativas WNS com c# fila Accionadores

Este exemplo mostra como utilizar tipos de definidas na [Biblioteca do Microsoft Azure notificação concentradores](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/) para enviar uma notificação de alerta WNS nativa. 

    #r "Microsoft.Azure.NotificationHubs"
    #r "Newtonsoft.Json"
    
    using System;
    using Microsoft.Azure.NotificationHubs;
    using Newtonsoft.Json;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<Notification> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a new user to be processed in the form of a JSON string with 
        // a "name" value.
        //
        // The XML format for a native WNS toast notification is ...
        // <?xml version="1.0" encoding="utf-8"?>
        // <toast>
        //   <visual>
        //     <binding template="ToastText01">
        //       <text id="1">notification message</text>
        //     </binding>
        //   </visual>
        // </toast>

        log.Info($"Sending WNS toast notification of a new user");  
        dynamic user = JsonConvert.DeserializeObject(myQueueItem);  
        string wnsNotificationPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                                        "<toast><visual><binding template=\"ToastText01\">" +
                                            "<text id=\"1\">" + 
                                                "A new user wants to be added (" + user.name + ")" + 
                                            "</text>" +
                                        "</binding></visual></toast>";

        log.Info($"{wnsNotificationPayload}");
        await notification.AddAsync(new WindowsNotification(wnsNotificationPayload));       
    }


## <a name="template-notification-examples"></a>Exemplos de notificação de modelo

#### <a name="template-example-for-nodejs-timer-triggers"></a>Exemplo de modelo para Node.js accionadores de temporizador 

Este exemplo envia uma notificação de [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
       
        if(myTimer.isPastDue)
        {
            context.log('Node.js is running late!');
        }
        context.log('Node.js timer trigger function ran!', timeStamp);  
        context.bindings.notification = {
            location: "Redmond",
            message: "Hello from Node!"
        };
        context.done();
    };

#### <a name="template-example-for-f-timer-triggers"></a>Exemplo de modelo para F # temporizador accionadores

Este exemplo envia uma notificação de [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém `location` e `message`.

    let Run(myTimer: TimerInfo, notification: byref<IDictionary<string, string>>) =
        notification = dict [("location", "Redmond"); ("message", "Hello from F#!")]


#### <a name="template-example-using-an-out-parameter"></a>Exemplo de modelo utilizando um parâmetro de saída 

Este exemplo envia uma notificação de [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém uma `message` marcador de posição no modelo.

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
     
    public static void Run(string myQueueItem,  out IDictionary<string, string> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = GetTemplateProperties(myQueueItem);
    }
     
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return templateProperties;
    }

#### <a name="template-example-with-asynchronous-function"></a>Exemplo de modelo com a função assíncrona

Se estiver a utilizar o código assíncrono, parâmetros de saída não são permitidos. Neste caso utilize `IAsyncCollector` para voltar a sua notificação de modelo. O código seguinte é um exemplo assíncrono de código acima. 

    using System;
    using System.Threading.Tasks;
    using System.Collections.Generic;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<IDictionary<string,string>> notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        log.Info($"Sending Template Notification to Notification Hub");
        await notification.AddAsync(GetTemplateProperties(myQueueItem));    
    }
    
    private static IDictionary<string, string> GetTemplateProperties(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["user"] = "A new user wants to be added : " + message;
        return templateProperties;
    }

#### <a name="template-example-using-json"></a>Exemplo de modelo utilizando JSON 

Este exemplo envia uma notificação de [registo de modelo](../notification-hubs/notification-hubs-templates-cross-platform-push-messages.md) que contém uma `message` marcador de posição no modelo de utilizar uma cadeia JSON válida.

    using System;
     
    public static void Run(string myQueueItem,  out string notification, TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
        notification = "{\"message\":\"Hello from C#. Processed a queue item!\"}";
    }


#### <a name="template-example-using-notification-hubs-library-types"></a>Exemplo de modelo utilizando tipos de biblioteca concentradores de notificação

Este exemplo mostra como utilizar tipos de definidas na [Biblioteca do Microsoft Azure notificação concentradores](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/). 


    #r "Microsoft.Azure.NotificationHubs"

    using System;
    using System.Threading.Tasks;
    using Microsoft.Azure.NotificationHubs;
     
    public static void Run(string myQueueItem,  out Notification notification, TraceWriter log)
    {
       log.Info($"C# Queue trigger function processed: {myQueueItem}");
       notification = GetTemplateNotification(myQueueItem);
    }

    private static TemplateNotification GetTemplateNotification(string message)
    {
        Dictionary<string, string> templateProperties = new Dictionary<string, string>();
        templateProperties["message"] = message;
        return new TemplateNotification(templateProperties);
    }

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
