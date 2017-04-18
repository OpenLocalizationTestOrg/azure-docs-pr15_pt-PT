<properties
 pageTitle="Operações de IoT concentrador de monitorização"
 description="Uma descrição geral de operações de Azure IoT concentrador monitorização, permitindo-lhe monitorizar o estado de operações no seu centro IoT em tempo real"
 services="iot-hub"
 documentationCenter=""
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/11/2016"
 ms.author="nberdy"/>

# <a name="introduction-to-operations-monitoring"></a>Introdução às operações de monitorização

Operações de IoT concentrador monitorização permite-lhe monitorizar o estado de operações no seu centro IoT em tempo real. Concentrador IoT controla eventos através de várias categorias de operações e, pode optar por permitir para enviar eventos a partir de uma ou mais categorias para um ponto final do seu centro IoT para processamento. Pode monitorizar os dados de erros ou configurar o processamento mais complexo com base em padrões de dados.

Concentrador IoT monitoriza cinco categorias de eventos:

- Operações de identidade do dispositivo
- Telemetria do dispositivo
- Comandos de dispositivo de nuvem
- Ligações
- Carregamentos de ficheiros

## <a name="how-to-enable-operations-monitoring"></a>Como ativar operações de monitorização

1. Crie um concentrador de IoT. Poderá encontrar instruções sobre como criar um concentrador de IoT na [Introdução ao] [ lnk-get-started] guia.

2. Abra o pá do seu centro IoT. A partir daí, clique em **operações de monitorização**.

    ![][1]

3. Selecione as categorias de monitorização que pretender monitorizar e, em seguida, clique em **Guardar**. Os eventos estão disponíveis para leitura a partir do ponto final compatível com o evento concentrador listada nas **definições de monitorização**. O ponto final IoT concentrador chama `messages/operationsmonitoringevents`.

    ![][2]

## <a name="event-categories-and-how-to-use-them"></a>Categorias de eventos e como utilizá-las

Cada operações monitorização faixas de categoria um tipo diferente de interação com IoT concentrador e cada categoria monitorização tem um esquema que define como estão estruturados eventos nessa categoria.

### <a name="device-identity-operations"></a>Operações de identidade do dispositivo

Na categoria de operações de identidade do dispositivo controla os erros que ocorrem quando tenta criar, atualizar ou eliminar uma entrada no registo de identidade do seu centro IoT. Controlo nesta categoria é útil para aprovisionar cenários.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### <a name="device-telemetry"></a>Telemetria do dispositivo

Na categoria de telemetria do dispositivo controla erros que ocorrem em concentrador IoT e estão relacionados com pipeline de telemetria. Esta categoria inclui erros que ocorrem quando eventos de telemetria (tal como a limitação) a enviar e receber eventos de telemetria (tal como o leitor não autorizada). Tenha em atenção que esta categoria não é possível detetar erros causados no próprio dispositivo a execução de código.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### <a name="cloud-to-device-commands"></a>Comandos de dispositivo de nuvem

Na categoria comandos de dispositivo de nuvem controla erros que ocorrem em concentrador IoT e estão relacionados com um pipeline de comandos do dispositivo. Esta categoria inclui erros que ocorrem quando enviar comandos (como remetente não autorizada), a receber comandos (tal como entrega contagem de saltos excedida) e receber comentários de comando (tais como comentários expiraram). Esta categoria não detetar erros a partir de um dispositivo que indevidamente processa um comando, se o comando foi entregue com êxito.

    {
         "messageSizeInBytes": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### <a name="connections"></a>Ligações

Na categoria de ligações controla erros que ocorrem quando dispositivos ligar ou desligar a partir de um concentrador de IoT. Controlo nesta categoria é útil para identificar tentativas de ligação não autorizado e para controlar quando perde uma ligação para dispositivos em áreas de fraca conectividade.

    {
         "durationMs": 1234,
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### <a name="file-uploads"></a>Carregamentos de ficheiros

Na categoria de carregamento de ficheiro controla erros que ocorrem em concentrador IoT e estão relacionadas com a funcionalidade de carregamento de ficheiro. Esta categoria inclui erros que ocorram com o URI SA (por exemplo, quando expira antes de um dispositivo notifica o concentrador de um carregamento concluído), falhados carregamentos comunicados pelo dispositivo e, quando um ficheiro não se encontra no armazenamento durante a criação de mensagem de notificação de IoT concentrador. Tenha em atenção que esta categoria não é possível detetar erros ocorridos diretamente enquanto o dispositivo está a carregar um ficheiro para o armazenamento.

    {
         "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## <a name="next-steps"></a>Próximos passos

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md