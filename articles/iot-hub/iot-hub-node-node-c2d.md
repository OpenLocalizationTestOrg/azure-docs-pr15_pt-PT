<properties
    pageTitle="Enviar mensagens de dispositivo de nuvem com IoT concentrador | Microsoft Azure"
    description="Siga este tutorial para aprender a enviar mensagens de dispositivo de nuvem utilizando o Azure IoT concentrador com Java."
    services="iot-hub"
    documentationCenter="nodejs"
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="javascript"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/23/2016"
     ms.author="dobett"/>

# <a name="tutorial-how-to-send-cloud-to-device-messages-with-iot-hub-and-nodejs"></a>Tutorial: Como enviar mensagens de dispositivo de nuvem com IoT concentrador e Node.js

[AZURE.INCLUDE [iot-hub-selector-c2d](../../includes/iot-hub-selector-c2d.md)]

## <a name="introduction"></a>Introdução

Azure IoT concentrador é um serviço totalmente gerido que ajuda a ativar fiáveis e seguras as bidirecional comunicações entre milhões de IoT dispositivos e de fim de uma aplicação novamente. O tutorial de [Introdução ao centro de IoT] mostra como criar um concentrador de IoT, aprovisionar uma identidade de dispositivo no mesmo e o código de um dispositivo simulado que envia mensagens de dispositivo a nuvem.

Neste tutorial constrói no [artigo Introdução ao IoT concentrador]. Mostra-lhe como para:

- A partir da sua aplicação nuvem back-end, envie mensagens de dispositivo de nuvem para um único dispositivo através de IoT concentrador.
- Receba mensagens de dispositivo de nuvem num dispositivo.
- A partir do seu nuvem aplicação back-end, pedir a confirmação de entrega (*comentários*) para mensagens enviadas para um dispositivo a partir do IoT Hub.

