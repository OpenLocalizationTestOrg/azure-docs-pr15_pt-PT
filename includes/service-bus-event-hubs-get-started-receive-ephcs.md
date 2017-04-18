## <a name="receive-messages-with-eventprocessorhost"></a>Receber mensagens com EventProcessorHost

[EventProcessorHost][] é uma classe de .NET que simplifica receção eventos de evento concentradores ao gerir os pontos de verificação persistentes e paralelamente recebe desses concentradores evento. Utilizar [EventProcessorHost][], pode dividir eventos através de vários destinatários, mesmo quando alojado em nós diferentes. Este exemplo mostra como utilizar [EventProcessorHost][] para um único destinatário. O exemplo de [processamento de escala saída evento][] mostra como utilizar [EventProcessorHost][] com vários destinatários.

Para utilizar [EventProcessorHost][], tem de ter uma [conta de armazenamento do Windows Azure][]:

1. Inicie a sessão [portal do Azure][]e clique em **Novo** na parte superior esquerdo do ecrã.

2. Clique em **dados + armazenamento**, em seguida, clique em **conta de armazenamento**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage1.png)

3. Na pá **criar a conta de armazenamento** , escreva um nome para a conta de armazenamento. Selecione uma subscrição do Azure, grupo de recursos e a localização na qual pretende criar o recurso. Em seguida, clique em **Criar**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage2.png)

4. Na lista de contas de armazenamento, clique na conta de armazenamento recentemente criado.

5. Na pá de conta de armazenamento, clique em **teclas de acesso**. Copie o valor de **chave1** para utilizar posteriormente neste tutorial.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-storage3.png)

4. No Visual Studio, crie um novo projeto do Visual aplicação de ambiente de trabalho da c# utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **Recetor**.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp1.png)

5. No Explorador de solução, a solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet para solução**.

6. Clique no separador **Procurar** , em seguida, procure `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Certifique-se de que o nome do projeto (**Recetor**) está especificado na caixa de **versões** . Clique em **instalar**e aceite os termos de utilização.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-eph-csharp1.png)

    Visual Studio transferências, instalações e adiciona uma referência do [Concentrador de eventos do Azure Service Bus – EventProcessorHost NuGet pacote](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com as respectivas dependências.

7. Com o botão direito do projecto **Recetor** , clique em **Adicionar**e, em seguida, clique em **classe**. Atribua um nome da nova classe **SimpleEventProcessor**e, em seguida, clique em **Adicionar** para criar a classe.

    ![](./media/service-bus-event-hubs-getstarted-receive-ephcs/create-receiver-csharp2.png)

8. Adicione as seguintes instruções na parte superior do ficheiro SimpleEventProcessor.cs:

    ```
    using Microsoft.ServiceBus.Messaging;
    using System.Diagnostics;
    ```

    Em seguida, substitua o código seguinte para o corpo a classe de:

    ```
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());

                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }
    ```

    Esta classe será chamada pela **EventProcessorHost** para eventos de processo recebido a partir do centro do evento. Tenha em atenção que a `SimpleEventProcessor` classe utiliza um cronómetro periodicamente chamar o método de ponto de verificação no contexto **EventProcessorHost** . Este procedimento garante que, se o destinatário for reiniciado, irá perder não mais de cinco minutos do processamento de trabalho.

9. Na classe de **programa** , adicione o seguinte `using` declaração na parte superior do ficheiro:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

    Em seguida, substitua a `Main` método na `Program` classe com o código seguinte, substituindo o nome do concentrador de evento e a cadeia de ligação de nível de espaço de nomes que guardou anteriormente, e a conta de armazenamento e a chave que copiou nas secções anteriores. 

    ```
    static void Main(string[] args)
    {
      string eventHubConnectionString = "{Event Hub connection string}";
      string eventHubName = "{Event Hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      var options = new EventProcessorOptions();
      options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

> [AZURE.NOTE] Neste tutorial, utiliza uma única ocorrência de [EventProcessorHost][]. Para aumentar débito, recomendamos que execute várias instâncias de [EventProcessorHost][], conforme mostrado no exemplo [com escala saída processamento de eventos][] . Nestes casos, várias instâncias automaticamente estão coordenados com outro para balanceamento de carga os eventos recebidos. Se pretender vários destinatários para cada de processo *todos* os eventos, tem de utilizar o conceito de **ConsumerGroup** . Quando receber eventos do máquinas diferentes, poderá ser útil especificar nomes para [EventProcessorHost][] instâncias com base em máquinas (ou funções) que são implementados. Para mais informações sobre estes tópicos, consulte os tópicos de [Descrição geral do evento concentradores][] e [Guia de programação de concentradores do evento][] .

<!-- Links -->
[Descrição geral de concentradores do evento]: ../articles/event-hubs/event-hubs-overview.md
[Guia de programação de concentradores de evento]: ../articles/event-hubs/event-hubs-programming-guide.md
[Dimensionada saída processamento de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Conta de armazenamento Azure]: ../articles/storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Portal do Azure]: https://portal.azure.com