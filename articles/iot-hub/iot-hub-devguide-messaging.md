<properties
 pageTitle="Guia do programador - mensagens | Microsoft Azure"
 description="Azure guia de Programador de IoT concentrador - nuvem de dispositivo e mensagens de dispositivo de nuvem"
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

# <a name="send-and-receive-messages-with-iot-hub"></a>Enviar e receber mensagens com IoT concentrador

## <a name="overview"></a>Descrição geral

Concentrador IoT fornece as seguintes primitivas mensagens para comunicar com um dispositivo:

- [Nuvem de dispositivo] [ lnk-d2c] a partir de um dispositivo à aplicação back-end.
- [Dispositivo de nuvem] [ lnk-c2d] de uma aplicação back-end (*serviço* ou *na nuvem*).

Propriedades de Core de funcionalidade mensagens IoT concentrador estão a fiabilidade e o durabilidade das mensagens. Estas propriedades ativar resistência a conectividade intermitente no lado dispositivo e para carregar picos evento de processamento no lado na nuvem. Concentrador IoT implementa *pelo menos uma vez* garantias de entrega para o dispositivo para nuvem e na nuvem-a-dispositivo messaging.

Concentrador IoT suporta vários [dispositivo destinado protocolos] [ lnk-protocols] (como MQTT, AMQP e HTTP). Para suportar interoperabilidade de forma totalmente integrada entre protocolos, IoT concentrador define um [formato de mensagem comum] [ lnk-message-format] que suportam todos os protocolos de dispositivo destinado.

Concentrador IoT expõe um [ponto final de compatível com o evento concentrador] [ lnk-compatible-endpoint] para ativar as aplicações de back-end ler as mensagens de dispositivo para nuvem recebidas pelo concentrador.

### <a name="when-to-use"></a>Quando utilizar

Mensagens é uma funcionalidade de core do IoT concentrador. Utilizá-la sempre que precisar enviar mensagens a partir do seu dispositivo para o seu back-end ou enviar mensagens a partir do seu back-end para um dispositivo.

Para uma comparação dos serviços IoT concentrador e concentradores de evento, consulte o artigo [concentrador de comparação de IoT e evento concentradores][lnk-compare].

## <a name="device-to-cloud-messages"></a>Nuvem de dispositivo de mensagens

Enviar mensagens de dispositivo para nuvem através de um ponto final de dispositivo destinado (**/devices/ {deviceId} / mensagens/eventos**). O serviço de back-end recebe mensagens de dispositivo para nuvem através de um serviço destinado ponto final de (**/messages/events**) que é compatível com o [Evento concentradores][lnk-event-hubs]. Por conseguinte, pode utilizar padrão [integração de evento concentradores e SDK] [ lnk-compatible-endpoint] para receber mensagens de dispositivo a nuvem.

Concentrador IoT implementa o dispositivo para nuvem mensagens de forma semelhante ao [Evento concentradores][lnk-event-hubs]. Mensagens de dispositivo para nuvem do concentrador de IoT são mais do como evento concentradores *eventos* que [Serviço Bus] [ lnk-servicebus] *mensagens*.

Esta implementação tem as seguintes implicações:

* Do mesmo modo para eventos de evento concentradores, mensagens de dispositivo para nuvem são resistentes e retidos num concentrador de IoT até sete dias (consulte o artigo [Opções de configuração do dispositivo para nuvem][lnk-d2c-configuration]).
* Nuvem de dispositivo mensagens são divididas ao longo de um conjunto de partições fixos definida no momento de criação (consulte o artigo [Opções de configuração do dispositivo para nuvem][lnk-d2c-configuration]).
* Forma análoga a concentradores de evento, clientes ler mensagens de nuvem de dispositivo devem processar partições e pontos de verificação. Consulte o artigo [Concentradores evento - consumir eventos][lnk-event-hubs-consuming-events].
* Como o evento concentradores eventos, as mensagens nuvem de dispositivo podem ser no máximo 256 KB e podem ser agrupadas em secções para otimizar o envia. Secções podem ser no máximo 256 KB e, em mais de 500 mensagens.

Existem distinções, no entanto, alguns importantes entre IoT concentrador dispositivo para nuvem mensagens e concentradores evento:

* Como é explicado em a [controlar o acesso a IoT concentrador] [ lnk-devguide-security] secção, IoT concentrador permite por dispositivo autenticação e controlo de acesso.
* Concentrador IoT permite milhões de dispositivos ligados em simultâneo (consulte o artigo [Quotas e limitação][lnk-quotas]), enquanto o evento concentradores está limitado a 5000 AMQP ligações por espaço de nomes.
* Não permitir arbitrário a partições utilizando um **PartitionKey**IoT concentrador. Nuvem de dispositivo mensagens são divididas com base na sua origem **deviceId**.
* Dimensionamento IoT concentrador é ligeiramente diferente de dimensionamento concentradores do evento. Para obter mais informações, consulte o artigo [Dimensionamento concentrador IoT][lnk-guidance-scale].

> [AZURE.NOTE] Não é possível substituir IoT concentrador para evento concentradores em todos os cenários. Por exemplo, em alguns cálculos de processamento de evento, poderá ser necessário criar a partições eventos relativamente a uma propriedade diferente ou campo antes de analisar as sequências de dados. Neste cenário, pode utilizar um concentrador de evento para separar duas partes da sequência de processamento em curso. Para obter mais informações, consulte o artigo a *partições* na [Descrição geral do Azure evento concentradores][lnk-eventhub-partitions].

Para obter detalhes sobre como utilizar a nuvem de dispositivo de mensagens, consulte [IoT concentrador APIs e SDK][lnk-sdks].

> [AZURE.NOTE] Ao utilizar HTTP para enviar mensagens de nuvem de dispositivo, nomes de propriedades e valores só podem a conter carateres alfanuméricos ASCII, assim como ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="non-telemetry-traffic"></a>Tráfego de telemetria não

Muitas vezes, para além de pontos de dados de telemetria, dispositivos também enviar mensagens e pedidos de que necessitam de execução e processamento da camada de lógica de negócio de aplicação. Por exemplo, alertas críticos que tem acionar uma acção específica no back-end ou dispositivo respostas a convites para os comandos enviados a partir do back-end.

Para mais informações sobre a melhor forma de processo este tipo de mensagem, consulte o artigo o [Tutorial: como processar IoT concentrador dispositivo para nuvem mensagens] [ lnk-d2c-tutorial] tutorial.

### <a name="device-to-cloud-configuration-options"></a>Opções de configuração de dispositivo à nuvem

Um concentrador de IoT expõe as seguintes propriedades para permitem-lhe controlar mensagens de dispositivo a nuvem.

* **Contagem de partições**. Defina esta propriedade na criação para definir o número de partições para ingestão evento nuvem de dispositivo.
* **Tempo de retenção**. Esta propriedade especifica o tempo de retenção para mensagens de dispositivo a nuvem. A predefinição é um dia, mas pode ser aumentado para sete dias.

Além disso, forma análoga a concentradores de evento, IoT concentrador permite-lhe gerir grupos do consumidor no dispositivo para nuvem recebem ponto final.

Pode modificar todas as seguintes propriedades, quer através de programação através do [fornecedor de recursos IoT concentrador REST APIs][lnk-resource-provider-apis], ou utilizando o [Azure portal][lnk-management-portal].

### <a name="anti-spoofing-properties"></a>Anti-spoofing propriedades

Para evitar dispositivo spoofing em mensagens de nuvem de dispositivo, IoT concentrador carimbos todas as mensagens com as seguintes propriedades:

* **ConnectionDeviceId**
* **ConnectionDeviceGenerationId**
* **ConnectionAuthMethod**

As duas primeiras contêm **deviceId** e **generationId** do dispositivo origem, de acordo com [as propriedades de identidade do dispositivo][lnk-device-properties].

A propriedade **ConnectionAuthMethod** contém um objeto JSON serializado, com as seguintes propriedades:

```
{
  "scope": "{ hub | device}",
  "type": "{ symkey | sas}",
  "issuer": "iothub"
}
```

## <a name="cloud-to-device-messages"></a>Mensagens de dispositivo de nuvem

Enviar mensagens de dispositivo de nuvem através de um ponto final de serviço destinado (**/messages/devicebound**). Um dispositivo recebe-los através de um ponto final de específicas do dispositivo (**/devices/ {deviceId} / mensagens/devicebound**).

Cada mensagem na nuvem para o dispositivo está atribuída a um único dispositivo definindo **a** propriedade como **/devices/ {deviceId} / mensagens/devicebound**.

>[AZURE.IMPORTANT] Cada fila de dispositivo contém pelo mais de 50 nuvem-a-dispositivo mensagens. A tentar enviar mensagens de mais para os mesmos resultados de dispositivo num erro.

> [AZURE.NOTE] Quando envia mensagens de dispositivo de nuvem, nomes de propriedades e valores só podem a conter carateres alfanuméricos ASCII, assim como ``{'!', '#', '$', '%, '&', "'", '*', '*', '+', '-', '.', '^', '_', '`', '|', '~'}``.

### <a name="message-lifecycle"></a>Ciclo de vida de mensagem

Para garantir pelo menos uma vez a entrega de mensagens, IoT concentrador persiste na nuvem para o dispositivo de mensagens em filas de por dispositivo. Dispositivos explicitamente tem reconheça a *conclusão* de concentrador de IoT para removê-los da fila de espera. Isto garante RDP contra conectividade e falhas do dispositivo.

O diagrama seguinte mostra o gráfico de estado do ciclo de vida para uma mensagem de dispositivo de nuvem.

![Ciclo de vida do dispositivo de nuvem mensagem][img-lifecycle]

Quando o serviço envia uma mensagem, é considerada a *colocar em fila*. Quando um dispositivo pretende *receber* uma mensagem, IoT concentrador *bloqueios* a mensagem (define o estado para **invisível**) permitindo que outros tópicos no mesmo dispositivo para começar a receber outras mensagens. Quando um tópico de dispositivo concluir o processamento de uma mensagem, notifica IoT concentrador, *Concluir* a mensagem.

Também pode um dispositivo:

- *Rejeitar* a mensagem, o que faz com que IoT concentrador de defini-la para o estado de **Deadlettered** . Nota: dispositivos ligarem-se com MQTT não é possível rejeitar C2D mensagens.
- *Abandonar* a mensagem, o que faz com que IoT concentrador colocar a mensagem anterior na fila de espera, com o estado definido como **colocado em fila**.

Um tópico poderá falhar processar uma mensagem sem notificar IoT concentrador. Neste caso, mensagens automaticamente transição do Estado de **invisível** novamente para o estado de **colocado em fila** após um *tempo limite visibilidade (ou bloquear)*. O valor predefinido de tempo limite deste é um minuto.

Uma mensagem pode transição entre o **colocado em fila** e **invisíveis** Estados-membros no máximo, o número de vezes especificado na propriedade de **contagem de entrega max** IoT concentrador. Depois desse número de transições, IoT concentrador define o estado da mensagem para **Deadlettered**. Do mesmo modo, o concentrador IoT define o estado de uma mensagem para **Deadlettered** após o tempo de expiração (consulte o artigo [Time to live][lnk-ttl]).

Para obter um tutorial nas mensagens de dispositivo de nuvem, consulte o artigo [Tutorial: como enviar mensagens de dispositivo de nuvem com IoT concentrador][lnk-c2d-tutorial]. Para tópicos de referência sobre como os diferentes APIs e SDK expõem a funcionalidade de dispositivo de nuvem, consulte o artigo [IoT concentrador APIs e SDK][lnk-sdks].

> [AZURE.NOTE] Normalmente, mensagens de dispositivo de nuvem execute sempre a perda da mensagem seria não afeta a lógica da aplicação. Por exemplo, o conteúdo da mensagem foi com êxito persistiu no armazenamento local ou uma operação foi executada com êxito. A mensagem foi igualmente apresentem informações breves, cuja perda seria não causam impacto na funcionalidade da aplicação. Por vezes, tarefas de execução longa, pode concluir a mensagem na nuvem-a-dispositivo depois persistência a descrição da tarefa na armazenamento local. Em seguida, pode notificar o aplicação back-end com um ou mais mensagens de dispositivo para nuvem em várias fases do progresso da tarefa.

### <a name="message-expiration-time-to-live"></a>Expiração da mensagem (tempo para live)

Todas as mensagens na nuvem-a-dispositivo tem uma hora de expiração. Desta vez é definida pelo serviço (na propriedade **ExpiryTimeUtc** ) ou pelo IoT concentrador utilizando a predefinida *TTL* especificado como uma propriedade IoT concentrador. Consulte o artigo [Opções de configuração de dispositivo de nuvem][lnk-c2d-configuration].

> [AZURE.NOTE] Enviar mensagens para dispositivos desligados, é uma forma comum para tirar partido de expiração da mensagem e evitar definir a hora abreviada para live valores. Esta abordagem atinge o mesmo resultado que manter o estado de ligação de dispositivo, enquanto a ser mais eficaz. Quando pedir confirmações de mensagem, IoT concentrador notifica-o quais os dispositivos estão conseguir receber mensagens e os dispositivos que não estão online ou tem falhado.

### <a name="message-feedback"></a>Comentários de mensagem

Quando envia uma mensagem de dispositivo de nuvem, o serviço pode pedir a entrega de comentários por mensagem sobre o estado dessa mensagem final.

- Se definir a propriedade **Ack** para **positivo**, IoT concentrador gera uma mensagem de comentários, e apenas se, a mensagem de dispositivo de nuvem atingiu o estado de **concluído** .
- Se definir a propriedade **Ack** para **negativo**, IoT concentrador gera uma mensagem de comentários, se e apenas se, a mensagem de dispositivo de nuvem atinge o estado de **Deadlettered** .
- Se definir a propriedade **Ack** para **completa**, IoT concentrador gera uma mensagem de comentários em ambos os casos.

> [AZURE.NOTE] Se está **cheio** **Ack** e não receberá uma mensagem de comentários, significa que a mensagem de comentários expirou. O serviço não é possível saber o que aconteceu à mensagem original. Na prática, um serviço deve Certifique-se de que pode processar os comentários antes que expire. O tempo de validade máximo está dois dias, que lhe permite muito tempo para realizar o serviço em execução novamente se ocorrer uma falha.

Como é explicado em [pontos finais][lnk-endpoints], IoT Centro proporciona comentários através de um ponto final de serviço destinado (**/messages/servicebound/feedback**) como mensagens. A semântica para receber comentários são iguais para mensagens de dispositivo de nuvem e tem a mesma [mensagem ciclo de vida][lnk-lifecycle]. Sempre que possível, comentários de mensagem são enviadas em batches numa única mensagem, com o seguinte formato:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTime | Data/hora que indica que a mensagem foi criada. |
| ID de utilizador | `{iot hub name}` |
| ContentType | `application/vnd.microsoft.iothub.feedback.json` |

Corpo é uma serializado JSON matriz de registos, cada uma com as seguintes propriedades:

| Propriedade | Descrição |
| -------- | ----------- |
| EnqueuedTimeUtc | Data/hora que indica quando aconteceu o resultado da mensagem. Por exemplo, o dispositivo concluída ou na mensagem expirou. |
| OriginalMessageId | **MessageId** da mensagem à qual esta informação comentários refere-se na nuvem para dispositivo. |
| StatusCode | Número necessário inteiro. Utilizados nas mensagens de comentários geradas por IoT concentrador. <br/> 0 = sucesso <br/> 1 = mensagem expirou <br/> 2 = contagem de entrega máximo saltos excedida <br/> 3 = mensagem rejeitada |
| Descrição | Os valores de cadeia para **StatusCode**. |
| DeviceId | **DeviceId** do dispositivo de destino da nuvem para dispositivo mensagem à qual pertence este bloco de comentários. |
| DeviceGenerationId | **DeviceGenerationId** do dispositivo de destino da nuvem para dispositivo mensagem à qual pertence este bloco de comentários. |


>[AZURE.IMPORTANT] O serviço tem de especificar um **MessageId** para a mensagem na nuvem-a-dispositivo possam seus comentários se ligam à mensagem original.

O exemplo seguinte mostra o corpo de uma mensagem de comentários.

```
[
  {
    "OriginalMessageId": "0987654321",
    "EnqueuedTimeUtc": "2015-07-28T16:24:48.789Z",
    "StatusCode": 0
    "Description": "Success",
    "DeviceId": "123",
    "DeviceGenerationId": "abcdefghijklmnopqrstuvwxyz"
  },
  {
    ...
  },
  ...
]
```

### <a name="cloud-to-device-configuration-options"></a>Opções de configuração de dispositivo de nuvem

Cada concentrador IoT expõe as seguintes opções de configuração para o dispositivo de nuvem messaging.

| Propriedade | Descrição | Intervalo e predefinido |
| -------- | ----------- | ----------------- |
| defaultTtlAsIso8601 | TTL predefinido para mensagens de dispositivo de nuvem. | Intervalo de ISO_8601 até 2D (mínimo 1 minuto). Predefinição: 1 hora. |
| maxDeliveryCount | Contagem de entrega máximo para filas de dispositivo de nuvem por dispositivos. | 1 e 100. Predefinido: 10. |
| feedback.ttlAsIso8601 | Retenção para mensagens de serviço vinculadas comentários. | Intervalo de ISO_8601 até 2D (mínimo 1 minuto). Predefinição: 1 hora. |
| feedback.maxDeliveryCount | Contagem de entrega máximo para fila de comentários. | 1 e 100. Predefinido: 100. |

Para obter mais informações, consulte o artigo [Criar IoT concentradores][lnk-portal].

## <a name="read-device-to-cloud-messages"></a>Ler mensagens de nuvem de dispositivo

Concentrador IoT expõe um ponto final para os seus serviços de back-end ler as mensagens de dispositivo para nuvem recebidas pelo seu centro. O ponto final é o evento concentrador compatível com, que permite-lhe utilizar qualquer um dos mecanismos o serviço de evento concentradores suporta para ler as mensagens.

Quando utiliza o [Azure Service Bus SDK para .NET] [ lnk-servicebus-sdk] ou [Evento concentradores - anfitrião de processador de eventos][lnk-eventprocessorhost], pode utilizar quaisquer cadeias de ligação IoT concentrador com as permissões corretas. Em seguida, utilize **mensagens/eventos** como o nome do concentrador de evento.

Quando utilizar SDK (ou integrações produto) que estão ignora do concentrador de IoT, terá de obter um ponto final de compatível com o evento concentrador e o nome de compatível com o evento concentrador partir as definições de IoT concentrador no [portal do Azure][lnk-management-portal]:

1. Na pá de concentrador IoT, clique em **serviço de mensagens**.
2. Na secção **definições de dispositivo para nuvem** , encontrar os seguintes valores: **ponto final de compatível com o evento concentrador**, **evento concentrador compatível com nome**e **partições**.

    ![Definições de dispositivo à nuvem][img-eventhubcompatible]

> [AZURE.NOTE] Se o SDK requer um valor **Hostname** ou **espaço de nomes** , remova o esquema do **ponto final de compatível com o evento concentrador**. Por exemplo, se o seu ponto final compatível com o evento concentrador é **sb://iothub-ns-myiothub-1234.servicebus.windows.net/**, o **nome do anfitrião** seria **iothub ns-myiothub 1234.servicebus.windows.net**e o **espaço de nomes** seria ser **iothub-ns myiothub 1234**.

Em seguida, pode utilizar qualquer política de segurança do access partilhada que tenha as permissões de **ServiceConnect** para ligar ao centro do evento especificado.

Se precisar de criar uma cadeia de ligação do Centro de evento, utilizando as informações anterior, utilize o seguinte padrão:

```
Endpoint={Event Hub-compatible endpoint};SharedAccessKeyName={iot hub policy name};SharedAccessKey={iot hub policy key}
```

Segue-se uma lista de SDK e integrações que pode utilizar com os pontos finais de compatível com o evento concentrador que IoT concentrador expõe:

* [Cliente Java evento concentradores](https://github.com/hdinsight/eventhubs-client)
* [Apache tempestade spout](../hdinsight/hdinsight-storm-develop-csharp-event-hub-topology.md). Pode ver [spout origem](https://github.com/apache/storm/tree/master/external/storm-eventhubs) na GitHub.
* [Integração de motores de Apache](../hdinsight/hdinsight-apache-spark-eventhub-streaming.md)

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre a trocar mensagens com IoT concentrador.

## <a name="message-format"></a>Formato da mensagem

Mensagens de IoT concentrador incluem:

* Um conjunto de *Propriedades do sistema*. Propriedades que IoT concentrador interpreta ou conjuntos de. Este conjunto é predeterminado.
* Um conjunto de *Propriedades da aplicação*. Um dicionário de propriedades de cadeia que pode definir a aplicação e aceda, sem ser necessário especificá-las serialização do corpo da mensagem. Concentrador IoT nunca modifica estas propriedades.
* Um corpo binário opaco.

Para mais informações sobre como a mensagem é codificada nos protocolos diferentes, consulte o artigo [IoT concentrador APIs e SDK][lnk-sdks].

A tabela seguinte lista o conjunto de propriedades do sistema em mensagens de IoT concentrador.

| Propriedade | Descrição |
| -------- | ----------- |
| MessageId | Um identificador utilizador pode ser definida para a mensagem, utilizada para os padrões de resposta do pedido. Formato: Uma entre maiúsculas e minúsculas cadeia (até 128 caracteres) de caracteres alfanuméricos ASCII de 7 bits + `{'-', ':',’.', '+', '%', '_', '#', '*', '?', '!', '(', ')', ',', '=', '@', ';', '$', '''}`. |
| Número de sequência | Um número de (por fila de dispositivo exclusivo) atribuído pelo IoT concentrador para cada mensagem na nuvem para dispositivo. |
| Para | Um destino especificado na [Nuvem para dispositivo] [ lnk-c2d] mensagens. |
| ExpiryTimeUtc | Data e hora de expiração da mensagem. |
| EnqueuedTime | Data e hora que a mensagem foi recebida pelo IoT concentrador. |
| CorrelationId | Uma propriedade de cadeia numa mensagem de resposta que normalmente contém MessageId do pedido, padrões de resposta do pedido. |
| ID de utilizador | Um ID utilizado para especificar a origem de mensagens. Quando as mensagens são geradas pelo IoT concentrador, está definido para `{iot hub name}`. |
| ACK | Um gerador de mensagem de comentários. Esta propriedade é utilizada em mensagens de dispositivo de nuvem para pedir IoT concentrador para gerar mensagens de comentários estatístico como resultado do consumo da mensagem pelo dispositivo. Os valores possíveis: **nenhuma** (predefinido): nenhuma mensagem de comentários é gerada, **positivo**: receber uma mensagem de comentários se a mensagem foi concluída, **negativo**: receber uma mensagem de comentários se a mensagem expirou (ou contagem de entrega máximo foi atingida) sem a ser efetuada pelo dispositivo, ou **completo**: positivos e negativos. Para obter mais informações, consulte o artigo [comentários mensagem][lnk-feedback]. |
| ConnectionDeviceId | Um ID definido pelo IoT concentrador em nuvem de dispositivo de mensagens. Contém o **deviceId** do dispositivo que enviou a mensagem. |
| ConnectionDeviceGenerationId | Um ID definido pelo IoT concentrador em nuvem de dispositivo de mensagens. Contém o **generationId** (de acordo com [as propriedades de identidade do dispositivo][lnk-device-properties]) do dispositivo que enviou a mensagem. |
| ConnectionAuthMethod | Um método de autenticação definido pelo IoT concentrador em nuvem de dispositivo de mensagens. Esta propriedade contém informações sobre o método de autenticação utilizado para autenticar o dispositivo de enviar a mensagem. Para obter mais informações, consulte o artigo [dispositivo para o cloud anti spoofing][lnk-antispoofing].|

## <a name="communication-protocols"></a>Protocolos de comunicação

Concentrador IoT permite que os dispositivos utilizar [MQTT][lnk-mqtt], MQTT sobre WebSockets, [AMQP][lnk-amqp], AMQP sobre WebSockets e HTTP protocolos para comunicações do lado do dispositivo. A tabela seguinte fornece as recomendações de alto nível para a sua escolha do protocolo:

| Protocolo | Quando deve Selecione este protocolo |
| -------- | ------------------------------------ |
| MQTT <br> MQTT sobre WebSocket     | Utilizar em todos os dispositivos que não necessitam de ligar vários dispositivos (cada com as suas próprias credenciais por dispositivo) sobre a mesma ligação TLS. |
| AMQP <br> AMQP sobre WebSocket    | Utilizar no campo e nuvem de gateways para tirar partido de ligação multiplexing em todos os dispositivos. |
| HTTP    | Utilize para os dispositivos que não é possível suportar outros protocolos. |

Considere os seguintes pontos ao escolher o protocolo para comunicações do lado do dispositivo:

* **Nuvem-a-dispositivo padrão**. HTTP não tem uma forma eficaz para implementar o push do servidor. Como tal, quando estiver a utilizar HTTP, dispositivos as inquérito IoT concentrador para mensagens de dispositivo de nuvem. Esta abordagem é ineficaz para o dispositivo e o IoT concentrador. Em diretrizes HTTP atuais, cada um dos dispositivos deve consultar para mensagens de cada 25 minutos ou mais. Por outro lado, MQTT AMQP suporte e push do servidor quando receber mensagens de dispositivo de nuvem. Que os mesmos ativam puxa imediata das mensagens a partir do IoT Hub para o dispositivo. Se a latência da entrega é uma questão, MQTT ou AMQP são os protocolos melhor a utilizar. Para dispositivos ligados raramente, HTTP funciona bem.
* **Os gateways de campo**. Quando utilizar MQTT e HTTP, não é possível ligar vários dispositivos (cada com as suas próprias credenciais por dispositivo) utilizando a mesma ligação TLS. Assim, para [cenários de gateway campo][lnk-azure-gateway-guidance], estes protocolos são reduzidos porque necessitam de uma ligação de TLS entre o gateway de campo e o concentrador de IoT para cada dispositivo ligada ao gateway campo.
* **Dispositivos de baixa recurso**. Bibliotecas de MQTT e HTTP tem um menores que as bibliotecas AMQP mais pequeno. Como tal, se o dispositivo tem de recursos (por exemplo, menor que 1 MB de RAM) limitados, estes protocolos poderá a implementação do protocolo apenas disponível.
* **Traversal de rede**. O protocolo AMQP padrão utiliza a porta 5671, enquanto MQTT recebe na porta 8883, que pode causar problemas em redes que estejam fechados para protocolos que não sejam HTTP. MQTT sobre WebSockets, AMQP sobre WebSockets e HTTP estão disponíveis para ser utilizado neste cenário.
* **Tamanho de carga útil**. MQTT e AMQP são protocolos binários, que resultam payloads mais compactos que HTTP.

> [AZURE.NOTE] Ao utilizar HTTP, cada um dos dispositivos deve consultar para mensagens de dispositivo de nuvem cada 25 minutos ou mais. No entanto, durante desenvolvimento, é aceitável para consultar mais frequentemente em minutos 25.

## <a name="port-numbers"></a>Números de porta

Dispositivos podem comunicar com IoT concentrador no Azure utilizando vários protocolos. Normalmente, a escolha de protocolo é condicionada pelos requisitos específicos da solução. A tabela seguinte lista as portas saídas que tem de estar abertas para um dispositivo possam utilizar um protocolo específico:

| Protocolo | Porta (s) |
| -------- | ------- |
| MQTT     | 8883    |
| MQTT sobre WebSockets | 443    |
| AMQP     | 5671    |
| AMQP sobre WebSockets | 443    |
| HTTP     | 443     |
| LWM2M (gestão de dispositivos) | 5684 |

Depois de ter criado um concentrador de IoT numa região Azure, o concentrador mantém o mesmo endereço IP para o tempo de vida desse concentrador. No entanto, para manter a qualidade do serviço, se Microsoft move o concentrador IoT para uma unidade de escala diferente, em seguida, é atribuído um novo endereço IP.

## <a name="notes-on-mqtt-support"></a>Notas sobre o suporte MQTT

Concentrador IoT implementa o protocolo de v3.1.1 MQTT com as seguintes limitações e comportamento específico:

  * **QoS 2 não é suportada**. Quando um cliente de dispositivo publica uma mensagem com **QoS 2**, IoT concentrador fecha a ligação de rede. Quando subscreve um cliente de dispositivo a um tópico com **QoS 2**, IoT concentrador concede máximo QoS nível 1 no pacote **SUBACK** .
  * **Manter mensagens não persistirem**. Se um cliente de dispositivo publica uma mensagem com o sinalizador de RETER definido como 1, IoT concentrador adiciona o **x-optar ativamente por participar-reter** propriedade da aplicação para a mensagem. Neste caso, IoT concentrador não continuarem a mensagem de reter, mas em vez disso, transmite-lo para a aplicação de back-end.

Para obter mais informações, consulte o artigo [IoT concentrador MQTT suporte][lnk-devguide-mqtt].

Como uma consideração final, devem consultar o [gateway de protocolo Azure IoT] [ lnk-azure-protocol-gateway] que permite-lhe implementar um gateway de protocolo personalizado de alto desempenho interfaces diretamente com IoT concentrador. O gateway de protocolo Azure IoT permite-lhe personalizar o protocolo de dispositivo para acomodar implementações do brownfield MQTT ou outros protocolos personalizados. No entanto, esta abordagem requerem que executar e trabalhar com um gateway protocolo personalizado.

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como enviar e receber mensagens com IoT concentrador, poderá estar interessado os seguintes tópicos de guia do programador:

- [Carregar ficheiros a partir de um dispositivo][lnk-devguide-upload]
- [Gestão de identidades do dispositivo no Centro de IoT][lnk-devguide-identities]
- [Controlar o acesso a IoT concentrador][lnk-devguide-security]
- [Utilizar gémeos dispositivo para sincronizar distrito e configurações][lnk-devguide-device-twins]
- [Invocar um método direto num dispositivo][lnk-devguide-directmethods]
- [Agendar tarefas em múltiplos dispositivos][lnk-devguide-jobs]

Se quiser experimentar alguns dos conceitos descritos neste artigo, poderá estar interessado os tutoriais IoT concentrador seguintes:

- [Introdução ao Azure IoT concentrador][lnk-getstarted-tutorial]
- [Como enviar mensagens de dispositivo de nuvem com IoT concentrador][lnk-c2d-tutorial]
- [Como mensagens de processo IoT concentrador dispositivo à nuvem][lnk-d2c-tutorial]


[img-lifecycle]: ./media/iot-hub-devguide-messaging/lifecycle.png
[img-eventhubcompatible]: ./media/iot-hub-devguide-messaging/eventhubcompatible.png

[lnk-resource-provider-apis]: https://msdn.microsoft.com/library/mt548492.aspx
[lnk-azure-gateway-guidance]: iot-hub-devguide-endpoints.md#field-gateways
[lnk-guidance-scale]: iot-hub-scaling.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md
[lnk-amqp]: http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf
[lnk-mqtt]: http://docs.oasis-open.org/mqtt/mqtt/v3.1.1/mqtt-v3.1.1.pdf
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/
[lnk-event-hubs-consuming-events]: ../event-hubs/event-hubs-programming-guide.md#event-consumers
[lnk-management-portal]: https://portal.azure.com
[lnk-servicebus]: http://azure.microsoft.com/documentation/services/service-bus/
[lnk-eventhub-partitions]: ../event-hubs/event-hubs-overview.md#partitions
[lnk-portal]: iot-hub-create-through-portal.md

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-compatible-endpoint]: iot-hub-devguide-messaging.md#read-device-to-cloud-messages
[lnk-protocols]: iot-hub-devguide-messaging.md#communication-protocols
[lnk-message-format]: iot-hub-devguide-messaging.md#message-format
[lnk-d2c-configuration]: iot-hub-devguide-messaging.md#device-to-cloud-configuration-options
[lnk-device-properties]: iot-hub-devguide-identity-registry.md#device-identity-properties
[lnk-ttl]: iot-hub-devguide-messaging.md#message-expiration-time-to-live
[lnk-c2d-configuration]: iot-hub-devguide-messaging.md#cloud-to-device-configuration-options
[lnk-lifecycle]: iot-hub-devguide-messaging.md#message-lifecycle
[lnk-feedback]: iot-hub-devguide-messaging.md#message-feedback
[lnk-antispoofing]: iot-hub-devguide-messaging.md#anti-spoofing-properties
[lnk-compare]: iot-hub-compare-event-hubs.md

[lnk-devguide-upload]: iot-hub-devguide-file-upload.md
[lnk-devguide-identities]: iot-hub-devguide-identity-registry.md
[lnk-devguide-security]: iot-hub-devguide-security.md
[lnk-devguide-device-twins]: iot-hub-devguide-device-twins.md
[lnk-devguide-directmethods]: iot-hub-devguide-direct-methods.md
[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-servicebus-sdk]: https://www.nuget.org/packages/WindowsAzure.ServiceBus
[lnk-eventprocessorhost]: http://blogs.msdn.com/b/servicebus/archive/2015/01/16/event-processor-host-best-practices-part-1.aspx


[lnk-getstarted-tutorial]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-tutorial]: iot-hub-csharp-csharp-c2d.md
[lnk-d2c-tutorial]: iot-hub-csharp-csharp-process-d2c.md
