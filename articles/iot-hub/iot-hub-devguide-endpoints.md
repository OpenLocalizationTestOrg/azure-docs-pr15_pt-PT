<properties
 pageTitle="Guia do programador - os pontos finais de IoT concentrador | Microsoft Azure"
 description="Guia do programador IoT concentrador Azure - informações de referência sobre os pontos finais de IoT concentrador"
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

# <a name="reference---iot-hub-endpoints"></a>Referência - os pontos finais de IoT concentrador

## <a name="list-of-iot-hub-endpoints"></a>Lista de pontos finais de IoT concentrador

Azure IoT concentrador é um serviço de inquilino com várias que expõe o respetiva funcionalidade para vários intervenientes. O diagrama seguinte mostra os pontos finais vários que expõe IoT concentrador.

![Pontos finais de IoT concentrador][img-endpoints]

Segue-se uma descrição dos pontos finais:

* **Fornecedor de recursos**. O fornecedor de recurso IoT concentrador expõe um [Gestor de recursos do Azure] [ lnk-arm] interface que permite que os proprietários de subscrições Azure criar e eliminar IoT concentradores e actualizar IoT concentrador propriedades. Propriedades de IoT concentrador governem [políticas de segurança de nível de concentrador][lnk-accesscontrol], por oposição a controlo de acesso ao nível do dispositivo e opções funcionais para envio de mensagens na nuvem para o dispositivo e nuvem de dispositivo. O fornecedor de recurso IoT concentrador também permite-lhe para [Exportar identidades do dispositivo][lnk-importexport].
* **Gestão de identidades do dispositivo**. Cada concentrador IoT expõe um conjunto de HTTP resto os pontos finais para a gestão de identidades do dispositivo (criar, obter, atualizar e eliminar). [Identidades do dispositivo] [ lnk-device-identities] são utilizadas para controlo de autenticação e o acesso do dispositivo.
* **Gestão de duplos dispositivos**. Cada concentrador IoT expõe um conjunto de ponto final de HTTP resto do serviço destinado de consulta e atualizar [gémeos dispositivo] [ lnk-twins] (atualizar etiquetas e propriedades).
* **Gestão de projectos**. Cada concentrador IoT expõe um conjunto de ponto final de HTTP resto do serviço direccionado para consultar e gerir [tarefas de][lnk-jobs].
* **Os pontos finais de dispositivo**. Para cada dispositivo aprovisionado no registo de identidade do dispositivo, IoT concentrador expõe um conjunto de pontos finais que pode utilizar um dispositivo para enviar e receber mensagens:
    - *Enviar mensagens de dispositivo a nuvem*. Utilizar este ponto final para [enviar mensagens de dispositivo para nuvem][lnk-d2c].
    - *Receber mensagens de dispositivo de nuvem*. Um dispositivo utiliza este ponto final para receber alvo [na nuvem-a-dispositivo mensagens][lnk-c2d].
    - *Iniciar carregamentos de ficheiros*. Um dispositivo utiliza este ponto final para receber um URI de SA de armazenamento do Azure a partir do IoT Hub para [carregar um ficheiro][lnk-upload].
    - *Obter e propriedades do duplos de atualização*. Um dispositivo utiliza esta pontos finais para aceder à sua [duplos dispositivo][lnk-twins]de propriedades.
    - *Pedidos de métodos direta de receção*. Um dispositivo utiliza esta pontos finais para ouvir [métodos diretos][lnk-methods]do pedidos.

    Estes pontos finais são expostos utilizando [MQTT v3.1.1][lnk-mqtt], HTTP 1.1 e [AMQP 1.0] [ lnk-amqp] protocolos. Note que AMQP também está disponível ao longo do [WebSockets] [ lnk-websockets] na porta 443.
    
    Os métodos de e gémeos endpoins estão disponíveis apenas utilizar [MQTT v3.1.1][lnk-mqtt].

