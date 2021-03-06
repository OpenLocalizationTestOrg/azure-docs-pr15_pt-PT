<properties
    pageTitle="Simular um dispositivo com o SDK do Gateway IoT | Microsoft Azure"
    description="Azure IoT Gateway SDK instruções passo a passo utilizando o Windows para ilustrar o envio de telemetria num dispositivo simulada utilizando o SDK do Azure IoT Gateway."
    services="iot-hub"
    documentationCenter=""
    authors="chipalost"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/29/2016"
     ms.author="andbuc"/>


# <a name="azure-iot-gateway-sdk-beta--send-device-to-cloud-messages-with-a-simulated-device-using-windows"></a>Azure IoT Gateway SDK (beta) – enviar mensagens de dispositivo para nuvem com um dispositivo simulada utilizando o Windows

[AZURE.INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar o exemplo

Antes de começar, tem de:

- [Configurar o seu ambiente de desenvolvimento] [ lnk-setupdevbox] para trabalhar com o SDK no Windows.
- [Criar um concentrador de IoT] [ lnk-create-hub] na sua subscrição do Azure, terá do nome do seu Centro para concluir este tutorial. Se não tiver uma conta, pode criar uma [conta gratuita] [ lnk-free-trial] apenas de duas minutos.
- Adicionar dois dispositivos ao seu centro IoT e tome nota das suas ids e chaves de dispositivo. Pode utilizar o [Explorador de dispositivo ou Explorador de iothub] [ lnk-explorer-tools] ferramenta para adicionar os seus dispositivos ao concentrador IoT que criou no passo anterior e obter as respetivas chaves.

Para criar o exemplo:

1. Abra uma linha de comandos de **linha de comandos para programadores para VS2015** .
2. Navegue para a pasta de raiz na sua cópia local do repositório **azure-iot gateway sdk** .
3. Executar o **ferramentas\\build.cmd** script. Este script cria um ficheiro de solução do Visual Studio, constrói a solução e executa os testes. Pode encontrar a solução do Visual Studio na pasta **construir** na sua cópia local do repositório **azure-iot gateway sdk** .

Para executar o exemplo:

No editor de texto, abra o ficheiro **amostras\\simulated_device_cloud_upload\\src\\simulated_device_cloud_upload_win.json** na sua cópia local do repositório **azure-iot gateway sdk** . Este ficheiro configura os módulos no gateway de exemplo:

- O módulo **IoTHub** liga-se a sua concentrador IoT. Tem de configurá-lo para enviar dados para o seu centro IoT. Especificamente, defina o valor de **IoTHubName** para o nome do seu centro IoT e defina o valor de **IoTHubSuffix** para **azure devices.net**. Defina o valor de **transporte** para um dos: "HTTP", "AMQP" ou "MQTT". Tenha em atenção que atualmente, só "HTTP" irão partilhar uma ligação de TCP para todas as mensagens de dispositivo. Se definir o valor para "AMQP" ou "MQTT", o gateway irá manter uma ligação de TCP separada a IoT concentrador para cada dispositivo.
- Módulo de **mapeamento** de mapas os endereços de MAC dos seus dispositivos simulados a sua ids de dispositivo IoT concentrador. Certifique-se de que os valores de **deviceId** correspondem aos ids dos dois dispositivos que adicionou ao seu centro IoT e de que os valores de **deviceKey** contêm as teclas dos seus dispositivos de duas.
- Os módulos **BLE1** e **BLE2** estão os dispositivos simulados. Tenha em atenção como os respetivos endereços MAC corresponderem no módulo **mapeamento** .
- Módulo de **registo** regista sua atividade de gateway para um ficheiro.
- Os valores de **caminho de módulo** mostrados abaixo partem do pressuposto de que clonar do repositório de IoT Gateway SDK na raiz da sua unidade **c:** . Se tiver transferido para outra localização, terá de ajustar os valores do **caminho do módulo** , consoante adequado.
- A matriz de **hiperligações** na parte inferior do ficheiro JSON liga-se a **BLE1** e **BLE2** módulos para o módulo de **mapeamento** e o módulo de **mapeamento** do módulo **IoTHub** . Também assegura que todas as mensagens são registadas pelo módulo do **registo** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\iothub\\Debug\\iothub_hl.dll",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\identitymap\\Debug\\identity_map_hl.dll",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\simulated_device\\Debug\\simulated_device_hl.dll",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "C:\\azure-iot-gateway-sdk\\build\\modules\\logger\\Debug\\logger_hl.dll",
            "args":
            {
                "filename":"C:\\azure-iot-gateway-sdk\\deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}
```

Guarde as alterações que efetuou para o ficheiro de configuração.

Para executar o exemplo:

1. Numa linha de comandos, navegue para a pasta raiz da sua cópia local do repositório **azure-iot gateway sdk** .
2. Execute o seguinte comando:
  
    ```
    build\samples\simulated_device_cloud_upload\Debug\simulated_device_cloud_upload_sample.exe samples\simulated_device_cloud_upload\src\simulated_device_cloud_upload_win.json
    ```

3. Pode utilizar o [Explorador de dispositivo ou Explorador de iothub] [ lnk-explorer-tools] ferramenta para monitorizar as mensagens que recebe de IoT concentrador de gateway.


## <a name="next-steps"></a>Próximos passos

Se pretender obter uma compreensão mais avançada do IoT Gateway SDK e experimentar alguns exemplos de código, visite os seguintes tutoriais do programador e recursos:

- [Enviar mensagens de dispositivo para nuvem a partir de um dispositivo real com o SDK do Gateway IoT][lnk-physical-device]
- [Azure IoT Gateway SDK][lnk-gateway-sdk]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: ./iot-hub-devguide.md
[lnk-create-hub]: iot-hub-create-through-portal.md 