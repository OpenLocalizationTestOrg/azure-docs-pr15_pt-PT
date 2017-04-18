<properties
    pageTitle="Azure IoT concentrador para Java introdução | Microsoft Azure"
    description="Azure IoT concentrador com Java introdução de iniciação. Utilize Azure IoT concentrador e Java com o SDK do Microsoft Azure IoT para implementar uma solução de Internet de coisas."
    services="iot-hub"
    documentationCenter="java"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="java"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/11/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-java"></a>Introdução ao Azure IoT concentrador para Java

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, tem três aplicações de consola Java:

* **criar dispositivo-identidade**, que cria uma identidade de dispositivo e uma chave de segurança associadas para ligar o seu dispositivo simulado.
* **as mensagens lidas-d2c**, que apresenta telemetria enviada pelo seu dispositivo simulado.
* **dispositivo simulado**, que liga-se ao seu centro IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria cada segunda utilizando o protocolo AMQP.

> [AZURE.NOTE] O artigo [IoT concentrador SDK] [ lnk-hub-sdks] fornece informações sobre as vários SDK, que pode utilizar para criar ambas as aplicações para ser executado em dispositivos e back-end sua solução.

Para concluir este tutorial, precisa do seguinte:

+ Java SE 8. <br/> [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Java para este tutorial no Windows ou Linux.

+ Maven 3.  <br/> [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Maven para este tutorial no Windows ou Linux.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Como um passo final, tome nota do valor de **chave primária** e, em seguida, clique em **serviço de mensagens**. No pá **mensagens** , tome nota do **evento concentrador compatível com nome** e o **ponto final de compatível com o evento concentrador**. Precisa destes três valores quando criar a sua aplicação de **mensagens de d2c de leitura** .

![Azure pá portal IoT concentrador mensagens][6]

Agora ter criado o seu centro IoT e tiver o concentrador IoT hostname, cadeia de ligação IoT concentrador, IoT concentrador de tipos de chave primária, nome compatível com o evento concentrador e ponto final compatível com o evento concentrador que necessário para concluir este tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta secção, crie uma aplicação de consola Java que cria uma nova identidade do dispositivo no registo identidade no seu centro IoT. Um dispositivo não é possível ligar a IoT concentrador, a menos que tenha uma entrada no registo de identidade do dispositivo. Consulte a secção de **Registo de identidade do dispositivo** do [Guia de programador do concentrador de IoT] [ lnk-devguide-identity] para obter mais informações. Quando executar esta aplicação consola, gera um ID de dispositivo exclusivo e uma tecla de que o dispositivo pode utilizar para identificar o próprio quando envia mensagens de dispositivo para nuvem a IoT concentrador.

1. Crie uma nova pasta vazia denominada iniciados de iot java obter. Na pasta iniciado de iot java obter, crie um novo projeto de Maven designado **criar dispositivo-identidade** utilizando o seguinte comando na sua linha de comandos. Nota: Este é um comando único e extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=create-device-identity -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de criar dispositivo-identidade.

3. Utilizando um editor de texto, abra o ficheiro de pom.xml na pasta identidade de dispositivo de criar e adicionar a dependência seguinte para o nó **dependências** . Permite-lhe utilizar o pacote de serviço de iothub-sdk na sua aplicação:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.9</version>
    </dependency>
    ```
    
4. Guarde e feche o ficheiro pom.xml.

5. Utilizando um editor de texto, abra o ficheiro de create-device-identity\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o ficheiro:

    ```
    import com.microsoft.azure.iot.service.exceptions.IotHubException;
    import com.microsoft.azure.iot.service.sdk.Device;
    import com.microsoft.azure.iot.service.sdk.RegistryManager;

    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione variáveis de nível de classe que se seguem para a classe de **aplicação** , substituindo **{yourhubconnectionstring}** com o valor da sua anotados anterior:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "myFirstJavaDevice";
    
    ```
    
8. Modificar a assinatura do método **principal** para incluir as exceções da seguinte forma:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException, Exception
    ```
    
9. Adicione o seguinte código como o corpo do método **principal** . Este código cria um dispositivo chamado *javadevice* no registo de identidade do concentrador de IoT se ainda não exista. Em seguida, apresenta o id do dispositivo e a chave que precisa de mais tarde:

    ```
    RegistryManager registryManager = RegistryManager.createFromConnectionString(connectionString);

    Device device = Device.createFromId(deviceId, null, null);
    try {
      device = registryManager.addDevice(device);
    } catch (IotHubException iote) {
      try {
        device = registryManager.getDevice(deviceId);
      } catch (IotHubException iotf) {
        iotf.printStackTrace();
      }
    }
    System.out.println("Device id: " + device.getDeviceId());
    System.out.println("Device key: " + device.getPrimaryKey());
    ```

10. Guarde e feche o ficheiro App.java.

11. Para criar a aplicação de **identidade de dispositivo de criar** utilizando Maven, execute o seguinte comando na linha de comandos na pasta identidade de dispositivo de criar:

    ```
    mvn clean package -DskipTests
    ```

12. Para executar a aplicação de **identidade de dispositivo de criar** utilizando Maven, execute o seguinte comando na linha de comandos na pasta identidade de dispositivo de criar:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

13. Tome nota do **id de dispositivo** e da **chave do dispositivo**. Terá destes posterior quando cria uma aplicação que liga a IoT concentrador como um dispositivo.

> [AZURE.NOTE] Registo de identidade do concentrador de IoT apenas armazena identidades do dispositivo de ativar o acesso seguro para o concentrador. Armazena dispositivo IDs e chaves para utilizar como as credenciais de segurança e um sinalizador de activado/desactivado que pode utilizar para desativar o acesso de um dispositivo de individual. Se a aplicação necessitar armazenar outros metadados específicas do dispositivo, deve utilizar uma loja específicos da aplicação. Consulte [IoT concentrador programador guia] [ lnk-devguide-identity] para obter mais informações.

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens de nuvem de dispositivo

Nesta secção, crie uma aplicação de consola de Java lê as mensagens de dispositivo para nuvem a partir do IoT Hub. Um concentrador de IoT expõe um [Concentrador de evento][lnk-event-hubs-overview]-ponto final compatível para que possa ler mensagens de dispositivo a nuvem. Para manter as coisas simples, este tutorial cria um leitor de básico que não é adequado para uma implementação débito alta. As [mensagens de nuvem de dispositivo de processo] [ lnk-process-d2c-tutorial] tutorial mostra-lhe como processar mensagens de dispositivo para nuvem à escala. [Introdução ao evento concentradores] [ lnk-eventhubs-tutorial] tutorial fornece informações mais detalhadas sobre como a mensagens de processo de evento concentradores e é aplicável aos pontos finais compatível com o evento de concentrador IoT concentrador.

> [AZURE.NOTE] O ponto final compatível com o evento concentrador para ler mensagens de dispositivo para nuvem sempre utiliza o protocolo AMQP.

1. Na pasta iniciado de iot java get que criou na secção *criar uma identidade de dispositivo* , crie um novo projeto de Maven denominado **as mensagens lidas-d2c** utilizando o seguinte comando na sua linha de comandos. Nota: Este é um comando único e extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=read-d2c-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de mensagens de d2c de leitura.

3. Utilizando um editor de texto, abra o ficheiro pom.xml na pasta as mensagens lidas-d2c e adicione a dependência seguinte para o nó **dependências** . Permite-lhe utilizar o pacote de cliente eventhubs na sua aplicação para ler a partir do ponto final compatível com o evento concentrador:

    ```
    <dependency> 
        <groupId>com.microsoft.azure</groupId> 
        <artifactId>azure-eventhubs</artifactId> 
        <version>0.8.2</version> 
    </dependency>
    ```

4. Guarde e feche o ficheiro pom.xml.

5. Utilizando um editor de texto, abra o ficheiro de read-d2c-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o ficheiro:

    ```
    import java.io.IOException;
    import com.microsoft.azure.eventhubs.*;
    import com.microsoft.azure.servicebus.*;
    
    import java.io.IOException;
    import java.nio.charset.Charset;
    import java.time.*;
    import java.util.Collection;
    import java.util.concurrent.ExecutionException;
    import java.util.function.*;
    import java.util.logging.*;
    ```

7. Adicione as seguintes variáveis de nível de classe para a classe de **aplicação** . **{Youriothubkey}**, **{youreventhubcompatibleendpoint}**e **{youreventhubcompatiblename}** substitua os valores que anotou anteriormente:

    ```
    private static String connStr = "Endpoint={youreventhubcompatibleendpoint};EntityPath={youreventhubcompatiblename};SharedAccessKeyName=iothubowner;SharedAccessKey={youriothubkey}";
    ```

8. Adicione o seguinte método de **receiveMessages** para a classe de **aplicação** . Este método cria uma instância de **EventHubClient** para ligar para o ponto final compatível com o evento concentrador e, em seguida, modo assíncrono cria uma instância de **PartitionReceiver** para ler a partir de uma partição de evento concentrador. -Ciclos continuamente e imprime os detalhes da mensagem, até que a aplicação terminar.

    ```
    private static EventHubClient receiveMessages(final String partitionId)
    {
      EventHubClient client = null;
      try {
        client = EventHubClient.createFromConnectionStringSync(connStr);
      }
      catch(Exception e) {
        System.out.println("Failed to create client: " + e.getMessage());
        System.exit(1);
      }
      try {
        client.createReceiver( 
          EventHubClient.DEFAULT_CONSUMER_GROUP_NAME,  
          partitionId,  
          Instant.now()).thenAccept(new Consumer<PartitionReceiver>()
        {
          public void accept(PartitionReceiver receiver)
          {
            System.out.println("** Created receiver on partition " + partitionId);
            try {
              while (true) {
                Iterable<EventData> receivedEvents = receiver.receive(100).get();
                int batchSize = 0;
                if (receivedEvents != null)
                {
                  for(EventData receivedEvent: receivedEvents)
                  {
                    System.out.println(String.format("Offset: %s, SeqNo: %s, EnqueueTime: %s", 
                      receivedEvent.getSystemProperties().getOffset(), 
                      receivedEvent.getSystemProperties().getSequenceNumber(), 
                      receivedEvent.getSystemProperties().getEnqueuedTime()));
                    System.out.println(String.format("| Device ID: %s", receivedEvent.getProperties().get("iothub-connection-device-id")));
                    System.out.println(String.format("| Message Payload: %s", new String(receivedEvent.getBody(),
                      Charset.defaultCharset())));
                    batchSize++;
                  }
                }
                System.out.println(String.format("Partition: %s, ReceivedBatch Size: %s", partitionId,batchSize));
              }
            }
            catch (Exception e)
            {
              System.out.println("Failed to receive messages: " + e.getMessage());
            }
          }
        });
      }
      catch (Exception e)
      {
        System.out.println("Failed to create receiver: " + e.getMessage());
      }
      return client;
    }
    ```

    > [AZURE.NOTE] Este método utiliza um filtro quando que cria no auscultador, para que o destinatário lê apenas as mensagens enviadas a IoT concentrador depois no auscultador começa a funcionar. Isto é útil num ambiente de teste para que possa ver o conjunto atual de mensagens. Num ambiente de produção seu código deve certificar-se que a mesma processa todas as mensagens - ver [como processar IoT concentrador nuvem de dispositivo mensagens] [ lnk-process-d2c-tutorial] tutorial para obter mais informações.

9. Modificar a assinatura do método **principal** para incluir a exceção da seguinte forma:

    ```
    public static void main( String[] args ) throws IOException
    ```

10. Adicione o seguinte código para o método **principal** da turma de **aplicação** . Este código cria duas instâncias de **EventHubClient** e **PartitionReceiver** e permite-lhe fechar a aplicação quando terminar de processamento de mensagens:

    ```
    EventHubClient client0 = receiveMessages("0");
    EventHubClient client1 = receiveMessages("1");
    System.out.println("Press ENTER to exit.");
    System.in.read();
    try
    {
      client0.closeSync();
      client1.closeSync();
      System.exit(0);
    }
    catch (ServiceBusException sbe)
    {
      System.exit(1);
    }
    ```

    > [AZURE.NOTE] Este código assume que criou o seu centro IoT na camada F1 (gratuita). Um concentrador de IoT gratuito tem duas partições com o nome "0" e "1".

11. Guarde e feche o ficheiro App.java.

12. Para criar a aplicação das **mensagens lidas-d2c** utilizando Maven, execute o seguinte comando na linha de comandos na pasta as mensagens lidas-d2c:

    ```
    mvn clean package -DskipTests
    ```

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, crie uma aplicação de consola de Java simula um dispositivo que envia mensagens de dispositivo para nuvem a um concentrador de IoT.

1. Na pasta iniciado de iot java get que criou na secção *criar uma identidade de dispositivo* , crie um novo projeto de Maven denominado **dispositivo simulado** utilizando o seguinte comando na sua linha de comandos. Nota: Este é um comando único e extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=simulated-device -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de dispositivo simulado.

3. Utilizando um editor de texto, abra o ficheiro de pom.xml na pasta dispositivo simulado e adicione as seguintes dependências ao nó **dependências** . Permite-lhe utilizar o pacote de cliente de java iothub na sua aplicação para comunicar com o seu centro IoT e para serializar Java objectos para JSON:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-device-client</artifactId>
      <version>1.0.14</version>
    </dependency>
    <dependency>
      <groupId>com.google.code.gson</groupId>
      <artifactId>gson</artifactId>
      <version>2.3.1</version>
    </dependency>
    ```

4. Guarde e feche o ficheiro pom.xml.

5. Utilizando um editor de texto, abra o ficheiro de simulated-device\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o ficheiro:

    ```
    import com.microsoft.azure.iothub.DeviceClient;
    import com.microsoft.azure.iothub.IotHubClientProtocol;
    import com.microsoft.azure.iothub.Message;
    import com.microsoft.azure.iothub.IotHubStatusCode;
    import com.microsoft.azure.iothub.IotHubEventCallback;
    import com.microsoft.azure.iothub.IotHubMessageResult;
    import com.google.gson.Gson;
    import java.io.IOException;
    import java.net.URISyntaxException;
    import java.util.Random;
    import java.util.concurrent.Executors;
    import java.util.concurrent.ExecutorService;
    ```

7. Adicione variáveis de nível de classe que se seguem para a classe de **aplicação** , substituindo **{youriothubname}** com o seu nome do concentrador IoT e **{yourdevicekey}** com o valor de chave do dispositivo gerado na secção *criar uma identidade de dispositivo* :

    ```
    private static String connString = "HostName={youriothubname}.azure-devices.net;DeviceId=myFirstJavaDevice;SharedAccessKey={yourdevicekey}";
    private static IotHubClientProtocol protocol = IotHubClientProtocol.AMQP;
    private static String deviceId = "myFirstJavaDevice";
    private static DeviceClient client;
    ```

    Esta aplicação exemplo utiliza a variável de **protocolo** quando-iniciar instância de um objeto de **DeviceClient** . Pode utilizar o protocolo HTTP ou AMQP para comunicar com IoT concentrador.

8. Adicione que o seguinte procedimento aninhada **TelemetryDataPoint** classe dentro da classe de **aplicação** para especificar os dados de telemetria que dispositivo envia para o seu centro IoT:

    ```
    private static class TelemetryDataPoint {
      public String deviceId;
      public double windSpeed;
        
      public String serialize() {
        Gson gson = new Gson();
        return gson.toJson(this);
      }
    }
    ```

9. Adicione a seguinte classe **EventCallback** aninhada dentro da classe de **aplicação** para apresentar o estado de confirmação que devolve o concentrador IoT quando processa uma mensagem a partir do dispositivo simulada. Este método também irá notificá-tópico principal de na aplicação quando a mensagem foi processada:

    ```
    private static class EventCallback implements IotHubEventCallback
    {
      public void execute(IotHubStatusCode status, Object context) {
        System.out.println("IoT Hub responded to message with status: " + status.name());
      
        if (context != null) {
          synchronized (context) {
            context.notify();
          }
        }
      }
    }
    ```

10. Adicione a seguinte classe **MessageSender** aninhada dentro da classe de **aplicação** . O método de **Executar** este aula gera dados de telemetria de exemplo para enviar a sua concentrador IoT e aguarda uma confirmação antes de enviar a mensagem seguinte:

    ```
    private static class MessageSender implements Runnable {
      public volatile boolean stopThread = false;
      
      public void run()  {
        try {
          double avgWindSpeed = 10; // m/s
          Random rand = new Random();
          
          while (!stopThread) {
            double currentWindSpeed = avgWindSpeed + rand.nextDouble() * 4 - 2;
            TelemetryDataPoint telemetryDataPoint = new TelemetryDataPoint();
            telemetryDataPoint.deviceId = deviceId;
            telemetryDataPoint.windSpeed = currentWindSpeed;
            
            String msgStr = telemetryDataPoint.serialize();
            Message msg = new Message(msgStr);
            System.out.println("Sending: " + msgStr);
            
            Object lockobj = new Object();
            EventCallback callback = new EventCallback();
            client.sendEventAsync(msg, callback, lockobj);
            
            synchronized (lockobj) {
              lockobj.wait();
            }
            Thread.sleep(1000);
          }
        } catch (InterruptedException e) {
          System.out.println("Finished.");
        }
      }
    }
    ```

    Este método envia uma nova mensagem de dispositivo para nuvem um segundo depois do concentrador IoT reconhece a mensagem anterior. A mensagem contém um objeto serializado JSON com o ID de dispositivo e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

11. Substitua o método **principal** com o seguinte código que cria um tópico para enviar mensagens de nuvem de dispositivo para o seu centro IoT:

    ```
    public static void main( String[] args ) throws IOException, URISyntaxException {
      client = new DeviceClient(connString, protocol);
      client.open();

      MessageSender sender = new MessageSender();

      ExecutorService executor = Executors.newFixedThreadPool(1);
      executor.execute(sender);

      System.out.println("Press ENTER to exit.");
      System.in.read();
      executor.shutdownNow();
      client.close();
    }
    ```

12. Guarde e feche o ficheiro App.java.

13. Para criar a aplicação de **dispositivo simulado** utilizando Maven, execute o seguinte comando na linha de comandos na pasta dispositivo simulado:

    ```
    mvn clean package -DskipTests
    ```

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (tal como um duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Numa-linha de comandos na pasta d2c de leitura, execute o seguinte comando para começar a monitorizar a primeira partição no seu centro IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Aplicação de cliente do serviço Java IoT concentrador para monitorizar a nuvem de dispositivo de mensagens][7]

2. Numa-linha de comandos na pasta dispositivo simulado, execute o seguinte comando para começar a enviar dados de telemetria para o seu centro IoT:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Aplicação de cliente do dispositivo Java IoT concentrador para enviar mensagens de nuvem de dispositivo][8]

3. O mosaico de **utilização** no [portal do Azure] [ lnk-portal] mostra o número de mensagens enviadas para o concentrador:

    ![Azure portal a utilização mosaico a mostrar o número de mensagens enviadas a IoT concentrador][43]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, configurado um concentrador de IoT novo no portal do Azure e, em seguida, criada uma identidade de dispositivo no registo de identidade do concentrador. Esta identidade do dispositivo que utilizou para ativar a aplicação de dispositivo simulada enviar mensagens de nuvem de dispositivo para o concentrador. Também criou uma aplicação que apresenta as mensagens recebidas pelo concentrador. 

Para continuar a introdução ao centro de IoT e para explorar outras situações IoT consulte:

- [Ligar o seu dispositivo][lnk-connect-device]
- [Introdução à gestão de dispositivos][lnk-device-management]
- [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK]

Para saber como expandir a sua solução IoT e processar as mensagens de dispositivo para nuvem à escala, ver as [mensagens de nuvem de dispositivo de processo] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[6]: ./media/iot-hub-java-java-getstarted/create-iot-hub6.png
[7]: ./media/iot-hub-java-java-getstarted/runapp1.png
[8]: ./media/iot-hub-java-java-getstarted/runapp2.png
[43]: ./media/iot-hub-java-java-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/