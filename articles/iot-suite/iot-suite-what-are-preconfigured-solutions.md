<properties
 pageTitle="Azure IoT soluções pré-configuradas | Microsoft Azure"
 description="Uma descrição do IoT Azure pré-configurado soluções e os respetivos arquitetura com ligações para recursos adicionais."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="08/09/2016"
 ms.author="dobett"/>

# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Quais são as soluções do conjunto de aplicações do IoT Azure pré-configurado?

As soluções do conjunto de aplicações do IoT Azure pré-configurado são implementações do padrões de solução IoT comuns que pode implementar Azure utilizar a sua subscrição. Pode utilizar as soluções pré-configurado:

- Como ponto de partida para o seus próprio solutions IoT.
- Para saber mais sobre padrões comuns na estrutura de solução IoT e desenvolvimento.

Cada solução pré-configurado é uma implementação completa e ponto a ponto que utiliza simulados dispositivos para gerar telemetria.

Para além de implementação e execução das soluções no Azure, pode transferir o código de origem concluída e, em seguida, personalizar e expandir a solução para aos seus requisitos de IoT específicos.

> [AZURE.NOTE] Para implementar uma das soluções pré-configurado, visite [o conjunto de aplicações do Microsoft Azure IoT][lnk-azureiotsuite]. O artigo [começar a trabalhar com as soluções IoT pré-configurado] [ lnk-getstarted-preconfigured] fornece mais informações sobre como implementar e executar uma das soluções.

A tabela seguinte mostra como as soluções mapeiam para funcionalidades específicas de IoT:

| Solução | Ingestão de dados | Identidade do dispositivo | Comando e controlo | Regras e ações | Análise de aspeto do Office |
|------------------------|-----|-----|-----|-----|-----|
| [Monitorização remota][lnk-getstarted-preconfigured] | Sim | Sim | Sim | Sim | -   |
| [Manutenção do aspeto do Office][lnk-predictive-maintenance] | Sim | Sim | Sim | Sim | Sim |

- *Ingestão de dados*: penetração de dados na escala na nuvem.
- *Identidade do dispositivo*: gestão de identidades exclusivas de todos os dispositivos ligados.
- *Comando e o controlo*: enviar mensagens para um dispositivo a partir da nuvem para fazer com que o dispositivo para efetuar algumas ação.
- *Regras e ações*: solução back-end utiliza regras para agir em dados específicos do dispositivo a nuvem.
- *Análise de aspeto do Office*: solução back-end aplica-se analisa dados dispositivo à nuvem para prever quando ações específicas que devem ter lugar. Por exemplo, analisar telemetria do motor de avião para determinar quando vence manutenção motor.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Descrição geral da solução monitorização pré-configurado remoto

Vamos optou discutir a solução pré-configurado monitorização remota neste artigo, uma vez que ilustra a vários elementos da estrutura comuns que partilham as outras soluções.

O diagrama seguinte ilustra os elementos chave da solução monitorização remoto. As secções abaixo fornecem mais informações sobre estes elementos.

![Arquitetura de solução de monitorização pré-configurado remota][img-remote-monitoring-arch]

## <a name="devices"></a>Dispositivos

Quando implementar a solução pré-configurado monitorização remota, quatro dispositivos simulados são previamente aprovisionados da solução que simular um dispositivo refrigeração. Estes dispositivos simulados têm um modelo de temperatura e humidade incorporado que emite telemetria. Nestes dispositivos simulados são incluídos para ilustrar o fluxo de ponto a ponto de dados através da solução e para fornecer uma fonte conveniente de telemetria e um alvo de comandos de, se for um programador de back-end utilizando a solução como ponto de partida para uma implementação personalizada.

Quando um dispositivo liga pela primeira vez a IoT concentrador da solução pré-configurado monitorização remoto, a mensagem de informações do dispositivo enviada para o concentrador IoT enumera a lista de comandos que o dispositivo pode responder. Na remota solução pré-configurado monitorização, os comandos são: 

- *Dispositivo de ping*: O dispositivo responde a este comando com qualquer tipo de confirmação. Isto é útil para verificar que o dispositivo está a escutar e ainda active.
- *Iniciar telemetria*: instrui o dispositivo para começar a enviar telemetria.
- *Parar de telemetria*: instrui o dispositivo para parar o envio de telemetria.
- *Alterar definir ponto de temperatura*: controla os valores de telemetria de temperatura simulada o dispositivo envia. Isto é útil para testar a lógica de back-end.
- *Telemetria diagnóstico*: controla se o dispositivo deve enviar a temperatura externa como telemetria.
- *Alterar o estado do dispositivo*.: define a propriedade de metadados do Estado de dispositivo que o dispositivo de relatórios. Isto é útil para testar a lógica de back-end.

