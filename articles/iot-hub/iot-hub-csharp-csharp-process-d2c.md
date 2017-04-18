<properties
    pageTitle="Processar as mensagens de dispositivo para nuvem IoT concentrador (.Net) | Microsoft Azure"
    description="Siga este tutorial para saber mais útil padrões para processar IoT concentrador dispositivo para nuvem mensagens."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="csharp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="10/05/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-net"></a>Tutorial: Como processar IoT concentrador dispositivo para nuvem mensagens utilizando o .net

[AZURE.INCLUDE [iot-hub-selector-process-d2c](../../includes/iot-hub-selector-process-d2c.md)]

## <a name="introduction"></a>Introdução

Azure IoT concentrador é um serviço totalmente gerido que permite fiável e seguras bidirecional as comunicações entre milhões de IoT dispositivos e uma aplicação back-end. Outros tutoriais ([Introdução ao centro de IoT] e [enviar mensagens de dispositivo de nuvem com IoT concentrador][lnk-c2d]) mostrar-lhe como utilizar a nuvem de dispositivo e na nuvem-a-dispositivo mensagens funcionalidade básica do IoT concentrador.

Neste tutorial constrói no código mostrado no tutorial [Introdução ao centro de IoT] e mostra duas padrões dimensionáveis que pode utilizar para processar nuvem de dispositivo de mensagens:

- O armazenamento fiável das mensagens de dispositivo para nuvem no [armazenamento de Blobs do Azure]. Um cenário comum é analytics *caminho frio* , na qual guardar dados de telemetria no blobs para utilizar como entrada para processos de análise. Estes processos podem ser conduzidos através de ferramentas como [Azure dados fábrica] ou na pilha de [HDInsight (Hadoop)] .

- O fiável processamento das mensagens de dispositivo para nuvem *interativos* . Nuvem de dispositivo mensagens são interativas quando são imediatas accionadores para um conjunto de ações no aplicação back-end. Por exemplo, um dispositivo poderá enviar uma mensagem de alarme que accionadores inserir uma permissão para um sistema CRM. Por outro lado, mensagens de *ponto de dados* simplesmente feed motor de análise. Por exemplo, telemetria temperatura a partir de um dispositivo que está a ser armazenado para análise posterior é uma mensagem de ponto de dados.

Uma vez que IoT concentrador expõe um [Concentrador de evento][lnk-event-hubs]-ponto final compatível para receber mensagens de dispositivo para nuvem, este tutorial utiliza uma instância de [EventProcessorHost] . Esta instância:

* Forma fiável armazena as mensagens de *ponto de dados* no armazenamento de Blobs do Azure.
* Reencaminha *interativos* mensagens de dispositivo para nuvem para uma Azure [Service Bus fila de espera] para processamento de imediato.

Serviço Bus ajuda a garantir fiável processamento das mensagens interativos, tal como fornece os pontos de verificação por mensagem e duplicação da com base em janela de tempo.

> [AZURE.NOTE] Uma instância de **EventProcessorHost** é apenas uma forma para processar mensagens interativas. Outras opções incluem [Azure Service ferro] [ lnk-service-fabric] e [Análises de sequência Azure][lnk-stream-analytics].

No final deste tutorial, executar três aplicações de consola do Windows:

* **SimulatedDevice**, uma versão modificada da aplicação criada no tutorial [Introdução ao centro de IoT] , envia dados mensagens de dispositivo para nuvem do ponto de todas as mensagens de dispositivo para nuvem segunda e interativos cada 10 segundos. Esta aplicação utiliza o protocolo AMQP para comunicar com IoT concentrador.
* **ProcessDeviceToCloudMessages** utiliza a classe de [EventProcessorHost] para obter as mensagens a partir do ponto final compatível com o evento concentrador. -Lo, em seguida, sujeito armazena as mensagens de ponto de dados no armazenamento de Blobs do Azure e reencaminhe mensagens interativas para uma fila Bus de serviço.
* **ProcessD2CInteractiveMessages** filas anular as mensagens da fila de serviço Bus interativas.

> [AZURE.NOTE] Concentrador IoT tem suporte SDK para muitos plataformas de dispositivos e idiomas, incluindo C, Java e JavaScript. Para saber como substituir o dispositivo simulado neste tutorial com um dispositivo físico e como ligar dispositivos a um concentrador de IoT, consulte o [Centro de programadores do Azure IoT].

