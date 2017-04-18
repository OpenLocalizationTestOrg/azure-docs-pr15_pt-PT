<properties
    pageTitle="Enviar mensagens de dispositivo de nuvem com IoT concentrador | Microsoft Azure"
    description="Siga este tutorial para aprender a enviar mensagens de dispositivo de nuvem utilizando o Azure IoT concentrador com c#."
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="06/23/2016"
     ms.author="elioda"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-net"></a>Tutorial: Como enviar mensagens de dispositivo de nuvem com IoT concentrador e .net

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

Azure IoT concentrador é um serviço totalmente gerido que ajuda a ativar fiáveis e seguras as bidirecional comunicações entre milhões de IoT dispositivos e de fim de uma aplicação novamente. O tutorial de [Introdução ao centro de IoT] mostra como criar um concentrador de IoT, aprovisionar uma identidade de dispositivo no mesmo e o código de um dispositivo simulado que envia mensagens de dispositivo a nuvem.

Neste tutorial constrói no [artigo Introdução ao IoT concentrador]. Mostra-lhe como para:

- A partir da sua aplicação nuvem back-end, envie mensagens de dispositivo de nuvem para um único dispositivo através de IoT concentrador.
- Receba mensagens de dispositivo de nuvem num dispositivo.
- A partir do seu nuvem aplicação back-end, pedir a confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo a partir do IoT Hub.

