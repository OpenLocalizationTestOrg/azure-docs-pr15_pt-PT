<properties
 pageTitle="Azure IoT concentrador dimensionamento | Microsoft Azure"
 description="Descreve como dimensionar Azure IoT concentrador."
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
 ms.date="09/19/2016"
 ms.author="elioda"/>

# <a name="scaling-iot-hub"></a>Dimensionamento IoT concentrador

Azure IoT concentrador pode suportar até um milhão em simultâneo ligados dispositivos. Para obter mais informações, consulte o artigo [IoT concentrador preços][lnk-pricing]. Cada unidade de IoT concentrador permite que um determinado número de mensagens diárias.

Para dimensionar adequadamente a sua solução, considere a utilização do concentrador de IoT específica. Em particular, considere o débito pico necessários para as seguintes categorias de operações:

* Nuvem de dispositivo de mensagens
* Mensagens de dispositivo de nuvem
* Operações de registo de identidade

Para além destas informações de débito, consulte o artigo [IoT concentrador quotas e limitações][] e estruturar a solução em conformidade.

## <a name="device-to-cloud-and-cloud-to-device-message-throughput"></a>Produtividade das mensagens de nuvem de dispositivo e na nuvem para dispositivo

É a melhor forma de uma solução IoT concentrador de tamanho avaliar o tráfego numa base por unidade.

Nuvem de dispositivo mensagens siga estas diretrizes de débito constante.

| Camada | Constante débito | Taxa de enviar constante |
| ---- | -------------------- | ------------------- |
| S1 | Até 1 111 KB/minuto por unidade<br/>(1,5 GB/dia/unidade) | Média de 278 mensagens por minuto por unidade<br/>(400.000 mensagens/dia por unidade) |
| S2 | Até 16 MB/minuto por unidade<br/>(22.8 GB/dia/unidade) | Média de 4167 mensagens por minuto por unidade<br/>(6 milhões mensagens/dia por unidade.) |
| S3 | Até 814 MB/minuto por unidade<br/>(1144.4 GB/dia/unidade) | Média de 208,333 mensagens por minuto por unidade<br/>(300 milhões de mensagens/dia por unidade.) |

## <a name="identity-registry-operation-throughput"></a>Débito de operação de registo de identidade

Operações de registo de identidade IoT concentrador não são suposto runtime operações, devem para ser como estão principalmente relacionadas com o aprovisionamento de dispositivo.

Para os números de desempenho de rajada específico, consulte o artigo [IoT concentrador quotas e limitações][].

## <a name="sharding"></a>Sharding

Enquanto um concentrador de IoT única pode dimensionar para milhões de dispositivos, por vezes, a solução requer características de desempenho específicos que não garante um concentrador de IoT único. Nesse caso, recomenda-se que partição seus dispositivos para vários IoT concentradores. Vários IoT concentradores lisa tráfego rajada e obter o débito necessário ou taxas de operação que são necessárias.

## <a name="next-steps"></a>Próximos passos

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[As quotas de IoT concentrador e limitações]: iot-hub-devguide-quotas-throttling.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
