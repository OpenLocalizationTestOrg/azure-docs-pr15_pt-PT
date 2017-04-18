<properties
 pageTitle="Como agendar trabalhos | Microsoft Azure"
 description="Este tutorial mostra-lhe como agendar tarefas"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="tutorial-schedule-and-broadcast-jobs-preview"></a>Tutorial: Agendar e difundir tarefas (pré-visualização)

## <a name="introduction"></a>Introdução
Azure IoT concentrador é um serviço totalmente gerido que permite uma aplicação back-end criar e controlar tarefas que agendar e atualizar milhões de dispositivos.  Tarefas podem ser utilizadas para as seguintes ações:

- Atualizar as propriedades do dispositivo duplos pretendido
- Atualizar etiquetas duplos de dispositivo
- Invocar métodos de dispositivo de nuvem

Em termos conceptuais, uma tarefa é moldado uma das seguintes ações e controla o progresso da execução de um conjunto de dispositivos, que é definida por uma consulta de duplos.  Por exemplo, utilizando uma tarefa de back-end uma aplicação pode invocar um método de reiniciar o computador em 10.000 dispositivos, especificado por uma consulta de duplos e agendada uma vez futuras.  A aplicação, em seguida, pode controlar o progresso medida que cada um dos dispositivos, receber e executar o método de reiniciar o computador.

Saiba mais sobre cada uma das seguintes funcionalidades nestes artigos:

- Duplos de dispositivo e propriedades: [Introdução ao duplos] [ lnk-get-started-twin] e [Tutorial: como utilizar as propriedades de duplos][lnk-twin-props]
- Métodos de dispositivo de nuvem: [Guia do programador - métodos diretas] [ lnk-dev-methods] e [Tutorial: C2D métodos][lnk-c2d-methods]

Este tutorial mostra-lhe como para:

- Crie um dispositivo simulado que tenha um método direto, que permite aos lockDoor que pode ser designada pela aplicação novamente fim.
- Crie uma aplicação de consola que liga para o método directo lockDoor no dispositivo simulado utilizando uma tarefa e atualiza as propriedades de duplos pretendido utilizando uma tarefa de dispositivo.

No final deste tutorial, tem duas aplicações de consola Node.js:

**simDevice.js**, que liga-se ao seu centro IoT com a identidade do dispositivo e recebe um método direta lockDoor.

**scheduleJobService.js**, que liga um método direto no dispositivo simulado e atualizar as propriedades de disired a duplos utilizando uma tarefa.

Para concluir este tutorial, precisa do seguinte:

Versão node.js 0.12.x ou posterior, <br/>  [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Node.js para este tutorial no Windows ou Linux.

Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, criar uma aplicação de consola Node.js que responde a um método direto chamado por nuvem accionadores um reinício dispositivo simulada e utiliza o duplos dispositivo comunicado propriedades para activar consultas de duplos dispositivo identificar dispositivos e quando estes última reiniciado.

1. Crie uma nova pasta vazia denominada **simDevice**.  Na pasta **simDevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```
    npm init
    ```
    
2. Na sua-linha de comandos na pasta **simDevice** , execute o seguinte comando para instalar o pacote de dispositivo SDK de **dispositivo de iot azure** e **azure-iot dispositivo mqtt** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **simDevice.js** na pasta **simDevice** .

4. Adicione o seguinte procedimento 'exigir' declarações no início do ficheiro **simDevice.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicionar uma variável de **connectionString** e utilizá-la para criar um cliente do dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId={yourdeviceid};SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione a função seguinte para processar o método de lockDoor.

    ```
    var onLockDoor = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        console.log('Locking Door!');
    };
    ```

7. Adicione o seguinte código para registar a alça para o método de lockDoor.

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not connect to IotHub client.');
        }  else {
            console.log('Client connected to IoT Hub.  Waiting for reboot direct method.');
            client.onDeviceMethod('lockDoor', onLockDoor);
        }
    });
    ```
    
8. Guarde e feche o ficheiro **simDevice.js** .

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (tal como um duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].

## <a name="schedule-jobs-for-calling-a-direct-method-and-updating-a-twins-properties"></a>Agendar tarefas para chamar um método direto e atualizar as propriedades de um duplos

Nesta secção, criar uma aplicação de consola Node.js que inicia uma lockDoor remoto num dispositivo utilizando um método direto e atualizar as propriedades de duplos.

1. Crie uma nova pasta vazia denominada **scheduleJobService**.  Na pasta **scheduleJobService** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```
    npm init
    ```
    
2. Na sua-linha de comandos na pasta **scheduleJobService** , execute o seguinte comando para instalar o pacote de dispositivo SDK **azure iothub** e **azure-iot dispositivo mqtt** pacote:

    ```
    npm install azure-iothub@dtpreview uuid --save
    ```
    
3. Utilizando um editor de texto, crie um novo ficheiro de **scheduleJobService.js** na pasta **scheduleJobService** .

4. Adicione o seguinte procedimento 'exigir' declarações no início do ficheiro **dmpatterns_gscheduleJobServiceetstarted_service.js** :

    ```
    'use strict';

    var uuid = require('uuid');
    var JobClient = require('azure-iothub').JobClient;
    ```

5. Adicione as seguintes declarações de variáveis e substitua os valores de marcador de posição:

    ```
    var connectionString = '{iothubconnectionstring}';
    var deviceArray = ['myDeviceId'];
    var startTime = new Date();
    var maxExecutionTimeInSeconds =  3600;
    var jobClient = JobClient.fromConnectionString(connectionString);
    ```
    
6. Adicione a função seguinte que será utilizada para monitorizar a execução da tarefa:

    ```
    function monitorJob (jobId, callback) {
        var jobMonitorInterval = setInterval(function() {
            jobClient.getJob(jobId, function(err, result) {
            if (err) {
                console.error('Could not get job status: ' + err.message);
            } else {
                console.log('Job: ' + jobId + ' - status: ' + result.status);
                if (result.status === 'completed' || result.status === 'failed' || result.status === 'cancelled') {
                clearInterval(jobMonitorInterval);
                callback(null, result);
                }
            }
            });
        }, 5000);
    }
    ```

7. Adicione o seguinte código para agendar a tarefa que liga para o método de dispositivo:

    ```
    var methodParams = {
        methodName: 'lockDoor',
        payload: null,
        timeoutInSeconds: 45
    };

    var methodJobId = uuid.v4();
    console.log('scheduling Device Method job with id: ' + methodJobId);
    jobClient.scheduleDeviceMethod(methodJobId,
                                deviceArray,
                                methodParams,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule device method job: ' + err.message);
        } else {
            monitorJob(methodJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor device method job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
        
8. Adicione o seguinte código para agendar a tarefa para atualizar o duplos:

    ```
    var twinPatch = {
        etag: '*',
        desired: {
            building: '43',
            floor: 3
        }
    };

    var twinJobId = uuid.v4();

    console.log('scheduling Twin Update job with id: ' + twinJobId);
    jobClient.scheduleTwinUpdate(twinJobId,
                                deviceArray,
                                twinPatch,
                                startTime,
                                maxExecutionTimeInSeconds,
                                function(err) {
        if (err) {
            console.error('Could not schedule twin update job: ' + err.message);
        } else {
            monitorJob(twinJobId, function(err, result) {
                if (err) {
                    console.error('Could not monitor twin update job: ' + err.message);
                } else {
                    console.log(JSON.stringify(result, null, 2));
                }
            });
        }
    });
    ```
    
9. Guarde e feche o ficheiro **scheduleJobService.js** .

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Na linha de comandos na pasta **simDevice** , execute o seguinte comando para começar a ouvir referentes ao método direta de reiniciar o computador.

    ```
    node simDevice.js
    ```

2. No comando pedido na pasta **scheduleJobService** , execute o seguinte comando para acionar a reiniciar o computador remoto e uma consulta para duplos o dispositivo localizar o último reinicie o tempo.

    ```
    node scheduleJobService.js
    ```

3. Irá ver o resultado de dispositivo e aplicações de back-end.


## <a name="next-steps"></a>Próximos passos

Neste tutorial, uma tarefa que utilizou para agendar um método direto para um dispositivo e a atualização das propriedades do duplos de dispositivo.

Para continuar a introdução ao IoT concentrador e padrões de gestão de dispositivo como remote sobre a actualização de firmware ar, consulte:

[Tutorial: Como efetuar uma atualização de firmware][lnk-fwupdate]

Para continuar a introdução ao centro de IoT, consulte o artigo [introdução com o SDK do Gateway IoT][lnk-gateway-SDK].

[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-props]: iot-hub-node-node-twin-how-to-configure.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-fwupdate]: iot-hub-firmware-update.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx