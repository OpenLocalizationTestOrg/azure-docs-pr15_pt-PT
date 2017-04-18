<properties
 pageTitle="Métricas IoT concentrador de diagnóstico"
 description="Uma descrição geral de métricas de Azure IoT concentrador, permitindo aos utilizadores avaliar o estado de funcionamento geral do seu recurso"
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

# <a name="introduction-to-diagnostic-metrics"></a>Introdução ao métricas de diagnóstico

Métricas diagnóstico dar-lhe melhores dados sobre o estado dos recursos Azure na sua subscrição do Azure. Métricas permitem-lhe avaliar o estado de funcionamento geral do serviço e os dispositivos ligados à mesma. Estatísticas de utilizador destinado são importantes porque o podem ajudar a ver o que é o problema com os problemas de cabo IoT concentrador e ajuda sem necessitar de contactar o suporte Azure.

Pode ativar a métricas diagnóstico a partir do portal Azure.

## <a name="how-to-enable-diagnostic-metrics"></a>Como ativar métricas de diagnóstico

1. Crie um concentrador de IoT. Poderá encontrar instruções sobre como criar um concentrador de IoT na [Introdução ao] [ lnk-get-started] guia.

2. Abra o pá do seu centro IoT. A partir daí, clique em **diagnóstico**.

    ![][1]

3. Configure o seu diagnósticos ao definir o estado para **no** e selecionar uma conta de armazenamento do Windows Azure para armazenar os dados de diagnóstico. **Métricas**de verificar e, em seguida, prima **Guardar**. Tenha em atenção que a conta de armazenamento do Windows Azure tem de ser criada antecedência e é cobrados separadamente por armazenamento. Também pode optar por enviar os seus dados de diagnóstico para um ponto final concentradores do evento.

    ![][2]

4. Depois de ter configurado o diagnóstico, regresse ao pá de concentrador de IoT a **Descrição geral** . Informações de métricas são povoadas na secção de **monitorização** da pá. Clicar no gráfico abre o painel de métricas onde pode ver um resumo das informações de métricas para o seu centro IoT e editar a seleção de métricas apresentadas no mesmo. Também pode configurar alertas baseadas nos valores métricas.

    ![][3]

## <a name="metrics-and-how-to-use-them"></a>Métricas e como utilizá-las

Concentrador de IoT fornece várias métricas que lhe fornece uma descrição geral do Estado de funcionamento do seu centro e o número total dos dispositivos ligados à mesma. Pode combinar informações a partir de várias métricas de uma imagem maior do Estado de concentrador IoT o paint. A tabela seguinte descreve as métricas que cada concentrador IoT controla e como cada métrica se relaciona com o estado do concentrador IoT geral.

| Métrica | Métrica descrição | A métrica para que é utilizada |
| ---- | ---- | ---- |
| d2c.telemetry.Ingress.allProtocol | A contagem de mensagens enviadas em todos os dispositivos | Envia dados de descrição geral sobre a mensagem |
| d2c.telemetry.Ingress.Success | A contagem de todas as mensagens com êxito ao concentrador | Descrição geral de penetração mensagem bem sucedido, no concentrador |
| c2d.Commands.egress.Complete.Success | A contagem de todas as mensagens de comando efetuada pelo dispositivo de recepção em todos os dispositivos | Juntamente com as métricas no abandon e rejeitar, fornece uma descrição geral da taxa de sucesso comando C2D global |
| c2d.Commands.egress.Abandon.Success | A contagem de todas as mensagens com êxito abandonadas pelo dispositivo de recepção em todos os dispositivos | Realça potenciais problemas se as mensagens são introdução abandonadas com mais frequência que o esperado |
| c2d.Commands.egress.Reject.Success | A contagem de todas as mensagens com êxito rejeitada pelo dispositivo de recepção em todos os dispositivos | Realça potenciais problemas se as mensagens são introdução rejeitadas com mais frequência que o esperado |
| devices.totalDevices | A média, mínimo e máximo do número de dispositivos registado com o concentrador de IoT | O número de dispositivos registados ao concentrador |
| devices.connectedDevices.allProtocol | A média, mínimo e máximo do número de dispositivos ligados simultâneos | Descrição geral do número de dispositivos ligados à concentrador |

## <a name="next-steps"></a>Próximos passos

Agora que visualizou uma descrição geral de métricas diagnóstico, siga esta ligação para saber mais sobre a gestão de Azure IoT concentrador:

- [Operações de monitorização][lnk-monitor]

Para explorar ainda mais as funcionalidades do concentrador de IoT, consulte:

- [Guia de programador][lnk-devguide]
- [Simulação de um dispositivo com o SDK do Gateway IoT][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-metrics/enable-metrics-1.png
[2]: media/iot-hub-metrics/enable-metrics-2.png
[3]: media/iot-hub-metrics/enable-metrics-3.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-operations-monitoring]: iot-hub-operations-monitoring.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md
