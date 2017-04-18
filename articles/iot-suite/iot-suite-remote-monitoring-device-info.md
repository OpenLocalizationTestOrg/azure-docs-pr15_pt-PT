<properties
 pageTitle="Dispositivo informações metadados da solução monitorização remoto | Microsoft Azure"
 description="Uma descrição do IoT Azure pré-configurado solução monitorização e remota respectiva arquitectura."
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
 ms.date="09/12/2016"
 ms.author="dobett"/>

# <a name="device-information-metadata-in-the-remote-monitoring-preconfigured-solution"></a>Dispositivo informações metadados da solução pré-configurado monitorização remoto

O conjunto de IoT Azure monitorização solução pré-configurado remota demonstra uma abordagem para gerir metadados de dispositivo. Este artigo descreve a abordagem que esta solução demora a permitem-lhe compreender:

- Quais os metadados dispositivo armazena a solução.
- Como a solução gere os metadados de dispositivo.

## <a name="context"></a>Contexto

A solução pré-configurado monitorização remota utiliza [Azure IoT concentrador] [ lnk-iot-hub] para ativar os seus dispositivos enviar dados para a nuvem. Concentrador IoT inclui um [registo de identidade do dispositivo] [ lnk-identity-registry] para controlar o acesso a IoT concentrador. O registo de identidade do dispositivo IoT concentrador está separado do controlo remoto monitorização de solução específicas de um *registo do dispositivo* que armazena os metadados de informações do dispositivo. Solução de controlo remota utiliza um [DocumentDB] [ lnk-docdb] base de dados para implementar o seu registo de dispositivo para armazenar metadados de informações do dispositivo. A [Arquitetura de referência do Microsoft Azure IoT] [ lnk-ref-arch] descreve a função de registo do dispositivo numa solução IoT típica.

> [AZURE.NOTE] A solução pré-configurado monitorização remota mantém o registo de identidade do dispositivo sincronizado com o registo de dispositivo. Ambos utilizam o mesmo id do dispositivo para identificar exclusivamente cada dispositivo ligado ao seu centro IoT.

A [pré-visualização de gestão do concentrador de IoT dispositivo] [ lnk-dm-preview] adiciona funcionalidades a IoT concentrador que são semelhantes às funcionalidades de gestão de informações do dispositivo é descritas neste artigo. Atualmente, a solução de controlo remota utiliza apenas geralmente funcionalidades (das versões DG) no Centro de IoT.

## <a name="device-information-metadata"></a>Metadados de informações do dispositivo

Dispositivo informações metadados JSON documento armazenado na base de dados do Gestor de registo de dispositivo DocumentDB tem a seguinte estrutura:

```
{
  "DeviceProperties": {
    "DeviceID": "deviceid1",
    "HubEnabledState": null,
    "CreatedTime": "2016-04-25T23:54:01.313802Z",
    "DeviceState": "normal",
    "UpdatedTime": null
    },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [],
  "CommandHistory": [],
  "IsSimulatedDevice": false,
  "id": "fe81a81c-bcbc-4970-81f4-7f12f2d8bda8"
}
```

- **DeviceProperties**: próprio dispositivo escreve estas propriedades e o dispositivo está a autoridade para estes dados. Outras propriedades do dispositivo de exemplo incluem fabricante, o número do modelo e o número de série. 
- **DeviceID**: O id de dispositivo exclusivo. Este valor é o mesmo no registo de identidade do concentrador de IoT dispositivo.
- **HubEnabledState**: O estado do dispositivo no Centro de IoT. Este valor inicialmente está definido como **Nulo** até que o dispositivo liga pela primeira vez. No portal do solução, um valor **Nulo** é representado como o dispositivo que está a ser "registados mas não apresentar".
- **CreatedTime**: A hora em que o dispositivo foi criado.
- **DeviceState**: O estado comunicados pelo dispositivo.
- **UpdatedTime**: A hora no dispositivo última foi atualizado através do portal de solução.
- **SystemProperties**: O portal de solução escreve as propriedades do sistema e o dispositivo não tem conhecimento estas propriedades. Uma propriedade de sistema do exemplo é o **ICCID** se a solução é autorizada com e ligado a um serviço de gestão de dispositivos com permissão para SIM.
- **Comandos**: o dispositivo suporta a uma lista dos comandos. O dispositivo fornece estas informações para a solução.
- **CommandHistory**: uma lista dos comandos que foi enviado pela solução de controlo remota para o dispositivo e o estado dos comandos.
- **IsSimulatedDevice**: um sinalizador que identifica um dispositivo como um dispositivo simulado.
- **ID de**: O identificador exclusivo do DocumentDB para este documento de dispositivo.

> [AZURE.NOTE] Informações sobre o dispositivo também pode incluir metadados para descrever telemetria que o dispositivo envia a IoT concentrador. Solução de controlo remota utiliza esta metadados de telemetria para personalizar a forma como o dashboard apresenta [telemetria dinâmica][lnk-dynamic-telemetry].

## <a name="lifecycle"></a>Ciclo de vida