Pode encontrar mais informações sobre mensagens de dispositivo de nuvem no [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

No final deste tutorial, será executada duas aplicações de consola do Windows:

* **SimulatedDevice**, uma versão modificada da aplicação criada no [artigo Introdução ao centro de IoT], que liga-se ao seu centro IoT e recebe mensagens de dispositivo de nuvem.
* **SendCloudToDevice**, que envia uma mensagem de dispositivo de nuvem para o dispositivo simulado através de IoT concentrador e, em seguida, recebe respetiva confirmação de entrega.

> [AZURE.NOTE] Concentrador IoT tem suporte SDK para vários idiomas (incluindo C, Java e Javascript) através do Azure IoT dispositivo SDK e plataformas de dispositivos. Para obter instruções passo a passo sobre como ligar o seu dispositivo para código neste tutorial e geralmente Azure IoT Hub, consulte o [Centro de programadores do Azure IoT].

Para concluir este tutorial, terá o seguinte procedimento:

+ Microsoft Visual Studio 2015

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens no dispositivo simulada

Nesta secção, irá modificar a aplicação de dispositivo simulada que criou no [artigo Introdução ao centro de IoT] para receber mensagens de dispositivo de nuvem a partir do hub a IoT.

1. No Visual Studio, no **SimulatedDevice** project, adicione o seguinte método para a classe de **programa** .

        private static async void ReceiveC2dAsync()
        {
            Console.WriteLine("\nReceiving cloud to device messages from service");
            while (true)
            {
                Message receivedMessage = await deviceClient.ReceiveAsync();
                if (receivedMessage == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received message: {0}", Encoding.ASCII.GetString(receivedMessage.GetBytes()));
                Console.ResetColor();

                await deviceClient.CompleteAsync(receivedMessage);
            }
        }

    O `ReceiveAsync` método modo assíncrono devolve a mensagem recebida no momento em que for recebida pelo dispositivo. Devolve *valor nulo* após um período de tempo limite specifiable (neste caso, a predefinição de um minuto é utilizada). Quando isto acontece, o código deve continuar a aguardar para novas mensagens. Esta é a razão para o `if (receivedMessage == null) continue` linha.

    A chamada para `CompleteAsync()` notifica IoT concentrador que a mensagem foi processada com êxito. A mensagem pode ser removida da fila de dispositivo. Se algo que impedido a aplicação de dispositivo de concluir o processamento da mensagem, IoT concentrador fornece-as novamente. Em seguida, é importante que lógica de processamento de mensagem na aplicação do dispositivo ser *idempotent*, para que a mesma mensagem a receber várias vezes produz o mesmo resultado. Uma aplicação pode também temporariamente abandonar uma mensagem, o que resulta no Centro de IoT reter a mensagem na fila de espera para consumo futura. Em alternativa, a aplicação pode rejeitar uma mensagem que remove permanentemente a mensagem da fila de espera. Para mais informações sobre o ciclo de vida de mensagem de dispositivo de nuvem, consulte o [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

    > [AZURE.NOTE] Quando através de HTTP em vez de MQTT ou AMQP como um transporte, o `ReceiveAsync` método devolve imediatamente. O padrão de suportados para mensagens de dispositivo de nuvem com HTTP está intermitentemente os dispositivos ligados que marcar para mensagens com pouca frequência (inferior a cada minutos 25). Emissão HTTP mais recebe os resultados no Centro de IoT limitação os pedidos. Para mais informações sobre as diferenças entre suporte MQTT, AMQP e HTTP e IoT concentrador limitação, consulte o [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

2. Adicione o seguinte método no método **principal** , para a direita antes do `Console.ReadLine()` linha:

        ReceiveC2dAsync();

> [AZURE.NOTE] Para saké do simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar políticas de repetir (como duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves].

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de dispositivo de nuvem

Nesta secção, irá escrever uma aplicação de consola do Windows que envia mensagens de dispositivo de nuvem para a aplicação de dispositivo simulada.

1. Na solução do Visual Studio atual, crie um novo projeto do Visual aplicação de ambiente de trabalho da c# utilizando o modelo de projeto de **Aplicação de consola** . Nome do projeto **SendCloudToDevice**.

    ![Novo projeto no Visual Studio][20]

2. No Explorador de solução, a solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet para solução...**. 

    Esta ação abre a janela **Gerir pacotes de NuGet** .

3. Procurar `Microsoft Azure Devices`, clique em **instalar**e aceite os termos de utilização. 

    Isto transfere, instalações e adiciona uma referência para o [Azure IoT - serviço SDK NuGet pacote].

4. Adicione o seguinte `using` declaração na parte superior do ficheiro **Program.cs** :

        using Microsoft.Azure.Devices;

5. Adicione os campos seguintes para a classe de **programa** . Substitua o valor de marcador de posição com a cadeia de ligação IoT concentrador de [Introdução ao centro de IoT]:

        static ServiceClient serviceClient;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método para a classe de **programa** :

        private async static Task SendCloudToDeviceMessageAsync()
        {
            var commandMessage = new Message(Encoding.ASCII.GetBytes("Cloud to device message."));
            await serviceClient.SendAsync("myFirstDevice", commandMessage);
        }

    Este método envia uma nova mensagem de dispositivo de nuvem para o dispositivo com o ID `myFirstDevice`. Altere este parâmetro, por conseguinte, no caso de que o modificou a partir da que utilizou no [artigo Introdução ao IoT concentrador].

7. Por fim, adicione as seguintes linhas para o método **principais** :

        Console.WriteLine("Send Cloud-to-Device message\n");
        serviceClient = ServiceClient.CreateFromConnectionString(connectionString);

        Console.WriteLine("Press any key to send a C2D message.");
        Console.ReadLine();
        SendCloudToDeviceMessageAsync().Wait();
        Console.ReadLine();

8. Do Visual Studio, com o botão direito a solução e selecione **projetos de definir o arranque...**. Selecione **vários projetos de arranque**e, em seguida, selecione a ação **Iniciar** para **ProcessDeviceToCloudMessages**, **SimulatedDevice**e **SendCloudToDevice**.

9.  Prima **F5**. Todas as aplicações de três deverão começar. Selecione as janelas de **SendCloudToDevice** e prima **Enter**. Deverá ver a mensagem a ser recebida pela aplicação do simulada.

    ![Mensagem de receção de aplicação][21]

## <a name="receive-delivery-feedback"></a>Receber comentários de entrega
É possível para confirmações de entrega (ou expiração) pedido a partir do IoT Hub para cada mensagem na nuvem para dispositivo. Permite que o cloud back-end informar facilmente lógica de repetição ou compensação. Para mais informações sobre comentários de dispositivo de nuvem, consulte o [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

Nesta secção, irá modificar a aplicação de **SendCloudToDevice** para solicitar comentários e recebê-lo a partir do IoT Hub.

1. No Visual Studio, no **SendCloudToDevice** project, adicione o seguinte método para a classe de **programa** .
   
        private async static void ReceiveFeedbackAsync()
        {
            var feedbackReceiver = serviceClient.GetFeedbackReceiver();

            Console.WriteLine("\nReceiving c2d feedback from service");
            while (true)
            {
                var feedbackBatch = await feedbackReceiver.ReceiveAsync();
                if (feedbackBatch == null) continue;

                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine("Received feedback: {0}", string.Join(", ", feedbackBatch.Records.Select(f => f.StatusCode)));
                Console.ResetColor();

                await feedbackReceiver.CompleteAsync(feedbackBatch);
            }
        }

    Note que o padrão de receção é o mesmo que utilizou para receber mensagens de dispositivo de nuvem a partir da aplicação de dispositivo.

2. Adicione o seguinte método **principais** do método, à direita após a `serviceClient = ServiceClient.CreateFromConnectionString(connectionString)` linha:

        ReceiveFeedbackAsync();

3. Para pedir comentários para a entrega da mensagem de dispositivo de nuvem, tem de especificar uma propriedade o método de **SendCloudToDeviceMessageAsync** . Adicione a linha seguinte, à direita após a `var commandMessage = new Message(...);` linha:

        commandMessage.Ack = DeliveryAcknowledgement.Full;

4.  Execute as aplicações premindo a tecla **F5**. Deverá ver todas as aplicações de três iniciar. Selecione as janelas de **SendCloudToDevice** e prima **Enter**. Deverá ver a mensagem a ser recebida pela aplicação do simulada e depois de aguardar alguns segundos, a mensagem de comentários que está a ser recebida pela aplicação **SendCloudToDevice** .

    ![Mensagem de receção de aplicação][22]

> [AZURE.NOTE] Para saké do simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar políticas de repetir (como duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves].

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como enviar e receber mensagens de dispositivo de nuvem. 

Para ver exemplos de soluções de ponto a ponto completas que utilizam IoT concentrador, consulte [o conjunto de aplicações do Azure IoT].

Para saber mais sobre como desenvolver soluções com IoT concentrador, consulte o [Guia de programador do IoT concentrador].

<!-- Images -->
[20]: ./media/iot-hub-csharp-csharp-c2d/create-identity-csharp1.png
[21]: ./media/iot-hub-csharp-csharp-c2d/sendc2d1.png
[22]: ./media/iot-hub-csharp-csharp-c2d/sendc2d2.png

<!-- Links -->

[Azure IoT - serviço SDK NuGet pacote]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md

[Guia do Programador de IoT concentrador]: iot-hub-devguide.md
[Introdução ao centro de IoT]: iot-hub-csharp-csharp-getstarted.md
[Centro de programadores do Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Azure IoT conjunto de aplicações]: https://azure.microsoft.com/documentation/suites/iot-suite/