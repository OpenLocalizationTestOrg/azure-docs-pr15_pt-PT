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

No final deste tutorial, terá de um .NET e uma aplicação de consola Node.js:

* **AddTagsAndQuery.sln**, uma aplicação de .NET concebida para serem executados a partir do back-end, que adiciona etiquetas e consultas gémeos dispositivo.
* **TwinSimulatedDevice.js**, uma aplicação de Node.js que simula um dispositivo que liga-se ao seu centro IoT com a identidade do dispositivo criada anteriormente e relatórios sua condição de conectividade.

> [AZURE.NOTE] O artigo [IoT concentrador SDK] [ lnk-hub-sdks] fornece informações sobre os vários SDK que pode utilizar para criar aplicações de dispositivo e back-end.

Para concluir este tutorial precisa do seguinte:

+ Microsoft Visual Studio 2015.

+ Versão node.js 0.10.x ou posterior.

+ Uma conta do Azure active. (Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.)

[AZURE.INCLUDE [iot-hub-get-started-create-hub-pp](../../includes/iot-hub-get-started-create-hub-pp.md)]

## <a name="create-the-service-app"></a>Criar a aplicação de serviço

Nesta secção, crie uma aplicação de consola Node.js que adiciona meta-dados de localização para duplos associado **myDeviceId**. -Lo, em seguida, consultas gémeos armazenados no centro do selecionar os dispositivos localizado nos EUA e, em seguida, naqueles esse relatório de uma ligação de dados móveis.

1. No Visual Studio, adicione um projeto do Visual c# Windows clássico ambiente de trabalho para a solução actual utilizando o modelo de **Aplicação de consola** do projecto. Nome do projeto **AddTagsAndQuery**.

    ![Novo projeto do Visual c# clássica ambiente de trabalho Windows][img-createapp]

2. No Explorador de solução, com o botão direito do projecto **AddTagsAndQuery** e, em seguida, clique em **Gerir pacotes de Nuget**.

3. Na janela do **Gestor de pacote Nuget** , certifique-se de que a **versão de pré-lançamento do incluir** está selecionada, procure **microsoft.azure.devices**, selecione **instalar** para instalar a versão de *pré-lançamento* da **Microsoft.Azure.Devices** compactar e aceite os termos de utilização. Este procedimento transferências, instalações e adiciona uma referência para o [Microsoft Azure IoT serviço SDK] [ lnk-nuget-service-sdk] Nuget pacote e as respectivas dependências.

    ![Janela Gestor de pacote Nuget][img-servicenuget]

4. Adicione o seguinte `using` declarações na parte superior do ficheiro **Program.cs** :

        using Microsoft.Azure.Devices;

5. Adicione os campos seguintes para a classe de **programa** . Substitua o valor de marcador de posição com a cadeia de ligação para o concentrador de IoT que criou na secção anterior.

        static RegistryManager registryManager;
        static string connectionString = "{iot hub connection string}";

6. Adicione o seguinte método para a classe de **programa** :

        public static async Task AddTagsAndQuery()
        {
            var twin = await registryManager.GetTwinAsync("myDeviceId");
            var patch =
                @"{
                    tags: {
                        location: {
                            region: 'US',
                            plant: 'Redmond43'
                        }
                    }
                }";
            await registryManager.UpdateTwinAsync(twin.DeviceId, patch, twin.ETag);

            var query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43'", 100);
            var twinsInRedmond43 = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43: {0}", string.Join(", ", twinsInRedmond43.Select(t => t.DeviceId)));

            query = registryManager.CreateQuery("SELECT * FROM devices WHERE tags.location.plant = 'Redmond43' AND properties.reported.connectivity.type = 'cellular'", 100);
            var twinsInRedmond43UsingCellular = await query.GetNextAsTwinAsync();
            Console.WriteLine("Devices in Redmond43 using cellular network: {0}", string.Join(", ", twinsInRedmond43UsingCellular.Select(t => t.DeviceId)));
        }

    A classe de **RegistryManager** expõe todos os métodos de necessário para interagir com gémeos de dispositivo de serviço. O código anterior inicia pela primeira vez no objeto **registryManager** e, em seguida, obtém duplos para **myDeviceId**e finalmente actualiza suas etiquetas com as informações de localização pretendida.

    Depois de actualizar, segue executa duas consultas: o primeiro seleciona os gémeos de dispositivo de dispositivos localizados na fábrica de **Redmond43** e o segundo melhora a consulta para selecionar apenas os dispositivos que também estão ligados através da rede móvel.

    Tenha em atenção que o código anterior, quando cria o objeto de **consulta** , especifica um número máximo de documentos devolvidos. O objeto de **consulta** contém uma propriedade booleana **HasMoreResults** que pode utilizar para invocar os métodos de **GetNextAsTwinAsync** várias vezes para obter todos os resultados. Um método chamado **GetNextAsJson** está disponível para obter os resultados que não estão dispositivo gémeos por exemplo, os resultados de consultas de agregação.

