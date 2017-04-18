<properties
    pageTitle="Azure funções Twilio encadernação | Microsoft Azure"
    description="Compreenda como utilizar Twilio enlaces com funções Azure."
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
    ms.date="10/20/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-twilio-output-binding"></a>Ligação de saída do Azure Twilio de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e utilizar Twilio enlaces com funções de Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Azure suporta de funções Twilio enlaces para ativar a sua funções para enviar mensagens de texto SMS com algumas linhas de código e uma conta de [Twilio](https://www.twilio.com/) de saída. 
 

## <a name="functionjson-for-azure-notification-hub-output-binding"></a>ligação de saída Function.JSON para concentrador de notificação do Azure

O ficheiro function.json fornece as seguintes propriedades:

- `name`: Nome variável utilizado no código de função para a mensagem de texto Twilio SMS.
- `type`: tem de estar definida para *"twilioSms"*.
- `accountSid`: Este valor tem de estar definida para o nome de uma definição de aplicação que detém o Sid de conta Twilio.
- `authToken`: Este valor tem de estar definida para o nome de uma definição de aplicação que detém o token de autenticação Twilio.
- `to`: Este valor está definido para o número de telefone de texto SMS é enviado para.
- `from`: Este valor está definido para o número de telefone de texto SMS é enviado a partir de.
- `direction`: tem de estar definida para *"saída"*.
- `body`: Este valor pode ser utilizado para codificar a mensagem de texto SMS caso não precise de defini-la dinamicamente o código para a sua função. 

 
Exemplo function.json:

    {
      "type": "twilioSms",
      "name": "message",
      "accountSid": "TwilioAccountSid",
      "authToken": "TwilioAuthToken",
      "to": "+1704XXXXXXX",
      "from": "+1425XXXXXXX",
      "direction": "out",
      "body": "Azure Functions Testing"
    }


## <a name="example-c-queue-trigger-with-twilio-output-binding"></a>Ligação de saída de exemplo c# fila accionador com Twilio

#### <a name="synchronous"></a>Síncrono

Este exemplo síncrono de código para um acionador de fila de armazenamento do Windows Azure utiliza um parâmetro de saída para enviar uma mensagem de texto para um cliente quem uma encomenda.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"

    using System;
    using Newtonsoft.Json;
    using Twilio;

    public static void Run(string myQueueItem, out SMSMessage message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        message = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        message.Body = msg;
        message.To = order.mobileNumber;
    }

#### <a name="asynchronous"></a>Assíncrona

Este exemplo assíncrona de código para um acionador de fila de armazenamento do Windows Azure envia uma mensagem de texto para um cliente que uma encomenda.

    #r "Newtonsoft.Json"
    #r "Twilio.Api"
     
    using System;
    using Newtonsoft.Json;
    using Twilio;
    
    public static async Task Run(string myQueueItem, IAsyncCollector<SMSMessage> message,  TraceWriter log)
    {
        log.Info($"C# Queue trigger function processed: {myQueueItem}");

        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        dynamic order = JsonConvert.DeserializeObject(myQueueItem);
        string msg = "Hello " + order.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the SMSMessage variable.
        SMSMessage smsText = new SMSMessage();

        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        smsText.Body = msg;
        smsText.To = order.mobileNumber;
        
        await message.AddAsync(smsText);
    }


## <a name="example-nodejs-queue-trigger-with-twilio-output-binding"></a>Ligação de saída de accionador fila de Node.js de exemplo com Twilio

Este exemplo Node.js envia uma mensagem de texto para um cliente que uma encomenda.

    module.exports = function (context, myQueueItem) {
        context.log('Node.js queue trigger function processed work item', myQueueItem);
    
        // In this example the queue item is a JSON string representing an order that contains the name of a 
        // customer and a mobile number to send text updates to.
        var msg = "Hello " + myQueueItem.name + ", thank you for your order.";
    
        // Even if you want to use a hard coded message and number in the binding, you must at least 
        // initialize the message binding.
        context.bindings.message = {};
    
        // A dynamic message can be set instead of the body in the output binding. In this example, we use 
        // the order information to personalize a text message to the mobile number provided for
        // order status updates.
        context.bindings.message = {
            body : msg,
            to : myQueueItem.mobileNumber
        };
    
        context.done();
    };

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
