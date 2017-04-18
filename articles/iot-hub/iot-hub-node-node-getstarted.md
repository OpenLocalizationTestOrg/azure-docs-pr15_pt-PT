<properties
    pageTitle="Azure IoT concentrador para Node.js introdução | Microsoft Azure"
    description="Azure IoT concentrador com Node.js introdução de iniciação. Utilize o Azure IoT concentrador e Node.js com o SDK do Microsoft Azure IoT para implementar uma solução de Internet de coisas."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="hero-article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/12/2016"
     ms.author="dobett"/>

# <a name="get-started-with-azure-iot-hub-for-nodejs"></a>Introdução ao Azure IoT concentrador para Node.js

[AZURE.INCLUDE [iot-hub-selector-get-started](../../includes/iot-hub-selector-get-started.md)]

No final deste tutorial, tem três aplicações de consola Node.js:

* **CreateDeviceIdentity.js**, que cria uma identidade de dispositivo e uma chave de segurança associadas para ligar o seu dispositivo simulado.
* **ReadDeviceToCloudMessages.js**, que apresenta telemetria enviada pelo seu dispositivo simulado.
* **SimulatedDevice.js**, que liga-se ao seu centro IoT com a identidade do dispositivo criada anteriormente e envia uma mensagem de telemetria cada segunda utilizando o protocolo AMQP.

> [AZURE.NOTE] O artigo [IoT concentrador SDK] [ lnk-hub-sdks] fornece informações sobre as vários SDK, que pode utilizar para criar ambas as aplicações para ser executado em dispositivos e back-end sua solução.

Para concluir este tutorial, precisa do seguinte:

+ Versão node.js 0.10.x ou posterior.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub](../../includes/iot-hub-get-started-create-hub.md)]

Agora ter criado o seu centro IoT. Tiver o nome do anfitrião IoT concentrador e a cadeia de ligação IoT concentrador que necessita para concluir o resto deste tutorial.

## <a name="create-a-device-identity"></a>Criar uma identidade de dispositivo

Nesta secção, crie uma aplicação de consola Node.js que cria uma identidade de dispositivo no registo identidade no seu centro IoT. Um dispositivo não é possível ligar a IoT concentrador, a menos que tenha uma entrada no registo de identidade do dispositivo. Consulte a secção de **Registo de identidade do dispositivo** do [Guia de programador do concentrador de IoT] [ lnk-devguide-identity] para obter mais informações. Quando executar esta aplicação consola, gera um ID de dispositivo exclusivo e uma tecla de que o dispositivo pode utilizar para identificar o próprio quando envia mensagens de dispositivo para nuvem a IoT concentrador.

1. Crie uma nova pasta vazia denominada **createdeviceidentity**. Na pasta **createdeviceidentity** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **createdeviceidentity** , execute o seguinte comando para instalar o pacote de serviço SDK **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um ficheiro de **CreateDeviceIdentity.js** na pasta **createdeviceidentity** .

4. Adicione o seguinte `require` declaração no início do ficheiro **CreateDeviceIdentity.js** :

    ```
    'use strict';
    
    var iothub = require('azure-iothub');
    ```

5. Adicione o seguinte código para o ficheiro **CreateDeviceIdentity.js** e substitua o valor de marcador de posição com a cadeia de ligação para o concentrador de IoT que criou na secção anterior: 

    ```
    var connectionString = '{iothub connection string}';
    
    var registry = iothub.Registry.fromConnectionString(connectionString);
    ```

6. Adicione o seguinte código para criar uma definição de dispositivo no registo de identidade do dispositivo no seu centro IoT. Este código cria um dispositivo se o id do dispositivo não existir no registo, caso contrário, devolve a tecla do dispositivo existente:

    ```
    var device = new iothub.Device(null);
    device.deviceId = 'myFirstNodeDevice';
    registry.create(device, function(err, deviceInfo, res) {
      if (err) {
        registry.get(device.deviceId, printDeviceInfo);
      }
      if (deviceInfo) {
        printDeviceInfo(err, deviceInfo, res)
      }
    });

    function printDeviceInfo(err, deviceInfo, res) {
      if (deviceInfo) {
        console.log('Device id: ' + deviceInfo.deviceId);
        console.log('Device key: ' + deviceInfo.authentication.SymmetricKey.primaryKey);
      }
    }
    ```

7. Guarde e feche o ficheiro **CreateDeviceIdentity.js** .