Quando cria um dispositivo no portal do solução pela primeira vez, a solução cria uma entrada no seu registo de dispositivo, como é mostrado anteriormente. Muitas informações está inicialmente stubbed e a **HubEnabledState** está definido como **Nulo**. Neste momento, a solução também cria uma entrada para o dispositivo no registo de identidade do dispositivo, gera as teclas o dispositivo que utiliza para autenticar com IoT concentrador.

Quando um dispositivo pela primeira vez se liga para a solução, envia uma mensagem de informação do dispositivo. Esta mensagem de informações do dispositivo inclui as propriedades do dispositivo como o fabricante do dispositivo, o número de modelo e o número de série. Uma mensagem de informação do dispositivo também inclui uma lista dos comandos que o dispositivo suporta incluindo informações sobre quaisquer parâmetros de comando. Quando a solução recebe esta mensagem,-atualiza os metadados de informações do dispositivo no registo do dispositivo.

### <a name="view-and-edit-device-information-in-the-solution-portal"></a>Ver e editar informações sobre o dispositivo no portal do solução

Lista de dispositivos no portal do solução apresenta as seguintes propriedades de dispositivo, como colunas: **Estado**, **DeviceId**, **fabricante**, **Modelo de número**, **número de série**, **Firmware**, **plataforma**, **processador**e **RAM instalado**. As propriedades do dispositivo **de Latitude** e **Longitude** unidade a localização no mapa do Bing no dashboard. 

![Lista de dispositivos][img-device-list]

Se clicar em **Editar** no painel de **Detalhes do dispositivo** no portal do solução, pode editar todos os estas propriedades. Editar estas propriedades atualiza o registo para o dispositivo na base de dados DocumentDB. No entanto, se um dispositivo envia uma mensagem de informações do dispositivo actualizado, são substituídos quaisquer alterações efetuadas no portal do solução. Não pode editar o **DeviceId**, **Hostname**, **HubEnabledState**, **CreatedTime**, **DeviceState**e **UpdatedTime** propriedades no portal do solução porque apenas o dispositivo tem certificação sobre estas propriedades.

![Editar de dispositivo][img-device-edit]

Pode utilizar o portal de solução para remover um dispositivo da sua solução. Quando remove um dispositivo, a solução remove os metadados de informações do dispositivo do registo do dispositivo de solução e remove a entrada de dispositivo no registo de identidade do concentrador de IoT dispositivo. Antes de poder remover um dispositivo, tem de o desativar.

![Remover de dispositivo][img-device-remove]

## <a name="device-information-message-processing"></a>Processamento de mensagens de informações do dispositivo

Mensagens de informação do dispositivo que foi enviadas por um dispositivo forem distintas das mensagens de telemetria. Mensagens de informação do dispositivo incluem informações tais como as propriedades do dispositivo, os comandos que um dispositivo pode responder a e qualquer histórico de comandos. Próprio IoT Hub processos a mensagem da mesma forma processa qualquer mensagem nuvem de dispositivo e não tem conhecimento dos metadados contido numa mensagem de informações do dispositivo. Na solução monitorização remota, uma [Análise de sequência Azure] [ lnk-stream-analytics] tarefa (ASA) lê as mensagens a partir do IoT Hub. Filtros para as mensagens que contêm da tarefa a análise de sequência **DeviceInfo** **"ObjectType": "DeviceInfo"** e encaminha-os para a instância de anfitrião **EventProcessorHost** que é executado numa tarefa web. Lógica na instância **EventProcessorHost** utiliza o id do dispositivo para localizar o registo de DocumentDB para o dispositivo específico e atualize o registo. O registo de registo de dispositivo agora inclui informações tais como as propriedades do dispositivo, comandos e histórico de comandos.

> [AZURE.NOTE] Uma mensagem de informação do dispositivo é uma mensagem de dispositivo para nuvem padrão. A solução distinga entre mensagens de informações do dispositivo e mensagens de telemetria utilizando ASA consultas.

## <a name="example-device-information-records"></a>Registos de informações de dispositivo de exemplo

A solução pré-configurado monitorização remota utiliza dois tipos de registos de informações do dispositivo: registos para os dispositivos simulados implementados com os registos para os dispositivos personalizados ligar para a solução e solução.

### <a name="simulated-device"></a>Dispositivo simulado

O exemplo seguinte mostra o registo de informações de dispositivo JSON para um dispositivo simulado. Este registo tem um valor definido para **UpdatedTime**, que indica que o dispositivo tenha enviado uma mensagem de **DeviceInfo** a IoT concentrador. O registo inclui algumas propriedades comuns do dispositivo, define seis comandos os dispositivos simulados de suporte, tem o sinalizador **IsSimulatedDevice** definido como **1**.

