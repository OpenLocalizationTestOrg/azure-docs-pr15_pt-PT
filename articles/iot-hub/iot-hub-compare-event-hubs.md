<properties
 pageTitle="Comparar o Azure concentrador de IoT para evento Azure concentradores | Microsoft Azure"
 description="Uma comparação dos serviços Azure IoT concentrador e Azure evento concentradores realce diferenças funcionais e casos de utilização."
 services="iot-hub"
 documentationCenter=""
 authors="fsautomata"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="06/06/2016"
 ms.author="elioda"/>

# <a name="comparison-of-azure-iot-hub-and-azure-event-hubs"></a>Comparação entre o Azure IoT concentrador e concentradores evento Azure

Um dos casos de utilização principal para IoT concentrador é reunir telemetria a partir de dispositivos. Por este motivo, IoT concentrador é frequentemente comparado com [Azure evento concentradores][]. Como o concentrador IoT, o evento concentradores é um evento serviço que permite que o evento e telemetria penetração na nuvem na escala grandes, com baixa latência e fiabilidade alta de processamento.

No entanto, os serviços têm muitas diferenças, encontram-se descritos na seguinte tabela.

| Área | Concentrador IoT | Concentradores de evento |
| ---- | ------- | ---------- |
| Padrões de comunicação | Permite que o dispositivo para nuvem e na nuvem-a-dispositivo mensagens. | Só ativa penetração evento (normalmente considerada para cenários de dispositivo para nuvem). |
| Suporte de protocolo de dispositivo | Suporta MQTT, AMQP, AMQP através de WebSockets e HTTP. Para além disso, IoT concentrador funciona com o [gateway de protocolo Azure IoT][lnk-azure-protocol-gateway], uma implementação do gateway protocolo personalizáveis para suportar protocolos personalizados. | Suporta AMQP, AMQP sobre WebSockets e HTTP. |
| Segurança | Fornece o controlo de acesso revogável e de identidade por dispositivo. Consulte a [secção de segurança do Guia do Programador de IoT concentrador]. | Fornece toda a concentradores evento [políticas de acesso partilhado][Event Hubs - security], com revogação limitada suportar através [das políticas do publisher][Event Hubs publisher policies]. Soluções de IoT frequentemente são necessários para implementar uma solução personalizada para suportam credenciais por dispositivo e anti spoofing medidas. |
| Operações de monitorização | Permite IoT soluções subscrever um conjunto avançado de identidade gestão e de conectividade de eventos do dispositivo como os erros de autenticação de dispositivo individual, limitação e exceções formato incorretas. Estes eventos permitem-lhe identificar rapidamente os problemas de conectividade ao nível do dispositivo individuais. | Expõe apenas agregação métricas. |
| Escala | É otimizado para o suporte milhões de dispositivos ligados em simultâneo. | Pode suportar um número limitado mais de ligações em simultâneo – até 5.000 ligações AMQP, de acordo com [as quotas de Azure Service Bus][]. Por outro lado, evento concentradores permite-lhe especificar a partição para cada mensagem enviada. |
| Dispositivo SDK | Fornece [dispositivo SDK] [ Azure IoT Hub SDKs] para uma grande variedade de plataformas e idiomas. | É suportada no .NET e C. Também fornece AMQP e HTTP enviam interfaces. |
| Carregamento de ficheiro | Permite IoT soluções carregar ficheiros a partir de dispositivos para a nuvem. Inclui um ponto final a notificação de ficheiro para integração de fluxo de trabalho e operações de uma categoria para suporte de depuração de monitorização. | Utiliza um padrão de verificação afirmação para pedidos de ficheiros a partir de dispositivos e fornecer dispositivos com uma chave de armazenamento para a transação manualmente. |

Em resumo, mesmo se o caso de utilização apenas é penetração de telemetria nuvem de dispositivo, IoT concentrador fornece um serviço que concebidos especificamente para IoT conectividade de dispositivo. Este continuará a expandir as propostas de valor para estes cenários com funcionalidades específicas do IoT. Evento concentradores foi concebido para penetração evento numa escala de grandes, tanto no contexto de cenários, nomeadamente-Centro de dados e o Centro de dados interna.

Não é invulgar utilizar IoT concentrador e concentradores evento na mesma solução – onde IoT concentrador processa a comunicação de nuvem de dispositivo e evento concentradores processa penetração fase posterior evento para motores de processamento em tempo real.

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre planear a sua implementação IoT concentrador, consulte o artigo [escala, HA e DR][lnk-scaling].

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[Evento Azure concentradores]: ../event-hubs/event-hubs-what-is-event-hubs.md
[Secção de segurança do Guia do Programador de IoT concentrador]: iot-hub-devguide-security.md
[Event Hubs - security]: ../event-hubs/event-hubs-authentication-and-security-model-overview.md
[Event Hubs publisher policies]: ../event-hubs/event-hubs-overview.md#common-publisher-tasks
[Quotas de serviço Bus Azure]: ../service-bus-messaging/service-bus-quotas.md
[Azure IoT Hub SDKs]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
