<properties
    pageTitle="Azure dispositivo IoT SDK para C - IoTHubClient | Microsoft Azure"
    description="Saiba mais sobre como utilizar a biblioteca de IoTHubClient no dispositivo Azure IoT SDK para C"
    services="iot-hub"
    documentationCenter=""
    authors="olivierbloch"
    manager="timlt"
    editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="cpp"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="09/06/2016"
     ms.author="obloch"/>

# <a name="microsoft-azure-iot-device-sdk-for-c--more-about-iothubclient"></a>SDK do Microsoft Azure IoT dispositivo para C – mais informações sobre IoTHubClient

O [primeiro artigo](iot-hub-device-sdk-c-intro.md) nesta série introduzida o **dispositivo do Microsoft Azure IoT SDK para C**. Artigo explicado que não existem duas camadas de arquitecturais no SDK. Na base é a biblioteca de **IoTHubClient** que diretamente gere a comunicação com IoT concentrador. Também é a biblioteca de **serializador** que constrói na parte superior para fornecer serialização serviços. Este artigo irá fornecemos detalhes adicionais na biblioteca de **IoTHubClient** .

O artigo anterior descrita como utilizar a biblioteca de **IoTHubClient** para enviar eventos a IoT concentrador e receber mensagens. Este artigo expande o debate e explica como gerir com mais precisão *Quando* enviar e receber dados, introdução ao para a **API de níveis inferiores**. Também explicaremos como anexar propriedades para eventos (e obtê-los a partir de mensagens) utilizando a propriedade processamento funcionalidades na biblioteca de **IoTHubClient** . Por fim, irá fornecemos explicação adicional de diferentes formas para lidar com as mensagens recebidas a partir do IoT Hub.

O artigo conclui por abrangendo apenas alguns tópicos diversos, incluindo mais informações sobre credenciais de dispositivo e como alterar o comportamento da **IoTHubClient** através das opções de configuração.

Vamos utilizar os exemplos SDK **IoTHubClient** para explicar estes tópicos. Se pretender segui-los, consulte o artigo o **iothub\_cliente\_exemplo\_http** e **iothub\_cliente\_exemplo\_amqp** aplicações que são incluídas no dispositivo Azure IoT SDK para C. tudo o que é descrito nas secções seguintes é demonstrado nestes exemplos.

Pode localizar o **Azure IoT dispositivo SDK para C** no [Microsoft Azure IoT SDK](https://github.com/Azure/azure-iot-sdks) GitHub repositório e ver os detalhes da API na [referência da C API](http://azure.github.io/azure-iot-sdks/c/api_reference/index.html).

## <a name="the-lower-level-apis"></a>As API de níveis inferiores

O artigo anterior descrito a operação básica da **IotHubClient** dentro do contexto da **iothub\_cliente\_exemplo\_amqp** aplicação. Por exemplo, o explicação das como iniciar a biblioteca utilizando este código.

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Também é descrito como enviar eventos utilizando esta chamada de função.

```
IoTHubClient_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message);
```

O artigo também descrita como receber mensagens através do registo de uma função de chamada de retorno.

```
int receiveContext = 0;
IoTHubClient_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext);
```

O artigo mostrava também como libertar recursos utilizando código como as seguintes.

```
IoTHubClient_Destroy(iotHubClientHandle);
```

No entanto, existem funções acompanha para cada um dos seguintes APIs:

-   IoTHubClient\_TA\_CreateFromConnectionString

-   IoTHubClient\_TA\_SendEventAsync

-   IoTHubClient\_TA\_SetMessageCallback

-   IoTHubClient\_TA\_Destroy


Estas funções todos os incluam "Tudo" no nome do API. Diferente de que, os parâmetros de cada uma das seguintes funções são idênticos aos seus homólogos não ta. No entanto, o comportamento destas funções é diferente de uma forma importante.

Quando ligar para o **IoTHubClient\_CreateFromConnectionString**, as bibliotecas subjacentes criar um novo tópico que é executada em segundo plano. Este tópico envia eventos para e recebe as mensagens a partir de, IoT concentrador. Sem essa tópico é criado quando trabalhar com "Tudo" APIs. A criação do tópico de fundo é uma conveniência para o programador. Não tem de se preocupar explicitamente eventos de enviar e receber mensagens a partir do IoT Hub – para isto acontecer automaticamente em segundo plano. Em contrapartida, "Tudo" APIs dar-lhe controlo explícito sobre comunicação com IoT Hub, caso precise da mesma.

Para compreender melhor isto, vamos ver um exemplo:

Quando ligar para o **IoTHubClient\_SendEventAsync**, que está a realmente fazer é colocação de evento um intervalo de tempo. Tópico de fundo criado quando ligar para o **IoTHubClient\_CreateFromConnectionString** continuamente monitoriza esta memória intermédia e envia quaisquer dados que contém a IoT concentrador. Isto acontece em segundo plano ao mesmo tempo que o tópico principal está a executar outros trabalhos.

Da mesma forma, quando registar uma função de chamada de retorno para mensagens com **IoTHubClient\_SetMessageCallback**, está a com a indicação SDK ter tópico de fundo invocar a função de chamada de retorno quando uma mensagem é recebida, independentemente do tópico de principal.

"Tudo" APIs não cria um tópico de fundo. Em vez disso, uma nova API tem de ser chamado explicitamente enviar e receber dados a partir do IoT Hub. Isso é demonstrado no seguinte exemplo.

O **iothub\_cliente\_exemplo\_http** aplicação que está incluída no SDK demonstra as API de níveis inferiores. Nessa amostra, iremos enviar eventos a IoT concentrador com código como as seguintes:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Message_%d_From_IoTHubClient_LL_Over_HTTP", i);
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));

IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

As primeiras três linhas crie a mensagem e a última linha envia o evento. No entanto, tal como mencionado anteriormente, "Enviar" significa que o evento que os dados simplesmente são colocados numa memória intermédia. Nada é transmitido na rede quando chamamos **IoTHubClient\_TA\_SendEventAsync**. Ordem de penetração realmente os dados a IoT concentrador, tem de chamar **IoTHubClient\_TA\_DoWork**, como neste exemplo:

```
while (1)
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código (a partir do **iothub\_cliente\_exemplo\_http** aplicação) repetidamente chamadas **IoTHubClient\_TA\_DoWork**. Sempre que **IoTHubClient\_TA\_DoWork** é chamado envia alguns eventos da memória intermédia a IoT concentrador e obtém uma mensagem em fila a ser enviada para o dispositivo. O último caso significa que se podemos registado uma função de chamada de retorno para mensagens, em seguida, a chamada de retorno é chamada (partindo do princípio de que todas as mensagens são colocadas na fila para cima). Vamos seria ter registado como uma função de chamada de retorno com código como as seguintes:

```
IoTHubClient_LL_SetMessageCallback(iotHubClientHandle, ReceiveMessageCallback, &receiveContext)
```

O motivo que **IoTHubClient\_TA\_DoWork** é denominadas frequentemente um ciclo é que cada vez que é chamado, envia *algumas* memória intermédia eventos a IoT concentrador e obtém *a seguinte* mensagem na fila para o dispositivo. Cada chamada não é uma garantia para enviar todas as memória intermédia eventos ou obter todas as mensagens em fila. Se pretender enviar todos os eventos na memória intermédia e, em seguida, continue com outros processamento pode substituir este ciclo com código como as seguintes:

```
IOTHUB_CLIENT_STATUS status;

