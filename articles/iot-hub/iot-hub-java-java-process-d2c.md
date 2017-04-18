<properties
    pageTitle="Processar as mensagens de dispositivo para nuvem IoT concentrador (Java) | Microsoft Azure"
    description="Siga este tutorial Java para saber mais útil padrões para processar IoT concentrador dispositivo para nuvem mensagens."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/01/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-process-iot-hub-device-to-cloud-messages-using-java"></a>Tutorial: Como processar IoT concentrador dispositivo para nuvem mensagens utilizando Java

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

No final deste tutorial, executar três Java consola aplicações:

* **dispositivo simulado**, uma versão modificada da aplicação que criou no tutorial [Introdução ao centro de IoT] , envia mensagens de dispositivo para nuvem do ponto de dados todas as mensagens de dispositivo para nuvem segunda e interativos cada 10 segundos. Esta aplicação utiliza o protocolo AMQP para comunicar com IoT concentrador.
* **mensagens de processo d2c** utiliza a classe de [EventProcessorHost] para obter as mensagens a partir do ponto final compatível com o evento concentrador. -Lo, em seguida, sujeito armazena as mensagens de ponto de dados no armazenamento de Blobs do Azure e reencaminhe mensagens interativas para uma fila Bus de serviço.
* **processo interativos mensagens** filas anular as mensagens da fila de serviço Bus interativas.

> [AZURE.NOTE] Concentrador IoT tem suporte SDK para muitos plataformas de dispositivos e idiomas, incluindo C, Java e JavaScript. Para obter instruções sobre como substituir o dispositivo simulado neste tutorial com um dispositivo físico e saber como ligar dispositivos a um concentrador de IoT, consulte o artigo do [Centro de programadores do Azure IoT].

Neste tutorial é diretamente aplicável a outras formas de consumir mensagens compatível com o evento concentrador, como projetos [HDInsight (Hadoop)] . Para mais informações, consulte o artigo [Guia de programador do Azure IoT concentrador - dispositivo para na nuvem].

Para concluir este tutorial, precisa do seguinte:

+ Uma versão de trabalho completa do tutorial [Introdução ao IoT concentrador] .

