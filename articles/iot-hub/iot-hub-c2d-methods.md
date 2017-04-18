<properties
 pageTitle="Utilizar os métodos de diretos | Microsoft Azure"
 description="Este tutorial mostra-lhe como utilizar métodos diretos"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/05/2016"
 ms.author="nberdy"/>

# <a name="tutorial-use-direct-methods"></a>Tutorial: Utilizar os métodos de diretos

## <a name="introduction"></a>Introdução

Azure IoT concentrador é um serviço totalmente gerido que permite fiável e seguras bidirecional as comunicações entre milhões de IoT dispositivos e uma aplicação back-end. Tutoriais anterior ([Introdução ao centro de IoT] e [enviar mensagens de dispositivo de nuvem com IoT concentrador]) ilustram a nuvem de dispositivo e na nuvem-a-dispositivo mensagens funcionalidade básica do IoT concentrador. Concentrador IoT também dá-lhe a capacidade de invocar não duradouros métodos em dispositivos a partir da nuvem. Métodos representam uma interação pedido resposta com um dispositivo semelhante a uma chamada de HTTP, uma vez que estes teve êxito ou falharem imediatamente (depois de um limite de tempo especificado pelo utilizador) permitir que o utilizador saber o estado da chamada. [Invocar um método direto num dispositivo] [ lnk-devguide-methods] descreve métodos mais detalhadamente e oferece orientações sobre quando utilizar métodos versus mensagens de dispositivo de nuvem.

Este tutorial mostra-lhe como para:

- Utilize o portal do Azure para criar um concentrador de IoT e criar uma identidade de dispositivo no seu centro IoT.
- Crie um dispositivo simulado que tenha um método direto que pode ser designado na nuvem.
- Crie uma aplicação de consola chama um método direto no dispositivo simulado através do seu centro IoT.

> [AZURE.NOTE] Neste momento, os métodos diretos são acessíveis apenas a partir de dispositivos que se ligam a concentrador IoT utilizando o protocolo MQTT. Consulte para [suportar MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter a aplicação de dispositivo existente utilizar MQTT.

No final deste tutorial, tem duas aplicações de consola Node.js:

* **CallMethodOnDevice.js**, que liga um método no dispositivo simulado e apresenta a resposta.
* **SimulatedDevice.js**, que liga-se ao seu centro IoT com a identidade do dispositivo criada anteriormente e responde para o método chamado na nuvem.

> [AZURE.NOTE] O artigo [IoT concentrador SDK] [ lnk-hub-sdks] fornece informações sobre as vários SDK, que pode utilizar para criar ambas as aplicações para ser executado em dispositivos e back-end sua solução.

Para concluir este tutorial, precisa do seguinte:

+ Versão node.js 0.10.x ou posterior.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, crie uma aplicação de consola Node.js que responde a um método chamado na nuvem.

1. Crie uma nova pasta vazia denominada **simulateddevice**. Na pasta **simulateddevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **simulateddevice** , execute o seguinte comando para instalar o pacote de dispositivo SDK de **dispositivo de iot azure** e **azure-iot dispositivo mqtt** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **SimulatedDevice.js** na pasta **simulateddevice** .

4. Adicione o seguinte `require` declarações no início do ficheiro **SimulatedDevice.js** :

    ```
    'use strict';

    var Mqtt = require('azure-iot-device-mqtt').Mqtt;
    var DeviceClient = require('azure-iot-device').Client;
    ```

5. Adicionar uma variável de **connectionString** e utilizá-la para criar um cliente do dispositivo. Substitua **{cadeia de ligação de dispositivo}** com a cadeia de ligação gerado na secção *criar uma identidade de dispositivo* :

    ```
    var connectionString = '{device connection string}';
    var client = DeviceClient.fromConnectionString(connectionString, Mqtt);
    ```

6. Adicione a função seguinte para implementar o método no dispositivo:

    ```
    function onWriteLine(request, response) {
        console.log(request.payload);

        response.send(200, 'Input was written to log.', function(err) {
            if(err) {
                console.error('An error ocurred when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.' );
            }
        });
    }
    ```

7. Abrir a ligação ao seu IoT concentrador e comece a iniciar a escuta método:

    ```
    client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');
            client.onDeviceMethod('writeLine', onWriteLine);
        }
    });
    ```

8. Guarde e feche o ficheiro **SimulatedDevice.js** .

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (por exemplo, repetir de ligação), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].

