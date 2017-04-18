<properties
    pageTitle="Utilizar telemetria dinâmica | Microsoft Azure"
    description="Siga este tutorial para saber como utilizar telemetria dinâmica com a solução pré-configurado monitorização remota."
    services=""
    suite="iot-suite"
    documentationCenter=""
    authors="dominicbetts"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-suite"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="08/25/2016"
     ms.author="dobett"/>

# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Utilizar telemetria dinâmica com a solução pré-configurado monitorização remota

## <a name="introduction"></a>Introdução

Telemetria dinâmica permite-lhe visualizar qualquer telemetria enviada para a solução pré-configurado monitorização remota. Os dispositivos simulados que implementar com a solução pré-configurado enviam telemetria temperatura e humidade, que pode visualizar no dashboard. Se personalizar os dispositivos simulados existentes, criar novos dispositivos simulados ou ligar dispositivos físicos para a solução pré-configurado pode enviar outros valores de telemetria como a temperatura externa, rotações/minuto ou velocidade do vento. Em seguida, pode visualizar esta telemetria adicional no dashboard.

Neste tutorial, utiliza um dispositivo Node.js simulado simple que pode facilmente modificar para experimentar com telemetria dinâmica.

Para concluir este tutorial, terá de:

- Uma subscrição ativa do Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter mais detalhes, consulte o artigo [Versão de avaliação gratuita do Azure][lnk_free_trial].
- [NODE.js] [ lnk-node] versão 0.12.x ou posterior.

Pode concluir este tutorial em qualquer sistema operativo, tal como o Windows ou Linux, onde pode instalar Node.js.