+ Java SE 8. <br/> [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Java para este tutorial no Windows ou Linux.

+ Maven 3.  <br/> [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Maven para este tutorial no Windows ou Linux.

+ Uma conta do Azure active. <br/>Se não tiver uma conta, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) apenas de duas minutos.

Que deve ter alguns conhecimentos básicos de [Armazenamento do Windows Azure] e [Azure Service Bus].


## <a name="send-interactive-messages-from-a-simulated-device"></a>Enviar mensagens interativas a partir de um dispositivo simulado

Nesta secção, modifique a aplicação de dispositivo simulada que criou no tutorial [Introdução ao centro de IoT] para enviar mensagens de dispositivo para nuvem interativas para o concentrador IoT.

1. Utilize um editor de texto para abrir o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java. Este ficheiro contém o código para a aplicação de **dispositivo simulado** que criou no tutorial [Introdução ao IoT concentrador] .

2. Adicione a seguinte classe aninhada para a classe de **aplicação** :

    ```
    private static class InteractiveMessageSender implements Runnable {
      public void run() {
        try {
          while (true) {
            String msgStr = "Alert message!";
            Message msg = new Message(msgStr);
            msg.setMessageId(java.util.UUID.randomUUID().toString());
            msg.setProperty("messageType", "interactive");
            System.out.println("Sending interactive message: " + msgStr);

            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);

            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(10000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished sending interactive messages.");
        }
      }
    }
    ```

    Esta classe é semelhante à classe **MessageSender** no projeto **dispositivo simulado** . São as diferenças apenas que agora definir a propriedade de sistema **MessageId** e uma propriedade personalizada denominado **messageType**.
    O código atribui um identificador exclusivo universal (UUID) para a propriedade **MessageId** . O serviço Bus pode utilizar este identificador para duplicar anular as mensagens que recebe. O exemplo utiliza a propriedade **messageType** para distinguir interativos a partir do ponto de dados de mensagens. A aplicação transmite estas informações nas propriedades de mensagem, em vez de no corpo da mensagem, para que o processador de eventos não é necessário serialização da mensagem para executar o encaminhamento de mensagens.

    > [AZURE.NOTE] É importante criar **MessageId** utilizada para retirar duplicar interativas mensagens no código de dispositivo. Comunicações de rede intermitentes ou outras falhas, poderá resultar em várias retransmissão da mesma mensagem de que o dispositivo. Também pode utilizar um ID da mensagem semântico, tal como um hash dos campos de dados relevantes mensagem, em vez de um UUID.

3. Modificar o método **principal** para enviar ambas as mensagens interativas e ponto de dados mensagens conforme mostrado no seguinte fragmento de código:

    ````
    MessageSender sender = new MessageSender();
    InteractiveMessageSender interactiveSender = new InteractiveMessageSender();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(sender);
    executor.execute(interactiveSender);
    ````

4. Guarde e feche o ficheiro simulated-device\src\main\java\com\mycompany\app\App.java.

    > [AZURE.NOTE] Por razões de simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar uma política de repetir como duplicar exponencial, tal como sugerido no artigo MSDN [Processamento de falhas breves].

5. Para criar a aplicação de **dispositivo simulado** utilizando Maven, execute o seguinte comando na linha de comandos na pasta dispositivo simulado:

    ```
    mvn clean package -DskipTests
    ```

## <a name="process-device-to-cloud-messages"></a>Processar nuvem de dispositivo de mensagens

Nesta secção, crie uma aplicação de consola de Java processa mensagens de dispositivo para nuvem a partir do IoT Hub. Concentrador IOT expõe [concentrador de evento]-compatível com ponto final para ativar uma aplicação ler mensagens de dispositivo a nuvem. Neste tutorial utiliza a classe de [EventProcessorHost] para estas mensagens numa aplicação de consola do processo. Para mais informações sobre como mensagens de processo do evento concentradores, consulte o tutorial de [Introdução ao evento concentradores] .

O desafio principal quando implementar fiável armazenamento de mensagens do ponto de dados ou de reencaminhamento de mensagens interativas, é que o processamento de eventos depende do consumidor de mensagem para fornecer os pontos de verificação para o seu progresso. Além disso, para alcançar um débito alto, quando a ler de evento concentradores deve fornecer os pontos de verificação em lotes de grandes dimensões. Esta abordagem cria a possibilidade de processamento duplicados para um grande número de mensagens, se existir uma falha de e reverter para o ponto de verificação anterior. Neste tutorial, consulte como sincronizar escritas de armazenamento do Windows Azure e windows a duplicação de serviço Bus com **EventProcessorHost** pontos de verificação.

Para escrever mensagens de forma fiável ao armazenamento do Azure, exemplo utiliza a funcionalidade de consolidação de bloco individuais do [Bloco blobs][Azure Block Blobs]. Processador de eventos é acumulado mensagens na memória até que está na altura de fornecer um ponto de verificação. Por exemplo, depois da memória intermédia acumulada das mensagens de atinge o tamanho máximo do bloco de 4 MB ou depois do serviço Bus a duplicação decorrida janela de tempo. Em seguida, antes do ponto de verificação, o código consolida um novo bloco para o blob.

Processador de eventos utiliza concentradores evento Advance desloca a mensagem como bloco IDs. Este mecanismo permite que o processador de eventos executar uma verificação de duplicação a antes do novo bloco-consolida ao armazenamento, tendo o cuidado de uma falha de sistema possível entre cometer um bloco e o ponto de verificação.

> [AZURE.NOTE] Neste tutorial utiliza uma única conta de armazenamento do Windows Azure para escrever todas as mensagens obtidas a partir do IoT Hub. Para decidir se precisa de utilizar várias contas de armazenamento do Windows Azure na sua solução, consulte o artigo [escalabilidade de armazenamento do Windows Azure diretrizes].

A aplicação utiliza a funcionalidade da duplicação de serviço Bus para evitar duplicados quando processa mensagens interativas. O dispositivo simulado de data/nas cada mensagem interativa com um único **MessageId**. Este id Bus de serviço para se certificar de que, na janela de tempo especificado duplicação a, sem duas mensagens com o mesmo **MessageId** são entregues os destinatários. Esta a duplicação, juntamente com a semântica de conclusão por mensagem fornecida pela filas Bus de serviço, torna mais fácil implementar o processamento das mensagens interativos fiável.

Para se certificar de que nenhuma mensagem é submetida novamente fora da janela da duplicação, o código sincroniza o mecanismo de ponto de verificação **EventProcessorHost** com a janela da duplicação de fila Bus de serviço. Esta sincronização é efectuada por forçar um ponto de verificação pelo menos uma vez sempre que a janela a duplicação decorrida (neste tutorial, a janela é uma hora).

> [AZURE.NOTE] Neste tutorial, utiliza uma única fila de serviço Bus com partições processo todas as mensagens interativas obtidas a partir do IoT Hub. Para mais informações sobre como utilizar filas Bus de serviço para cumprir os requisitos de escalabilidade da sua solução, consulte a documentação do [Azure Service Bus] .

### <a name="provision-an-azure-storage-account-and-a-service-bus-queue"></a>Aprovisionar uma conta de armazenamento do Windows Azure e uma fila Bus de serviço

Para utilizar a classe de [EventProcessorHost] , tem de ter uma conta de armazenamento do Windows Azure para ativar a **EventProcessorHost** registar informações de ponto de verificação. Pode utilizar uma conta de armazenamento do Windows Azure existente ou siga as instruções [Sobre o armazenamento do Windows Azure] para criar um novo. Anote a cadeia de ligação de conta de armazenamento do Windows Azure.

> [AZURE.NOTE] Quando copiar e colar a cadeia de ligação de conta de armazenamento do Windows Azure, certifique-se de não existem sem espaços incluídos.

Também precisa de uma fila Bus de serviço para activar fiável processamento das mensagens interativos. Pode criar uma fila através de programação, com uma janela de duplicação a uma hora, tal como é explicado [como utilizar o serviço Bus filas][serviço Bus fila de espera]. Em alternativa, pode utilizar o [portal clássica Azure][lnk-classic-portal], seguindo estes passos:

1. No canto inferior esquerdo, clique em **Novo** . Em seguida, clique em **Serviços de aplicação** > **Serviço Bus** > **fila** > **Criar personalizado**. Introduza o nome **d2ctutorial**, selecione uma região e utilizar um espaço de nomes existente ou criar um novo. Tome nota do nome do espaço de nomes, precisa de-la mais tarde neste tutorial. Na página seguinte, selecione **Ativar deteção de duplicados**e definir **janela de tempo do histórico de deteção de duplicados** para uma hora. Em seguida, clique na marca de verificação no canto inferior direito para guardar a configuração de fila de espera.

    ![Criar uma fila no portal do Azure][30]

2. Na lista de filas Bus de serviço, clique em **d2ctutorial**e, em seguida, clique em **Configurar**. Crie duas políticas de acesso partilhada, uma chamada **Enviar** com permissões de **Enviar** e uma chamada de **ouvir** com permissões **ouvir** . Tome nota dos valores de **chave primária** de ambas as políticas, precisa de-las mais tarde neste tutorial. Quando tiver terminado, clique em **Guardar** na parte inferior.

    ![Configurar uma fila no portal do Azure][31]

### <a name="create-the-event-processor"></a>Criar o processador de eventos

Nesta secção, criar uma aplicação de Java para processar mensagens a partir do ponto final compatível com o evento concentrador.

É a primeira tarefa adicionar um projeto de Maven designado por **mensagens de d2c processo** que recebe mensagens de nuvem de dispositivo a partir do ponto final compatível com o evento de concentrador IoT concentrador e encaminha essas mensagens para outros serviços de back-end.

1. Na pasta iniciado de iot java get que criou no tutorial [Introdução ao centro de IoT] , crie um projeto de Maven denominado **mensagens de processo d2c** utilizando o seguinte comando na sua linha de comandos. Nota: Este é um comando único e extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de mensagens de d2c processo.

3. Utilizando um editor de texto, abra o ficheiro de pom.xml na pasta mensagens de d2c processo e adicione as seguintes dependências ao nó **dependências** . Estas dependências permitem-lhe utilizar o azure eventhubs, azure-eventhubs-eph e azure servicebus pacotes na sua aplicação para interagir com o seu IoT concentrador e fila Bus de serviço:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-eventhubs-eph</artifactId>
      <version>0.8.0</version>
    </dependency>
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Guarde e feche o ficheiro pom.xml.

A tarefa seguinte é adicionar uma classe **ErrorNotificationHandler** ao projeto.

1. Utilize um editor de texto para criar um ficheiro de process-d2c-messages\src\main\java\com\mycompany\app\ErrorNotificationHandler.java. Adicione o seguinte código para o ficheiro para apresentar as mensagens de erro da instância **EventProcesssorHost** :

    ```
    package com.mycompany.app;

    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements
        Consumer<ExceptionReceivedEventArgs> {
      @Override
      public void accept(ExceptionReceivedEventArgs t) {
        System.out.println("EventProcessorHost: Host " + t.getHostname()
            + " received general error notification during " + t.getAction() + ": "
            + t.getException().toString());
      }
    }
    ```

2. Guarde e feche o ficheiro ErrorNotificationHandler.java.

Agora pode adicionar uma classe que implementa a interface de **IEventProcessor** . A classe de **EventProcessorHost** chamadas esta classe para processar as mensagens de dispositivo para nuvem recebidas a partir do IoT Hub. O código nesta classe implementa a lógica para armazenar as mensagens de forma fiável num contentor de BLOBs e reencaminhar mensagens interativas para a fila Bus de serviço.

O método **onEvents** define a variável de **latestEventData** que controla o número de deslocamento e a sequência da mensagem mais recente de leitura este processador de eventos. Lembre-se de que cada processador é responsável por uma única partição. O método de **onEvents** , em seguida, recebe um lote das mensagens a partir do IoT Hub e processos-los da seguinte forma: envia mensagens interativas para a fila de serviço Bus e acrescenta mensagens de ponto de dados para a memória intermédia **toAppend** . Se a memória intermédia atinge o limite do bloco de 4 MB ou as janelas de tempo a duplicação decorrida (uma hora após o último ponto de verificação neste tutorial), o método accionadores um ponto de verificação.

O método de **AppendAndCheckPoint** pela primeira vez gera um **blockId** para o bloco acrescentar para o blob. Armazenamento Azure requer que bloquear todos os IDs de ter o mesmo comprimento para que o método pads o desvio com zeros à esquerda. Em seguida, se um bloco com este ID já estiver a ser o blob, o método substitui-o conteúdo de intervalo de tempo atual.

> [AZURE.NOTE] Para simplificar o código, este tutorial, utiliza um único blob por partição para armazenar as mensagens. Uma solução real seria implementar gradual através da criação de ficheiros adicionais após um determinado período de tempo ou quando chegar a um determinado tamanho de ficheiro. Lembre-se de que uma BLOBs do Azure bloco podem conter no máximo 195 GB de dados.

A tarefa seguinte é implementar a interface de **IEventProcessor** :

1. Utilize um editor de texto para criar um ficheiro de process-d2c-messages\src\main\java\com\mycompany\app\EventProcessor.java.

2. Adicione os seguintes importações e definição de classe para o ficheiro EventProcessor.java. A classe de **EventProcessor** implementa a interface de **IEventProcessor** que define o comportamento de cliente do evento concentradores:

    ```
    package com.mycompany.app;

    import java.io.ByteArrayInputStream;
    import java.io.ByteArrayOutputStream;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.nio.charset.StandardCharsets;
    import java.time.Duration;
    import java.time.Instant;
    import java.util.ArrayList;
    import java.util.Base64;
    import java.util.concurrent.ExecutionException;

    import com.microsoft.azure.eventhubs.EventData;
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.storage.*;
    import com.microsoft.azure.storage.blob.*;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.BrokeredMessage;

    public class EventProcessor implements IEventProcessor {

    }
    ```

3. Adicione os seguintes métodos para a classe de **EventProcessor** para implementar a interface de **IEventProcessor** :

    ```
    @Override
    public void onOpen(PartitionContext context) throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is opening");
    }

    @Override
    public void onClose(PartitionContext context, CloseReason reason)
        throws Exception {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " is closing for reason "
          + reason.toString());
    }

    @Override
    public void onError(PartitionContext context, Throwable error) {
      System.out.println("EventProcessorHost: Partition "
          + context.getPartitionId() + " onError: " + error.toString());
    }

    @Override
    public void onEvents(PartitionContext context, Iterable<EventData> messages)
        throws Exception {
    }
    ```

4. Adicione as seguintes variáveis de nível de classe à classe **EventProcessor** :

    ```
    public static CloudBlobContainer blobContainer;
    public static ServiceBusContract serviceBusContract;

    // Use a smaller MAX_BLOCK_SIZE value to test.
    final private int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
    final private Duration MAX_CHECKPOINT_TIME = Duration.ofHours(1);

    private ByteArrayOutputStream toAppend = new ByteArrayOutputStream(
        MAX_BLOCK_SIZE);
    private Instant start = Instant.now();
    private EventData latestEventData;
    ```

5. Adicione um método de **AppendAndCheckPoint** com a seguinte assinatura para a classe de **EventProcessor** :

    ```
    private void AppendAndCheckPoint(PartitionContext context)
      throws URISyntaxException, StorageException, IOException,
      IllegalArgumentException, InterruptedException, ExecutionException {
    }
    ```

6. Adicione o seguinte código para o método de **AppendAndCheckPoint** para obter a mensagem deslocamento e a sequência número atual na partição:

    ```
    String currentOffset = latestEventData.getSystemProperties().getOffset();
    Long currentSequence = latestEventData.getSystemProperties().getSequenceNumber();
    System.out
        .printf(
            "\nAppendAndCheckPoint using partition: %s, offset: %s, sequence: %s\n",
            context.getPartitionId(), currentOffset, currentSequence);
    ```

7. Método **AppendAndCheckPoint** , utilize o valor atual de deslocamento para criar uma instância de **BlockEntry** para o bloco seguinte guardar o blob:

    ```
    Long blockId = Long.parseLong(currentOffset);
    String blockIdString = String.format("startSeq:%1$025d", blockId);
    String encodedBlockId = Base64.getEncoder().encodeToString(
        blockIdString.getBytes(StandardCharsets.US_ASCII));
    BlockEntry block = new BlockEntry(encodedBlockId);
    ```

8. Método de **AppendAndCheckPoint** , carregar o conjunto de mensagens mais recente para o blob bloco e obter a lista atual de blocos de:

    ```
    String blobName = String.format("iothubd2c_%s", context.getPartitionId());
    CloudBlockBlob currentBlob = blobContainer.getBlockBlobReference(blobName);

    currentBlob.uploadBlock(block.getId(),
        new ByteArrayInputStream(toAppend.toByteArray()), toAppend.size());
    ArrayList<BlockEntry> blockList = currentBlob.downloadBlockList();
    ```

9. Método de **AppendAndCheckPoint** , criar o bloco inicial num novo blob ou acrescentar o bloco para o blob existente:

    ```
    if (currentBlob.exists()) {
      // Check if we should append new block or overwrite existing block
      BlockEntry last = blockList.get(blockList.size() - 1);
      if (blockList.size() > 0 && !last.getId().equals(block.getId())) {
        System.out.printf("Appending block %s to blob %s\n", blockId, blobName);
        blockList.add(block);
      } else {
        System.out.printf("Overwriting block %s in blob %s\n", blockId,
            blobName);
      }
    } else {
      System.out.printf("Creating initial block %s in new blob: %s\n", blockId,
          blobName);
      blockList.add(block);
    }
    currentBlob.commitBlockList(blockList);
    ```

10. Por fim no método de **AppendAndCheckPoint** , crie um ponto de verificação na partição e preparar para guardar o bloco de mensagens seguinte:

    ```
    context.checkpoint(latestEventData);

    // Reset everything after the checkpoint.
    toAppend.reset();
    start = Instant.now();
    System.out.printf("Checkpointed on partition id: %s\n",
        context.getPartitionId());
    ```

11. Método de **onEvents** , adicione o seguinte código para receber mensagens do ponto final de IoT concentrador e reencaminhar mensagens interativas para a fila Bus de serviço. Em seguida, chame o método de **AppendAndCheckPoint** quando o bloco de é completo ou o tempo limite expira:

    ```
    if (messages != null) {
      for (EventData eventData : messages) {
        latestEventData = eventData;
        byte[] data = eventData.getBody();
        if (eventData.getProperties().containsKey("messageType")
            && eventData.getProperties().get("messageType")
                .equals("interactive")) {
          String messageId = (String) eventData.getSystemProperties().get(
              "message-id");
          BrokeredMessage message = new BrokeredMessage(data);
          message.setMessageId(messageId);
          serviceBusContract.sendQueueMessage("d2ctutorial", message);
          continue;
        }
        if (toAppend.size() + data.length > MAX_BLOCK_SIZE
            || Duration.between(start, Instant.now()).compareTo(
                MAX_CHECKPOINT_TIME) > 0) {
          AppendAndCheckPoint(context);
        }
        toAppend.write(data);
      }
    }
    ```

12. Por fim do método **onEvents** , adicione uma cláusula 'else if' para ligar a **AppendAndCheckPoint** se o tempo limite expira enquanto não existem mensagens provenientes do concentrador de IoT para:

    ```
    else if ((toAppend.size() > 0)
        && Duration.between(start, Instant.now())
            .compareTo(MAX_CHECKPOINT_TIME) > 0) {
      AppendAndCheckPoint(context);
    }
    ```

13. Guarde as alterações ao ficheiro EventProcessor.java.

A tarefa no projeto **mensagens de processo d2c** final é adicionar código para o método **principal** que iniciar a instância de uma instância de **EventProcessorHost** .

1. Utilize um editor de texto para abrir o ficheiro process-d2c-messages\src\main\java\com\mycompany\app\App.java.

2. Adicione as seguintes instruções de **Importar** o ficheiro:

    ```
    import com.microsoft.azure.eventprocessorhost.*;
    import com.microsoft.azure.servicebus.ConnectionStringBuilder;
    import com.microsoft.azure.storage.CloudStorageAccount;
    import com.microsoft.azure.storage.StorageException;
    import com.microsoft.azure.storage.blob.CloudBlobClient;
    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusConfiguration;
    import com.microsoft.windowsazure.services.servicebus.ServiceBusService;

    import java.net.URISyntaxException;
    import java.security.InvalidKeyException;
    import java.util.concurrent.*;
    ```

3. Adicione a variável de nível de classe seguinte para a classe de **aplicação** . Substitua **{yourstorageaccountconnectionstring}** com a cadeia de ligação de conta de armazenamento do Windows Azure que efetuou uma nota de anteriormente na secção de [aprovisionamento de uma conta de armazenamento do Windows Azure e uma fila Bus de serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String storageConnectionString = "{yourstorageaccountconnectionstring}";
    ```

4. Adicione as seguintes variáveis de nível de classe para a classe de **aplicação** e substituir **{yourservicebusnamespace}** com o seu espaço de nomes de serviço Bus e **{yourservicebussendkey}** **Enviar** chave na fila. Que efetuou anteriormente uma nota de espaço de nomes e **ouvir** valores chaves na secção de [aprovisionamento de uma conta de armazenamento do Windows Azure e uma fila Bus de serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "send";
    private final static String serviceBusSASKey = "{yourservicebussendkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    ```

5. Adicione as seguintes variáveis de nível de classe para a classe de **aplicação** . Substitua o valor de ponto final compatível com o evento concentrador **{youreventhubcompatibleendpoint}** . O valor de ponto final aspeto **seu … espaço de nomes** , de modo que deve remover o **sb: / /** prefixo e sufixo **.servicebus.windows.net/** . Substitua **{youreventhubcompatiblename}** com o nome de compatível com o evento concentrador. Substitua a chave de **iothubowner** **{youriothubkey}** . Uma nota destes valores que efetuou para [criar um concentrador de IoT] [ lnk-create-an-iot-hub] secção no tutorial *Introdução ao Azure IoT concentrador para Java* :

    ```
    private final static String consumerGroupName = "$Default";
    private final static String namespaceName = "{youreventhubcompatibleendpoint}";
    private final static String eventHubName = "{youreventhubcompatiblename}";
    private final static String sasKeyName = "iothubowner";
    private final static String sasKey = "{youriothubkey}";
    ```

6. Modificar a assinatura do método **principal** da seguinte forma:

    ```
    public static void main(String args[]) throws InvalidKeyException,
      URISyntaxException, StorageException {
    }
    ```

7. Adicione o seguinte código para o método **principal** para obter uma referência para o contentor de BLOBs que armazena as mensagens:

    ```
    System.out.println("Process D2C messages using EventProcessorHost");
    CloudStorageAccount account = CloudStorageAccount
        .parse(storageConnectionString);
    CloudBlobClient client = account.createCloudBlobClient();
    EventProcessor.blobContainer = client
        .getContainerReference("d2cjavatutorial");
    EventProcessor.blobContainer.createIfNotExists();
    ```

8. Adicione o seguinte código para o método **principal** para obter uma referência para o serviço do serviço Bus:

    ```
    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    EventProcessor.serviceBusContract = ServiceBusService.create(config);
    ```

9. O método **principal** , configurar e cria uma instância de **EventProcessorHost** . A opção de **setInvokeProcessorAfterReceiveTimeout** garante a instância **EventProcessorHost** invoca o método de **onEvents** na interface de **IEventProcessor** , mesmo quando não existem mensagens para processar. O método de **onEvents** sempre a invocar, em seguida, o método **AppendAndCheckPoint** quando o tempo limite expira.

    ```
    ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder(
        namespaceName, eventHubName, sasKeyName, sasKey);
    EventProcessorHost host = new EventProcessorHost(eventHubName,
        consumerGroupName, eventHubConnectionString.toString(),
        storageConnectionString);
    EventProcessorOptions options = new EventProcessorOptions();
    options.setExceptionNotification(new ErrorNotificationHandler());
    options.setInvokeProcessorAfterReceiveTimeout(true);
    ```

10. Método de **principal** , registe-se a implementação de **IEventProcessor** com a instância **EventProcessorHost** :

    ```
    try {
      System.out.println("Registering host named " + host.getHostName());
      host.registerEventProcessor(EventProcessor.class, options).get();
    } catch (Exception e) {
      System.out.print("Failure while registering: ");
      if (e instanceof ExecutionException) {
        Throwable inner = e.getCause();
        System.out.println(inner.toString());
      } else {
        System.out.println(e.toString());
      }
      System.out.println(e.toString());
    }
    ```

11. Por fim, adicione lógica para o método **principal** para encerrar a instância **EventProcessorHost** :

    ```
    System.out.println("Press enter to stop");
    try {
      System.in.read();
      host.unregisterEventProcessor();

      System.out.println("Calling forceExecutorShutdown");
      EventProcessorHost.forceExecutorShutdown(120);
    } catch (Exception e) {
      System.out.println(e.toString());
      e.printStackTrace();
    }

    System.out.println("End of sample");
    ```

12. Guarde e feche o ficheiro process-d2c-messages\src\main\java\com\mycompany\app\App.java.

13. Para criar a aplicação de **mensagens de processo d2c** utilizando Maven, execute o seguinte comando na linha de comandos na pasta mensagens de d2c processo:

    ```
    mvn clean package -DskipTests
    ```

## <a name="receive-interactive-messages"></a>Receber mensagens interativas

Nesta secção, escreva uma aplicação de consola Java que recebe as mensagens interativas de fila de espera Bus de serviço.

É a primeira tarefa adicionar um projeto de Maven denominado **processo interativos mensagens** que recebe as mensagens enviadas na fila de Bus de serviço a partir das instâncias de **EventProcessor** .

1. Na pasta iniciado de iot java get que criou no tutorial [Introdução ao centro de IoT] , crie um projeto de Maven denominado **processo mensagens interativos** utilizando o seguinte comando na sua linha de comandos. Nota: Este é um comando único e extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=process-interactive-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de processo mensagens interativos.

3. Utilizando um editor de texto, abra o ficheiro pom.xml na pasta processo interativos mensagens e adicione a dependência seguinte para o nó **dependências** . Esta dependência permite-lhe utilizar o pacote do azure servicebus na sua aplicação para interagir com a sua fila Bus de serviço:

    ```
    <dependency>
      <groupId>com.microsoft.azure</groupId>
      <artifactId>azure-servicebus</artifactId>
      <version>0.9.4</version>
    </dependency>
    ```

4. Guarde e feche o ficheiro pom.xml.

A tarefa seguinte é adicionar código para obter as mensagens da fila Bus de serviço.

1. Utilize um editor de texto para abrir o ficheiro process-interactive-messages\src\main\java\com\mycompany\app\App.java.

2. Adicione o seguinte `import` declarações para o ficheiro:

    ```
    import java.io.IOException;
    import java.util.concurrent.ExecutorService;
    import java.util.concurrent.Executors;

    import com.microsoft.windowsazure.Configuration;
    import com.microsoft.windowsazure.exception.ServiceException;
    import com.microsoft.windowsazure.services.servicebus.*;
    import com.microsoft.windowsazure.services.servicebus.models.*;
    ```

3. Adicione as seguintes variáveis de nível de classe para a classe de **aplicação** e substituir **{yourservicebusnamespace}** com o seu espaço de nomes de serviço Bus e **{yourservicebuslistenkey}** **ouvir** chave na fila. Que efetuou anteriormente uma nota de espaço de nomes e **ouvir** valores chaves na secção de [aprovisionamento de uma conta de armazenamento do Windows Azure e uma fila Bus de serviço](#provision-an-azure-storage-account-and-a-service-bus-queue) :

    ```
    private final static String serviceBusNamespace = "{yourservicebusnamespace}";
    private final static String serviceBusSasKeyName = "listen";
    private final static String serviceBusSASKey = "{yourservicebuslistenkey}";
    private final static String serviceBusRootUri = ".servicebus.windows.net";
    private final static String queueName = "d2ctutorial";
    private static ServiceBusContract service = null;
    ```

4. Adicione a seguinte classe aninhada para a classe de **aplicação** a receber mensagens de fila de espera:

    ```
    private static class MessageReceiver implements Runnable {
      public void run() {
        ReceiveMessageOptions opts = ReceiveMessageOptions.DEFAULT;
        try {
          while (true) {
            ReceiveQueueMessageResult resultQM = service.receiveQueueMessage(
                queueName, opts);
            BrokeredMessage message = resultQM.getValue();
            if (message != null && message.getMessageId() != null) {
              System.out.println("MessageID: " + message.getMessageId());
              System.out.print("From queue: ");
              byte[] b = new byte[200];
              String s = null;
              int numRead = message.getBody().read(b);
              while (-1 != numRead) {
                s = new String(b);
                s = s.trim();
                System.out.print(s);
                numRead = message.getBody().read(b);
              }
              System.out.println();
            } else {
              Thread.sleep(1000);
            }
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        } catch (ServiceException e) {
          System.out.println("ServiceException: " + e.getMessage());
        } catch (IOException e) {
          System.out.println("IOException: " + e.getMessage());
        }
      }
    }
    ```

5. Modificar a assinatura do método **principal** da seguinte forma:

    ```
    public static void main(String args[]) throws ServiceException, IOException {
    }
    ```

6. Método de **principal** , adicione o código seguinte para começar a escutar para novas mensagens:

    ```
    System.out.println("Process interactive messages");

    Configuration config = ServiceBusConfiguration
        .configureWithSASAuthentication(serviceBusNamespace,
            serviceBusSasKeyName, serviceBusSASKey, serviceBusRootUri);
    service = ServiceBusService.create(config);

    MessageReceiver receiver = new MessageReceiver();

    ExecutorService executor = Executors.newFixedThreadPool(2);
    executor.execute(receiver);

    System.out.println("Press ENTER to exit.");
    System.in.read();
    executor.shutdownNow();
    ```

7. Guarde e feche o ficheiro process-interactive-messages\src\main\java\com\mycompany\app\App.java.

8. Para criar a aplicação de **processo mensagens interativos** utilizando Maven, execute o seguinte comando na linha de comandos na pasta processo interativos mensagens:

    ```
    mvn clean package -DskipTests
    ```

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as três aplicações.

1.  Para executar a aplicação de **processo interativos mensagens** , numa linha de comandos ou shell, navegue para a pasta de processo interativos mensagens e execute o seguinte comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o processo de mensagens interativos][processinteractive]

2.  Para executar a aplicação de **mensagens de d2c processo** , numa linha de comandos ou shell, navegue para a pasta de mensagens de d2c processo e execute o seguinte comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar a mensagens de d2c de processo][processd2c]

3.  Para executar a aplicação de **dispositivo simulado** , numa linha de comandos ou shell, navegue para a pasta de dispositivo simulado e execute o seguinte comando:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Executar o dispositivo simulado][simulateddevice]

> [AZURE.NOTE] Para ver as atualizações no seu blob, poderá ter de reduzir a constante **MAX_BLOCK_SIZE** na classe **StoreEventProcessor** para um valor mais pequeno, tal como **1024**. Esta alteração é útil porque demora algum tempo a chegar o limite de tamanho de bloco com os dados que foi enviados pelo dispositivo simulado. Com um tamanho mais pequeno bloco, pode não ter de esperar tanto tempo para ver o blob a ser criados e actualizados. No entanto, utilizar um tamanho de bloco maior faz com que a aplicação mais dimensionáveis.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como sujeito processar ponto de dados e mensagens de dispositivo para nuvem interativas utilizando a classe de [EventProcessorHost] .

[Como enviar mensagens de dispositivo de nuvem com IoT concentrador] [ lnk-c2d] mostra-lhe como enviar mensagens para os seus dispositivos a partir do seu back-end.

Para ver exemplos de soluções de ponto a ponto completas que utilizam IoT concentrador, consulte [o conjunto de aplicações do Azure IoT][lnk-suite].

Para saber mais sobre como desenvolver soluções com IoT concentrador, consulte o [Guia de programador do IoT concentrador].

<!-- Images. -->
[simulateddevice]: ./media/iot-hub-java-java-process-d2c/runsimulateddevice.png
[processinteractive]: ./media/iot-hub-java-java-process-d2c/runprocessinteractive.png
[processd2c]: ./media/iot-hub-java-java-process-d2c/runprocessd2c.png

[30]: ./media/iot-hub-java-java-process-d2c/createqueue2.png
[31]: ./media/iot-hub-java-java-process-d2c/createqueue3.png

<!-- Links -->

[Armazenamento de Blobs do Azure]: ../storage/storage-dotnet-how-to-use-blobs.md
[Dados Azure fábrica]: https://azure.microsoft.com/documentation/services/data-factory/
[HDInsight (Hadoop)]: https://azure.microsoft.com/documentation/services/hdinsight/
[Fila de Bus de serviço]: ../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md

[Guia do programador IoT concentrador Azure - dispositivo para o cloud]: iot-hub-devguide-messaging.md

[Armazenamento Azure]: https://azure.microsoft.com/documentation/services/storage/
[Serviço Azure Bus]: https://azure.microsoft.com/documentation/services/service-bus/

[Guia do Programador de IoT concentrador]: iot-hub-devguide.md
[Introdução ao centro de IoT]: iot-hub-java-java-getstarted.md
[Centro de programadores do Azure IoT]: https://azure.microsoft.com/develop/iot
[lnk-service-fabric]: https://azure.microsoft.com/documentation/services/service-fabric/
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-hubs]: https://azure.microsoft.com/documentation/services/event-hubs/
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh675232.aspx

<!-- Links -->
[Sobre o armazenamento do Azure]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Introdução ao evento concentradores]: ../event-hubs/event-hubs-java-ephjava-getstarted.md
[Escalabilidade do armazenamento Azure diretrizes]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[EventProcessorHost]: https://github.com/Azure/azure-event-hubs/tree/master/java/azure-eventhubs-eph
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-classic-portal]: https://manage.windowsazure.com
[lnk-c2d]: iot-hub-java-java-process-d2c.md
[lnk-suite]: https://azure.microsoft.com/documentation/suites/iot-suite/

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-create-an-iot-hub]: iot-hub-java-java-getstarted.md#create-an-iot-hub