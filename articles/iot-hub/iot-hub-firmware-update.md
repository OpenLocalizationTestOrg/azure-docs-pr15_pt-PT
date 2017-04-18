<properties
 pageTitle="Como efetuar uma atualização firmware | Microsoft Azure"
 description="Este tutorial mostra-lhe como efetuar uma atualização de firmware"
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

# <a name="tutorial-how-to-do-a-firmware-update-preview"></a>Tutorial: Como o pode fazer um firmware atualização (pré-visualização)

## <a name="introduction"></a>Introdução
[Começar a trabalhar com gestão de dispositivos] [ lnk-dm-getstarted] tutorial, viu como utilizar o [duplos dispositivo] [ lnk-devtwin] e [métodos de (C2D) na nuvem para dispositivo] [ lnk-c2dmethod] primitivas reiniciar um dispositivo remotamente. Neste tutorial utiliza as mesmas primitivas de IoT concentrador e dá-lhe e mostra-lhe como efetuar uma atualização de ponto a ponto simulada firmware.  Este padrão é utilizado na implementação de atualizar firmware para a amostra de dispositivo Intel Edison.

Este tutorial mostra-lhe como para:

- Crie uma aplicação de consola liga para o método directo firmwareUpdate no dispositivo simulado através do seu centro IoT.
- Crie um dispositivo simulado que implementa um método de direta firmwareUpdate que acede através de um processo em várias fases que aguarda para transferir a imagem firmware, transferências a imagem firmware e, por fim aplica-se a imagem de firmware ésimo.  Em toda a executar as utilizações de dispositivo de duplos comunicado propriedades para atualizar a cada fase de progresso.

No final deste tutorial, tem duas aplicações de consola Node.js:

**dmpatterns_fwupdate_service.js**, que liga um método direto no dispositivo simulado, apresenta a resposta e periodicamente (todas as 500ms) apresenta o duplos dispositivo actualizado comunicado propriedades.

**dmpatterns_fwupdate_device.js**, que liga ao seu centro IoT com a identidade do dispositivo criada anteriormente, recebe um método direta firmwareUpdate, é executado através de um processo para simular uma atualização de firmware incluindo múltiplos Estado: a aguardar que a transferência de imagem, transferir a nova imagem e aplicar finalmente a imagem.


Para concluir este tutorial, precisa do seguinte:

Versão node.js 0.12.x ou posterior, <br/>  [Preparar o seu ambiente de desenvolvimento] [ lnk-dev-setup] descreve como instalar Node.js para este tutorial no Windows ou Linux.

Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

Siga o artigo [começar a trabalhar com gestão de dispositivos](iot-hub-device-management-get-started.md) para criar o seu centro IoT e obter a cadeia de ligação.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-a-simulated-device-app"></a>Criar uma aplicação do dispositivo simulada

Nesta secção, criar uma aplicação de consola Node.js que responde a um método direto chamado por nuvem accionadores uma atualização de firmware dispositivo simulada e utiliza o duplos dispositivo comunicado propriedades para activar consultas de duplos dispositivo identificar dispositivos e quando estes última reiniciado.

1. Crie uma nova pasta vazia denominada **manageddevice**.  Na pasta **manageddevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```
    npm init
    ```
    
2. Na sua-linha de comandos na pasta **manageddevice** , execute o seguinte comando para instalar o **azure-iot-device@dtpreview** pacote SDK do dispositivo e **azure-iot-device-mqtt@dtpreview** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **dmpatterns_fwupdate_device.js** na pasta **manageddevice** .

4. Adicione o seguinte procedimento 'exigir' declarações no início do ficheiro **dmpatterns_fwupdate_device.js** :

    ```
    'use strict';

    var Client = require('azure-iot-device').Client;
    var Protocol = require('azure-iot-device-mqtt').Mqtt;
    ```

5. Adicionar uma variável de **connectionString** e utilizá-la para criar um cliente do dispositivo.  

    ```
    var connectionString = 'HostName={youriothostname};DeviceId=myDeviceId;SharedAccessKey={yourdevicekey}';
    var client = Client.fromConnectionString(connectionString, Protocol);
    ```

6. Adicione o seguinte função que será utilizada para atualizar duplos comunicado propriedades

    ```
    var reportFWUpdateThroughTwin = function(twin, firmwareUpdateValue) {
      var patch = {
          iothubDM : {
            firmwareUpdate : firmwareUpdateValue
          }
      };

      twin.properties.reported.update(patch, function(err) {
        if (err) throw err;
        console.log('twin state reported')
      });
    };
    ```
    