```
{
  "DeviceProperties": {
    "DeviceID": "SampleDevice001_455",
    "HubEnabledState": true,
    "CreatedTime": "2016-01-26T19:02:01.4550695Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-01T15:28:41.8105157Z",
    "Manufacturer": "Contoso Inc.",
    "ModelNumber": "MD-369",
    "SerialNumber": "SER9009",
    "FirmwareVersion": "1.39",
    "Platform": "Plat-34",
    "Processor": "i3-2191",
    "InstalledRAM": "3 MB",
    "Latitude": 47.583582,
    "Longitude": -122.130622
  },
  "Commands": [
    {
      "Name": "PingDevice",
      "Parameters": null
    },
    {
      "Name": "StartTelemetry",
      "Parameters": null
    },
    {
      "Name": "StopTelemetry",
      "Parameters": null
    },
    {
      "Name": "ChangeSetPointTemp",
      "Parameters": [
        {
          "Name": "SetPointTemp",
          "Type": "double"
        }
      ]
    },
    {
      "Name": "DiagnosticTelemetry",
      "Parameters": [
        {
          "Name": "Active",
          "Type": "boolean"
        }
      ]
    },
    {
      "Name": "ChangeDeviceState",
      "Parameters": [
        {
          "Name": "DeviceState",
          "Type": "string"
        }
      ]
    }
  ],
  "CommandHistory": [],
  "IsSimulatedDevice": 1,
  "Version": "1.0",
  "ObjectType": "DeviceInfo",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleDevice001_455",
    "ConnectionDeviceGenerationId": "635894317219942540",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  },
  "SystemProperties": {
    "ICCID": null
  },
  "id": "7101c002-085f-4954-b9aa-7466980a2aaf"
}
```

### <a name="custom-device"></a>Dispositivo personalizado

O exemplo seguinte mostra o registo de informações de dispositivo JSON para um dispositivo personalizado e tem o sinalizador de **IsSimulatedDevice** definido como **0**. Pode ver que este dispositivo personalizado suporta dois comandos e que o portal de solução lhe enviou um comando **SetTemperature** ao dispositivo:

```
{
  "DeviceProperties": {
    "DeviceID": "mydevice01",
    "HubEnabledState": true,
    "CreatedTime": "2016-03-28T21:05:06.6061104Z",
    "DeviceState": "normal",
    "UpdatedTime": "2016-06-07T22:05:34.2802549Z"
  },
  "SystemProperties": {
    "ICCID": null
  },
  "Commands": [
    {
      "Name": "SetHumidity",
      "Parameters": [
        {
          "Name": "humidity",
          "Type": "int"
        }
      ]
    },
    {
      "Name": "SetTemperature",
      "Parameters": [
        {
          "Name": "temperature",
          "Type": "int"
        }
      ]
    }
  ],
  "CommandHistory": [
    {
      "Name": "SetTemperature",
      "MessageId": "2a0cec61-5eca-4de7-92dc-9c0bc4211c46",
      "CreatedTime": "2016-06-07T21:05:18.140796Z",
      "Parameters": {
        "temperature": 20
      },
      "UpdatedTime": "2016-06-07T21:05:18.716076Z",
      "Result": "Expired"
    }
  ],
  "IsSimulatedDevice": 0,
  "id": "6184ae0f-2d94-4fbd-91cd-4b193aecc9d1",
  "ObjectType": "DeviceInfo",
  "Version": "1.0",
  "IoTHub": {
    "MessageId": null,
    "CorrelationId": null,
    "ConnectionDeviceId": "SampleCustom",
    "ConnectionDeviceGenerationId": "635947959068246845",
    "EnqueuedTime": "0001-01-01T00:00:00",
    "StreamId": null
  }
}
```

A imagem seguinte mostra a mensagem JSON **DeviceInfo** o dispositivo enviado para atualizar os metadados de informações do dispositivo:

```
{ "ObjectType":"DeviceInfo",
  "Version":"1.0",
  "IsSimulatedDevice":false,
  "DeviceProperties": { "DeviceID":"mydevice01", "HubEnabledState":true },
  "Commands": [
    {"Name":"SetHumidity", "Parameters":[{"Name":"humidity","Type":"double"}]},
    {"Name":"SetTemperature", "Parameters":[{"Name":"temperature","Type":"double"}]}
  ]
}
```

## <a name="next-steps"></a>Próximos passos

Agora acabar de aprendizagem como pode personalizar as soluções pré-configurado, pode explorar algumas das outras funcionalidades e capacidades de soluções de conjunto de aplicações IoT pré-configuradas automaticamente:

- [Descrição geral da solução do aspeto do Office manutenção pré-configurado][lnk-predictive-overview]
- [Perguntas mais frequentes do IoT conjunto de aplicações][lnk-faq]
- [Segurança IoT aprofundadamente][lnk-security-groundup]



<!-- Images and links -->
[img-device-list]: media/iot-suite-remote-monitoring-device-info/image1.png
[img-device-edit]: media/iot-suite-remote-monitoring-device-info/image2.png
[img-device-remove]: media/iot-suite-remote-monitoring-device-info/image3.png

[lnk-iot-hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-docdb]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-ref-arch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-stream-analytics]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-dm-preview]: ../iot-hub/iot-hub-device-management-overview.md
[lnk-dynamic-telemetry]: iot-suite-dynamic-telemetry.md

[lnk-predictive-overview]: iot-suite-predictive-overview.md
[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