8. Para executar a aplicação de **createdeviceidentity** , execute o seguinte comando na linha de comandos na pasta createdeviceidentity:

    ```
    node CreateDeviceIdentity.js 
    ```

9. Tome nota do **id de dispositivo** e da **chave do dispositivo**. Precisa destes valores mais tarde quando cria uma aplicação que liga a IoT concentrador como um dispositivo.

> [AZURE.NOTE] Registo de identidade do concentrador de IoT apenas armazena identidades do dispositivo de ativar o acesso seguro para o concentrador. Armazena dispositivo IDs e chaves para utilizar como as credenciais de segurança e um sinalizador de activado/desactivado que pode utilizar para desativar o acesso de um dispositivo de individual. Se a aplicação necessitar armazenar outros metadados específicas do dispositivo, deve utilizar uma loja específicos da aplicação. Consulte [IoT concentrador programador guia] [ lnk-devguide-identity] para obter mais informações.

## <a name="receive-device-to-cloud-messages"></a>Receber mensagens de nuvem de dispositivo

Nesta secção, crie uma aplicação de consola Node.js que lê as mensagens de dispositivo para nuvem a partir do IoT Hub. Um concentrador de IoT expõe um [Evento concentradores][lnk-event-hubs-overview]-ponto final compatível para que possa ler mensagens de dispositivo a nuvem. Para manter as coisas simples, este tutorial cria um leitor de básico que não é adequado para uma implementação débito alta. As [mensagens de nuvem de dispositivo de processo] [ lnk-process-d2c-tutorial] tutorial mostra-lhe como processar mensagens de dispositivo para nuvem à escala. [Introdução ao evento concentradores] [ lnk-eventhubs-tutorial] tutorial fornece informações mais detalhadas sobre como a mensagens de processo de evento concentradores e é aplicável aos pontos finais compatível com o evento de concentrador IoT concentrador.

> [AZURE.NOTE] O ponto final compatível com o evento concentrador para ler mensagens de dispositivo para nuvem sempre utiliza o protocolo AMQP.

1. Crie uma nova pasta vazia denominada **readdevicetocloudmessages**. Na pasta **readdevicetocloudmessages** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **readdevicetocloudmessages** , execute o seguinte comando para instalar o pacote de **evento-azure-concentradores** :

    ```
    npm install azure-event-hubs --save
    ```

3. Utilizando um editor de texto, crie um ficheiro de **ReadDeviceToCloudMessages.js** na pasta **readdevicetocloudmessages** .

4. Adicione o seguinte `require` declarações no início do ficheiro **ReadDeviceToCloudMessages.js** :

    ```
    'use strict';

    var EventHubClient = require('azure-event-hubs').Client;
    ```

5. Adicionar a declaração de variável seguinte e substitua o valor de marcador de posição com a cadeia de ligação para o seu centro IoT:

    ```
    var connectionString = '{iothub connection string}';
    ```

6. Adicione as seguintes duas funções saída na consola de impressão:

    ```
    var printError = function (err) {
      console.log(err.message);
    };

    var printMessage = function (message) {
      console.log('Message received: ');
      console.log(JSON.stringify(message.body));
      console.log('');
    };
    ```

7. Adicione o código seguinte para criar o **EventHubClient**, abra a ligação ao seu centro IoT e criar um destinatário para cada partição. Esta aplicação utiliza um filtro quando cria um destinatário para que o destinatário lê apenas as mensagens enviadas a IoT concentrador depois no auscultador começa a funcionar. Este filtro é útil num ambiente de teste para ver apenas o conjunto atual de mensagens. Num ambiente de produção, os seu código deve certificar-se de que processa todas as mensagens - Consulte o artigo [como processar IoT concentrador nuvem de dispositivo mensagens] [ lnk-process-d2c-tutorial] tutorial para obter mais informações:

    ```
    var client = EventHubClient.fromConnectionString(connectionString);
    client.open()
        .then(client.getPartitionIds.bind(client))
        .then(function (partitionIds) {
            return partitionIds.map(function (partitionId) {
                return client.createReceiver('$Default', partitionId, { 'startAfterTime' : Date.now()}).then(function(receiver) {
                    console.log('Created partition receiver: ' + partitionId)
                    receiver.on('errorReceived', printError);
                    receiver.on('message', printMessage);
                });
            });
        })
        .catch(printError);
    ```