Neste tutorial é diretamente aplicável a outras formas de consumir mensagens compatível com o evento concentrador, como projetos [HDInsight (Hadoop)] . Para mais informações, consulte o artigo [Guia de programador do Azure IoT concentrador - dispositivo para na nuvem].

Para concluir este tutorial, precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) apenas de duas minutos.

Que deve ter alguns conhecimentos básicos de [Armazenamento do Windows Azure] e [Azure Service Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Enviar mensagens interativas a partir de um dispositivo simulado

Nesta secção, modifique a aplicação de dispositivo simulada que criou no tutorial [Introdução ao centro de IoT] para enviar mensagens de dispositivo para nuvem interativas para o concentrador IoT.

1. No Visual Studio, no **SimulatedDevice** project, adicione o seguinte método para a classe de **programa** .

    ```
    private static async void SendDeviceToCloudInteractiveMessagesAsync()
    {
      while (true)
      {
        var interactiveMessageString = "Alert message!";
        var interactiveMessage = new Message(Encoding.ASCII.GetBytes(interactiveMessageString));
        interactiveMessage.Properties["messageType"] = "interactive";
        interactiveMessage.MessageId = Guid.NewGuid().ToString();

        await deviceClient.SendEventAsync(interactiveMessage);
        Console.WriteLine("{0} > Sending interactive message: {1}", DateTime.Now, interactiveMessageString);

        Task.Delay(10000).Wait();
      }
    }
    ```

    Este método é semelhante ao método **SendDeviceToCloudMessagesAsync** no projeto **SimulatedDevice** . São as diferenças apenas que agora definir a propriedade de sistema **MessageId** e uma propriedade de utilizador denominado **messageType**.
    O código atribui um identificador exclusivo global (GUID) para a propriedade **MessageId** . O serviço Bus pode utilizar este identificador para duplicar anular as mensagens que recebe. O exemplo utiliza a propriedade **messageType** para distinguir interativos de mensagens de ponto de dados. A aplicação transmite estas informações nas propriedades de mensagem, em vez de no corpo da mensagem, para que o processador de eventos não é necessário serialização da mensagem para executar o encaminhamento de mensagens.

    > [AZURE.NOTE] É importante criar **MessageId** utilizada para retirar duplicar interativas mensagens no código de dispositivo. Comunicações de rede intermitentes ou outras falhas, poderá resultar em várias retransmissão da mesma mensagem de que o dispositivo. Também pode utilizar um ID da mensagem semântico, tal como um hash dos campos de dados relevantes mensagem, em vez de um GUID.

2. Adicione o seguinte método no método **principal** , para a direita antes do `Console.ReadLine()` linha:

    ````
    SendDeviceToCloudInteractiveMessagesAsync();
    ````

    > [AZURE.NOTE] Por razões de simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar uma política de repetir como duplicar exponencial, tal como sugerido no artigo MSDN [Processamento de falhas breves].

## <a name="process-device-to-cloud-messages"></a>Processar nuvem de dispositivo de mensagens

Nesta secção, crie uma aplicação de consola do Windows que processa mensagens de dispositivo para nuvem a partir do IoT Hub. Concentrador IOT expõe [concentrador de evento]-compatível com ponto final para ativar uma aplicação ler mensagens de dispositivo a nuvem. Neste tutorial utiliza a classe de [EventProcessorHost] para estas mensagens numa aplicação de consola do processo. Para mais informações sobre como mensagens de processo do evento concentradores, consulte o tutorial de [Introdução ao evento concentradores] .

O desafio quando implementar o armazenamento fiável das mensagens de ponto de dados ou reencaminhamento de mensagens interativos é que processamento de evento depende do consumidor de mensagem para fornecer os pontos de verificação para o seu progresso. Além disso, para alcançar um débito alto, quando a ler de evento concentradores deve fornecer os pontos de verificação em lotes de grandes dimensões. Esta abordagem cria a possibilidade de processamento duplicados para um grande número de mensagens se existe uma falha na e reverter para o ponto de verificação anterior. Neste tutorial, consulte como sincronizar escritas de armazenamento do Windows Azure e windows a duplicação de serviço Bus com **EventProcessorHost** pontos de verificação.

Para escrever mensagens de forma fiável ao armazenamento do Azure, exemplo utiliza a funcionalidade de consolidação de bloco individuais do [Bloco blobs][Azure Block Blobs]. Processador de eventos é acumulado mensagens na memória até que está na altura de fornecer um ponto de verificação. Por exemplo, depois da memória intermédia acumulada das mensagens de atinge o tamanho máximo do bloco de 4 MB ou depois do serviço Bus a duplicação decorrida janela de tempo. Em seguida, antes do ponto de verificação, o código consolida um novo bloco para o blob.

Processador de eventos utiliza concentradores evento Advance desloca a mensagem como bloco IDs. Este mecanismo permite que o processador de eventos executar uma verificação de duplicação a antes do novo bloco-consolida ao armazenamento, tendo o cuidado de uma falha de sistema possível entre cometer um bloco e o ponto de verificação.

> [AZURE.NOTE] Neste tutorial utiliza uma única conta de armazenamento do Windows Azure para escrever todas as mensagens obtidas a partir do IoT Hub. Para decidir se precisa de utilizar várias contas de armazenamento do Windows Azure na sua solução, consulte o artigo [escalabilidade de armazenamento do Windows Azure diretrizes].

A aplicação utiliza a funcionalidade da duplicação de serviço Bus para evitar duplicados quando processa mensagens interativas. O dispositivo simulado de data/nas cada mensagem interativa com um único **MessageId**. Estes IDs de ativar Bus de serviço para se certificar de que, na janela de tempo especificado duplicação a, sem duas mensagens com o mesmo **MessageId** são entregues os destinatários. Esta a duplicação, juntamente com a semântica de conclusão por mensagem fornecida pela filas Bus de serviço, torna mais fácil implementar o processamento das mensagens interativos fiável.

Para se certificar de que nenhuma mensagem é submetida novamente fora da janela da duplicação, o código sincroniza o mecanismo de ponto de verificação **EventProcessorHost** com a janela da duplicação de fila Bus de serviço. Esta sincronização é efectuada por forçar um ponto de verificação pelo menos uma vez sempre que a janela a duplicação decorrida (neste tutorial, a janela é uma hora).

> [AZURE.NOTE] Neste tutorial, utiliza uma única fila de serviço Bus com partições processo todas as mensagens interativas obtidas a partir do IoT Hub. Para mais informações sobre como utilizar filas Bus de serviço para cumprir os requisitos de escalabilidade da sua solução, consulte a documentação do [Azure Service Bus] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Aprovisionar uma conta de armazenamento do Windows Azure e uma fila Bus de serviço
Para utilizar a classe de [EventProcessorHost] , tem de ter uma conta de armazenamento do Windows Azure para ativar a **EventProcessorHost** registar informações de ponto de verificação. Pode utilizar uma conta de armazenamento do Windows Azure existente ou siga as instruções [Sobre o armazenamento do Windows Azure] para criar um novo. Anote a cadeia de ligação de conta de armazenamento do Windows Azure.

> [AZURE.NOTE] Quando copiar e colar a cadeia de ligação de conta de armazenamento do Windows Azure, certifique-se de não existem sem espaços incluídos.

Também precisa de uma fila Bus de serviço para activar fiável processamento das mensagens interativos. Pode criar uma fila através de programação, com uma janela de duplicação a uma hora, tal como é explicado [como utilizar o serviço Bus filas][serviço Bus fila de espera]. Em alternativa, pode utilizar o [portal clássica Azure][lnk-classic-portal], seguindo estes passos:

1. No canto inferior esquerdo, clique em **Novo** . Em seguida, clique em **Serviços de aplicação** > **Serviço Bus** > **fila** > **Criar personalizado**. Introduza o nome **d2ctutorial**, selecione uma região e utilizar um espaço de nomes existente ou criar um novo. Na página seguinte, selecione **Ativar deteção de duplicados**e definir **janela de tempo do histórico de deteção de duplicados** para uma hora. Em seguida, clique na marca de verificação no canto inferior direito para guardar a configuração de fila de espera.

    ![Criar uma fila no portal do Azure][30]

2. Na lista de filas Bus de serviço, clique em **d2ctutorial**e, em seguida, clique em **Configurar**. Crie duas políticas de acesso partilhada, uma chamada **Enviar** com permissões de **Enviar** e uma chamada de **ouvir** com permissões **ouvir** . Quando tiver terminado, clique em **Guardar** na parte inferior.

    ![Configurar uma fila no portal do Azure][31]

3. Clique em **Dashboard** no topo e, em seguida, **informações de ligação** na parte inferior. Anote as duas de cadeias de ligação.

    ![Dashboard de fila de espera no portal do Azure][32]

### <a name="create-the-event-processor"></a>Criar o processador de eventos

1. No atual solução do Visual Studio, para criar um projeto do Visual c# Windows utilizando o modelo de projeto de **Aplicação de consola** , clique em **ficheiro** > **Adicionar** > **Novo projeto**. Certifique-se de que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **ProcessDeviceToCloudMessages**e clique em **OK**.

    ![Novo projeto no Visual Studio][10]

2. No Explorador de solução, com o botão direito do projecto **ProcessDeviceToCloudMessages** e, em seguida, clique em **Gerir pacotes de NuGet**. É apresentada a caixa de diálogo **Gestor de pacotes NuGet** .

3. Procurar **WindowsAzure.ServiceBus**, clique em **instalar**e aceite os termos de utilização. Esta operação transfere, instalações e adiciona uma referência para o [pacote do Azure Service Bus NuGet](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com as respectivas dependências.

4. Procurar **Microsoft.Azure.ServiceBus.EventProcessorHost**, clique em **instalar**e aceite os termos de utilização. Esta operação transfere, instalações e adiciona uma referência do [Concentrador de eventos do Azure Service Bus – EventProcessorHost NuGet pacote](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), com as respectivas dependências.

5. Com o botão direito do projecto **ProcessDeviceToCloudMessages** , clique em **Adicionar**e, em seguida, clique em **classe**. Atribua um nome da nova classe **StoreEventProcessor**e, em seguida, clique em **OK** para criar a classe.

6. Adicione as seguintes instruções na parte superior do ficheiro StoreEventProcessor.cs:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Substitua o código seguinte para o corpo a classe de:

    ```
    class StoreEventProcessor : IEventProcessor
    {
      private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
      public static string StorageConnectionString;
      public static string ServiceBusConnectionString;

      private CloudBlobClient blobClient;
      private CloudBlobContainer blobContainer;
      private QueueClient queueClient;

      private long currentBlockInitOffset;
      private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

      private Stopwatch stopwatch;
      private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

      public StoreEventProcessor()
      {
        var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
        blobClient = storageAccount.CreateCloudBlobClient();
        blobContainer = blobClient.GetContainerReference("d2ctutorial");
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString);
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

        if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
        {
          currentBlockInitOffset = 0;
        }
        stopwatch = new Stopwatch();
        stopwatch.Start();

        return Task.FromResult<object>(null);
      }

      async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
      {
        foreach (EventData eventData in messages)
        {
          byte[] data = eventData.GetBytes();

          if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
          {
            var messageId = (string) eventData.SystemProperties["message-id"];

            var queueMessage = new BrokeredMessage(new MemoryStream(data));
            queueMessage.MessageId = messageId;
            queueMessage.Properties["messageType"] = "interactive";
            await queueClient.SendAsync(queueMessage);

            WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
            continue;
          }

          if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
          {
            await AppendAndCheckpoint(context);
          }
          await toAppend.WriteAsync(data, 0, data.Length);

          Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
            context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
        }
      }

      private async Task AppendAndCheckpoint(PartitionContext context)
      {
        var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
        var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
        toAppend.Seek(0, SeekOrigin.Begin);
        byte[] md5 = MD5.Create().ComputeHash(toAppend);
        toAppend.Seek(0, SeekOrigin.Begin);

        var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
        var currentBlob = blobContainer.GetBlockBlobReference(blobName);

        if (await currentBlob.ExistsAsync())
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var blockList = await currentBlob.DownloadBlockListAsync();
          var newBlockList = new List<string>(blockList.Select(b => b.Name));

          if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
          {
            newBlockList.Add(blockId);
            WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
          }
          else
          {
            WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
          }
          await currentBlob.PutBlockListAsync(newBlockList);
        }
        else
        {
          await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
          var newBlockList = new List<string>();
          newBlockList.Add(blockId);
          await currentBlob.PutBlockListAsync(newBlockList);

          WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
        }

        toAppend.Dispose();
        toAppend = new MemoryStream(MAX_BLOCK_SIZE);

        // checkpoint.
        await context.CheckpointAsync();
        WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

        currentBlockInitOffset = long.Parse(context.Lease.Offset);
        stopwatch.Restart();
      }

      private void WriteHighlightedMessage(string message)
      {
        Console.ForegroundColor = ConsoleColor.Yellow;
        Console.WriteLine(message);
        Console.ResetColor();
      }
    }
    ```

    A classe de **EventProcessorHost** chamadas esta classe para processar as mensagens de dispositivo para nuvem recebidas a partir do IoT Hub. O código nesta classe implementa a lógica para armazenar as mensagens de forma fiável num contentor de BLOBs e reencaminhar mensagens interativas para a fila Bus de serviço.

    O método **OpenAsync** inicia a variável de **currentBlockInitOffset** , que controla o deslocamento atual da primeira mensagem, este processador de eventos de leitura. Lembre-se de que cada processador é responsável por uma única partição.

    O método de **ProcessEventsAsync** recebe um lote das mensagens a partir do IoT Hub e processa-los da seguinte forma: envia mensagens interativas para a fila de serviço Bus e acrescenta mensagens de ponto de dados para a memória intermédia denominada **toAppend**. Se a memória intermédia atinge o limite de 4 MB ou as janelas de tempo a duplicação decorrida (uma hora após um ponto de verificação neste tutorial), em seguida, a aplicação de accionadores um ponto de verificação.

    O método de **AppendAndCheckpoint** pela primeira vez gera um blockId para o bloco acrescentar. Armazenamento Azure requer bloquear todos os IDs de ter o mesmo comprimento para que o método pads o desvio com zeros à esquerda - `currentBlockInitOffset.ToString("0000000000000000000000000")`. Em seguida, se um bloco com este ID já estiver a ser o blob, o método substitui-la com os conteúdos da memória intermédia atuais.

    > [AZURE.NOTE] Para simplificar o código, este tutorial, utiliza um único blob por partição para armazenar as mensagens. Uma solução real seria implementar gradual através da criação de ficheiros adicionais após um determinado período de tempo ou quando chegar a um determinado tamanho de ficheiro. Lembre-se de que uma BLOBs do Azure bloco podem conter no máximo 195 GB de dados.

8. Na classe de **programa** , adicione a seguinte instrução **utilizando** na parte superior:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Modificar o método **principais** da turma de **programa** da seguinte forma. Substitua a cadeia de ligação **iothubowner** a partir do tutorial de [Introdução ao centro de IoT] **{cadeia de ligação de concentrador iot}** . Substitua a cadeia de ligação de armazenamento a cadeia de ligação que anotou no início nesta secção. Substitua a cadeia de ligação de serviço Bus com permissões de **Enviar** para a fila denominada **d2ctutorial** que anotou no início nesta secção:

    ```
    static void Main(string[] args)
    {
      string iotHubConnectionString = "{iot hub connection string}";
      string iotHubD2cEndpoint = "messages/events";
      StoreEventProcessor.StorageConnectionString = "{storage connection string}";
      StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
    ```

    > [AZURE.NOTE] Por razões de simplificar, este tutorial utiliza uma única ocorrência de classe [EventProcessorHost] . Para obter mais informações, consulte o [Guia de programação de concentradores do evento].

## <a name="receive-interactive-messages"></a>Receber mensagens interativas
Nesta secção, escreva uma aplicação de consola do Windows que recebe as mensagens interativas de fila de espera Bus de serviço. Para mais informações sobre como Arquitectar uma solução utilizando Bus de serviço, consulte o artigo [criar aplicações de várias camadas com o serviço Bus][].

1. Na solução do Visual Studio atual, crie um projeto do Visual c# Windows utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **ProcessD2CInteractiveMessages**.

2. No Explorador de solução, com o botão direito do projecto **ProcessD2CInteractiveMessages** e, em seguida, clique em **Gerir pacotes de NuGet**. Esta operação apresenta a janela do **Gestor de pacote NuGet** .

3. Procurar **WindowsAzure.ServiceBus**, clique em **instalar**e aceite os termos de utilização. Esta operação transfere, instalações e adiciona uma referência para o [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus), com as respectivas dependências.

4. Adicione as seguintes instruções de **utilizar** na parte superior do ficheiro **Program.cs** :

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Por fim, adicione as seguintes linhas para o método **principal** . Substitua a cadeia de ligação com **ouvir** permissões para a fila denominada **d2ctutorial**:

    ```
    Console.WriteLine("Process D2C Interactive Messages app\n");

    string connectionString = "{service bus listen connection string}";
    QueueClient Client = QueueClient.CreateFromConnectionString(connectionString);

    OnMessageOptions options = new OnMessageOptions();
    options.AutoComplete = false;
    options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

    Client.OnMessage((message) =>
    {
      try
      {
        var bodyStream = message.GetBody<Stream>();
        bodyStream.Position = 0;
        var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

        Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

        message.Complete();
      }
      catch (Exception)
      {
        message.Abandon();
      }
    }, options);

    Console.WriteLine("Receiving interactive messages from SB queue...");
    Console.WriteLine("Press any key to exit.");
    Console.ReadLine();
    ```

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  No Visual Studio, no Explorador de solução, a solução com o botão direito e selecione **Configurar projetos de arranque**. Selecione **vários projetos de arranque**e, em seguida, selecione **Iniciar** como a ação para os projectos **ProcessDeviceToCloudMessages**, **SimulatedDevice**e **ProcessD2CInteractiveMessages** .

2.  Prima **F5** para iniciar as aplicações de três consola. A aplicação **ProcessD2CInteractiveMessages** deve processar todas as mensagens interativas enviadas a partir da aplicação **SimulatedDevice** .

  ![Aplicações de consola de três][50]

> [AZURE.NOTE] Para ver as atualizações no seu blob, poderá ter de reduzir a constante **MAX_BLOCK_SIZE** na classe **StoreEventProcessor** para um valor mais pequeno, tal como **1024**. Esta alteração é útil porque demora algum tempo a chegar o limite de tamanho de bloco com os dados que foi enviados pelo dispositivo simulado. Com um tamanho mais pequeno bloco, pode não ter de esperar tanto tempo para ver o blob a ser criados e actualizados. No entanto, utilizar um tamanho de bloco maior faz com que a aplicação mais dimensionáveis.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como forma fiável processar ponto de dados e mensagens de dispositivo para nuvem interativas utilizando a classe de [EventProcessorHost] .

[Como enviar mensagens de dispositivo de nuvem com IoT concentrador] [ lnk-c2d] mostra-lhe como enviar mensagens para os seus dispositivos a partir do seu back-end.

Para ver exemplos de soluções de ponto a ponto completas que utilizam IoT concentrador, consulte [o conjunto de aplicações do Azure IoT][lnk-suite].

Para saber mais sobre como desenvolver soluções com IoT concentrador, consulte o [Guia de programador do IoT concentrador].

<!-- Images. -->
[50]: ./media/iot-hub-csharp-csharp-process-d2c/run1.png
[10]: ./media/iot-hub-csharp-csharp-process-d2c/create-identity-csharp1.png

[30]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue2.png
[31]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue3.png
[32]: ./media/iot-hub-csharp-csharp-process-d2c/createqueue4.png

<!-- Links -->

[Armazenamento de Blobs do Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Dados Azure fábrica]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Fila de Bus de serviço]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guia do programador IoT concentrador Azure - dispositivo para o cloud]: iot-hub-devguide-messaging.md

[Armazenamento Azure]: https://azure.microsoft.com/documentation/services/storage/
[Serviço Azure Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do Programador de IoT concentrador]: iot-hub-devguide.md
[Introdução ao centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro de programadores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Sobre o armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introdução ao evento concentradores]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[Escalabilidade do armazenamento Azure diretrizes]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Guia de programação de concentradores de evento]: ../event-hubs/event-hubs-programming-guide.md
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Criar aplicações de várias camadas com o serviço Bus]: ../service-bus-messaging/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-csharp-csharp-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/
