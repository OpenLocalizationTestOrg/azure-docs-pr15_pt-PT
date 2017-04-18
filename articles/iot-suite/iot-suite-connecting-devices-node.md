<properties
   pageTitle="Ligar um dispositivo com Node.js | Microsoft Azure"
   description="Descreve como ligar um dispositivo à Azure IoT Suite pré-configurado remoto monitorização solução com uma aplicação escrita Node.js."
   services=""
   suite="iot-suite"
   documentationCenter="na"
   authors="dominicbetts"
   manager="timlt"
   editor=""/>

<tags
   ms.service="iot-suite"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="dobett"/>


# <a name="connect-your-device-to-the-remote-monitoring-preconfigured-solution-nodejs"></a>Ligar o seu dispositivo para a solução pré-configurado monitorização remota (Node.js)

[AZURE.INCLUDE [iot-suite-selector-connecting](../../includes/iot-suite-selector-connecting.md)]

## <a name="build-and-run-the-nodejs-sample-solution"></a>Criar e executar a solução de exemplo node.js

1. Para clonar do repositório de GitHub do *Microsoft Azure IoT SDK* e instalar o *Microsoft Azure IoT dispositivo SDK para Node.js* no seu ambiente de trabalho, siga o [artigo preparar o seu ambiente de desenvolvimento] [ lnk-github-prepare] instruções.

2. A partir da sua cópia local do [azure-iot-SDK] [ lnk-github-repo] repositório, copiar as duas seguintes ficheiros a partir da pasta de dispositivo/nó/amostras para uma pasta no seu dispositivo:

  - Packages.JSON
  - remote_monitoring.js

3. Abra o ficheiro remote_monitoring.js e procurar a variável de seguinte:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

4. Substitua **[cadeia de ligação de dispositivo IoT concentrador]** a cadeia de ligação do dispositivo. Pode encontrar os valores para o seu centro IoT hostname, id do dispositivo e chave do dispositivo no dashboard de monitorização solução remoto. Uma cadeia de ligação de dispositivo tem o seguinte formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se o nome do anfitrião IoT concentrador é **contoso** e o seu id de dispositivo é **mydevice**, a cadeia de ligação tem a seguinte apresentação:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

5. Guarde o ficheiro. Execute os seguintes comandos na linha de comandos na pasta que contém estes ficheiros para instalar os pacotes necessários e, em seguida, executar a aplicação de exemplo:

    ```
    npm install --save
    node remote_monitoring.js
    ```

[AZURE.INCLUDE [iot-suite-visualize-connecting](../../includes/iot-suite-visualize-connecting.md)]

[lnk-github-repo]: https://github.com/azure/azure-iot-sdks
[lnk-github-prepare]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/get_started/node-devbox-setup.md