## <a name="receive-messages-with-eventprocessorhost-in-java"></a>Recebe mensagens com EventProcessorHost Java

EventProcessorHost é uma classe Java que simplifica receção eventos de evento concentradores ao gerir os pontos de verificação persistentes e paralelamente recebe desses concentradores evento. Utilizar EventProcessorHost pode dividir eventos através de vários destinatários, mesmo quando alojado em nós diferentes. Este exemplo mostra como utilizar EventProcessorHost para um único destinatário.

###<a name="create-a-storage-account"></a>Criar uma conta de armazenamento

Para que possa utilizar EventProcessorHost, tem de ter uma [conta de armazenamento do Windows Azure][]:

1. Inicie a sessão [portal clássica Azure][]e clique em **Novo** na parte inferior do ecrã.

2. Clique em **Serviços de dados**, **armazenamento**e, em seguida **Criação rápida**e, em seguida, escreva um nome para a sua conta de armazenamento. Selecione a região pretendida e, em seguida, clique em **Criar a conta de armazenamento**.

    ![][11]

3. Clique na conta de armazenamento recentemente criado e, em seguida, clique em **Gerir as teclas de acesso**:

    ![][12]

    Copie a tecla de acesso principal para utilizar posteriormente neste tutorial.

###<a name="create-a-java-project-using-the-eventprocessor-host"></a>Criar um projeto de Java utilizando o anfitrião EventProcessor

A biblioteca do cliente Java para evento concentradores está disponível para utilizar em projetos Maven a partir do [Repositório Central Maven][Maven Package]e pode ser referenciados utilizando a seguinte declaração de dependência dentro de ficheiro do projeto avançou:    

``` XML
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>{VERSION}</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>{VERSION}</version>
</dependency>
```
 
Para diferentes tipos de compilação ambientes, explicitamente pode obter os mais recentes para caixa ficheiros a partir do [Repositório Central Maven] [ Maven Package] ou a partir [do ponto de distribuição de lançamento no GitHub](https://github.com/Azure/azure-event-hubs/releases).  

1. Para o exemplo seguinte, crie um novo projeto de Maven para uma aplicação de consola/shell no seu ambiente de desenvolvimento Java favorito. A classe será chamada ```ErrorNotificationHandler```.     

    ``` Java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```

2. Utilize o seguinte código para criar uma nova classe denominada ```EventProcessor```.

    ```Java
    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.CloseReason;
    import com.microsoft.azure.eventprocessorhost.IEventProcessor;
    import com.microsoft.azure.eventprocessorhost.PartitionContext;

    public class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }
        
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> messages) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got message batch");
            int messageCount = 0;
            for (EventData data : messages)
            {
                System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                        data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBody(), "UTF8"));
                messageCount++;
                
                this.checkpointBatchingCount++;
                if ((checkpointBatchingCount % 5) == 0)
                {
                    System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                        data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                    context.checkpoint(data);
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + messageCount + " for host " + context.getOwner());
        }
    }
    ```

3. Criar uma classe final chamada ```EventProcessorSample```, utilizando o código seguinte.

    ```Java
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.eventhubs.EventData;

    public class EventProcessorSample
    {
        public static void main(String args[])
        {
            final String consumerGroupName = "$Default";
            final String namespaceName = "----ServiceBusNamespaceName-----";
            final String eventHubName = "----EventHubName-----";
            final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
            final String sasKey = "---SharedAccessSignatureKey----";

            final String storageAccountName = "---StorageAccountName----";
            final String storageAccountKey = "---StorageAccountKey----";
            final String storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=" + storageAccountName + ";AccountKey=" + storageAccountKey;
            
            ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(namespaceName, eventHubName, sasKeyName, sasKey);
            
            EventProcessorHost host = new EventProcessorHost(eventHubName, consumerGroupName, eventHubConnectionString.toString(), storageConnectionString);
            
            System.out.println("Registering host named " + host.getHostName());
            EventProcessorOptions options = new EventProcessorOptions();
            options.setExceptionNotification(new ErrorNotificationHandler());
            try
            {
                host.registerEventProcessor(EventProcessor.class, options).get();
            }
            catch (Exception e)
            {
                System.out.print("Failure while registering: ");
                if (e instanceof ExecutionException)
                {
                    Throwable inner = e.getCause();
                    System.out.println(inner.toString());
                }
                else
                {
                    System.out.println(e.toString());
                }
            }

            System.out.println("Press enter to stop");
            try
            {
                System.in.read();
                host.unregisterEventProcessor();
                
                System.out.println("Calling forceExecutorShutdown");
                EventProcessorHost.forceExecutorShutdown(120);
            }
            catch(Exception e)
            {
                System.out.println(e.toString());
                e.printStackTrace();
            }
            
            System.out.println("End of sample");
        }
    }
    ```

4. Substitua os seguintes campos com os valores que utilizou quando criou o concentrador de evento e a conta de armazenamento.

    ``` Java
    final String namespaceName = "----ServiceBusNamespaceName-----";
    final String eventHubName = "----EventHubName-----";

    final String sasKeyName = "-----SharedAccessSignatureKeyName-----";
    final String sasKey = "---SharedAccessSignatureKey----";

    final String storageAccountName = "---StorageAccountName----"
    final String storageAccountKey = "---StorageAccountKey----";
    ```

> [AZURE.NOTE] Neste tutorial, utiliza uma única ocorrência de EventProcessorHost. Para aumentar débito, é recomendado que execute várias instâncias de EventProcessorHost. Nestes casos, várias instâncias automaticamente estão coordenados com os outros para poder balanceamento de carga os eventos recebidos. Se pretender vários destinatários para cada de processo *todos* os eventos, tem de utilizar o conceito de **ConsumerGroup** . Quando receber eventos do máquinas diferentes, poderá ser útil especificar nomes para EventProcessorHost instâncias com base no máquinas (ou funções) que são implementados.

<!-- Links -->
[Event Hubs overview]: ../articles/event-hubs/event-hubs-overview.md
[Conta de armazenamento Azure]: ../articles/storage/storage-create-storage-account.md
[Azure portal clássico]: http://manage.windowsazure.com
[Maven Package]: https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22

<!-- Images -->
[11]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-get-started-receive-ephjava/create-eph-csharp3.png