[AZURE.INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

## <a name="configure-the-nodejs-simulated-device"></a>Configurar o dispositivo simulado Node.js

1. No dashboard de remoto monitorização, clique em **+ Adicionar um dispositivo** e, em seguida, adicione um dispositivo personalizado. Tome nota do concentrador IoT hostname, id do dispositivo e chave do dispositivo. Tem-las mais tarde neste tutorial quando se preparar a aplicação de cliente do dispositivo remote_monitoring.js.

2. Certifique-se de que versão do Node.js 0.12.x ou posterior instalado no seu computador de desenvolvimento. Executar `node --version` numa linha de comandos ou numa shell para verificar a versão. Para obter informações sobre como utilizar o Gestor de pacote para instalar Node.js em Linux, consulte o artigo [Instalar o Node.js através do Gestor de pacotes][node-linux].

3. Quando tiver instalado Node.js, clonar a versão mais recente do [azure-iot-SDK] [ lnk-github-repo] repositório para o seu computador de desenvolvimento. Utilize sempre o ramo **principal** para a versão mais recente das bibliotecas e amostras.

4. A partir da sua cópia local do [azure-iot-SDK] [ lnk-github-repo] repositório, copiar as duas seguintes ficheiros a partir da pasta de dispositivo/nó/amostras para esvaziar a pasta no seu computador de desenvolvimento:

  - Packages.JSON
  - remote_monitoring.js

5. Abra o ficheiro remote_monitoring.js e procure a definição de variável seguinte:

    ```
    var connectionString = "[IoT Hub device connection string]";
    ```

6. Substitua **[cadeia de ligação de dispositivo IoT concentrador]** a cadeia de ligação do dispositivo. Utilize os valores para o seu nome do anfitrião IoT concentrador, id do dispositivo e chave do dispositivo que efetuou uma nota no passo 1. Uma cadeia de ligação de dispositivo tem o seguinte formato:

    ```
    HostName={your IoT Hub hostname};DeviceId={your device id};SharedAccessKey={your device key}
    ```

    Se o nome do anfitrião IoT concentrador é **contoso** e o seu id de dispositivo é **mydevice**, a cadeia de ligação aspeto o seguinte procedimento:

    ```
    var connectionString = "HostName=contoso.azure-devices.net;DeviceId=mydevice;SharedAccessKey=2s ... =="
    ```

7. Guarde o ficheiro. Execute os seguintes comandos numa shell ou linha de comandos na pasta que contém estes ficheiros para instalar os pacotes necessários e, em seguida, executar a aplicação de exemplo:

    ```
    npm install
    node remote_monitoring.js
    ```

## <a name="observe-dynamic-telemetry-in-action"></a>Observar telemetria dinâmica em ação

Dashboard de mostra a temperatura e humidade telemetria a partir de dispositivos simuladas existentes:

![O dashboard predefinido][image1]

Se selecionar o dispositivo de simulada Node.js que executou na secção anterior, consulte temperatura, humidade e telemetria temperatura externos:

![Adicionar temperatura externa ao dashboard][image2]

Solução de controlo remota Deteta o tipo de telemetria de temperatura externos adicionais e adiciona-lo no gráfico no dashboard de automaticamente.

## <a name="add-a-telemetry-type"></a>Adicionar um tipo de telemetria

O passo seguinte é substituir telemetria gerada pelo Node.js simulado dispositivo com um novo conjunto de valores:

1. Pare o dispositivo simulado Node.js escrevendo **Ctrl + C** na sua linha de comandos ou da shell de.

2. No ficheiro de remote_monitoring.js, pode ver os valores de base de dados de temperatura existente, humidade e telemetria temperatura externos. Adicione um valor de base de dados para **rotações/minuto** da seguinte forma:

    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```

3. O dispositivo simulado Node.js utiliza a função de **generateRandomIncrement** no ficheiro remote_monitoring.js para adicionar um incremento aleatório para os valores de base de dados. Ordenar aleatoriamente o valor de **rotações/minuto** , adicionando uma linha de código após as randomizations existentes da seguinte forma:

    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```

4. Adicione o novo valor rotações/minuto para a carga útil JSON que o dispositivo envia a IoT concentrador:

    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```

5. Execute o dispositivo de simulada Node.js utilizando o seguinte comando:

    ```
    node remote_monitoring.js
    ```

6. Observe o novo tipo de telemetria rotações/minuto que é apresentada no gráfico no dashboard de:

![Adicionar rotações/minuto ao dashboard][image3]

> [AZURE.NOTE] Poderá ter de desativar e, em seguida, ativar o dispositivo Node.js na página de **dispositivos** no dashboard para ver a alteração imediatamente.

## <a name="customize-the-dashboard-display"></a>Personalizar a apresentação de dashboard

A mensagem de **Informações sobre o dispositivo** pode incluir metadados sobre telemetria que o dispositivo pode enviar a IoT concentrador. Este metadados podem especificar os tipos de telemetria que o dispositivo envia. Modificar o valor de **deviceMetaData** no ficheiro remote_monitoring.js para incluir uma definição de **telemetria** seguir a definição de **comandos** . O fragmento de código seguinte mostra a definição de **comandos** (não se esqueça de adicionar um `,` após a definição de **comandos** ):

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [AZURE.NOTE] Solução de controlo remota utiliza uma correspondência maiúsculas e minúsculas para comparar a definição de metadados com dados no fluxo de telemetria.

Adicionar uma definição de **telemetria** conforme o fragmento de código anterior não altera o comportamento do dashboard. No entanto, os metadados também podem incluir um atributo **DisplayName** para personalizar a apresentação no dashboard. Atualize a definição de metadados de **telemetria** conforme mostrado no seguinte fragmento:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

A captura de ecrã seguinte mostra como esta alteração modifica a legenda do gráfico no dashboard de:

![Personalizar a legenda do gráfico][image4]

> [AZURE.NOTE] Poderá ter de desativar e, em seguida, ativar o dispositivo Node.js na página de **dispositivos** no dashboard para ver a alteração imediatamente.

## <a name="filter-the-telemetry-types"></a>Filtrar os tipos de telemetria

Por predefinição, o gráfico no dashboard de mostra todas as séries de dados no fluxo de telemetria. Pode utilizar os metadados de **Informações sobre o dispositivo** para suprimir a apresentação dos tipos de telemetria específico no gráfico. 

Para tornar o gráfico Mostrar apenas telemetria temperatura e humidade, omita **ExternalTemperature** dos metadados de **telemetria** do **Dispositivo de informações** da seguinte forma:

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

A **Temperatura ao ar-livre** já não é apresentada no gráfico:

![Filtrar telemetria no dashboard][image5]

Esta alteração afeta apenas a apresentação de gráfico. Os valores dos dados **ExternalTemperature** ainda são armazenados e disponibilizados para qualquer processamento de back-end.

> [AZURE.NOTE] Poderá ter de desativar e, em seguida, ativar o dispositivo Node.js na página de **dispositivos** no dashboard para ver a alteração imediatamente.

## <a name="handle-errors"></a>Processar erros

Para um fluxo de dados apresentar no gráfico, o **tipo de** nos metadados **Informações de dispositivo** tem de corresponder o tipo de dados dos valores de telemetria. Por exemplo, se os metadados especificam que o **tipo** de dados de humidade é **int** e um **duplo** encontra-se na sequência de telemetria, em seguida, de telemetria humidade não apresentados no gráfico. No entanto, os valores de **humidade** ainda são armazenados e disponibilizados para qualquer processamento de back-end.

## <a name="next-steps"></a>Próximos passos

Agora que visualizou como utilizar telemetria dinâmica, pode obter mais informações sobre como as soluções pré-configurado utilizam informações sobre o dispositivo: [monitorização de metadados de informações do dispositivo no controlo remoto solução pré-configurado][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image1]: media/iot-suite-dynamic-telemetry/image1.png
[image2]: media/iot-suite-dynamic-telemetry/image2.png
[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org
[node-linux]: https://github.com/nodejs/node-v0.x-archive/wiki/Installing-Node.js-via-package-manager
[lnk-github-repo]: https://github.com/Azure/azure-iot-sdks
