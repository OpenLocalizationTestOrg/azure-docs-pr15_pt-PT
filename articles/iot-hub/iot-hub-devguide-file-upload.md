<properties
 pageTitle="Guia do programador - carregamento de ficheiro | Microsoft Azure"
 description="Guia de programador do Azure IoT concentrador - carregamento de ficheiros a partir de um dispositivo a IoT concentrador"
 services="iot-hub"
 documentationCenter=".net"
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="dobett"/>

# <a name="upload-files-from-a-device"></a>Carregar ficheiros a partir de um dispositivo

## <a name="overview"></a>Descrição geral

Como detalhadas nos [pontos finais de IoT concentrador] [ lnk-endpoints] artigo, dispositivos podem iniciar carregamentos de ficheiros ao enviar uma notificação através de um ponto final de dispositivo destinado (**/devices/ {deviceId} / ficheiros**).  Quando um dispositivo notifica IoT concentrador de um carregamento concluído, IoT concentrador gera notificações de carregamento de ficheiro que possa receber através de um ponto final de serviço destinado (**/messages/servicebound/filenotifications**) como mensagens.

Em vez de intermediação mensagens através de concentrador IoT próprio, o concentrador IoT em vez disso age como um distribuidor para uma conta de armazenamento do Windows Azure associada. Um dispositivo pedir um token de armazenamento a partir do IoT Hub que seja específico para o ficheiro que queira o dispositivo para carregar. O dispositivo utiliza o URI SA para carregar o ficheiro para o armazenamento e, quando o carregamento estar concluído o dispositivo envia uma notificação de conclusão a IoT concentrador. Concentrador IoT verifica se o ficheiro foi carregado e, em seguida, adiciona uma notificação de carregamento de ficheiro para o novo serviço destinado ficheiro notificação mensagens ponto final.

Antes de carregar um ficheiro a IoT concentrador a partir de um dispositivo, tem de configurar o seu centro associando [Um armazenamento do Windows Azure] [ lnk-associate-storage] de conta para a mesma.

O dispositivo, em seguida, pode [Iniciar um carregamento] [ lnk-initialize] e, em seguida, [notifique IoT concentrador] [ lnk-notify] quando o carregamento está concluída. Opcionalmente, quando um dispositivo notifica IoT concentrador que o carregamento estar concluído, o serviço pode gerar uma [mensagem de notificação][lnk-service-notification].

### <a name="when-to-use"></a>Quando utilizar

Utilize esta funcionalidade IoT concentrador quando precisar de carregar um ficheiro a partir de um dispositivo para o seu serviço de back-end em vez de enviar uma mensagem através de IoT concentrador.

## <a name="associate-an-azure-storage-account-with-iot-hub"></a>Associar uma conta de armazenamento do Windows Azure IoT concentrador

Para utilizar a funcionalidade de carregamento de ficheiros, primeiro tem de associar uma conta de armazenamento do Windows Azure a concentrador IoT. Pode fazê-lo através de do [Azure portal][lnk-management-portal], ou através de programação através do [fornecedor de recursos IoT concentrador REST APIs][lnk-resource-provider-apis]. Assim que tiver associadas uma conta de armazenamento do Windows Azure com o seu centro IoT, o serviço devolve um URI SA para um dispositivo quando o dispositivo inicia um pedido de carregamento de ficheiro.

> [AZURE.NOTE] O [Azure IoT concentrador SDK] [ lnk-sdks] processar automaticamente a obter o URI SA, carregar o ficheiro e notificando IoT concentrador de um carregamento concluído.

## <a name="initialize-a-file-upload"></a>Iniciar um carregamento de ficheiro

Concentrador IoT tem um ponto final especificamente para dispositivos pedir um URI de SA para armazenamento para carregar um ficheiro. O dispositivo inicia o processo de carregamento de ficheiro ao enviar uma mensagem para o concentrador IoT na `{iot hub}.azure-devices.net/devices/{deviceId}/files` com o corpo JSON seguinte:

```
{
    "blobName": "{name of the file for which a SAS URI will be generated}"
}
```

Concentrador IoT devolve o seguinte, que utiliza o dispositivo para carregar o ficheiro:

```
{
    "correlationId": "somecorrelationid",
    "hostname": "contoso.azure-devices.net",
    "containerName": "testcontainer",
    "blobName": "test-device1/image.jpg",
    "sasToken": "1234asdfSAStoken"
}
```

### <a name="deprecated-initialize-a-file-upload-with-a-get"></a>Preterido: iniciar o carregamento de um ficheiro com um GET

> [AZURE.NOTE] Esta secção descreve funcionalidade preterida como receber um URI SA a partir do IoT Hub. Utilize o método POST descrito acima.

Concentrador IoT tem dois resto pontos finais para suportar o carregamento de ficheiro, uma para obter o URI SA para armazenamento e a outra para notificar concentrador IoT de um carregamento concluído. O dispositivo inicia o processo de carregamento de ficheiro ao enviar obter para o concentrador IoT na `{iot hub}.azure-devices.net/devices/{deviceId}/files/{filename}`. O concentrador devolve um URI SA específicas para o ficheiro a ser carregados e um ID de correlação para ser utilizado depois do carregamento está concluído.