while ((IoTHubClient_LL_GetSendStatus(iotHubClientHandle, &status) == IOTHUB_CLIENT_OK) && (status == IOTHUB_CLIENT_SEND_STATUS_BUSY))
{
    IoTHubClient_LL_DoWork(iotHubClientHandle);
    ThreadAPI_Sleep(1000);
}
```

Este código chamadas **IoTHubClient\_TA\_DoWork** até que todos os eventos na memória intermédia foram enviados a IoT concentrador. Tenha em atenção que isto não também implica recebidas todas as mensagens em fila de espera. Parte do motivo para isto é que verificar a existência de mensagens "todos" não é como determinista uma ação. O que acontece se obtiver "todos" das mensagens, mas, em seguida, outro é enviado para o dispositivo imediatamente a seguir? A melhor forma mais fácil lidar com que seja com um limite de tempo programado. Por exemplo, a função de chamada de retorno da mensagem foi repor um temporizador sempre que é chamado. Em seguida, pode escrever lógica para continuar o processamento se, por exemplo, não tem sido recebidas mensagens nos últimos *X* segundos.

Quando está ingressing terminado eventos e receber mensagens, certifique-se chamar a função de limpar o recursos correspondente.

```
IoTHubClient_LL_Destroy(iotHubClientHandle);
```

Basicamente é apenas um conjunto de APIs para enviar e receber dados com um tópico de fundo e outro conjunto de APIs que é que a mesma coisa sem tópico de fundo. Muitas os programadores poderão preferir as APIs não TA, mas as API de níveis inferiores são úteis quando pretende que o programador explícito controlo sobre transmissões de rede. Por exemplo, alguns dispositivos recolhem dados ao longo do tempo e eventos de penetração apenas em intervalos especificados (por exemplo, uma vez uma hora ou uma vez por dia). As API de níveis inferiores dar-lhe a capacidade para controlar explicitamente quando enviar e receber dados a partir do IoT Hub. As outras pessoas irão simplesmente preferir a simplificar fornecidas as API de níveis inferiores. Tudo o que acontece no tópico de principal em vez de algumas acontece de trabalho em segundo plano.

Independentemente modelo que escolher, certifique-se de que forma consistente nos quais APIs que utiliza. Se começar ao contactar o suporte **IoTHubClient\_TA\_CreateFromConnectionString**, certifique-se de que só é utilizar as API de níveis inferiores correspondentes para qualquer seguimento trabalho:

-   IoTHubClient\_TA\_SendEventAsync

-   IoTHubClient\_TA\_SetMessageCallback

-   IoTHubClient\_TA\_Destroy

-   IoTHubClient\_TA\_DoWork

O oposto também é verdadeiro. Se começar com **IoTHubClient\_CreateFromConnectionString**, em seguida, utilize as APIs não ta para qualquer processamento adicional.

No Azure IoT dispositivo SDK para C, consulte o artigo o **iothub\_cliente\_exemplo\_http** aplicação para um exemplo completo das APIs de níveis inferiores. O **iothub\_cliente\_exemplo\_amqp** aplicação pode ser referenciada por um exemplo completo de que não sejam - TA APIs do.

## <a name="property-handling"></a>Processamento de propriedade

Até ao momento quando descrevemos envio de dados, podemos tenha sido referir-se ao corpo da mensagem. Por exemplo, considere este código:

```
EVENT_INSTANCE message;
sprintf_s(msgText, sizeof(msgText), "Hello World");
message.messageHandle = IoTHubMessage_CreateFromByteArray((const unsigned char*)msgText, strlen(msgText));
IoTHubClient_LL_SendEventAsync(iotHubClientHandle, message.messageHandle, SendConfirmationCallback, &message)
```

Este exemplo envia uma mensagem a IoT concentrador com o texto "Olá". No entanto, IoT concentrador também lhe permite propriedades para ser anexado a cada mensagem. Propriedades estão pares valor/nome que podem ser anexados a mensagem. Por exemplo, vamos pode modificar o código anterior para anexar uma propriedade à mensagem:

```
MAP_HANDLE propMap = IoTHubMessage_Properties(message.messageHandle);
sprintf_s(propText, sizeof(propText), "%d", i);
Map_AddOrUpdate(propMap, "SequenceNumber", propText);
```

Vamos começar por telefone **IoTHubMessage\_propriedades** e transmitir a alça da nossa mensagem. O que recomendamos voltar é uma **mapa\_PROCESSAR** referência que permite-nos começar a adicionar propriedades. O último acontece ao contactar o suporte **mapa\_AddOrUpdate**, que assume uma referência a um mapa\_ALÇA, o nome da propriedade e o valor da propriedade. Com esta API podemos pode adicionar tantas propriedades quiser.

Quando o evento é lido a partir do **Evento concentradores**, o destinatário pode enumerar as propriedades e obter os respetivos valores correspondentes. Por exemplo, no .NET seria fazê-lo acedendo a [coleção propriedades no objeto EventData](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventdata.properties.aspx).

No exemplo anterior, podemos estiver a anexar propriedades para um evento que enviarmos a IoT concentrador. Também podem ser anexadas propriedades mensagens recebidas a partir do IoT Hub. Se queremos recuperar as propriedades de uma mensagem, podemos pode utilizar o código como o seguinte na função de chamada de retorno nosso mensagem:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .

    // Retrieve properties from the message
    MAP_HANDLE mapProperties = IoTHubMessage_Properties(message);
    if (mapProperties != NULL)
    {
        const char*const* keys;
        const char*const* values;
        size_t propertyCount = 0;
        if (Map_GetInternals(mapProperties, &keys, &values, &propertyCount) == MAP_OK)
        {
            if (propertyCount > 0)
            {
                printf("Message Properties:\r\n");
                for (size_t index = 0; index < propertyCount; index++)
                {
                    printf("\tKey: %s Value: %s\r\n", keys[index], values[index]);
                }
                printf("\r\n");
            }
        }
    }

    . . .
}
```

A chamada para **IoTHubMessage\_propriedades** devolve o **mapa\_PROCESSAR** referência. Vamos passar, em seguida, essa referência para **mapa\_GetInternals** para obter uma referência a uma matriz dos pares valor/nome (bem como uma contagem das propriedades). Nesse momento é tão simples como enumeração das propriedades para obter os valores que pretendemos.

