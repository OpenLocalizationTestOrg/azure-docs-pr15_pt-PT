<properties
    pageTitle="Enviar mensagens de dispositivo de nuvem com IoT concentrador | Microsoft Azure"
    description="Siga este tutorial para aprender a enviar mensagens de dispositivo de nuvem utilizando o Azure IoT concentrador com Java."
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
     ms.date="09/13/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-java"></a>Tutorial: Como enviar mensagens de dispositivo de nuvem com IoT concentrador e Java

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

Azure IoT concentrador é um serviço totalmente gerido que ajuda a ativar fiáveis e seguras as bidirecional comunicações entre milhões de IoT dispositivos e de fim de uma aplicação novamente. O tutorial de [Introdução ao centro de IoT] mostra como criar um concentrador de IoT, aprovisionar uma identidade de dispositivo no mesmo e o código de um dispositivo simulado que envia mensagens de dispositivo a nuvem.

Neste tutorial constrói no [artigo Introdução ao IoT concentrador]. Mostra-lhe como para:

- A partir da sua aplicação nuvem back-end, envie mensagens de dispositivo de nuvem para um único dispositivo através de IoT concentrador.
- Receba mensagens de dispositivo de nuvem num dispositivo.
- A partir do seu nuvem aplicação back-end, pedir a confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo a partir do IoT Hub.