## <a name="notify-iot-hub-of-a-completed-file-upload"></a>Notificar IoT Centro de carregamento de um ficheiro concluída

O dispositivo é responsável por ao carregar o ficheiro para o armazenamento dos SDK de armazenamento do Azure a utilizar. Depois do carregamento está concluído, o dispositivo envia uma mensagem com o concentrador de IoT na `{iot hub}.azure-devices.net/devices/{deviceId}/files/notifications` com o corpo JSON seguinte:

```
{
    "correlationId": "{correlation ID received from the initial request}",
    "isSuccess": bool,
    "statusCode": XXX,
    "statusDescription": "Description of status"
}
```

O valor de `isSuccess` é uma booleana que representa se ou não o ficheiro foi carregado com êxito. O código de estado para `statusCode` é o estado para o carregamento do ficheiro ao armazenamento bem como a `statusDescription` corresponde à `statusCode`.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre o carregamento de ficheiros a partir de um dispositivo.

## <a name="file-upload-notifications"></a>Notificações de carregamento de ficheiro

Quando um dispositivo envia um ficheiro e notifica IoT concentrador de conclusão de carregamento, o serviço gera opcionalmente uma mensagem de notificação que contém o nome e armazenamento a localização do ficheiro.

Como é explicado em [pontos finais][lnk-endpoints], IoT concentrador fornece notificações de carregamento de ficheiros através de um ponto final de serviço destinado (**/messages/servicebound/fileuploadnotifications**) como mensagens. A semântica de receção para notificações de carregamento de ficheiro são os mesmos do dispositivo de nuvem mensagens e têm a mesma [mensagem ciclo de vida][lnk-lifecycle]. Cada mensagem obtida a partir do ponto final notificação de carregamento de ficheiro é um registo JSON com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Data/hora que indica que a notificação foi criada. |
| DeviceId | **DeviceId** do dispositivo que carregou o ficheiro. |
| BlobUri | URI do ficheiro carregado. |
| BlobName | Nome do ficheiro carregado. |
| LastUpdatedTime | Data/hora que indicam quando o ficheiro foi actualizada pela última vez. |
| BlobSizeInBytes | Tamanho do ficheiro carregado. |

**Exemplo**. Este é um corpo de exemplo de uma mensagem de notificação de carregamento de ficheiro.

```
{
    "deviceId":"mydevice",
    "blobUri":"https://{storage account}.blob.core.windows.net/{container name}/mydevice/myfile.jpg",
    "blobName":"mydevice/myfile.jpg",
    "lastUpdatedTime":"2016-06-01T21:22:41+00:00",
    "blobSizeInBytes":1234,
    "enqueuedTimeUtc":"2016-06-01T21:22:43.7996883Z"
}
```

## <a name="file-upload-notification-configuration-options"></a>Opções de configuração de notificação de carregamento de ficheiro

Cada concentrador IoT expõe as seguintes opções de configuração de notificações de carregamento de ficheiro:

| Propriedade | Descrição | Intervalo e predefinido |
| -------- | ----------- | ----------------- |
| **enableFileUploadNotifications** | Controla se são escritas notificações de carregamento de ficheiro para o ponto final de notificações de ficheiro. | Booleano. Predefinido: VERDADEIRO. |
| **fileNotifications.ttlAsIso8601** | TTL predefinido para as notificações de carregamento de ficheiros. | ISO_8601 intervalo até 48 H (mínimo 1 minuto). Predefinição: 1 hora. |
| **fileNotifications.lockDuration** | Bloquear duração para a fila de notificações de carregamento de ficheiro. | 5 a 300 segundos (número mínimo de 5 segundos). Predefinição: 60 segundos. |
| **fileNotifications.maxDeliveryCount** | Fila de notificação de carregamento de contagem máximo de entrega para o ficheiro. | 1 e 100. Predefinido: 100. |

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como carregar ficheiros a partir de dispositivos utilizando IoT concentrador, poderá estar interessado os seguintes tópicos de guia do programador:

- [Gestão de identidades do dispositivo no Centro de IoT][lnk-devguide-identities]
- [Controlar o acesso a IoT concentrador][lnk-devguide-security]
- [Utilizar gémeos dispositivo para sincronizar distrito e configurações][lnk-devguide-device-twins]
- [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
- [Agendar tarefas em múltiplos dispositivos][lnk-devguide-jobs]

Se gostaria experimentar a alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial IoT concentrador seguinte:

- [Como carregar ficheiros a partir de dispositivos na nuvem com IoT concentrador][lnk-fileupload-tutorial]

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-management-portal]: https://portal.azure.com
[lnk-fileupload-tutorial]: iot-hub-csharp-csharp-file-upload.md
[lnk-associate-storage]: iot-hub-devguide-file-upload.md#associate-an-azure-storage-account-with-iot-hub
[lnk-initialize]: iot-hub-devguide-file-upload.md#initialize-a-file-upload
[lnk-notify]: iot-hub-devguide-file-upload.md#notify-iot-hub-of-a-completed-file-upload
[lnk-service-notification]: iot-hub-devguide-file-upload.md#file-upload-notifications
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle

[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