Não tem de utilizar as propriedades na sua aplicação. No entanto, se precisar de defini-las eventos ou obtê-los a partir de mensagens, a biblioteca **IoTHubClient** torna mais fácil.

## <a name="message-handling"></a>Tratamento de mensagens

Tal como mencionado anteriormente, chegada de mensagens a partir do IoT Hub responde a biblioteca de **IoTHubClient** por invocar uma função de chamada de retorno registado. Existe um parâmetro de retorno desta função que merece algumas explicação adicional. Eis um excerto da função chamada de retorno na **iothub\_cliente\_exemplo\_http** aplicação de exemplo:

```
static IOTHUBMESSAGE_DISPOSITION_RESULT ReceiveMessageCallback(IOTHUB_MESSAGE_HANDLE message, void* userContextCallback)
{
    . . .
    return IOTHUBMESSAGE_ACCEPTED;
}
```

Tenha em atenção que o tipo devolvido é **IOTHUBMESSAGE\_eliminação\_resultado** e neste caso em particular é devolvido **IOTHUBMESSAGE\_ACEITES**. Existem outros valores que podemos pode voltar a partir desta função, alterar a forma como a biblioteca **IoTHubClient** reage para a chamada de retorno da mensagem. Aqui estão as opções.

-   **IOTHUBMESSAGE\_ACEITES** – a mensagem foi processada com êxito. A biblioteca de **IoTHubClient** não será invocar a função de chamada de retorno novamente com a mesma mensagem.

-   **IOTHUBMESSAGE\_rejeitado** – a mensagem não foi processada e que não haja nenhuma vontade para fazê-lo no futuro. A biblioteca de **IoTHubClient** não deve invocar a função de chamada de retorno novamente com a mesma mensagem.

-   **IOTHUBMESSAGE\_ABANDONED** – a mensagem não foi processada com êxito, mas a biblioteca de **IoTHubClient** deverá chamar a função de chamada de retorno novamente com a mesma mensagem.

Para os dois primeiros códigos de retorno, a biblioteca **IoTHubClient** envia uma mensagem a IoT concentrador que indica que a mensagem ser eliminada da fila de dispositivo e não entregues novamente. O efeito líquido é a mesma (a mensagem é eliminada da fila de dispositivo), mas se a mensagem foi aceitaram ou rejeitaram ainda estiver registado.  Gravação esta distinção é útil para remetentes da mensagem quem podem ouvir para comentários e saber se um dispositivo tem aceitaram ou rejeitaram uma mensagem específica.

No último caso uma mensagem é também é enviada para IoT concentrador, mas indica que a mensagem deve ser reenviada. Normalmente, irá abandonar uma mensagem se encontrar algum erro, mas pretende experimentar a mensagem para voltar a processar. Em contrapartida, rejeitar uma mensagem é conveniente quando se deparar com um erro irrecuperável (ou simplesmente decidir que não pretende processar a mensagem).

Em qualquer caso, tenha em atenção os diferentes de códigos de retorno para que pode conferir o comportamento que pretende a partir da biblioteca de **IoTHubClient** .

## <a name="alternate-device-credentials"></a>Credenciais do dispositivo alternativo

Como é explicado anteriormente, a primeira coisa a fazer quando trabalhar com a biblioteca de **IoTHubClient** é obter uma **IOTHUB\_cliente\_PROCESSAR** com uma chamada como o seguinte:

```
IOTHUB_CLIENT_HANDLE iotHubClientHandle;
iotHubClientHandle = IoTHubClient_CreateFromConnectionString(connectionString, AMQP_Protocol);
```

Os argumentos para **IoTHubClient\_CreateFromConnectionString** são a cadeia de ligação do nosso dispositivo e um parâmetro que indica o protocolo utilizamos para comunicar com IoT concentrador. A cadeia de ligação tem um formato que é apresentada da seguinte forma:

```
HostName=IOTHUBNAME.IOTHUBSUFFIX;DeviceId=DEVICEID;SharedAccessKey=SHAREDACCESSKEY
```

Existem quatro partes de informações nesta cadeia de: nome do concentrador de IoT, sufixo IoT concentrador, ID do dispositivo e chave de acesso partilhado. Obter o nome de domínio completamente qualificado (FQDN) de um concentrador de IoT quando criar a instância do concentrador de IoT no portal do Azure — Isto dá-lhe o nome do concentrador de IoT (a primeira parte do FQDN) e o sufixo de concentrador IoT (o resto do FQDN). Obter o ID do dispositivo e a tecla de acesso partilhado quando registar o seu dispositivo com IoT concentrador (conforme descrito no [artigo anterior](iot-hub-device-sdk-c-intro.md)).