7. Por fim, adicione as seguintes linhas para o método **principais** :

        registryManager = RegistryManager.CreateFromConnectionString(connectionString);
        AddTagsAndQuery().Wait();
        Console.WriteLine("Press Enter to exit.");
        Console.ReadLine();

8. Executar esta aplicação e, deverá ver um dispositivo nos resultados da consulta pedir a todos os dispositivos localizados em **Redmond43** e nenhum para a consulta que restringe os resultados a dispositivos que utilizam uma rede móvel.

    ![Resultados da consulta, na janela][img-addtagapp]

Na secção seguinte cria uma aplicação do dispositivo que reporta as informações de ligação e altera o resultado da consulta, na secção anterior.

## <a name="create-the-device-app"></a>Criar a aplicação de dispositivo

Nesta secção, crie uma Node.js aplicação de consola que liga-se ao seu centro como **myDeviceId**e, em seguida, atualizações respetivo duplos comunicado propriedades para contêm as informações que está ligado através de uma rede móvel.

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

6. Agora que o dispositivo reportados as suas informações de conectividade, deverá aparecer em ambas as consultas. Execute a aplicação de.NET **AddTagsAndQuery** para executar as consultas novamente. Neste momento **myDeviceId** deverão aparecer no ambos os resultados da consulta.

    ![][img-addtagapp2]

## <a name="next-steps"></a>Próximos passos
Neste tutorial, configurado um concentrador de IoT novo no portal do Azure e, em seguida, criada uma identidade de dispositivo no registo de identidade do concentrador. Adicionado dispositivo metadados como etiquetas a partir de uma aplicação de back-end e escrever uma aplicação do dispositivo simulada às informações de conectividade de dispositivo de relatório no duplos dispositivo. Aprendeu também como consultar esta informação a utilizar o concentrador de IoT linguagem de consulta SQL gosto.

Utilize os seguintes recursos para saber como:

- Enviar telemetria a partir de dispositivos com [Introdução ao centro de IoT] [ lnk-iothub-getstarted] tutorial,
- configurar dispositivos utilizando as propriedades pretendidas do duplos com [as propriedades pretendidas para configurar os dispositivos de utilizar] [ lnk-twin-how-to-configure] tutorial,
- controlar os dispositivos de forma interativa (tal como ativar uma ventoinha através de uma aplicação controlado pelo utilizador) com os [métodos de diretos de utilização] [ lnk-methods-tutorial] tutorial.

<!-- images -->
[img-servicenuget]: media/iot-hub-csharp-node-twin-getstarted/servicesdknuget.png
[img-createapp]: media/iot-hub-csharp-node-twin-getstarted/createnetapp.png
[img-addtagapp]: media/iot-hub-csharp-node-twin-getstarted/addtagapp.png
[img-addtagapp2]: media/iot-hub-csharp-node-twin-getstarted/addtagapp2.png

<!-- links -->
[lnk-hub-sdks]: iot-hub-devguide-sdks.md
[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-nuget-service-sdk]: https://www.nuget.org/packages/Microsoft.Azure.Devices/1.1.0-preview-004

[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-identity]: iot-hub-devguide-identity-registry.md

[lnk-iothub-getstarted]: iot-hub-node-node-getstarted.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-twin-how-to-configure]: iot-hub-csharp-node-twin-how-to-configure.md

[lnk-dev-setup]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md

