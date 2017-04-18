<properties
    pageTitle="Azure funções serviço Bus accionadores e enlaces | Microsoft Azure"
    description="Compreenda como utilizar accionadores Bus de serviço do Azure e enlaces no Azure funções."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
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
    ms.date="08/22/2016"
    ms.author="chrande; glenga"/>

# <a name="azure-functions-service-bus-triggers-and-bindings-for-queues-and-topics"></a>Azure funções serviço Bus accionadores e enlaces para filas e tópicos

[AZURE.INCLUDE [functions-selector-bindings](../../includes/functions-selector-bindings.md)]

Este artigo explica como configurar e código Azure Service Bus accionadores e enlaces nas funções Azure. 

[AZURE.INCLUDE [intro](../../includes/functions-bindings-intro.md)] 

## <a id="sbtrigger"></a>Acionador de fila ou tópico Bus de serviço

#### <a name="functionjson"></a>Function.JSON

O ficheiro *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para a fila ou tópico ou a mensagem fila ou um tópico. 
- `queueName`: Para fila acionar apenas, o nome da fila de espera para as de inquérito.
- `topicName`: Para o tópico acionar apenas, o nome do tópico para as de inquérito.
- `subscriptionName`: Para o tópico acionar apenas, o nome da subscrição.
- `connection`: O nome de uma aplicação a definição que contém uma cadeia de ligação Bus de serviço. A cadeia de ligação tem de ser um espaço de nomes do serviço Bus não limitado a um fila específica ou um tópico. Se a cadeia de ligação não tiver direitos de gestão, defina o `accessRights` propriedade. Se as deixar `connection` vazio, accionador ou encadernação funcionam com a cadeia de ligação de serviço Bus predefinido para a aplicação, função, que é indicada pela definição de aplicação AzureWebJobsServiceBus.
- `accessRights`: Especifica os direitos de acesso disponíveis para a cadeia de ligação. Valor predefinido é `manage`. Configurar para `listen` se estiver a utilizar uma cadeia de ligação que não fornece gerir permissões. Caso contrário, as funções runtime pode tentar e falhas para efetuar operações que precisam de gerir direitos.
- `type`: Tem de estar definida para *serviceBusTrigger*.
- `direction`: Tem de estar definida *na*. 

Exemplo *function.json* para um accionador de fila Bus de serviço:

```json
{
  "bindings": [
    {
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "name": "myQueueItem",
      "type": "serviceBusTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

#### <a name="c-code-example-that-processes-a-service-bus-queue-message"></a>C# exemplo de código que processa uma mensagem de fila Bus de serviço

```csharp
public static void Run(string myQueueItem, TraceWriter log)
{
    log.Info($"C# ServiceBus queue trigger function processed message: {myQueueItem}");
}
```

#### <a name="f-code-example-that-processes-a-service-bus-queue-message"></a>F # exemplo de código que processa uma mensagem de fila Bus de serviço

```fsharp
let Run(myQueueItem: string, log: TraceWriter) =
    log.Info(sprintf "F# ServiceBus queue trigger function processed message: %s" myQueueItem)