**IoTHubClient\_CreateFromConnectionString** dá-lhe uma forma de iniciar a biblioteca. Se preferir, pode criar um novo **IOTHUB\_cliente\_PROCESSAR** ao utilizar estes parâmetros individuais em vez da cadeia de ligação. Pode fazê-com o seguinte código:

```
IOTHUB_CLIENT_CONFIG iotHubClientConfig;
iotHubClientConfig.iotHubName = "";
iotHubClientConfig.deviceId = "";
iotHubClientConfig.deviceKey = "";
iotHubClientConfig.iotHubSuffix = "";
iotHubClientConfig.protocol = HTTP_Protocol;
IOTHUB_CLIENT_HANDLE iotHubClientHandle = IoTHubClient_LL_Create(&iotHubClientConfig);
```

Isto efetua a mesma coisa que **IoTHubClient\_CreateFromConnectionString**.

Parecer óbvio que iria pretende utilizar **IoTHubClient\_CreateFromConnectionString** em vez deste método mais verboso de inicialização. Tenha em atenção, no entanto, quando registar um dispositivo no Centro de IoT obter é um ID do dispositivo e chave do dispositivo (não uma cadeia de ligação). A ferramenta de **Gestor de dispositivos** SDK introduzida no [artigo anterior](iot-hub-device-sdk-c-intro.md) utiliza bibliotecas no **serviço Azure IoT SDK** para criar a cadeia de ligação a partir do ID do dispositivo, a chave do dispositivo e o nome de anfitrião de IoT concentrador. Assim que entra em contacto **IoTHubClient\_TA\_criar** poderá ser preferível uma vez que guarda no passo do gerar uma cadeia de ligação. Utilize independentemente do método for conveniente.

## <a name="configuration-options"></a>Opções de configuração

Até ao momento tudo o que é descrito sobre a forma como funciona a biblioteca **IoTHubClient** reflete o comportamento predefinido. No entanto, existem algumas opções que pode definir para alterar como funciona a biblioteca. Isto é feito, tirando partido de **IoTHubClient\_TA\_SetOption** API. Considere neste exemplo:

```
unsigned int timeout = 30000;
IoTHubClient_LL_SetOption(iotHubClientHandle, "timeout", &timeout);
```

Existem duas opções que são normalmente utilizadas:

-   **SetBatching** (booleano) – se **Verdadeiro**, em seguida, dados enviados a IoT concentrador é enviada em lotes. Se **Falso**, em seguida, as mensagens são enviadas individualmente. A predefinição é **Falso**. Tenha em atenção que a opção **SetBatching** aplica-se apenas para o protocolo HTTP e não para os protocolos MQTT ou AMQP.

-   **Tempo limite** (não assinado int) – este valor é representado em milissegundos. Se enviar um pedido de HTTP ou receber uma resposta demora mais tempo que desta vez, em seguida, a ligação o tempo limite.

A opção lotes é importante. Por predefinição, os eventos de ingresses biblioteca individualmente (um único evento é tudo o que passam para **IoTHubClient\_TA\_SendEventAsync**). Se a opção lotes for **Verdadeiro**, a biblioteca recolhe quantos eventos como-o partir do intervalo de tempo (por excesso para o tamanho máximo da mensagem que IoT concentrador aceitará).  O lote de evento é enviado para IoT concentrador numa única chamada HTTP (os eventos individuais estão agrupados numa matriz JSON). Ativar lotes normalmente resulta num ganhos de desempenho grande, uma vez que está a redução IDA de rede. Também significativamente reduz largura de banda uma vez que vai enviar um conjunto de cabeçalhos de HTTP com um lote de evento em vez de um conjunto de cabeçalhos para cada evento individual. Se não tiver um motivo específico para fazer caso contrário, normalmente irá pretende ativar lotes.

## <a name="next-steps"></a>Próximos passos

Este artigo descreve em detalhe o comportamento da biblioteca de **IoTHubClient** que se encontram no **dispositivo Azure IoT SDK para C**. Com esta informação, que deve ter uma boa compreensão das capacidades da biblioteca de **IoTHubClient** . O [seguinte artigo](iot-hub-device-sdk-c-serializer.md) fornece detalhes semelhantes na biblioteca de **serializador** .

Para saber mais sobre como desenvolver IoT concentrador, consulte o [SDK do concentrador de IoT][lnk-sdks].

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-sdks]: iot-hub-devguide-sdks.md

[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
