<properties
 pageTitle="Começar a trabalhar com gestão de dispositivos | Microsoft Azure"
 description="Este tutorial mostra-lhe como começar com gestão de dispositivos no Azure IoT concentrador"
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

# <a name="tutorial-get-started-with-device-management-preview"></a>Tutorial: Começar a trabalhar com gestão de dispositivos (pré-visualização)

## <a name="introduction"></a>Introdução
IoT nuvem aplicações podem utilizar primitivas no Azure IoT Hub, nomeadamente as duplos de dispositivo e os métodos diretos, para iniciar o remotamente e monitorizar acções de gestão de dispositivo em dispositivos.  Este artigo fornece código e orientações para como uma aplicação de nuvem IoT e um dispositivo funcionam em conjunto para iniciar e monitorizar reiniciar o computador remoto dispositivo utilizar IoT concentrador.

Para iniciar e monitorizar acções de gestão de dispositivo nos seus dispositivos através de uma aplicação baseada na nuvem, back-end remotamente, utilize primitivas Azure IoT concentrador como [duplos dispositivo] [ lnk-devtwin] e [na nuvem-a-dispositivo (C2D) métodos][lnk-c2dmethod]. Este tutorial mostra-lhe como uma aplicação de back-end e um dispositivo podem trabalhar em conjunto para permitem-lhe iniciar e monitorizar o dispositivo remoto reinicie a partir do IoT Hub.

Utilize um método de C2D para iniciar ações de gestão do dispositivo (como reiniciar o computador, repor fábrica e actualização de firmware) a partir de uma aplicação de back-end na nuvem. O dispositivo é responsável por:

- Processamento o pedido de método enviado a partir do IoT Hub.
- Iniciar a ação específica de dispositivo correspondente no dispositivo.
- Fornecer as atualizações de estado através de duplos dispositivo comunicou propriedades a IoT concentrador.

Pode utilizar uma aplicação de back-end na nuvem para executar consultas de duplos dispositivo reportar o progresso das suas ações de gestão do dispositivo.

Este tutorial mostra-lhe como para:

- Utilize o portal do Azure para criar um concentrador de IoT e criar uma identidade de dispositivo no seu centro IoT.
- Crie um dispositivo simulado que tenha um método direto, que permite aos reinicie que pode ser designada na nuvem.
- Crie uma aplicação de consola liga para o método directo reinicie no dispositivo simulado através do seu centro IoT.

No final deste tutorial, tem duas aplicações de consola Node.js:

**dmpatterns_getstarted_device.js**, que liga ao seu centro IoT com a identidade do dispositivo criada anteriormente, recebe um método de direta de reiniciar o computador, simula um reinício físico e relatórios a hora para o último reinício.

**dmpatterns_getstarted_service.js**, que chama um método direto no dispositivo simulado, apresenta a resposta e apresenta o duplos dispositivo actualizado comunicou propriedades.

Para concluir este tutorial, precisa do seguinte:

Versão node.js 0.12.x ou posterior, <br/>  [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Node.js para este tutorial no Windows ou Linux.

Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, criar uma aplicação de consola Node.js que responde a um método direto chamado por nuvem accionadores um reinício dispositivo simulada e utiliza o duplos dispositivo comunicado propriedades para activar consultas de duplos dispositivo identificar dispositivos e quando estes última reiniciado.

1. Crie uma nova pasta vazia denominada **manageddevice**.  Na pasta **manageddevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```
    npm init
    ```
    
2. Na sua-linha de comandos na pasta **manageddevice** , execute o seguinte comando para instalar o **azure-iot-device@dtpreview** pacote SDK do dispositivo e **azure-iot-device-mqtt@dtpreview** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **dmpatterns_getstarted_device.js** na pasta **manageddevice** .

4. Adicione o seguinte procedimento 'exigir' declarações no início do ficheiro **dmpatterns_getstarted_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicionar uma variável de **connectionString** e utilizá-la para criar um cliente do dispositivo.  Substitua a cadeia de ligação a cadeia de ligação do dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicionar a função seguinte para implementar o método directo no dispositivo

    ```
    var onReboot = function(request, response) {
        
        // Respond the cloud app for the direct method
        response.send(200, 'Reboot started', function(err) {
            if (!err) {
                console.error('An error occured when sending a method response:\n' + err.toString());
            } else {
                console.log('Response to method \'' + request.methodName + '\' sent successfully.');
            }
        });
        
        // Report the reboot before the physical restart
        var date = new Date();
        var patch = {
            iothubDM : {
                reboot : {
                    lastReboot : date.toISOString(),
                }
            }
        };

        // Get device Twin
        client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                console.log('twin acquired');
                twin.properties.reported.update(patch, function(err) {
                    if (err) throw err;
                    console.log('Device reboot twin state reported')
                });  
            }
        });
        
        // Add your device's reboot API for physical restart.
        console.log('Rebooting!');
    };
    ```

7. Abra a ligação ao seu centro IoT e iniciar a escuta método directo:

    ```
    client.open(function(err) {
        if (err) {
            console.error('Could not open IotHub client');
        }  else {
            console.log('Client opened.  Waiting for reboot method.');
            client.onDeviceMethod('reboot', onReboot);
        }
    });
    ```
    
8. Guarde e feche o ficheiro **dmpatterns_getstarted_device.js** .

 [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (tal como um duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].

## <a name="trigger-a-remote-reboot-on-the-device-using-a-direct-method"></a>Acionar reiniciar o computador remoto no dispositivo utilizando um método direto 

Nesta secção, crie uma aplicação de consola Node.js que inicia reiniciar o computador remoto num dispositivo utilizando um método direto e utiliza consultas de duplos dispositivo para encontrar a última vez reiniciar o computador para que o dispositivo.

1. Crie uma nova pasta vazia denominada **triggerrebootondevice**.  Na pasta **triggerrebootondevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```
    npm init
    ```
    
2. Na sua-linha de comandos na pasta **triggerrebootondevice** , execute o seguinte comando para instalar o **azure-iothub@dtpreview** pacote SDK do dispositivo e **azure-iot-device-mqtt@dtpreview** pacote:

    ```
    npm install azure-iothub@dtpreview --save
    ```
    
3. Utilizando um editor de texto, crie um novo ficheiro de **dmpatterns_getstarted_service.js** na pasta **triggerrebootondevice** .

4. Adicione o seguinte procedimento 'exigir' declarações no início do ficheiro **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações de variáveis e substitua os valores de marcador de posição:

    ```
    var connectionString = '{iothubconnectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToReboot = 'myDeviceId';
    ```
    
6. Adicione a função seguinte invocar o método de dispositivo para reinicie o dispositivo de destino:

    ```
    var startRebootDevice = function(twin) {

        var methodName = "reboot";
        
        var methodParams = {
            methodName: methodName,
            payload: null,
            timeoutInSeconds: 30
        };
        
        client.invokeDeviceMethod(deviceToReboot, methodParams, function(err, result) {
            if (err) { 
                console.error("Direct method error: "+err.message);
            } else {
                console.log("Successfully invoked the device to reboot.");  
            }
        });
    };
    ```

7. Adicione a função seguinte para consultar o dispositivo e obter a última vez que reiniciar o computador:

    ```
    var queryTwinLastReboot = function() {

        registry.getTwin(deviceToReboot, function(err, twin){

            if (twin.properties.reported.iothubDM != null)
            {
                if (err) {
                    console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
                } else {
                    var lastRebootTime = twin.properties.reported.iothubDM.reboot.lastReboot;
                    console.log('Last reboot time: ' + JSON.stringify(lastRebootTime, null, 2));
                }
            } else 
                console.log('Waiting for device to report last reboot time.');
        });
    };
    ```
    
8. Adicione o seguinte código para ligar as funções que irão acionar o método direta de reiniciar o computador e a consulta pela última vez reiniciar o computador:

    ```
    startRebootDevice();
    setInterval(queryTwinLastReboot, 2000);
    ```
    
9. Guarde e feche o ficheiro **dmpatterns_getstarted_service.js** .

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Na linha de comandos na pasta **manageddevice** , execute o seguinte comando para começar a ouvir referentes ao método direta de reiniciar o computador.

    ```
    node dmpatterns_getstarted_device.js
    ```

2. No comando pedido na pasta **triggerrebootondevice** , execute o seguinte comando para acionar a reiniciar o computador remoto e uma consulta para duplos o dispositivo localizar o último reinicie o tempo.

    ```
    node dmpatterns_getstarted_service.js
    ```

3. Irá ver reagem para o método direta imprimindo as a mensagem

## <a name="customize-and-extend-the-device-management-actions"></a>Personalizar ou expandir o dispositivo acções de gestão

As soluções IoT podem expanda o conjunto definido de padrões de gestão do dispositivo ou padrões personalizados através da utilização da duplos de dispositivo e C2D primitivas de método de ativar. Outras ações de gestão de dispositivo exemplos de reposição de fábrica do mesmo, actualização de firmware, atualização de software, gestão de energia, gestão de conectividade e de rede e encriptação de dados.

## <a name="device-maintenance-windows"></a>Windows de manutenção de dispositivo

Normalmente, pode configura dispositivos para executar ações uma vez que minimiza interrupções e tempo de inatividade.  As janelas de manutenção do dispositivo são um padrão utilizado com frequência para definir a hora quando um dispositivo deve atualizar a sua configuração. As soluções de back-end podem utilizar as propriedades pretendidas de duplos o dispositivo para definir e ativar uma política no seu dispositivo que permite uma janela de manutenção. Quando um dispositivo recebe a política de janela de manutenção, pode utilizar a propriedade denunciada de duplos o dispositivo para comunicar o estado da política. A aplicação de back-end, em seguida, pode utilizar consultas de duplos dispositivo para certificar-se para conformidade de dispositivos e cada política.

## <a name="next-steps"></a>Próximos passos

Neste tutorial, que utilizou um método direto para acionar reiniciar o computador remoto num dispositivo, utilizado duplos o dispositivo denunciadas propriedades para comunicar as últimas reinicie o dispositivo e consultado para duplos o dispositivo descobrir a última vez reiniciar o computador do dispositivo a partir da nuvem.

Para continuar a introdução ao IoT concentrador e padrões de gestão de dispositivo como remote sobre a actualização de firmware ar, consulte:

[Tutorial: Como efetuar uma atualização de firmware][lnk-fwupdate]

Para saber como pode aumentar a sua IoT chamadas de método de solução e agenda em múltiplos dispositivos, consulte a [agenda e tarefas de implementação de difusão] [ lnk-tutorial-jobs] tutorial.

Para continuar a introdução ao centro de IoT, consulte o artigo [introdução com o SDK do Gateway IoT][lnk-gateway-SDK].


<!-- images and links -->
[img-output]: media/iot-hub-get-started-with-dm/image6.png
[img-dm-ui]: media/iot-hub-get-started-with-dm/dmui.png

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-fwupdate]: iot-hub-firmware-update.md
[Azure portal]: https://portal.azure.com/
[Using resource groups to manage your Azure resources]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx