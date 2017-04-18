<properties
    pageTitle="Introdução ao gémeos | Microsoft Azure"
    description="Este tutorial mostra-lhe como utilizar gémeos"
    services="iot-hub"
    documentationCenter="node"
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

# <a name="tutorial-get-started-with-device-twins-preview"></a>Tutorial: Introdução ao gémeos dispositivo (pré-visualização)

[AZURE.INCLUDE [iot-hub-selector-twin-get-started](../../includes/iot-hub-selector-twin-get-started.md)]

No final deste tutorial, terá de duas aplicações de consola Node.js:

* **AddTagsAndQuery.js**, uma aplicação de Node.js concebida para serem executados a partir do back-end, que adiciona etiquetas e consultas gémeos dispositivo.
* **TwinSimulatedDevice.js**, uma aplicação de Node.js que simula um dispositivo que liga-se ao seu centro IoT com a identidade do dispositivo criada anteriormente e relatórios sua condição de conectividade.

> [AZURE.NOTE] O artigo [IoT concentrador SDK] [ lnk-hub-sdks] fornece informações sobre os vários SDK que pode utilizar para criar aplicações de dispositivo e back-end.

Para concluir este tutorial precisa do seguinte:

+ Versão node.js 0.10.x ou posterior.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, crie uma aplicação de consola Node.js que adiciona meta-dados de localização para duplos associado **myDeviceId**. -Lo, em seguida, consultas gémeos armazenados no centro do selecionar os dispositivos localizado nos EUA e, em seguida, naqueles esse relatório de uma ligação de dados móveis.

1. Crie uma nova pasta vazia denominada **addtagsandqueryapp**. Na pasta **addtagsandqueryapp** , crie um novo ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **addtagsandqueryapp** , execute o seguinte comando para instalar o pacote do **azure iothub** :

    ```
    npm install azure-iothub@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **AddTagsAndQuery.js** na pasta **addtagsandqueryapp** .

4. Adicione o seguinte código para o ficheiro **AddTagsAndQuery.js** e substitua o marcador de posição de **{cadeia de ligação de serviço}** com a cadeia de ligação que copiou quando criou o seu Centro:

        'use strict';
        var iothub = require('azure-iothub');
        var connectionString = '{service hub connection string}';
        var registry = iothub.Registry.fromConnectionString(connectionString);

        registry.getTwin('myDeviceId', function(err, twin){
            if (err) {
                console.error(err.constructor.name + ': ' + err.message);
            } else {
                var patch = {
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                      }
                    }
                };
             
                twin.update(patch, function(err) {
                  if (err) {
                    console.error('Could not update twin: ' + err.constructor.name + ': ' + err.message);
                  } else {
                    console.log(twin.deviceId + ' twin updated successfully');
                    queryTwins();
                  }
                });
            }
        });

    O objeto de **registo** expõe todos os métodos de necessário para interagir com gémeos de dispositivo de serviço. O código anterior inicia pela primeira vez no objeto de **registo** e, em seguida, obtém duplos para **myDeviceId**e finalmente actualiza suas etiquetas com as informações de localização pretendida.

    Depois de etiquetas de actualizar chama a função **queryTwins** .

7. Adicione o código seguinte no final do **AddTagsAndQuery.js** para implementar a função **queryTwins** :

        var queryTwins = function() {
            var query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
            
            query = registry.createQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            query.nextAsTwin(function(err, results) {
                if (err) {
                    console.error('Failed to fetch the results: ' + err.message);
                } else {
                    console.log("Devices in Redmond43 using cellular network: " + results.map(function(twin) {return twin.deviceId}).join(','));
                }
            });
        };

    O código anterior executa duas consultas: o primeiro seleciona os gémeos de dispositivo de dispositivos localizados na fábrica de **Redmond43** e o segundo melhora a consulta para selecionar apenas os dispositivos que também estão ligados através da rede móvel.

    Tenha em atenção que o código anterior, quando cria o objeto de **consulta** , especifica um número máximo de documentos devolvidos. O objeto de **consulta** contém uma propriedade booleana **hasMoreResults** que pode utilizar para invocar os métodos de **nextAsTwin** várias vezes para obter todos os resultados. Um método chamado **seguinte** está disponível para obter os resultados que não estão dispositivo gémeos por exemplo, os resultados de consultas de agregação.

8. Execute a aplicação com:

        node AddTagsAndQuery.js

    Deverá visualizar um dispositivo nos resultados da consulta pedir a todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede móvel.

    ![][1]

Na secção seguinte cria uma aplicação do dispositivo que reporta as informações de ligação e altera o resultado da consulta, na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo

Nesta secção, crie uma Node.js aplicação de consola que liga-se ao seu centro como **myDeviceId**e, em seguida, atualizações respetivo duplos comunicado propriedades para contêm as informações que está ligado através de uma rede móvel.

> [AZURE.NOTE] Neste momento, são acessíveis apenas a partir de dispositivos que se ligam a concentrador IoT utilizando o protocolo MQTT gémeos dispositivo. Consulte para [suportar MQTT] [ lnk-devguide-mqtt] artigo para obter instruções sobre como converter a aplicação de dispositivo existente utilizar MQTT.

1. Crie uma nova pasta vazia denominada **reportconnectivity**. Na pasta **reportconnectivity** , crie um novo ficheiro de package.json utilizando o seguinte comando na sua linha de comandos. Aceite todas as predefinições:

    ```
    npm init
    ```

2. Na sua-linha de comandos na pasta **reportconnectivity** , execute o seguinte comando para instalar o **dispositivo de iot azure**e **azure-iot dispositivo mqtt** pacote:

    ```
    npm install azure-iot-device@dtpreview azure-iot-device-mqtt@dtpreview --save
    ```

3. Utilizando um editor de texto, crie um novo ficheiro de **ReportConnectivity.js** na pasta **reportconnectivity** .

4. Adicione o seguinte código para o ficheiro **ReportConnectivity.js** e substitua o marcador de posição de **{cadeia de ligação de dispositivo}** com a cadeia de ligação que copiou quando criou a identidade do dispositivo **myDeviceId** :

        'use strict';
        var Client = require('azure-iot-device').Client;
        var Protocol = require('azure-iot-device-mqtt').Mqtt;

        var connectionString = '{device connection string}';
        var client = Client.fromConnectionString(connectionString, Protocol);

        client.open(function(err) {
        if (err) {
            console.error('could not open IotHub client');
        }  else {
            console.log('client opened');

            client.getTwin(function(err, twin) {
            if (err) {
                console.error('could not get twin');
            } else {
                var patch = {
                    connectivity: {
                        type: 'cellular'
                    }
                };

                twin.properties.reported.update(patch, function(err) {
                    if (err) {
                        console.error('could not update twin');
                    } else {
                        console.log('twin state reported');
                        process.exit();
                    }
                });
            }
            });
        }
        });

    O objeto de **cliente** expõe todos os métodos de que exigir para interagir com gémeos dispositivo do dispositivo. O código anterior, depois de que inicia o objeto de **cliente** , obtém duplos para **myDeviceId** e atualiza a respetiva propriedade denunciada com as informações de ligação.

5. Executar a aplicação de dispositivo

        node ReportConnectivity.js

    Deverá visualizar a mensagem `twin state reported`.

6. Agora que o dispositivo comunicou as suas informações de conectividade, deverá aparecer em ambas as consultas. Aceda novamente na pasta **addtagsandqueryapp** e execute novamente as consultas:

        node AddTagsAndQuery.js

    Neste momento **myDeviceId** deverão aparecer no ambos os resultados da consulta.

    ![][3]

## <a name="next-steps"></a>Próximos passos
Neste tutorial, configurado um concentrador de IoT novo no portal do Azure e, em seguida, criada uma identidade de dispositivo no registo de identidade do concentrador. Adicionado dispositivo metadados como etiquetas a partir de uma aplicação de back-end e escrever uma aplicação do dispositivo simulada às informações de conectividade de dispositivo de relatório no duplos dispositivo. Aprendeu também como consultar esta informação a utilizar o concentrador de IoT linguagem de consulta SQL gosto.

Utilize os seguintes recursos para saber como:

- Enviar telemetria a partir de dispositivos com [Introdução ao centro de IoT] [ lnk-iothub-getstarted] tutorial,
- configurar dispositivos utilizando as propriedades pretendidas do duplos com [as propriedades pretendidas para configurar os dispositivos de utilizar] [ lnk-twin-how-to-configure] tutorial,
- controlar os dispositivos de forma interativa (tal como ativar uma ventoinha através de uma aplicação controlado pelo utilizador), com os [métodos de diretos de utilização] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[1]: media/iot-hub-node-node-twin-getstarted/service1.png
[3]: media/iot-hub-node-node-twin-getstarted/service2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-device-management]: iot-hub-device-management-get-started.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md
[lnk-connect-device]: https://azure.microsoft.com/develop/iot/

[lnk-twin-how-to-configure]: iot-hub-node-node-twin-how-to-configure.md
[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md