7. Adicione as seguintes funções que vai simular a transferência e aplicar da imagem firmware.

    ```
    var simulateDownloadImage = function(imageUrl, callback) {
      var error = null;
      var image = "[fake image data]";
      
      console.log("Downloading image from " + imageUrl);
      
      callback(error, image);
    }

    var simulateApplyImage = function(imageData, callback) {
      var error = null;
      
      if (!imageData) {
        error = {message: 'Apply image failed because of missing image data.'};
      }
      
      callback(error);
    }
    ```

8. Adicione o seguinte função que irão atualizar o estado de actualização de firmware através de duplos comunicado propriedades à espera para transferir.  Normalmente, dispositivos são informados sobre uma atualização de segurança e um administrador definidos política causas o dispositivo para iniciar a transferência e aplicar a atualização.  Este é o local onde a lógica para permitir essa política seria executado.  Para simplificar, podemos está atrasar para 4 segundos e continuar a transferir a firmware imagem. 

    ```
    var waitToDownload = function(twin, fwPackageUriVal, callback) {
      var now = new Date();
      
      reportFWUpdateThroughTwin(twin, {
        fwPackageUri: fwPackageUriVal,
        status: 'waiting',
        error : null,
        startedWaitingTime : now.toISOString()
      });
      setTimeout(callback, 4000);
    };
    ```
    
9. Adicione o seguinte função que irão atualizar o estado de actualização de firmware através de duplos comunicado propriedades para transferir a imagem firmware.  -A seguir através da simulação de transferência de uma firmware e finalmente atualiza o estado de actualização de firmware para o informar de uma transferência sucesso ou falha.

    ```
    var downloadImage = function(twin, fwPackageUriVal, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'downloading',
      });
      
      setTimeout(function() {
        // Simulate download
        simulateDownloadImage(fwPackageUriVal, function(err, image) {
          
          if (err)
          {
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadfailed',
              error: {
                code: error_code,
                message: error_message,
              }
            });
          }
          else {        
            reportFWUpdateThroughTwin(twin, {
              status: 'downloadComplete',
              downloadCompleteTime: now.toISOString(),
            });
            
            setTimeout(function() { callback(image); }, 4000);   
          }
        });
        
      }, 4000);
    }
    ```
    
10. Adicione o seguinte função que irão atualizar o estado de actualização de firmware através de duplos comunicado propriedades para aplicar a imagem firmware.  -A seguir através da simulação de uma aplicação da imagem firmware e finalmente atualiza o estado de actualização de firmware para o informar de um sucesso aplicar ou falha.

    ```
    var applyImage = function(twin, imageData, callback) {
      var now = new Date();   
      
      reportFWUpdateThroughTwin(twin, {
        status: 'applying',
        startedApplyingImage : now.toISOString()
      });
      
      setTimeout(function() {
        
        // Simulate apply firmware image
        simulateApplyImage(imageData, function(err) {
          if (err) {
            reportFWUpdateThroughTwin(twin, {
              status: 'applyFailed',
              error: {
                code: err.error_code,
                message: err.error_message,
              }
            });
          } else { 
            reportFWUpdateThroughTwin(twin, {
              status: 'applyComplete',
              lastFirmwareUpdate: now.toISOString()
            });    
            
          }
        });
        
        setTimeout(callback, 4000);
        
      }, 4000);
    }
    ```

11. Adicione o seguinte functoin que processar o método de firmwareUpdate e iniciar o processo de atualização em várias fases firmware.

    ```
    var onFirmwareUpdate = function(request, response) {
            
      // Respond the cloud app for the direct method
      response.send(200, 'FirmwareUpdate started', function(err) {
        if (!err) {
          console.error('An error occured when sending a method response:\n' + err.toString());
        } else {
          console.log('Response to method \'' + request.methodName + '\' sent successfully.');
        }
      });

      // Get the parameter from the body of the method request
      var fwPackageUri = JSON.parse(request.payload).fwPackageUri;

      // Obtain the device twin
      client.getTwin(function(err, twin) {
        if (err) {
          console.error('Could not get device twin.');
        } else {
          console.log('Device twin acquired.');
          
          // Start the multi-stage firmware update
          waitToDownload(twin, fwPackageUri, function() {
            downloadImage(twin, fwPackageUri, function(imageData) {
              applyImage(twin, imageData, function() {});    
            });  
          });

        }
      });
    }
    ```
    
12. Por fim, adicione o seguinte código que liga a IoT concentrador como um dispositivo, 

    ```
    client.open(function(err) {
      if (err) {
        console.error('Could not connect to IotHub client');
      }  else {
        console.log('Client connected to IoT Hub.  Waiting for firmwareUpdate direct method.');
      }
      
      client.onDeviceMethod('firmwareUpdate', onFirmwareUpdate(request, response));
    });
    ```

> [AZURE.NOTE] Para manter as coisas simples, este tutorial não implementa qualquer política de repetir. No código de produção, deve implementar políticas de repetir (tal como um duplicar exponencial), tal como sugerido no artigo MSDN [Processamento de falhas breves][lnk-transient-faults].