## <a name="call-a-method-on-a-device"></a>Chamar um método num dispositivo

Nesta secção, crie uma aplicação de consola do Node.js que liga um método no dispositivo simulado e, em seguida, apresenta a resposta.

1. Crie uma nova pasta vazia denominada **callmethodondevice**. Na pasta **callmethodondevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **callmethodondevice** , execute o seguinte comando para instalar o pacote do **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um ficheiro de **CallMethodOnDevice.js** na pasta **callmethodondevice** .

4. Adicione o seguinte `require` declarações no início do ficheiro **CallMethodOnDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iothub').Client;
    ```

5. Adicionar a declaração de variável seguinte e substitua o valor de marcador de posição com a cadeia de ligação para o seu centro IoT:

    ```
    var connectionString = '{iothub connection string}';
    var methodName = 'writeLine';
    var deviceId = 'myDeviceId';
    ```

6. Crie o cliente para abrir a ligação para o seu centro IoT.

    ```
    var client = Client.fromConnectionString(connectionString);
    ```
    
7. Adicione a função seguinte invocar o método de dispositivo e imprimir a resposta na consola do dispositivo:

    ```
    var methodParams = {
        methodName: methodName,
        payload: 'a line to be written',
        timeoutInSeconds: 30
    };

    client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
        if (err) {
            console.error('Failed to invoke method \'' + methodName + '\': ' + err.message);
        } else {
            console.log(methodName + ' on ' + deviceId + ':');
            console.log(JSON.stringify(result, null, 2));
        }
    });
    ```

7. Guarde e feche o ficheiro **CallMethodOnDevice.js** .

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Numa-linha de comandos na pasta **simulateddevice** , execute o seguinte comando para começar a escutar para chamadas de método a partir do seu centro IoT:

    ```
    node SimulatedDevice.js
    ```

    ![][7]
    
2. Numa-linha de comandos na pasta **callmethodondevice** , execute o seguinte comando para começar a monitorizar o seu centro IoT:

    ```
    node CallMethodOnDevice.js 
    ```

    ![][8]
    
3. Irá ver o dispositivo reagir para o método imprimindo as a mensagem e a aplicação denominada a apresentação de método a resposta do dispositivo:

    ![][9]
    
## <a name="next-steps"></a>Próximos passos

Neste tutorial, configurado um concentrador de IoT novo no portal do Azure e, em seguida, criada uma identidade de dispositivo no registo de identidade do concentrador. Esta identidade do dispositivo que utilizou para ativar a aplicação de dispositivo simulada responder a métodos chamados na nuvem. Também criou uma aplicação que invoca métodos no dispositivo e apresenta a resposta do dispositivo. 

Para continuar a introdução ao centro de IoT e para explorar outras situações IoT, consulte:

- [Introdução ao centro de IoT]
- [Agendar tarefas em múltiplos dispositivos][lnk-devguide-jobs]

Para saber como pode aumentar a sua IoT chamadas de método de solução e agenda em múltiplos dispositivos, consulte a [agenda e tarefas de implementação de difusão] [ lnk-tutorial-jobs] tutorial.

<!-- Images. -->
[7]: ./media/iot-hub-c2d-methods/run-simulated-device.png
[8]: ./media/iot-hub-c2d-methods/run-callmethodondevice.png
[9]: ./media/iot-hub-c2d-methods/methods-output.png

<!-- Links -->
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-portal]: https://portal.azure.com/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-devguide-methods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[Enviar mensagens de nuvem-a-dispositivo com IoT concentrador]: iot-hub-csharp-csharp-c2d.md
[Process Device-to-Cloud messages]: iot-hub-csharp-csharp-process-d2c.md
[Introdução ao centro de IoT]: iot-hub-node-node-getstarted.md