Pode encontrar mais informações sobre mensagens de dispositivo de nuvem no [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

No final deste tutorial, executa duas aplicações de consola Java:

* **dispositivo simulado**, uma versão modificada da aplicação criada no [artigo Introdução ao centro de IoT], que liga-se ao seu centro IoT e recebe mensagens de dispositivo de nuvem.
* **Enviar-c2d-mensagens**que envia uma mensagem de dispositivo de nuvem para o dispositivo simulado através de IoT concentrador e, em seguida, recebe respetiva confirmação de entrega.

> [AZURE.NOTE] Concentrador IoT tem suporte SDK para vários idiomas (incluindo C, Java e Javascript) através do Azure IoT dispositivo SDK e plataformas de dispositivos. Para obter instruções passo a passo sobre como ligar o seu dispositivo para código neste tutorial e geralmente Azure IoT Hub, consulte o [Centro de programadores do Azure IoT].

Para concluir este tutorial, precisa do seguinte:

+ Java SE 8. <br/> [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Java para este tutorial no Windows ou Linux.

+ Maven 3.  <br/> [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Maven para este tutorial no Windows ou Linux.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens no dispositivo simulada

Nesta secção, pode modifica a aplicação de dispositivo simulada que criou no [artigo Introdução ao centro de IoT] para receber mensagens de dispositivo de nuvem a partir do hub a IoT.

1. Utilizando um editor de texto, abra o ficheiro de simulated-device\src\main\java\com\mycompany\app\App.java.

2. Adicione a seguinte classe **MessageCallback** como uma classe aninhada dentro da classe de **aplicação** . O método **Executar** é chamado quando o dispositivo recebe uma mensagem a partir do IoT Hub. Neste exemplo, o dispositivo sempre notifica o concentrador que ela estar concluída a mensagem:

    ```
    private static class MessageCallback implements
    com.microsoft.azure.iothub.MessageCallback {
      public IotHubMessageResult execute(Message msg, Object context) {
        System.out.println("Received message from hub: "
          + new String(msg.getBytes(), Message.DEFAULT_IOTHUB_MESSAGE_CHARSET));

        return IotHubMessageResult.COMPLETE;
      }
    }
    ```

3. Modificar o método **principal** para criar uma instância de **MessageCallback** e chamar o método de **setMessageCallback** antes de ser aberto o cliente da seguinte forma:

    ```
    client = new DeviceClient(connString, protocol);

    MessageCallback callback = new MessageCallback();
    client.setMessageCallback(callback, null);
    client.open();
    ```

    > [AZURE.NOTE] Se utilizar HTTP em vez de MQTT ou AMQP como o transporte, verifica a instância **DeviceClient** para mensagens a partir do Hub de IoT com pouca frequência (inferior a cada minutos 25). Para mais informações sobre as diferenças entre suporte MQTT, AMQP e HTTP e IoT concentrador limitação, consulte o [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de dispositivo de nuvem

Nesta secção, crie uma aplicação de consola Java que envia mensagens de dispositivo de nuvem para a aplicação de dispositivo simulada. Tem o dispositivo de Id do dispositivo que adicionou no tutorial [Introdução ao IoT concentrador] . Também precisa da cadeia de ligação para o seu centro de IoT que pode encontrar no [portal do Azure].

1. Crie um projeto de Maven denominado **Enviar-c2d-mensagens** utilizando o seguinte comando na sua linha de comandos. Nota: Este é um comando único e extenso:

    ```
    mvn archetype:generate -DgroupId=com.mycompany.app -DartifactId=send-c2d-messages -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
    ```

2. Na sua linha de comandos, navegue para a nova pasta de mensagens de c2d enviar.

3. Utilizando um editor de texto, abra o ficheiro de pom.xml na pasta a enviar-c2d-mensagens e adicione a dependência seguinte para o nó **dependências** . Adicionar a dependência permite-lhe utilizar o pacote de **iothub-java-serviço-client** na sua aplicação para comunicar com o seu serviço de concentrador IoT:

    ```
    <dependency>
      <groupId>com.microsoft.azure.iothub-java-client</groupId>
      <artifactId>iothub-java-service-client</artifactId>
      <version>1.0.7</version>
    </dependency>
    ```

4. Guarde e feche o ficheiro pom.xml.

5. Utilizando um editor de texto, abra o ficheiro de send-c2d-messages\src\main\java\com\mycompany\app\App.java.

6. Adicione as seguintes instruções de **Importar** o ficheiro:

    ```
    import com.microsoft.azure.iot.service.sdk.*;
    import java.io.IOException;
    import java.net.URISyntaxException;
    ```

7. Adicione variáveis de nível de classe que se seguem para a classe de **aplicação** , substituir **{yourhubconnectionstring}** e **{yourdeviceid}** com os valores seu anotados anterior:

    ```
    private static final String connectionString = "{yourhubconnectionstring}";
    private static final String deviceId = "{yourdeviceid}";
    private static final IotHubServiceClientProtocol protocol = IotHubServiceClientProtocol.AMQP;
    ```
    
8. Substitua o método **principal** com o seguinte código que liga-se ao seu centro IoT, envia uma mensagem para o seu dispositivo e, em seguida, aguarda uma confirmação de que o dispositivo recebido e processadas a mensagem:

    ```
    public static void main(String[] args) throws IOException,
        URISyntaxException, Exception {
      ServiceClient serviceClient = ServiceClient.createFromConnectionString(
        connectionString, protocol);
      
      if (serviceClient != null) {
        serviceClient.open();
        FeedbackReceiver feedbackReceiver = serviceClient
          .getFeedbackReceiver(deviceId);
        if (feedbackReceiver != null) feedbackReceiver.open();

        Message messageToSend = new Message("Cloud to device message.");
        messageToSend.setDeliveryAcknowledgement(DeliveryAcknowledgement.Full);

        serviceClient.send(deviceId, messageToSend);
        System.out.println("Message sent to device");

        FeedbackBatch feedbackBatch = feedbackReceiver.receive(10000);
        if (feedbackBatch != null) {
          System.out.println("Message feedback received, feedback time: "
            + feedbackBatch.getEnqueuedTimeUtc().toString());
        }

        if (feedbackReceiver != null) feedbackReceiver.close();
        serviceClient.close();
      }
    }
    ```

    > [AZURE.NOTE] Para saké do simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar políticas de repetir (como duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves].

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Numa-linha de comandos na pasta dispositivo simulado, execute o seguinte comando para começar a enviar telemetria para o seu centro IoT e para escutar de dispositivo de nuvem mensagens enviadas a partir do seu centro de:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App" 
    ```

    ![Executar a aplicação de dispositivo simulada][img-simulated-device]

2. Numa-linha de comandos na pasta a enviar-c2d-mensagens, execute o seguinte comando para enviar uma mensagem de dispositivo de nuvem e aguarde que uma confirmação de comentários:

    ```
    mvn exec:java -Dexec.mainClass="com.mycompany.app.App"
    ```

    ![Execute o comando para enviar a mensagem de dispositivo de nuvem][img-send-command]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como enviar e receber mensagens de dispositivo de nuvem. 

Para ver exemplos de soluções de ponto a ponto completas que utilizam IoT concentrador, consulte [o conjunto de aplicações do Azure IoT].

Para saber mais sobre como desenvolver soluções com IoT concentrador, consulte o [Guia de programador do IoT concentrador].


<!-- Images -->
[img-simulated-device]: media/iot-hub-java-java-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-java-java-c2d/sendc2d.png
<!-- Links -->

[Introdução ao centro de IoT]: iot-hub-java-java-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guia do Programador de IoT concentrador]: iot-hub-devguide.md
[Centro de programadores do Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/java-devbox-setup.md
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Azure IoT conjunto de aplicações]: https://azure.microsoft.com/documentation/suites/iot-suite/