8. Guarde e feche o ficheiro **ReadDeviceToCloudMessages.js** .

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, crie uma aplicação de consola de Node.js simula um dispositivo que envia mensagens de dispositivo para nuvem a um concentrador de IoT.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **simulateddevice** , execute o seguinte comando para instalar o pacote de dispositivo SDK de **dispositivo de iot azure** e **azure-iot dispositivo amqp** pacote:

    ```
    npm install azure-iot-device azure-iot-device-amqp --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **SimulatedDevice.js** na pasta **simulateddevice** .

4. Adicione o seguinte `require` declarações no início do ficheiro **SimulatedDevice.js** :

    ```
    'use strict';

    var clientFromConnectionString = require('azure-iot-device-amqp').clientFromConnectionString;
    var Message = require('azure-iot-device').Message;
    ```

5. Adicionar uma variável de **connectionString** e utilizá-la para criar um cliente do dispositivo. Substituir **{youriothostname}** com o nome do concentrador IoT que criou a secção *criar um concentrador de IoT* . Substitua o valor de chave do dispositivo gerado na secção *criar uma identidade de dispositivo* **{yourdevicekey}** :

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myFirstNodeDevice;SharedAccessKey={yourdevicekey}';
    
    var client = clientFromConnectionString(connectionString);
    ```

6. Adicione a função seguinte para apresentar o resultado da aplicação:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Criar uma chamada de retorno e utilize a função **setInterval** para enviar uma nova mensagem para o seu centro IoT cada segundo:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');

        // Create a message and send it to the IoT Hub every second
        setInterval(function(){
            var windSpeed = 10 + (Math.random() * 4);
            var data = JSON.stringify({ deviceId: 'myFirstNodeDevice', windSpeed: windSpeed });
            var message = new Message(data);
            console.log("Sending message: " + message.getData());
            client.sendEvent(message, printResultFor('send'));
        }, 1000);
      }
    };
    ```

8. Abra a ligação ao seu centro IoT e começar a enviar mensagens:

    ```
    client.open(connectCallback);
    ```

9. Guarde e feche o ficheiro **SimulatedDevice.js** .

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (tal como um duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].


## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Numa-linha de comandos na pasta **readdevicetocloudmessages** , execute o seguinte comando para começar a monitorizar o seu centro IoT:

    ```
    node ReadDeviceToCloudMessages.js 
    ```

    ![Aplicação de cliente do node.js IoT concentrador serviço para monitorizar a nuvem de dispositivo de mensagens][7]

2. Numa-linha de comandos na pasta **simulateddevice** , execute o seguinte comando para começar a enviar dados de telemetria para o seu centro IoT:

    ```
    node SimulatedDevice.js
    ```

    ![Aplicação de cliente do node.js IoT concentrador dispositivo para enviar mensagens de nuvem de dispositivo][8]

3. O mosaico de **utilização** no [portal do Azure] [ lnk-portal] mostra o número de mensagens enviadas para o concentrador:

    ![Azure portal a utilização mosaico a mostrar o número de mensagens enviadas a IoT concentrador][43]

## <a name="next-steps"></a>Próximos passos

Neste tutorial, configurado um concentrador de IoT novo no portal do Azure e, em seguida, criada uma identidade de dispositivo no registo de identidade do concentrador. Esta identidade do dispositivo que utilizou para ativar a aplicação de dispositivo simulada enviar mensagens de nuvem de dispositivo para o concentrador. Também criou uma aplicação que apresenta as mensagens recebidas pelo concentrador. 

Para continuar a introdução ao centro de IoT e para explorar outras situações IoT, consulte:

- [Ligar o seu dispositivo][lnk-connect-device]
- [Introdução à gestão de dispositivos][lnk-device-management]
- [Introdução ao SDK do Gateway IoT][lnk-gateway-SDK]

Para saber como expandir a sua solução IoT e processar as mensagens de dispositivo para nuvem à escala, ver as [mensagens de nuvem de dispositivo de processo] [ lnk-process-d2c-tutorial] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-node-node-getstarted/runapp1.png
[8]: ./media/iot-hub-node-node-getstarted/runapp2.png
[43]: ./media/iot-hub-csharp-csharp-getstarted/usage.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-eventhubs-tutorial]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[lnk-devguide-identity]: iot-hub-devguide-identity-registry.md
[lnk-event-hubs-overview]: ../event-hubs/event-hubs-overview.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-process-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