* **Pontos finais de serviço**. Cada concentrador IoT expõe um conjunto de pontos finais que seu back-end da aplicação pode utilizar para comunicar com os seus dispositivos. Estes pontos finais são atualmente apenas exposta a utilizar o [AMQP] [ lnk-amqp] protocolo, exceto o ponto final de invocação método que é apresentado quando se clica através de HTTP 1.1.
    - *Receber mensagens de dispositivo a nuvem*. Este ponto final é compatível com o [Azure evento concentradores][lnk-event-hubs]. Um serviço de back-end utilizá-lo para ler todas as [mensagens de dispositivo para nuvem] [ lnk-d2c] enviados por seus dispositivos.
    - *Enviar mensagens de dispositivo de nuvem e receber confirmações de entrega*. Estes pontos finais ativar a sua aplicação back-end enviar fiável [mensagens de dispositivo de nuvem][lnk-c2d]e receber a entrega correspondente ou confirmações de expiração.
    - *Receber notificações de ficheiro*. Este ponto final mensagens permite-lhe receber notificações de quando os seus dispositivos com êxito carregar um ficheiro. 
    - *Chamada do método direta*. Este ponto final permite que um serviço back-end invocar um [método directo] [ lnk-methods] num dispositivo.

A [IoT concentrador APIs e SDK] [ lnk-sdks] artigo descreve as várias formas de aceder a estes pontos finais.

Por fim, é importante ter em atenção que todos os pontos finais de IoT concentrador utilizam os [TLS] [ lnk-tls] protocolo e nenhum ponto final nunca são expostas em canais não encriptado/não segura.

## <a name="field-gateways"></a>Gateways de campo

Numa solução IoT, um *gateway campo* encontra-se entre os dispositivos e os pontos finais IoT concentrador. É normalmente localizado fechar para os seus dispositivos. Os dispositivos de comunicam diretamente com o gateway campo utilizando um protocolo suportado pelos dispositivos. O gateway campo liga a um ponto final IoT concentrador utilizando um protocolo que é suportado pelo IoT concentrador. Um gateway campo pode ser hardware altamente especializado ou um computador de baixa power executar software que cumpre restantes o cenário de fim para fim para o qual destina-se o gateway.

Pode utilizar o [Azure IoT Gateway SDK] [ lnk-gateway-sdk] para implementar um gateway de campo. Este SDK oferece funcionalidades específicas, tal como a capacidade de multiplex comunicação a partir de vários dispositivos para a mesma ligação a IoT concentrador.

## <a name="next-steps"></a>Próximos passos

Outros tópicos de referência neste guia do Programador de IoT concentrador incluem:

- [Linguagem de consulta para gémeos, métodos e tarefas][lnk-devguide-query]
- [Quotas e limitação][lnk-devguide-quotas]
- [Suporte de IoT concentrador MQTT][lnk-devguide-mqtt]

[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk

[img-endpoints]: ./media/iot-hub-devguide-endpoints/endpoints.png
[lnk-amqp]: https://www.amqp.org/
[lnk-mqtt]: http://mqtt.org/
[lnk-websockets]: https://tools.ietf.org/html/rfc6455
[lnk-arm]: ../azure-resource-manager/resource-group-overview.md
[lnk-event-hubs]: http://azure.microsoft.com/documentation/services/event-hubs/

[lnk-tls]: https://tools.ietf.org/html/rfc5246


[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-accesscontrol]: iot-hub-devguide-security.md#access-control-and-permissions
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities
[lnk-d2c]: iot-hub-devguide-messaging.md#device-to-cloud-messages
[lnk-device-identities]: iot-hub-devguide-identity-registry.md
[lnk-upload]: iot-hub-devguide-file-upload.md
[lnk-c2d]: iot-hub-devguide-messaging.md#cloud-to-device-messages
[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-twins]: iot-hub-devguide-device-twins.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-jobs]: iot-hub-devguide-jobs.md

[lnk-devguide-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md