```

#### <a name="nodejs-code-example-that-processes-a-service-bus-queue-message"></a>Exemplo de código de node.js processa uma mensagem de fila Bus de serviço

```javascript
module.exports = function(context, myQueueItem) {
    context.log('Node.js ServiceBus queue trigger function processed message', myQueueItem);
    context.done();
};
```

#### <a name="supported-types"></a>Tipos de suportados

A mensagem de fila de serviço Bus serialização pode ser anulada para qualquer um dos seguintes tipos de:

* Objeto (a partir do JSON)
* cadeia
* matriz de bytes 
* `BrokeredMessage`(C#) 

#### <a id="sbpeeklock"></a>Comportamento PeekLock

O tempo de execução de funções recebe uma mensagem na `PeekLock` chamadas e o modo `Complete` na mensagem se a função é concluída com êxito ou chamadas `Abandon` se a função falhar. Se a função for executado durante mais da `PeekLock` limite de tempo, o bloqueio é renovada automaticamente.

#### <a id="sbpoison"></a>A mensagem corrompida foi processamento

Serviço Bus é que a sua própria mensagem corrompida foi processamento que não pode ser controlado ou configurado na configuração do Azure funções ou código. 

#### <a id="sbsinglethread"></a>Módulos por única

Por predefinição, as funções runtime processa várias mensagens fila em simultâneo. Para direcionar o runtime para processar apenas uma única fila tópico mensagem ou uma vez, defina `serviceBus.maxConcurrrentCalls` para 1 no ficheiro *host.json* . Para obter informações sobre o ficheiro *host.json* , consulte o artigo [Estrutura de pastas](functions-reference.md#folder-structure) no artigo de referência para programadores e [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) no WebJobs.Script repositório wiki.

## <a id="sboutput"></a>Fila de serviço Bus ou o tópico de ligação de saída

#### <a name="functionjson"></a>Function.JSON

O ficheiro *function.json* Especifica as seguintes propriedades.

- `name`: O nome da variável utilizado no código de função para a fila ou mensagem de fila de espera. 
- `queueName`: Para fila acionar apenas, o nome da fila de espera para as de inquérito.
- `topicName`: Para o tópico acionar apenas, o nome do tópico para as de inquérito.
- `subscriptionName`: Para o tópico acionar apenas, o nome da subscrição.
- `connection`: Acionam idênticos aos Bus de serviço.
- `accessRights`: Especifica os direitos de acesso disponíveis para a cadeia de ligação. Valor predefinido é `manage`. Configurar para `send` se estiver a utilizar uma cadeia de ligação que não fornece gerir permissões. Caso contrário, as funções runtime pode tentar e falhas para efetuar operações que requerem direitos de gestão, tal como a criação de filas.
- `type`: Tem de estar definida para *serviceBus*.
- `direction`: Tem de estar definida para *fora*. 

Exemplo *function.json* para utilizar um accionador temporizador para escrever mensagens em fila Bus de serviço:

```JSON
{
  "bindings": [
    {
      "schedule": "0/15 * * * * *",
      "name": "myTimer",
      "runsOnStartup": true,
      "type": "timerTrigger",
      "direction": "in"
    },
    {
      "name": "outputSbQueue",
      "type": "serviceBus",
      "queueName": "testqueue",
      "connection": "MyServiceBusConnection",
      "direction": "out"
    }
  ],
  "disabled": false
}
``` 

#### <a name="supported-types"></a>Tipos de suportados

Funções Azure podem criar uma mensagem de fila Bus de serviço a partir de qualquer um dos seguintes tipos de.

* Objeto (sempre cria uma mensagem JSON, cria a mensagem com um objecto nulo se o valor é nulo quando termina a função)
* cadeia (cria uma mensagem, se o valor for não nulas quando termina a função)
* matriz de bytes (funciona como cadeia) 
* `BrokeredMessage`(C#, funciona como cadeia)

Para criar várias mensagens numa função c#, pode utilizar `ICollector<T>` ou `IAsyncCollector<T>`. É criada uma mensagem ao ligar para o `Add` método.

#### <a name="c-code-examples-that-create-service-bus-queue-messages"></a>C# exemplos de código que criar mensagens em fila Bus de serviço

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, out string outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue = message;
}
```

```csharp
public static void Run(TimerInfo myTimer, TraceWriter log, ICollector<string> outputSbQueue)
{
    string message = $"Service Bus queue message created at: {DateTime.Now}";
    log.Info(message); 
    outputSbQueue.Add("1 " + message);
    outputSbQueue.Add("2 " + message);
}
```

#### <a name="f-code-example-that-creates-a-service-bus-queue-message"></a>F # exemplo de código que cria uma mensagem de fila Bus de serviço

```fsharp
let Run(myTimer: TimerInfo, log: TraceWriter, outputSbQueue: byref<string>) =
    let message = sprintf "Service Bus queue message created at: %s" (DateTime.Now.ToString())
    log.Info(message)
    outputSbQueue = message
```

#### <a name="nodejs-code-example-that-creates-a-service-bus-queue-message"></a>Exemplo de código node.js que cria uma mensagem de fila Bus de serviço

```javascript
module.exports = function (context, myTimer) {
    var timeStamp = new Date().toISOString();
    
    if(myTimer.isPastDue)
    {
        context.log('Node.js is running late!');
    }
    var message = 'Service Bus queue message created at ' + timeStamp;
    context.log(message);   
    context.bindings.outputSbQueueMsg = message;
    context.done();
};
```

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [next steps](../../includes/functions-bindings-next-steps.md)] 
