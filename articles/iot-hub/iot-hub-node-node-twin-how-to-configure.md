<properties
    pageTitle="Utilizar as propriedades de duplos | Microsoft Azure"
    description="Este tutorial mostra-lhe como utilizar as propriedades de duplos"
    services="iot-hub"
    documentationCenter=".net"
    authors="fsautomata"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="node"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/13/2016"
     ms.author="elioda"/>

# <a name="tutorial-use-desired-properties-to-configure-devices-preview"></a>Tutorial: Utilizar as propriedades pretendidas para configurar os dispositivos (pré-visualização)

[AZURE.INCLUDE [iot-hub-selector-twin-how-to-configure](../../includes/iot-hub-selector-twin-how-to-configure.md)]

No final deste tutorial, terá de duas aplicações de consola Node.js:

* **SimulateDeviceConfiguration.js**, uma aplicação de dispositivo simulada que aguarda uma atualização configuração pretendida e relatórios de estado de um processo de atualização configuração simulada.
* **SetDesiredConfigurationAndQuery.js**, uma aplicação de Node.js concebida para serem executados a partir do back-end, que define a configuração pretendida num dispositivo e consultas o processo de atualização de configuração.

> [AZURE.NOTE] O artigo [IoT concentrador SDK] [ lnk-hub-sdks] fornece informações sobre os vários SDK que pode utilizar para criar aplicações de dispositivo e back-end.

Para concluir este tutorial precisa do seguinte:

+ Versão node.js 0.10.x ou posterior.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

Se seguiu [Introdução ao gémeos dispositivo] [ lnk-twin-tutorial] tutorial, já tem um concentrador de gestão de activada dispositivo e uma identidade de dispositivo denominado **myDeviceId**; e pode passar para a [criar a aplicação de dispositivo simulada] [ lnk-how-to-configure-createapp] secção.

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-simulated-device-app"></a>Criar a aplicação de dispositivo simulada

Nesta secção, crie uma aplicação de consola Node.js que liga-se ao seu centro como **myDeviceId**, aguarda uma atualização configuração pretendida e, em seguida, relatórios atualizações sobre o processo de atualização configuração simulada.

1. Crie uma nova pasta vazia denominada **simulatedeviceconfiguration**. Na pasta **simulatedeviceconfiguration** , crie um novo ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **simulatedeviceconfiguration** , execute o seguinte comando para instalar o **dispositivo de iot azure**e **azure-iot dispositivo mqtt** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **SimulateDeviceConfiguration.js** na pasta **simulatedeviceconfiguration** .

4. Adicione o seguinte código para o ficheiro **SimulateDeviceConfiguration.js** e substitua o marcador de posição de **{cadeia de ligação de dispositivo}** com a cadeia de ligação que copiou quando criou a identidade do dispositivo **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
            if (err) {
                console.error('could not open IotHub client');
            } else {
                client.getTwin(function(err, twin) {
                    if (err) {
                        console.error('could not get twin');
                    } else {
                        console.log('retrieved device twin');
                        twin.properties.reported.telemetryConfig = {
                            configId: "0",
                            sendFrequency: "24h"
                        }
                        twin.on('properties.desired', function(desiredChange) {
                            console.log("received change: "+JSON.stringify(desiredChange));
                            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
                            if (desiredChange.telemetryConfig &&desiredChange.telemetryConfig.configId !== currentTelemetryConfig.configId) {
                                initConfigChange(twin);
                            }
                        });
                    }
                });
            }
        });

    O objeto de **cliente** expõe todos os métodos de necessário para interagir com gémeos dispositivo do dispositivo. O código anterior, depois de que inicia o objeto de **cliente** , obtém duplos para **myDeviceId**e anexa um processador para a atualização as propriedades pretendidas. Verifica se o processador de que existe é um pedido de alteração de configuração real ao comparar a configIds, em seguida, invocar um método de que inicia a alteração de configuração.

    Tenha em atenção que, por motivos de simplificar, o código anterior utiliza uma predefinição codificada para a configuração inicial. Uma aplicação real provavelmente seria carregar que a configuração a partir de um armazenamento local.
    
    > [AZURE.IMPORTANT] Eventos de alteração de propriedade desejada são sempre emitidos uma vez na ligação do dispositivo, certifique-se verificar que existe uma alteração real as propriedades pretendidas antes de executar qualquer ação.

5. Adicionar os seguintes métodos antes do `client.open()` invocação de funções:

        var initConfigChange = function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.pendingConfig = twin.properties.desired.telemetryConfig;
            currentTelemetryConfig.status = "Pending";

            var patch = {
            telemetryConfig: currentTelemetryConfig
            };
            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.log('Could not report properties');
                } else {
                    console.log('Reported pending config change: ' + JSON.stringify(patch));
                    setTimeout(function() {completeConfigChange(twin);}, 60000);
                }
            });
        }

        var completeConfigChange =  function(twin) {
            var currentTelemetryConfig = twin.properties.reported.telemetryConfig;
            currentTelemetryConfig.configId = currentTelemetryConfig.pendingConfig.configId;
            currentTelemetryConfig.sendFrequency = currentTelemetryConfig.pendingConfig.sendFrequency;
            currentTelemetryConfig.status = "Success";
            delete currentTelemetryConfig.pendingConfig;
            
            var patch = {
                telemetryConfig: currentTelemetryConfig
            };
            patch.telemetryConfig.pendingConfig = null;

            twin.properties.reported.update(patch, function(err) {
                if (err) {
                    console.error('Error reporting properties: ' + err);
                } else {
                    console.log('Reported completed config change: ' + JSON.stringify(patch));
                }
            });
        };

    O método de **initConfigChange** actualiza denunciadas propriedades no objeto duplos local com o pedido de actualização de configuração e define o estado como **pendentes**, em seguida, atualiza o duplos dispositivo o serviço. Depois de actualizar com êxito o duplos,-simula um processo longa que termina em execução das **completeConfigChange**. Este método atualiza denunciado as propriedades o duplos local definir o estado para o **sucesso** e remover o objeto **pendingConfig** . Em seguida, actualiza duplos o serviço.

    Tenha em atenção que, para guardar a largura de banda, comunicados propriedades são atualizadas ao especificar apenas as propriedades de ser alterada (com nome de **patches** no código de acima), em vez de substituição de todo o documento.

    > [AZURE.NOTE] Neste tutorial não simular qualquer comportamento existência de actualizações de configuração em simultâneo. Alguns processos de atualização configuração poderão ser possível acomodar alterações da configuração de destino enquanto está a executar a atualização, as outras pessoas podem ter de fila de espera e outras pessoas rejeitá-los com uma condição de erro. Certifique-se da ter em consideração o comportamento pretendido para o processo de configuração específicas e adicione a lógica adequada antes de iniciar a alteração de configuração.

6. Execute a aplicação de dispositivo:

        node SimulateDeviceConfiguration.js

    Deverá visualizar a mensagem `retrieved device twin`. Manter a aplicação em execução.

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, irá criar uma aplicação de consola Node.js que atualiza as *propriedades pretendidas* na duplos associado **myDeviceId** com um novo objeto de configuração de telemetria. Em seguida, consultas gémeos o dispositivo armazenados no centro do e mostra a diferença entre as configurações desejadas e denunciadas do dispositivo.

1. Crie uma nova pasta vazia denominada **setdesiredandqueryapp**. Na pasta **setdesiredandqueryapp** , crie um novo ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **setdesiredandqueryapp** , execute o seguinte comando para instalar o pacote do **azure iothub** :

    ```
    npm install azure-iothub@dtpreview node-uuid --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **SetDesiredAndQuery.js** na pasta **addtagsandqueryapp** .

4. Adicione o seguinte código para o ficheiro **SetDesiredAndQuery.js** e substitua o marcador de posição de **{cadeia de ligação de serviço}** com a cadeia de ligação que copiou quando criou o seu Centro:

        'use strict';
        var iothub = require('azure-iothub');
        var uuid = require('node-uuid');
        var connectionString = '{service connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);
         
        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var newConfigId = uuid.v4();
                var newFrequency = process.argv[2] || "5m";
                var patch = {
                    properties: {
                        desired: {
                            telemetryConfig: {
                                configId: newConfigId,
                                sendFrequency: newFrequency
                            }
                        }
                    }
                }
                twin.update(patch, function(err) {
                    if (err) {
                        console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                    } else {
                        console.log(twin.deviceId + ' twin updated successfully');
                    }
                });
                setInterval(queryTwins, 10000);
            }
        });
            

    O objeto de **registo** expõe todos os métodos de necessário para interagir com gémeos de dispositivo de serviço. O código anterior, depois de que inicia o objeto de **registo** , obtém duplos para **myDeviceId**e atualiza as respetivas propriedades pretendidas com um novo objeto de configuração de telemetria. Após esta ação, chama o evento de função **queryTwins** 10 segundos.

    > [AZURE.IMPORTANT] Esta aplicação consultas IoT concentrador cada 10 segundos para fins ilustrativas. Utilize consultas para gerar relatórios de utilizador destinado ao longo de muitos dispositivos e não para detetar alterações. Se a sua solução requer notificações em tempo real de eventos do dispositivo, utilize [as mensagens de dispositivo para nuvem][lnk-d2c].

5. Adicionar à direita de código seguinte antes do `registry.getDeviceTwin()` invocação para implementar a função **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE deviceId = 'myDeviceId'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log();
                    results.forEach(function(twin) {
                        var desiredConfig = twin.properties.desired.telemetryConfig;
                        var reportedConfig = twin.properties.reported.telemetryConfig;
                        console.log("Config report for: " + twin.deviceId);
                        console.log("Desired: ");
                        console.log(JSON.stringify(desiredConfig, null, 2));
                        console.log("Reported: ");
                        console.log(JSON.stringify(reportedConfig, null, 2));
                    });
                }
            });
        };

    As consultas de código anterior as gémeos armazenadas no centro do e imprime as configurações de telemetria pretendida e denunciado. Referir-se para o [idioma de consulta IoT concentrador] [ lnk-query] para saber como gerar relatórios formatados em todos os seus dispositivos.


8. Com a execução de **SimulateDeviceConfiguration.js** , execute a aplicação com:

        node SetDesiredAndQuery.js 5m

    Deverá visualizar a denunciado mudança de configuração de **sucesso** para **pendentes** para o **sucesso das** novamente com o active novo enviar frequência de cinco minutos em vez de 24 horas.

    > [AZURE.IMPORTANT] Existe um atraso de até um minuto entre a operação de relatório de dispositivo e o resultado da consulta. Isto é para ativar a infraestrutura de consulta trabalhar em escala muito alta. Para obter consistentes vistas de uma única duplos utilizem o método de **getDeviceTwin** da turma de **registo** .

## <a name="next-steps"></a>Próximos passos

Neste tutorial, definir uma configuração pretendida como *propriedades pretendidas* a partir de uma aplicação de back-end e escrever uma aplicação do dispositivo simulada para detetar que altere e simular um processo de atualização com várias passo relato o respetivo estado como *comunicado propriedades* para o duplos.

Utilize os seguintes recursos para saber como:

- Enviar telemetria a partir de dispositivos com [Introdução ao centro de IoT] [ lnk-iothub-getstarted] tutorial,
- agendar ou desempenham operações em grandes conjuntos de dispositivos ver as [tarefas de utilização para agendar e operações de dispositivo de difusão] [ lnk-schedule-jobs] tutorial.
- controlar os dispositivos de forma interativa (tal como ativar uma ventoinha através de uma aplicação controlado pelo utilizador), com os [métodos de diretos de utilização] [ lnk-methods-tutorial] tutorial.


<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-dm-overview]: iot-hub-device-management-overview.md
[lnk-twin-tutorial]: iot-hub-node-node-twin-getstarted.md
[lnk-schedule-jobs]: iot-hub-schedule-jobs.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md

[lnk-guid]: https://en.wikipedia.org/wiki/Globally_unique_identifier

[lnk-how-to-configure-createapp]: iot-hub-node-node-twin-how-to-configure.md#create-the-simulated-device-app
