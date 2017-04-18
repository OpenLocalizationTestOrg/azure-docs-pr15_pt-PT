<properties 
    pageTitle="Descrição geral das APIs concentradores do evento Azure | Microsoft Azure"
    description="Um resumo de algumas do cliente de evento concentradores .NET chave APIs."
    services="event-hubs"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="event-hubs"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="08/16/2016"
    ms.author="sethm" />

# <a name="event-hubs-api-overview"></a>Descrição geral de concentradores API do evento

Este artigo resume algumas da chave de cliente do evento concentradores .NET APIs. Existem duas categorias: APIs de tempo de execução e gestão. Tempo de execução APIs composto por todas as operações necessárias para enviar e receber uma mensagem. Operações de gestão de permitem-lhe gerir o estado de entidade um evento concentradores ao criar, atualizar e eliminar entidades.

Gestão e a execução do intervalo de cenários de monitorização. Para a documentação de referência detalhadas sobre as APIs .NET, consulte as referências de [Serviço Bus .NET](https://msdn.microsoft.com/library/azure/mt419900.aspx) e [EventProcessorHost API](https://msdn.microsoft.com/library/azure/mt445521.aspx) .

## <a name="management-apis"></a>APIs de gestão

Para efetuar as seguintes operações de gestão, tem de ter permissões de **Gerir** no espaço de nomes concentradores evento:

### <a name="create"></a>Criar

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### <a name="update"></a>Actualização

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### <a name="delete"></a>Eliminar

```
namespaceManager.DeleteEventHubAsync("Event Hub name").Wait();
```

## <a name="run-time-apis"></a>Tempo de execução APIs

### <a name="create-publisher"></a>Criar o publisher

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("Event Hub name");
```

### <a name="publish-message"></a>Publicar a mensagem

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### <a name="create-consumer"></a>Criar consumidor

```
// Create the Event Hubs client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default consumer group
EventHubConsumerGroup defaultConsumerGroup = eventHubClient.GetDefaultConsumerGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### <a name="consume-message"></a>Consumir mensagem

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## <a name="event-processor-host-apis"></a>Anfitrião de processador de eventos APIs

Estas APIs fornecem RDP para processos de trabalho podem ficar indisponíveis, através da distribuição shards entre os trabalhadores disponíveis.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.

string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

A interface de [IEventProcessor](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.ieventprocessor.aspx) é definida da seguinte forma:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos

Para saber mais acerca de cenários de evento concentradores, visite estas ligações:

- [O que é o Azure evento concentradores?](event-hubs-what-is-event-hubs.md)
- [Descrição geral de concentradores do evento](event-hubs-overview.md)
- [Guia de programação de concentradores de evento](event-hubs-programming-guide.md)
- [Exemplos de código do evento concentradores](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hubs&f[0].Type=SearchText&ac=5)

As referências de .NET API estão aqui:

- [Referências Bus de serviço e de evento concentradores .NET API](https://msdn.microsoft.com/library/azure/mt419900.aspx)
- [Anfitrião de processador de eventos referência da API](https://msdn.microsoft.com/library/azure/mt445521.aspx)
