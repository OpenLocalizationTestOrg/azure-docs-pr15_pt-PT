<properties
    pageTitle="Azure enlaces de funções evento concentrador | Microsoft Azure"
    description="Compreenda como utilizar enlaces Azure evento concentrador nas funções Azure."
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
    ms.date="10/17/2016"
    ms.author="wesmc"/>

# <a name="azure-functions-event-hub-bindings"></a>Azure enlaces do concentrador de eventos de funções

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e enlaces [Azure evento concentrador](../event-hubs/event-hubs-overview.md) de código para as funções de Azure. Funções Azure suportam enlaces acionador e de saída para Azure evento concentradores.

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

Se estiver familiarizado com o Azure concentradores de evento, consulte o artigo [Descrição geral do Azure evento concentrador](../event-hubs/event-hubs-overview.md).

## <a name="azure-event-hub-trigger-binding"></a>Azure concentrador de evento acionar encadernação

Um acionador de Azure evento concentrador pode ser utilizado para responder a um evento enviado para uma sequência de eventos do concentrador de evento. Tem de ter acesso de leitura do concentrador de evento para configurar um enlace acionador.

#### <a name="functionjson-for-event-hub-trigger-binding"></a>Function.JSON para evento concentrador acionar encadernação

O ficheiro *function.json* para um acionador de concentrador de evento Azure Especifica as seguintes propriedades:

- `type`: Tem de estar definida para *eventHubTrigger*.
- `name`: O nome da variável utilizado no código de função para a mensagem de hub do evento. 
- `direction`: Tem de estar definida *na*. 
- `path`: O nome do centro do evento.
- `consumerGroup`: Esta é uma propriedade opcional utilizada para definir o [grupo do consumidor](../event-hubs-overview.md#consumer-groups) utilizado para subscrever eventos, no concentrador de. Se for omitido, o `$Default` consumidor grupo é utilizado. 
- `connection`: O nome de uma aplicação a definição que contém a cadeia de ligação para o espaço de nomes que reside centro do evento. Copie esta cadeia de ligação ao clicar no botão de **Informações de ligação** para o espaço de nomes, não o evento próprio hub.  Nesta cadeia de ligação deve ter, pelo menos, permissões de leitura para ativar o accionador.

        {
          "bindings": [
            {
              "type": "eventHubTrigger",
              "name": "myEventHubMessage",
              "direction": "in",
              "path": "MyEventHub",
              "connection": "myEventHubReadConnectionString"
            }
          ],
          "disabled": false
        }

#### <a name="azure-event-hub-trigger-c-example"></a>Exemplo de accionador c# do Azure concentrador de evento
 
Utilizar o function.json de exemplo acima, o corpo da mensagem de evento será registado utilizando o função código c# abaixo:
 
    using System;
    
    public static void Run(string myEventHubMessage, TraceWriter log)
    {
        log.Info($"C# Event Hub trigger function processed a message: {myEventHubMessage}");
    }

#### <a name="azure-event-hub-trigger-f-example"></a>Exemplo de accionador F # do Azure concentrador de evento

Utilizar o function.json de exemplo acima, o corpo da mensagem de evento será registado utilizando o F # função código abaixo:

    let Run(myEventHubMessage: string, log: TraceWriter) =
        log.Info(sprintf "F# eventhub trigger function processed work item: %s" myEventHubMessage)

#### <a name="azure-event-hub-trigger-nodejs-example"></a>Azure accionador concentrador de evento Node.js exemplo
 
Utilizar o function.json de exemplo acima, o corpo da mensagem de evento será registado utilizando o código da função Node.js abaixo:
 
    module.exports = function (context, myEventHubMessage) {
        context.log('Node.js eventhub trigger function processed work item', myEventHubMessage);    
        context.done();
    };


## <a name="azure-event-hub-output-binding"></a>Ligação de saída do Azure concentrador de evento

Um concentrador de evento Azure ligação de saída é utilizada para escrever eventos para uma sequência de eventos do concentrador de evento. Tem de ter permissão de enviar a um concentrador de evento para escrever eventos ao mesmo. 

#### <a name="functionjson-for-event-hub-output-binding"></a>ligação de saída Function.JSON para concentrador de evento

O ficheiro *function.json* para um concentrador de evento Azure saída encadernação Especifica as seguintes propriedades:

- `type`: Tem de estar definida para *eventHub*.
- `name`: O nome da variável utilizado no código de função para a mensagem de hub do evento. 
- `path`: O nome do centro do evento.
- `connection`: O nome de uma aplicação a definição que contém a cadeia de ligação para o espaço de nomes que reside centro do evento. Copie esta cadeia de ligação ao clicar no botão de **Informações de ligação** para o espaço de nomes, não o evento próprio hub.  Nesta cadeia de ligação tem de ter permissões de enviar para enviar a mensagem para a sequência de evento concentrador.
- `direction`: Tem de estar definida para *fora*. 

        {
          "type": "eventHub",
          "name": "outputEventHubMessage",
          "path": "myeventhub",
          "connection": "MyEventHubSend",
          "direction": "out"
        }


#### <a name="azure-event-hub-c-code-example-for-output-binding"></a>Azure evento concentrador c# exemplo do código de ligação de saída
 
O seguinte exemplo função código c# demonstra escrever um evento para uma sequência de evento concentrador de evento. Esta representa de exemplo encadernação mostrada acima de saída do concentrador de evento aplicado a um acionador de temporizador c#.  
 
    using System;
    
    public static void Run(TimerInfo myTimer, out string outputEventHubMessage, TraceWriter log)
    {
        String msg = $"TimerTriggerCSharp1 executed at: {DateTime.Now}";
    
        log.Verbose(msg);   
        
        outputEventHubMessage = msg;
    }

#### <a name="azure-event-hub-f-code-example-for-output-binding"></a>Azure evento concentrador F # exemplo do código de ligação de saída

O F # exemplo função código seguinte demonstra a escrever um evento para uma sequência de evento concentrador de evento. Esta representa de exemplo encadernação mostrada acima de saída do concentrador de evento aplicado a um acionador de temporizador c#.

    let Run(myTimer: TimerInfo, outputEventHubMessage: byref<string>, log: TraceWriter) =
        let msg = sprintf "TimerTriggerFSharp1 executed at: %s" DateTime.Now.ToString()
        log.Verbose(msg);
        outputEventHubMessage <- msg;

#### <a name="azure-event-hub-nodejs-code-example-for-output-binding"></a>Exemplo de código do evento concentrador Node.js Azure para ligação de saída
 
O código de função de exemplo Node.js seguinte demonstra a escrever um evento para uma sequência de evento concentrador de evento. Esta representa de exemplo encadernação mostrada acima de saída do concentrador de evento aplicado a um acionador de temporizador Node.js.  
 
    module.exports = function (context, myTimer) {
        var timeStamp = new Date().toISOString();
        
        if(myTimer.isPastDue)
        {
            context.log('TimerTriggerNodeJS1 is running late!');
        }

        context.log('TimerTriggerNodeJS1 function ran!', timeStamp);   
        
        context.bindings.outputEventHubMessage = "TimerTriggerNodeJS1 ran at : " + timeStamp;
    
        context.done();
    };

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)]
