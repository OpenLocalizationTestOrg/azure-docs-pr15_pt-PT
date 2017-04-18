<properties
 pageTitle="Guia do programador - métodos diretas | Microsoft Azure"
 description="Guia do programador IoT concentrador Azure - utilizar métodos direta para invocar código nos seus dispositivos"
 services="iot-hub"
 documentationCenter=".net"
 authors="nberdy"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="nberdy"/>

# <a name="invoke-a-direct-method-on-a-device-preview"></a>Invocar um método direto num dispositivo (pré-visualização)

## <a name="overview"></a>Descrição geral

Concentrador IoT dá-lhe capacidade de invocar métodos em dispositivos a partir da nuvem. Métodos representam uma interação pedido resposta com um dispositivo semelhante a uma chamada de HTTP, uma vez que estes teve êxito ou falharem imediatamente (depois de um limite de tempo especificado pelo utilizador) permitir que o utilizador saber o estado da chamada. Isto é útil para obter cenários onde o curso de imediato ação é diferente dependendo se o dispositivo conseguiu responder, tal como enviar uma SMS reactivar de segurança para um dispositivo se um dispositivo estiver offline (SMS a ser mais dispendioso do que uma chamada de método).

Poderá pensar um método como uma chamada de procedimento remoto diretamente para o dispositivo. Apenas os métodos que foram implementados num dispositivo podem ser chamados a partir da nuvem. Se tentativas de invocar um método num dispositivo que não possui esse método definido na nuvem, a chamada do método falha.

Cada método dispositivo destina-se um único dispositivo. [Tarefas de] [ lnk-devguide-jobs] fornece uma maneira de invocar métodos em múltiplos dispositivos e fila invocação de método para dispositivos desligados.

Qualquer pessoa com permissões de **serviço de ligar** IoT concentrador pode invocar um método num dispositivo.

### <a name="when-to-use"></a>Quando utilizar

Métodos de dispositivo são semelhantes às [mensagens de dispositivo de nuvem] [ lnk-devguide-messages] nessa ambos ativar nuvem back-end passar informações para um dispositivo, mas diferem de formas fundamentais. Em termos conceptuais, métodos são síncrono e não resistentes, mensagens de dispositivo de nuvem estão assíncronas com até 48 horas de durabilidade.

Métodos de seguem um padrão de resposta do pedido e não são resistentes. Falta de durabilidade fornece duas vantagens de imediatas quando são comandar dispositivos:

- **Comentários de imediato na execução do método** significa que não precisa para gerir a correlação entre o pedido e responder.
- **Débito superior** significa que o operações podem ser efetuadas mais rápido porque IoT concentrador não está a fornecer qualquer durabilidade. Concentrador IoT permite mais chamadas de método por unidade que mensagens de dispositivo de nuvem.

Mensagens de nuvem-a-dispositivo não são necessariamente comandos para o dispositivo, mas preferir representam um serviço na nuvem prisma algumas bit das informações para o dispositivo para que este Pegue na sua tempos livres e para que o dispositivo pode ou não pode responder. Mensagens de dispositivo de nuvem ter uma mais longa do limite de tempo (até 48 horas) enquanto métodos expirar muito mais rapidamente.

Utilize dispositivo métodos para invocação de comando imediata num dispositivo e de tarefas para agendada invocação de um comando num dispositivo.

## <a name="method-lifecycle"></a>Ciclo de vida do método

Métodos são implementados no dispositivo e podem exigir igual a zero ou mais entradas no payload método para declarar corretamente. Invocar um método direto através de um serviço destinado URI (`{iot hub}/twins/{device id}/methods/`). Um dispositivo recebe métodos diretos através de um tópico MQTT específicas do dispositivo (`$iothub/methods/POST/{method name}/`). Poderá suportamos métodos em adicionais protocolos de rede do lado do dispositivo no futuro.

