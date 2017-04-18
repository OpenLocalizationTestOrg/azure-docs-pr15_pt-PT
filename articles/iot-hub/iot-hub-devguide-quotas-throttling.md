<properties
 pageTitle="Guia do programador - quotas e limitação | Microsoft Azure"
 description="Guia de programador IoT concentrador Azure - descrição da quotas que se aplicam a IoT concentrador e comportamento optimização esperado"
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

# <a name="reference---quotas-and-throttling"></a>Referência - Quotas e limitação

## <a name="quotas-and-throttling"></a>Quotas e limitação

Cada subscrição Azure pode conter no máximo de 10 IoT concentradores.

Cada concentrador IoT está aprovisionado com um determinado número de unidades num SKU específico (para obter mais informações, consulte o artigo [Preços do Azure IoT concentrador][lnk-pricing]). O número de unidades e SKU determinam a quota de diária máxima das mensagens que pode enviar.

O SKU também determina os limites de optimização IoT concentrador impõe em todas as operações.

## <a name="operation-throttles"></a>Limitações de operação

Limitações de operação são as limitações da taxa que são aplicadas os intervalos minutos e destinam-se para evitar abuse. Tenta IoT concentrador evitar a devolver erros sempre que possível, mas é iniciado exceções a devolver se borboleta foi suspenso durante muito tempo.

Segue-se a lista de limitações impostas. Valores referem-se a um concentrador individual.

| Optimizar | Valor por concentrador |
| -------- | ------------- |
| Operações de registo de identidade (criar, obter, lista, atualizar, eliminar) | mínimo/5000/unidade (para S3) <br/> 100/mínimo/unidade de (para S1 e S2). |
| Ligações de dispositivos | 6000/seg/unidade (para S3), 120/seg/unidade (para S2), sec/12/unidade (para S1). <br/>Mínimo de 100/seg. <br/> Por exemplo, duas S1 unidades são 2\*12 = 24/seg, mas tem pelo menos de 100/seg ao longo do seu unidades. Com o nove S1 unidades, com 108/sec (9\*12) ao longo do seu unidades. |
| Nuvem de dispositivo envia | 6000/seg/unidade (para S3), 120/seg/unidade (para S2), sec/12/unidade (para S1). <br/>Mínimo de 100/seg. <br/> Por exemplo, duas S1 unidades são 2\*12 = 24/seg, mas tem pelo menos de 100/seg ao longo do seu unidades. Com o nove S1 unidades, com 108/sec (9\*12) ao longo do seu unidades. |
| Nuvem-a-dispositivo envia | mínimo/5000/unidade (para S3), 100/mínimo/unidade (para S1 e S2). |
| Nuvem para dispositivo recebe | mínimo/50000/unidade (para S3), 1000/mínimo/unidade (para S1 e S2). |
| Operações de carregamento de ficheiros | ficheiro de 5000 carregar notificações/mínimo/unidade (para S3), carregamento de ficheiro 100 notificações/mínimo/unidade (para S1 e S2). <br/> 10000 SA URIs pode ser saída para uma conta de armazenamento do Windows Azure ao mesmo tempo.<br/> 10 SA URIs/dispositivo pode ser saída de uma só vez. | 

É importante esclarecer que a limitação de *ligações de dispositivos* controla a taxa que é possível estabelecer ligações de dispositivo nova com um concentrador de IoT e não o número máximo de dispositivos ligados em simultâneo. O comando de aceleração depende o número de unidades que são aprovisionado para o concentrador.

Por exemplo, se comprar uma única unidade S1, receberá um controlo de potência de 100 ligações por segundo. Isto significa que para se ligar 100.000 dispositivos, bastam pelo menos de 1000 segundos (aproximadamente 16 minutos). No entanto, pode ter tantas dispositivos em simultâneo ligados à medida que tem dispositivos registados no registo de identidade do dispositivo.

Para uma aprofundada do concentrador de IoT limitação comportamento, consulte a mensagem [e concentrador IoT limitação]do blogue[lnk-throttle-blog].

>[AZURE.NOTE] Em qualquer altura determinada, é possível aumentar quotas ou limites de limitação por aumentar o número de unidades aprovisionadas num concentrador de IoT.

>[AZURE.IMPORTANT] Operações de registo de identidade destinam-se para utilização de tempo de execução em gestão de dispositivos e cenários de aprovisionamento. Ler ou actualizar um grande número de identidades do dispositivo é suportado através da [importação e exportação tarefas][lnk-importexport].

## <a name="next-steps"></a>Próximos passos

Outros tópicos de referência neste guia do Programador de IoT concentrador incluem:

- [Pontos finais de IoT concentrador][lnk-devguide-endpoints]
- [Linguagem de consulta para gémeos, métodos e tarefas][lnk-devguide-query]
- [Suporte de IoT concentrador MQTT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md