Pode encontrar mais informações sobre mensagens de dispositivo de nuvem no [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

No final deste tutorial, executa duas aplicações de consola Node.js:

* **SimulatedDevice**, uma versão modificada da aplicação criada no [artigo Introdução ao centro de IoT], que liga-se ao seu centro IoT e recebe mensagens de dispositivo de nuvem.
* **SendCloudToDeviceMessage**, que envia uma mensagem de dispositivo de nuvem para o dispositivo simulado através de IoT concentrador e, em seguida, recebe respetiva confirmação de entrega.

> [AZURE.NOTE] Concentrador IoT tem suporte SDK para vários idiomas (incluindo C, Java e Javascript) através do Azure IoT dispositivo SDK e plataformas de dispositivos. Para obter instruções passo a passo sobre como ligar o seu dispositivo para código neste tutorial e geralmente Azure IoT Hub, consulte o [Centro de programadores do Azure IoT].

Para concluir este tutorial, precisa do seguinte:

+ Versão node.js 0.10.x ou posterior.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

## <a name="receive-messages-on-the-simulated-device"></a>Receber mensagens no dispositivo simulada

Nesta secção, pode modifica a aplicação de dispositivo simulada que criou no [artigo Introdução ao centro de IoT] para receber mensagens de dispositivo de nuvem a partir do hub a IoT.

1. Utilizando um editor de texto, abra o ficheiro de SimulatedDevice.js.

2. Modificar a função **connectCallback** para processar as mensagens enviadas a partir do IoT Hub. Neste exemplo, o dispositivo sempre invoca a função **completa** para notificar IoT concentrador que esta tenha processada a mensagem. A nova versão da função **connectCallback** tem o seguinte aspeto:

    ```
    var connectCallback = function (err) {
      if (err) {
        console.log('Could not connect: ' + err);
      } else {
        console.log('Client connected');
        client.on('message', function (msg) {
          console.log('Id: ' + msg.messageId + ' Body: ' + msg.data);
          client.complete(msg, printResultFor('completed'));
        });
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

    > [AZURE.NOTE] Se utilizar HTTP em vez de MQTT ou AMQP como o transporte, verifica a instância **DeviceClient** para mensagens a partir do Hub de IoT com pouca frequência (inferior a cada minutos 25). Para mais informações sobre as diferenças entre suporte MQTT, AMQP e HTTP e IoT concentrador limitação, consulte o [Guia de programador do concentrador de IoT][IoT Hub Developer Guide - C2D].

## <a name="send-a-cloud-to-device-message"></a>Enviar uma mensagem de dispositivo de nuvem

Nesta secção, crie uma aplicação de consola Node.js que envia mensagens de dispositivo de nuvem para a aplicação de dispositivo simulada. Tem o dispositivo de Id do dispositivo que adicionou no tutorial [Introdução ao IoT concentrador] . Também precisa da cadeia de ligação para o seu centro de IoT que pode encontrar no [portal do Azure].

1. Crie uma pasta vazia denominada **sendcloudtodevicemessage**. Na pasta **sendcloudtodevicemessage** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **sendcloudtodevicemessage** , execute o seguinte comando para instalar o pacote do **azure iothub** :

    ```
    npm install azure-iothub --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **SendCloudToDeviceMessage.js** na pasta **sendcloudtodevicemessage** .

4. Adicione o seguinte `require` declarações no início do ficheiro **SendCloudToDeviceMessage.js** :

    ```
    'use strict';
    
    var Client = require('azure-iothub').Client;
    var Message = require('azure-iot-common').Message;
    ```

5. Adicione o código seguinte ao ficheiro de **SendCloudToDeviceMessage.js** . Substitua o valor de marcador de posição da cadeia de ligação a cadeia de ligação para o concentrador de IoT que criou no tutorial [Introdução ao IoT concentrador] . Substitua o marcador de posição de dispositivo de destino com o id do dispositivo do dispositivo que adicionou no tutorial [Introdução ao centro de IoT] :

    ```
    var connectionString = '{iot hub connection string}';
    var targetDevice = '{device id}';

    var serviceClient = Client.fromConnectionString(connectionString);
    ```

6. Adicione a função seguinte para imprimir os resultados de operação na consola:

    ```
    function printResultFor(op) {
      return function printResult(err, res) {
        if (err) console.log(op + ' error: ' + err.toString());
        if (res) console.log(op + ' status: ' + res.constructor.name);
      };
    }
    ```

7. Adicione a função seguinte para imprimir mensagens de comentários de entrega na consola:

    ```
    function receiveFeedback(err, receiver){
      receiver.on('message', function (msg) {
        console.log('Feedback message:')
        console.log(msg.getData().toString('utf-8'));
      });
    }
    ```

8. Adicione o seguinte código para enviar uma mensagem para o seu dispositivo e processe a mensagem de comentários quando o dispositivo reconhece a mensagem na nuvem-a-dispositivo:

    ```
    serviceClient.open(function (err) {
      if (err) {
        console.error('Could not connect: ' + err.message);
      } else {
        console.log('Service client connected');
        serviceClient.getFeedbackReceiver(receiveFeedback);
        var message = new Message('Cloud to device message.');
        message.ack = 'full';
        message.messageId = "My Message ID";
        console.log('Sending message: ' + message.getData());
        serviceClient.send(targetDevice, message, printResultFor('send'));
      }
    });
    ```

7. Guarde e feche o ficheiro **SendCloudToDeviceMessage.js** .

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Na linha de comandos na pasta **simulateddevice** , execute o seguinte comando para enviar telemetria para IoT concentrador e para ouvir para mensagens de dispositivo de nuvem:

    ```
    node SimulatedDevice.js 
    ```

    ![Executar a aplicação de dispositivo simulada][img-simulated-device]

2. Numa-linha de comandos na pasta **sendcloudtodevicemessage** , execute o seguinte comando para enviar uma mensagem de dispositivo de nuvem e aguarde que a comentários de confirmação:

    ```
    node SendCloudToDeviceMessage.js 
    ```

    ![Executar a aplicação para enviar o comando c2d][img-send-command]

    > [AZURE.NOTE] Para saké do simplificar, este tutorial não implementa qualquer política de repetir. No código de produção, deverá implementar políticas de repetir (como duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves].

## <a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como enviar e receber mensagens de dispositivo de nuvem. 

Para ver exemplos de soluções de ponto a ponto completas que utilizam IoT concentrador, consulte [o conjunto de aplicações do Azure IoT].

Para saber mais sobre como desenvolver soluções com IoT concentrador, consulte o [Guia de programador do IoT concentrador].

<!-- Images -->
[img-simulated-device]: media/iot-hub-node-node-c2d/receivec2d.png
[img-send-command]:  media/iot-hub-node-node-c2d/sendc2d.png

<!-- Links -->

[Introdução ao centro de IoT]: iot-hub-node-node-getstarted.md
[IoT Hub Developer Guide - C2D]: iot-hub-devguide-messaging.md
[Guia do Programador de IoT concentrador]: iot-hub-devguide.md
[Centro de programadores do Azure IoT]: http://www.azure.com/develop/iot
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[Processamento de breves de falhas]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Portal do Azure]: https://portal.azure.com
[Azure IoT conjunto de aplicações]: https://azure.microsoft.com/documentation/suites/iot-suite/