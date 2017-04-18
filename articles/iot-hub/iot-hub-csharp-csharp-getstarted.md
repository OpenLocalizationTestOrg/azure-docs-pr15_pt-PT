<properties
    pageTitle="Azure IoT concentrador para c# introdução | Microsoft Azure"
    description="Azure IoT concentrador com c# introdução de iniciação. Utilize Azure IoT concentrador e c# com o SDK do Microsoft Azure IoT para implementar uma solução de Internet de coisas."
    services="iot-hub"
    documentationCenter=".net"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-net"></a>Introdução ao Azure IoT concentrador para .NET

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, tem três aplicações de consola do Windows:

* **CreateDeviceIdentity**, que cria uma identidade de dispositivo e uma chave de segurança associadas para ligar o seu dispositivo simulado.
* **ReadDeviceToCloudMessages**, que apresenta telemetria enviada pelo seu dispositivo simulado.
* **SimulatedDevice**, que liga-se ao seu centro IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria cada segundo utilizando o protocolo AMQP.

> [AZURE.NOTE] Para obter informações sobre as vários SDK, que pode utilizar para criar ambas as aplicações para ser executado em dispositivos e back-end a solução, consulte o artigo [IoT concentrador SDK][lnk-hub-sdks].

Para concluir este tutorial, precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora ter criado o seu centro IoT e tiver a cadeia de ligação e hostname que necessita para concluir o resto deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta secção, crie uma aplicação de consola do Windows que cria uma identidade de dispositivo no registo identidade no seu centro IoT. Um dispositivo não é possível ligar a IoT concentrador, a menos que tenha uma entrada no registo de identidade do dispositivo. Para obter mais informações, consulte a secção "Registo de identidade do dispositivo" do [Guia de programador do IoT concentrador]de[lnk-devguide-identity]. Quando executar esta aplicação consola, gera um ID de dispositivo exclusivo e uma tecla de que o dispositivo pode utilizar para identificar o próprio quando envia mensagens de dispositivo para nuvem a IoT concentrador.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico ambiente de trabalho para a solução actual utilizando o modelo de **Aplicação de consola** do projecto. Certifique-se de que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **CreateDeviceIdentity**.

    ![Novo projeto do Visual c# clássica ambiente de trabalho Windows][10]

2. No Explorador de solução, com o botão direito do projecto **CreateDeviceIdentity** e, em seguida, clique em **Gerir pacotes de Nuget**.

3. Na janela do **Gestor de pacotes Nuget** , selecione **Procurar**, procure **microsoft.azure.devices**, selecione **instalar** para instalar o pacote de **Microsoft.Azure.Devices** e aceite os termos de utilização. Este procedimento transferências, instalações e adiciona uma referência para o [Microsoft Azure IoT serviço SDK] [ lnk-nuget-service-sdk] Nuget pacote e as respectivas dependências.

    ![Janela Gestor de pacote Nuget][11]

4. Adicione o seguinte `using` declarações na parte superior do ficheiro **Program.cs** :

        using Microsoft.Azure.Devices;
        using Microsoft.Azure.Devices.Common.Exceptions;

5. Adicione os campos seguintes para a classe de **programa** . Substitua o valor de marcador de posição com a cadeia de ligação para o concentrador de IoT que criou na secção anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método para a classe de **programa** :

        private static async Task AddDeviceAsync()
        {
            string deviceId = "myFirstDevice";
            Device device;
            try
            {
                device = await registryManager.AddDeviceAsync(new Device(deviceId));
            }
            catch (DeviceAlreadyExistsException)
            {
                device = await registryManager.GetDeviceAsync(deviceId);
            }
            Console.WriteLine("Generated device key: {0}", device.Authentication.SymmetricKey.PrimaryKey);
        }

    Este método cria uma identidade de dispositivo com ID **myFirstDevice**. (Se esse ID do dispositivo já existir no registo, o código simplesmente obtém as informações do dispositivo existente.) A aplicação, em seguida, apresenta a chave primária para essa identidade. Utilize esta chave no dispositivo simulado para ligar ao seu centro IoT.

7. Por fim, adicione as seguintes linhas para o método **principais** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddDeviceAsync().Wait();
        Console.ReadLine();

8. Executar esta aplicação e tome nota da chave de dispositivo.

    ![Chave gerada pela aplicação do dispositivo][12]

> [AZURE.NOTE] Registo de identidade do concentrador de IoT apenas armazena identidades do dispositivo de ativar o acesso seguro para o concentrador. Armazena dispositivo IDs e chaves para utilizar como as credenciais de segurança e um sinalizador de activado/desactivado que pode utilizar para desativar o acesso de um dispositivo de individual. Se a aplicação necessitar armazenar outros metadados específicas do dispositivo, deve utilizar uma loja específicos da aplicação. Para mais informações, consulte o artigo [Guia de programador do concentrador de IoT][lnk-devguide-identity].

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens de nuvem de dispositivo

Nesta secção, crie uma aplicação de consola do Windows que lê as mensagens de dispositivo para nuvem a partir do IoT Hub. Um concentrador de IoT expõe um [Azure evento concentradores][lnk-event-hubs-overview]-ponto final compatível para que possa ler mensagens de dispositivo a nuvem. Para manter as coisas simples, este tutorial cria um leitor de básico que não é adequado para uma implementação débito alta. Para saber como processar nuvem de dispositivo de mensagens em escala, ver as [mensagens de nuvem de dispositivo de processo] [ lnk-process-d2c-tutorial] tutorial. Para obter mais informações sobre como mensagens de processo de concentradores de evento, consulte o artigo [Introdução ao evento concentradores] [ lnk-eventhubs-tutorial] tutorial. (Este tutorial é aplicável para os pontos finais compatível com o evento de concentrador IoT concentrador.)

> [AZURE.NOTE] O ponto final compatível com o evento concentrador para ler mensagens de dispositivo para nuvem sempre utiliza o protocolo AMQP.

1. No Visual Studio, adicione um projeto do Visual c# clássica ambiente de trabalho Windows para a solução actual, utilizando o modelo de **Aplicação de consola** do projecto. Certifique-se de que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **ReadDeviceToCloudMessages**.

    ![Novo projeto do Visual c# clássica ambiente de trabalho Windows][10]

2. No Explorador de solução, com o botão direito do projecto **ReadDeviceToCloudMessages** e, em seguida, clique em **Gerir pacotes de Nuget**.

3. Na janela do **Gestor de pacotes Nuget** , procure **WindowsAzure.ServiceBus**, selecione **instalar**e aceite os termos de utilização. Este procedimento transferências, instalações e adiciona uma referência a [Azure Service Bus][lnk-servicebus-nuget], com as respectivas dependências. Este pacote permite que a aplicação ligar para o ponto final compatível com o evento concentrador no seu centro IoT.

4. Adicione o seguinte `using` declarações na parte superior do ficheiro **Program.cs** :

        using Microsoft.ServiceBus.Messaging;
        using System.Threading;

5. Adicione os campos seguintes para a classe de **programa** . Substitua o valor de marcador de posição com a cadeia de ligação para o concentrador de IoT que criou na secção "Criar um concentrador de IoT".

        static string connectionString = "{iothub connection string}";
        static string iotHubD2cEndpoint = "messages/events";
        static EventHubClient eventHubClient;

6. Adicione o seguinte método para a classe de **programa** :

        private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
        {
            var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
            while (true)
            {
                if (ct.IsCancellationRequested) break;
                EventData eventData = await eventHubReceiver.ReceiveAsync();
                if (eventData == null) continue;

                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
            }
        }

    Este método utiliza uma instância de **EventHubReceiver** para receber mensagens de todas as IoT concentrador dispositivo-para-nuvem recebem a partições. Repare como transmitir uma `DateTime.Now` parâmetro quando cria o objeto **EventHubReceiver** , para que receba mensagens enviadas depois de ser iniciado apenas. Este filtro é útil num ambiente de teste para que possa ver o conjunto atual de mensagens. Num ambiente de produção o seu código deve certificar-se de que processa todas as mensagens. Para obter mais informações, consulte o artigo [como processar IoT concentrador nuvem de dispositivo mensagens] [ lnk-process-d2c-tutorial] tutorial.

7. Por fim, adicione as seguintes linhas para o método **principais** :

        Console.WriteLine("Receive messages. Ctrl-C to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, iotHubD2cEndpoint);

        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;

        CancellationTokenSource cts = new CancellationTokenSource();

        System.Console.CancelKeyPress += (s, e) =>
        {
          e.Cancel = true;
          cts.Cancel();
          Console.WriteLine("Exiting...");
        };

        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
           tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }  
        Task.WaitAll(tasks.ToArray());

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, crie uma aplicação de consola do Windows que simula um dispositivo que envia mensagens de dispositivo para nuvem a um concentrador de IoT.

1. No Visual Studio, adicione um projeto do Visual c# clássica ambiente de trabalho Windows para a solução actual, utilizando o modelo de **Aplicação de consola** do projecto. Certifique-se de que a versão do .NET Framework é 4.5.1 ou posterior. Nome do projeto **SimulatedDevice**.

    ![Novo projeto do Visual c# clássica ambiente de trabalho Windows][10]

2. No Explorador de solução, com o botão direito do projecto **SimulatedDevice** e, em seguida, clique em **Gerir pacotes de Nuget**.

3. Na janela do **Gestor de pacotes Nuget** , selecione **Procurar**, procure **Microsoft.Azure.Devices.Client**, selecione **instalar** para instalar o pacote de **Microsoft.Azure.Devices.Client** e aceite os termos de utilização. Este procedimento transferências, instalações e adiciona uma referência para o [Azure IoT - pacote de dispositivo SDK Nuget] [ lnk-device-nuget] e as respectivas dependências.

4. Adicione o seguinte `using` declaração na parte superior do ficheiro **Program.cs** :

        using Microsoft.Azure.Devices.Client;
        using Newtonsoft.Json;

5. Adicione os campos seguintes para a classe de **programa** . Substitua os valores de marcador de posição com o nome do anfitrião IoT concentrador que recuperou na secção "Criar um concentrador de IoT" e a chave de dispositivo obtidos na secção "Criar uma identidade de dispositivo".

        static DeviceClient deviceClient;
        static string iotHubUri = "{iot hub hostname}";
        static string deviceKey = "{device key}";

6. Adicione o seguinte método para a classe de **programa** :

        private static async void SendDeviceToCloudMessagesAsync()
        {
            double avgWindSpeed = 10; // m/s
            Random rand = new Random();

            while (true)
            {
                double currentWindSpeed = avgWindSpeed + rand.NextDouble() * 4 - 2;

                var telemetryDataPoint = new
                {
                    deviceId = "myFirstDevice",
                    windSpeed = currentWindSpeed
                };
                var messageString = JsonConvert.SerializeObject(telemetryDataPoint);
                var message = new Message(Encoding.ASCII.GetBytes(messageString));

                await deviceClient.SendEventAsync(message);
                Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, messageString);

                Task.Delay(1000).Wait();
            }
        }

    Este método envia uma nova mensagem de dispositivo para nuvem cada segundo. A mensagem contém um objeto serializado JSON, com o ID do dispositivo e um número gerado aleatoriamente para simular um sensor de velocidade do vento.

7. Por fim, adicione as seguintes linhas para o método **principais** :

        Console.WriteLine("Simulated device\n");
        deviceClient = DeviceClient.Create(iotHubUri, new DeviceAuthenticationWithRegistrySymmetricKey("myFirstDevice", deviceKey));

        SendDeviceToCloudMessagesAsync();
        Console.ReadLine();

  Por predefinição, o método **Create** cria uma instância de **DeviceClient** que utiliza o protocolo AMQP para comunicar com IoT concentrador. Para utilizar o protocolo HTTP, utilize a substituição do método **Create** que permite-lhe especificar o protocolo. Se utilizar o protocolo HTTP, também deve adicionar o pacote de Nuget **Microsoft.AspNet.WebApi.Client** ao seu projeto para incluir o espaço de nomes de **System.Net.Http.Formatting** .

Neste tutorial leva-o através dos passos para criar um cliente de dispositivo IoT concentrador. Também pode utilizar o [Serviço ligado para Azure IoT concentrador] [ lnk-connected-service] extensão do Visual Studio para adicionar o código necessário a sua aplicação de cliente do dispositivo.


> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (tal como um duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1.  No Visual Studio, no Explorador de solução, a solução com o botão direito e, em seguida, clique em **Definir arranque projetos**. Selecione **vários projetos de arranque**e, em seguida, selecione **Iniciar** como a ação para a **ReadDeviceToCloudMessages** e o **SimulatedDevice** projetos.

    ![Propriedades de arranque do projeto][41]

2.  Prima **F5** para começar a ambas as aplicações em execução. O resultado da consola a partir da aplicação **SimulatedDevice** apresenta as mensagens que dispositivo simulado envia para o seu centro IoT. O resultado da consola a partir da aplicação **ReadDeviceToCloudMessages** mostra as mensagens que recebe o seu centro IoT.

    ![Saída da consola a partir de aplicações][42]

3. O mosaico de **utilização** no [portal do Azure] [ lnk-portal] mostra o número de mensagens enviadas para o concentrador:

    ![Azure mosaico de utilização de portal][43]


## <a name="next-steps"></a>Próximos passos

Neste tutorial, configurado um concentrador de IoT no portal do Azure e, em seguida, criada uma identidade de dispositivo no registo de identidade do concentrador. Esta identidade do dispositivo que utilizou para ativar a aplicação de dispositivo simulada enviar mensagens de nuvem de dispositivo para o concentrador. Também criou uma aplicação que apresenta as mensagens recebidas pelo concentrador. 

Para continuar a introdução ao centro de IoT e para explorar outras situações IoT, consulte:

- [Ligar o seu dispositivo][lnk-connect-device]
- [Introdução à gestão de dispositivos][lnk-device-management]
- [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK]

Para saber como expandir a sua solução IoT e processar as mensagens de dispositivo para nuvem à escala, ver as [mensagens de nuvem de dispositivo de processo] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[41]: ./media/iot-hub-csharp-csharp-getstarted/run-apps1.png
[42]: ./media/iot-hub-csharp-csharp-getstarted/run-apps2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png
[10]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp1.png
[11]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp2.png
[12]: ./media/iot-hub-csharp-csharp-getstarted/create-identity-csharp3.png

<!-- Links -->
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-servicebus-nuget]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[lnk-device-nuget]: https://www.nuget.org/packages/Microsoft.Azure.Devices.Client/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[lnk-connected-service]: https://visualstudiogallery.msdn.microsoft.com/e254a3a5-d72e-488e-9bd3-8fee8e0cd1d6
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