> [AZURE.NOTE] Quando invocar um método direto num dispositivo, valores e nomes de propriedade podem apenas conter US-ASCII imprimível alfanuméricos, exceto qualquer no conjunto de seguinte: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

Os métodos de são síncronos e quer teve êxito ou falhar após o período de tempo limite (predefinido: 30 segundos, pode ser definidos o para 3600 segundos). Métodos são úteis na interativos cenários onde pretende que um dispositivo para funcionar apenas se o dispositivo está onlinehttps e receção comandos, tal como ativar uma versão simplificada a partir de um número de telefone. Nestes cenários, que pretende ver um sucesso imediato ou o fracasso para que o serviço de nuvem pode agir no resultado mais cedo possível. O dispositivo pode devolver algumas corpo da mensagem como resultado o método, mas não é necessário para o método para fazê-lo. Não existe nenhuma garantia numa ordenação ou de qualquer semântica de simultaneidade em chamadas para o método.

Chamadas de método de dispositivo são HTTP só do lado na nuvem e MQTT só do lado do dispositivo.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre como utilizar métodos diretos.

## <a name="invoke-a-direct-method-from-a-back-end-app"></a>Invocar um método direto através de uma aplicação de back-end

### <a name="method-invocation"></a>Chamada do método

Exe método directo num dispositivo é chamadas HTTP que incluem:

- *URI* específicas para o dispositivo (`{iot hub}/twins/{device id}/methods/`)
- O *método* de mensagem
- *Cabeçalhos* que contêm a autorização pedir ID, tipo de conteúdo e codificação de conteúdo
- Um JSON transparente *corpo* no seguinte formato:

```
{
    "methodName": "reboot",
    "timeoutInSeconds": 200,
    "payload": {
        "input1": "someInput",
        "input2": "anotherInput"
    }
}
```

  Tempo limite é em segundos. Se o limite de tempo não estiver definida, esta assume a predefinição de 30 segundos.
  
### <a name="response"></a>Resposta

Back-end recebe uma resposta que inclui:

- *Código de estado HTTP*, que é utilizado para erros provenientes do centro do IoT, incluindo um erro 404 para dispositivos não está ligado
- *Cabeçalhos* que contêm etag, pedir ID, tipo de conteúdo e codificação de conteúdo
- Um JSON *corpo* no seguinte formato:

```
{
    "status" : 201,
    "payload" : {...}
}
```
  
   Ambos `status` e `body` são fornecidos pelo dispositivo e utilizado para responder com o código de estado do dispositivo e/ou a descrição.

## <a name="handle-a-direct-method-on-a-devcie"></a>Alça de um método direta num devcie

### <a name="method-invocation"></a>Chamada do método

Dispositivos recebem pedidos de método directo no tópico MQTT:`$iothub/methods/POST/{method name}/?$rid={request id}`

O corpo que recebe o dispositivo está no seguinte formato:

```
{
    "input1": "someInput",
    "input2": "anotherInput"
}
```

Pedidos de método são QoS 0.

### <a name="response"></a>Resposta

O dispositivo envia respostas a convites para `$iothub/methods/res/{status}/?$rid={request id}`, em que:

 - O `status` propriedade é o estado fornecido dispositivo execução do método.
 - O `$rid` propriedade é o ID do pedido a partir da chamada do método recebido a partir do IoT Hub.

Corpo é definido pelo dispositivo e pode ser qualquer Estado.

## <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Agora que aprendeu como utilizar diretos métodos, poderá estar interessado o seguinte tópico do Guia do programador:

- [Agendar tarefas em múltiplos dispositivos][lnk-devguide-jobs]

Se gostaria experimentar a alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial IoT concentrador seguinte:

- [Utilizar os métodos de dispositivo de nuvem][lnk-methods-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md

[lnk-devguide-jobs]: iot-hub-devguide-jobs.md
[lnk-methods-tutorial]: iot-hub-c2d-methods.md
[lnk-devguide-messages]: iot-hub-devguide-messaging.md