Pode adicionar mais simulados dispositivos para a solução que emitir a mesmo telemetria e responder aos comandos da mesma. 

## <a name="iot-hub"></a>Concentrador IoT

Nesta solução pré-configurado, a instância IoT concentrador corresponde ao *Gateway na nuvem* numa típica [arquitetura de solução IoT][lnk-what-is-azure-iot].

Um concentrador de IoT recebe telemetria a partir de dispositivos um ponto final único. Um concentrador de IoT também mantém o dispositivo pontos finais específicos onde cada dispositivos, podem recuperá os comandos que são enviados para o mesmo.

O concentrador IoT disponibiliza uma de telemetria recebida através de telemetria a lado do serviço de ponto final de leitura.

## <a name="azure-stream-analytics"></a>Análise de sequência Azure

A solução pré-configurado utiliza três [Azure da cadeia Analytics] [ lnk-asa] tarefas (ASA) para filtrar a sequência de telemetria a partir de dispositivos:


- *Tarefa de DeviceInfo* - exporta dados a um concentrador de evento que encaminhe o dispositivo registo mensagens específicas, enviadas quando um dispositivo liga pela primeira vez ou em resposta a um comando de **alterar o estado de dispositivo** , para o registo de dispositivo de solução (uma base de dados DocumentDB). 
- *Tarefa de telemetria* - envia todos os telemetria não processada ao armazenamento de Blobs do Azure fria armazenamento e calcula agregações de telemetria apresentados no dashboard de solução.
- *Tarefa de regras* - filtra a sequência de telemetria para valores que excedem limiares qualquer regra e exporta os dados a um concentrador de evento. Quando uma regra é acionada, a vista de portal dashboard solução apresenta este evento como uma nova linha na tabela Histórico alarme e accionadores uma ação com base nas definições definidas nas vistas de regras e ações no portal do solução.

Nesta solução pré-configurado, as tarefas de ASA formam parte de para a **solução IoT back-end** numa [arquitetura de solução IoT]típica[lnk-what-is-azure-iot].

## <a name="event-processor"></a>Processador de eventos

Nesta solução pré-configurado, o processador de eventos faz parte da **solução IoT back-end** numa típica [arquitetura de solução IoT][lnk-what-is-azure-iot].

As tarefas de **DeviceInfo** e **regras** ASA enviam a sua saída para concentradores de evento para entrega de outros serviços de back-end. A solução utiliza uma [EventPocessorHost] [ lnk-event-processor] instância, a ser executada numa [WebJob][lnk-web-job], para ler as mensagens a partir destes concentradores evento. O **EventProcessorHost** utiliza os dados de **DeviceInfo** para atualizar os dados de dispositivo na base de dados DocumentDB e utiliza os dados de **regras** para invocar a aplicação de lógica e atualizar que os alertas de apresentam no portal do solução.

## <a name="device-identity-registry-and-documentdb"></a>Registo de identidade do dispositivo e DocumentDB

Cada concentrador IoT inclui um [registo de identidade do dispositivo] [ lnk-identity-registry] que armazena chaves de dispositivo. Concentrador IoT utiliza estas informações autenticar dispositivos - um dispositivo tem de estar registado e tiver a tecla válida antes de poder aceder ao concentrador.

Esta solução armazena informações adicionais sobre dispositivos, como o seu estado, os comandos que suportam e outros metadados. A solução utiliza uma base de dados DocumentDB para armazenar dados este dispositivo solução específicos e o portal de solução obtém dados a partir desta base de dados DocumentDB para visualização e edição.

A solução também tem de manter as informações no registo de identidade do dispositivo sincronizado com o conteúdo da base de dados DocumentDB. O **EventProcessorHost** utiliza os dados da tarefa de análise da cadeia de **DeviceInfo** para gerir a sincronização.

## <a name="solution-portal"></a>Portal de solução

![Dashboard de solução][img-dashboard]

O portal de solução é uma IU baseada na web que é implementada na nuvem, como parte da solução pré-configurado. Permite-lhe:

- Ver histórico de telemetria e alarme num dashboard.
- Aprovisionar novos dispositivos.
- Gerir e monitorizar dispositivos.
- Envie comandos para dispositivos específicos.
- Gerir regras e ações.

Nesta solução pré-configurado, o portal de solução de formulários do parte de **solução IoT back-end** e da **transformação e da conectividade de negócio** no típica [arquitetura de solução IoT][lnk-what-is-azure-iot].

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre IoT arquitecturas de solução, consulte o artigo [Serviços Microsoft Azure IoT: arquitetura de referência][lnk-refarch].

Agora que sabe que uma solução pré-configurado for, pode começar por implementar a solução de *monitorização remota* pré-configurado: [começar a trabalhar com as soluções pré-configurado][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md