## <a name="trigger-a-remote-firmware-update-on-the-device-using-a-direct-method"></a>Acionar uma atualização firmware remoto no dispositivo utilizando um método direto 

Nesta secção, crie uma aplicação de consola Node.js que inicia uma atualização firmware remoto num dispositivo utilizando um método direto e utiliza consultas de duplos dispositivo para obter periodicamente o estado da atualização do active firmware nesse dispositivo.


1. Crie uma nova pasta vazia denominada **triggerfwupdateondevice**.  Na pasta **triggerfwupdateondevice** , crie um ficheiro de package.json utilizando o seguinte comando na sua linha de comandos.  Aceite todas as predefinições:

    ```
    npm init
    ```
    
2. Na sua-linha de comandos na pasta **triggerfwupdateondevice** , execute o seguinte comando para instalar o pacote de dispositivo SDK **azure iothub** e **azure-iot dispositivo mqtt** pacote:

    ```
    npm install azure-iot-hub@dtpreview --save
    ```
    
3. Utilizando um editor de texto, crie um novo ficheiro de **dmpatterns_getstarted_service.js** na pasta **triggerfwupdateondevice** .

4. Adicione o seguinte procedimento 'exigir' declarações no início do ficheiro **dmpatterns_getstarted_service.js** :

    ```
    'use strict';

    var Registry = require('azure-iothub').Registry;
    var Client = require('azure-iothub').Client;
    ```

5. Adicione as seguintes declarações de variáveis e substitua os valores de marcador de posição:

    ```
    var connectionString = '{device_connectionstring}';
    var registry = Registry.fromConnectionString(connectionString);
    var client = Client.fromConnectionString(connectionString);
    var deviceToUpdate = 'myDeviceId';
    ```
    
6. Adicionar a função seguinte para encontrar e apresentar o valor da firmwareUpdate comunicado propriedade.

    ```
    var queryTwinFWUpdateReported = function() {
        registry.getTwin(deviceToUpdate, function(err, twin){
            if (err) {
              console.error('Could not query twins: ' + err.constructor.name + ': ' + err.message);
            } else {
              console.log((JSON.stringify(twin.properties.reported.iothubDM.firmwareUpdate)) + "\n");
            }
        });
    };
    ```

7. Adicione a função seguinte invocar o método de firmwareUpdate para reinicie o dispositivo de destino:

    ```
    var startFirmwareUpdateDevice = function() {
      var params = {
          fwPackageUri: 'https://secureurl'
      };
      
      var methodName = "firmwareUpdate";
      var payloadData =  JSON.stringify(params);
      
      var methodParams = {
        methodName: methodName,
        payload: payloadData,
        timeoutInSeconds: 30
      };
      
      client.invokeDeviceMethod(deviceToUpdate, methodParams, function(err, result) {
        if (err) {
          console.error('Could not start the firmware update on the device: ' + err.message)
        } 
      });
    };
    ```

8. Por fim, adicionar a função seguinte para código para iniciar o firmware atualizar sequência e comece a periodicamente a mostrar o duplos referidos propriedades:

    ```
    startFirmwareUpdateDevice();
    setInterval(queryTwinFWUpdateReported, 500);
    ```
    
9. Guarde e feche o ficheiro **dmpatterns_fwupdate_service.js** .

## <a name="run-the-applications"></a>Execute as aplicações

Agora está pronto para executar as aplicações.

1. Na linha de comandos na pasta **manageddevice** , execute o seguinte comando para começar a ouvir referentes ao método direta de reiniciar o computador.

    ```
    node dmpatterns_fwupdate_device.js
    ```

2. No comando pedido na pasta **triggerfwupdateondevice** , execute o seguinte comando para acionar a reiniciar o computador remoto e uma consulta para duplos o dispositivo localizar o último reinicie o tempo.

    ```
    node dmpatterns_fwupdate_service.js
    ```

3. Irá ver reagem para o método direta imprimindo as a mensagem

## <a name="next-steps"></a>Próximos passos

Neste tutorial, que utilizou um método direto para acionar uma atualização firmware remoto num dispositivo e periodicamente utilizado o duplos comunicado propriedades para compreender o progresso do firmware atualizar o processo.  

Para saber como pode aumentar a sua IoT chamadas de método de solução e agenda em múltiplos dispositivos, consulte a [agenda e tarefas de implementação de difusão] [ lnk-tutorial-jobs] tutorial.

[lnk-devtwin]: iot-hub-devguide-device-twins.md
[lnk-c2dmethod]: iot-hub-devguide-direct-methods.md
[lnk-dm-getstarted]: iot-hub-device-management-get-started.md
[lnk-tutorial-jobs]: iot-hub-schedule-jobs.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-transient-faults]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
