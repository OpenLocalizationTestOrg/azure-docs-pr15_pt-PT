<properties
 pageTitle="Monitorização remota pré-configurado solução guiadas | Microsoft Azure"
 description="Uma descrição do IoT Azure pré-configurado solução monitorização e remota respectiva arquitectura."
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
 ms.date="08/17/2016"
 ms.author="dobett"/>

# <a name="remote-monitoring-preconfigured-solution-walkthrough"></a>Monitorização remota pré-configurado solução guiadas

## <a name="introduction"></a>Introdução

O controlo remoto IoT conjunto de aplicações de monitorização [pré-configurado solução] [ lnk-preconfigured-solutions] é uma aplicação de uma ponto a ponto monitorização solução para vários computadores a ser executada em localizações remotas. A solução combina serviços Azure chave para fornecer uma implementação genérica do cenário de negócio e pode utilizar como ponto de partida para a sua própria implementação. Pode [Personalizar] [ lnk-customize] a solução aos seus próprios requisitos de negócio específicas.

Este artigo explica alguns dos elementos principais da solução monitorização remoto para permitir a compreender como funciona. Este dados de conhecimento da ajudam-o a:

- Resolução de problemas da solução.
- Planear como personalizar a solução aos seus requisitos específicos. 
- Estruture o seu próprio IoT solução que utiliza serviços Azure.

## <a name="logical-architecture"></a>Arquitetura de lógica

O diagrama seguinte descreve os componentes lógicos da solução pré-configurado:

![Arquitetura de lógica](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


## <a name="simulated-devices"></a>Dispositivos simulados

Na solução pré-configurado, o dispositivo simulado representa um dispositivo refrigeração (como um edifício ar condicionado ou unidade de manuseamento air instalações). Quando implementar a solução pré-configurado, também automaticamente aprovisionar quatro dispositivos simulados que são executadas num [Azure WebJob][lnk-webjobs]. Os dispositivos simulados facilitam a explorar o comportamento da solução sem ser necessário para implementar quaisquer dispositivos físicos. Para implementar um dispositivo real físico, consulte o artigo [ligar o dispositivo para o controlo remoto monitorização solução pré-configurado] [ lnk-connect-rm] tutorial.

Cada dispositivo simulado pode enviar os seguintes tipos de mensagem a IoT concentrador:

| Mensagem  | Descrição |
|----------|-------------|
| Arranque  | Quando o dispositivo é iniciado,-envia uma mensagem de **informações sobre o dispositivo** que contém informações sobre si mesmo para o back-end. Estes dados incluem o id do dispositivo, os metadados de dispositivo, uma lista dos comandos o dispositivo suporta e a configuração atual do dispositivo. |
| Estado de presença | Um dispositivo periodicamente envia uma mensagem de **Estado de presença** para comunicar se o dispositivo pode Deteta a presença de um sensor. |
| Telemetria | Um dispositivo periodicamente envia uma mensagem de **telemetria** relatórios simulados valores para a temperatura e humidade recolhidas a partir do sensores simulada do dispositivo. |


Os dispositivos simulados enviar as seguintes propriedades de dispositivo numa mensagem de **informações do dispositivo** :

| Propriedade               |  Objetivo |
|------------------------|--------- |
| ID do dispositivo              | ID que seja fornecido ou atribuído quando é criado um dispositivo da solução. |
| Fabricante           | Fabricante do dispositivo |
| Número de modelo           | Número do modelo do dispositivo |
| Número de série          | Número de série do dispositivo |
| Firmware               | Versão atual do firmware no dispositivo |
| Plataforma               | Arquitetura de plataforma do dispositivo |
| Processador              | Processador a executar o dispositivo |
| RAM instalada          | Quantidade de RAM instalada no dispositivo |
| Estado do concentrador activado      | Propriedade de state IoT Hub do dispositivo |
| Hora de criação           | O dispositivo foi criada na solução |
| Hora atualizada           | Hora da última propriedades foram atualizadas para o dispositivo |
| Latitude               | Localização de latitude do dispositivo |
| Longitude              | Localização de longitude do dispositivo |

O simulator plantas estas propriedades na simulada dispositivos com valores de exemplo.  Sempre que o simulator inicia um dispositivo simulado, o dispositivo regista os metadados predefinidos IoT concentrador. Tenha em atenção como isto substitui qualquer metadados feitas no portal do dispositivo.


Os dispositivos simulados podem processar os seguintes comandos enviados a partir do dashboard de solução através do concentrador de IoT:

| Comando                | Descrição                                         |
|------------------------|-----------------------------------------------------|
| PingDevice             | Envia um _ping_ para o dispositivo para verificar que está activo   |
| StartTelemetry         | Inicia o dispositivo de envio de telemetria                 |
| StopTelemetry          | Deixa o dispositivo de envio de telemetria             |
| ChangeSetPointTemp     | Altere o valor de definir ponto à volta da qual são gerados os dados aleatórios |
| DiagnosticTelemetry    | Accionadores simulator o dispositivo para enviar um valor de telemetria adicionais (externalTemp) |
| ChangeDeviceState      | Altera uma propriedade de estado expandido para o dispositivo e envia a mensagem de informações do dispositivo a partir do dispositivo |

A confirmação de comando do dispositivo para o back-end da solução é fornecida através do concentrador de IoT.

## <a name="iot-hub"></a>Concentrador IoT

O [Centro de IoT] [ lnk-iothub] ingests dados enviados a partir de dispositivos para a nuvem e torna disponível para as tarefas de análise de sequência Azure (ASA). Concentrador IoT também envia comandos para os seus dispositivos em nome do portal do dispositivo. Cada tarefa ASA da cadeia utiliza um grupo do consumidor IoT concentrador separado para ler a sequência de mensagens seus dispositivos.

## <a name="azure-stream-analytics"></a>Análise de sequência Azure

Na solução monitorização remota, [Azure da cadeia Analytics] [ lnk-asa] (ASA) distribui mensagens de dispositivo recebidas pelo concentrador IoT a outros componentes de back-end para processamento ou armazenamento. Tarefas ASA diferentes executam funções específicas, com base no conteúdo das mensagens.

**Tarefa 1: informações de dispositivo** filtra as mensagens de informações do dispositivo da cadeia de mensagens recebidas e envia-os para um ponto final concentrador de evento. Um dispositivo envia mensagens de informações do dispositivo no arranque e em resposta a um comando **SendDeviceInfo** . Esta tarefa utiliza a seguinte definição de consulta para identificar **informações de dispositivo de** mensagens:

```
SELECT * FROM DeviceDataStream Partition By PartitionId WHERE  ObjectType = 'DeviceInfo'
```

Esta tarefa envia a sua saída a um concentrador de evento para processamento suplementar.

**Tarefa 2: regras** avalia valores recebidos de telemetria de temperatura e humidade contra limiares por dispositivo. Valores do limiar estão definidos no editor de regras no dashboard de solução disponível. Cada par de dispositivo/valor é armazenado por data/hora num blob de análise da cadeia lê como **Dados de referência**. A tarefa se compara um valor não vazios contra o limiar do conjunto de para o dispositivo. Se exceder o ' >' condição, a tarefa exporta um evento de **alarme** que indica que o limiar for excedido e fornece o dispositivo, valor e valores de data/hora. Esta tarefa utiliza a seguinte definição de consulta para identificar mensagens de telemetria que deverão accionar um alarme:

```
WITH AlarmsData AS 
(
SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Temperature' as ReadingType,
     Stream.Temperature as Reading,
     Ref.Temperature as Threshold,
     Ref.TemperatureRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Temperature IS NOT null AND Stream.Temperature > Ref.Temperature

UNION ALL

SELECT
     Stream.IoTHub.ConnectionDeviceId AS DeviceId,
     'Humidity' as ReadingType,
     Stream.Humidity as Reading,
     Ref.Humidity as Threshold,
     Ref.HumidityRuleOutput as RuleOutput,
     Stream.EventEnqueuedUtcTime AS [Time]
FROM IoTTelemetryStream Stream
JOIN DeviceRulesBlob Ref ON Stream.IoTHub.ConnectionDeviceId = Ref.DeviceID
WHERE
     Ref.Humidity IS NOT null AND Stream.Humidity > Ref.Humidity
)

SELECT *
INTO DeviceRulesMonitoring
FROM AlarmsData

SELECT *
INTO DeviceRulesHub
FROM AlarmsData
```

A tarefa envia a sua saída a um concentrador de evento para processamento suplementar e guarda os detalhes de cada alerta ao armazenamento blob a partir de onde o dashboard de solução pode ler as informações de alertas.

**Tarefa 3: telemetria** opera sobre a sequência de telemetria do dispositivo recebidos de duas maneiras. O primeiro envia todas as mensagens de telemetria a partir de dispositivos ao armazenamento de BLOBs persistente de armazenamento a longo prazo. O segundo fórmula calcula a média, mínimo e máximo humidade valores ao longo de uma janela deslizante de cinco minutos e envia estes dados ao armazenamento blob. Dashboard de solução lê os dados de telemetria a partir do armazenamento de BLOBs para preencher os gráficos. Esta tarefa utiliza a seguinte definição de consulta:

```
WITH 
    [StreamData]
AS (
    SELECT
        *
    FROM [IoTHubStream]
    WHERE
        [ObjectType] IS NULL -- Filter out device info and command responses
) 

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    Temperature,
    Humidity,
    ExternalTemperature,
    EventProcessedUtcTime,
    PartitionId,
    EventEnqueuedUtcTime,
    * 
INTO
    [Telemetry]
FROM
    [StreamData]

SELECT
    IoTHub.ConnectionDeviceId AS DeviceId,
    AVG (Humidity) AS [AverageHumidity],
    MIN(Humidity) AS [MinimumHumidity],
    MAX(Humidity) AS [MaxHumidity],
    5.0 AS TimeframeMinutes 
INTO
    [TelemetrySummary]
FROM [StreamData]
WHERE
    [Humidity] IS NOT NULL
GROUP BY
    IoTHub.ConnectionDeviceId,
    SlidingWindow (mi, 5)
```

## <a name="event-hubs"></a>Concentradores de evento

As tarefas ASA **regras** e **informações de dispositivo** de saída os respetivos dados a concentradores de evento para reencaminhar sujeito para o **Processador de eventos** na WebJob.

## <a name="azure-storage"></a>Armazenamento Azure

A solução utiliza o armazenamento de Blobs do Azure para persistirem todos os dados de telemetria não processados e resumidos a partir de dispositivos da solução. O dashboard lê os dados de telemetria a partir do armazenamento de BLOBs para preencher os gráficos. Para apresentar alertas, o dashboard lê os dados a partir do armazenamento de BLOBs que registe quando os valores de telemetria excedem os valores do limiar configurado. A solução também utiliza o armazenamento de BLOBs para gravar os valores do limiar que definir no dashboard.

## <a name="webjobs"></a>WebJobs

Para além de alojamento simuladores de dispositivo, WebJobs da solução também alojar o **Processador de eventos** numa WebJob Azure que alças dispositivo informações mensagens e respostas do comando. Utiliza:

- Mensagens de informações de dispositivo para atualizar o registo do dispositivo (armazenado na base de dados DocumentDB) com as informações do dispositivo atual.
- Mensagens de resposta de comando para atualizar o histórico de comandos do dispositivo (armazenado na base de dados DocumentDB).

## <a name="documentdb"></a>DocumentDB

A solução utiliza uma base de dados DocumentDB para armazenar informações sobre os dispositivos ligados à solução. Estas informações incluem metadados de dispositivo e o histórico de comandos enviados para dispositivos a partir do dashboard.

## <a name="web-apps"></a>Aplicações Web

### <a name="remote-monitoring-dashboard"></a>Dashboard de monitorização remoto
Esta página na aplicação web utiliza obter javascript controlos (consulte o artigo [obter piscam repo](https://www.github.com/Microsoft/PowerBI-visuals)) para visualizar os dados de telemetria a partir de dispositivos. A solução utiliza a tarefa de telemetria ASA para escrever os dados de telemetria para blob armazenamento.


### <a name="device-administration-portal"></a>Portal de administração do dispositivo

Esta aplicação web permite-lhe:

- Aprovisione um novo dispositivo. Esta ação define o id de dispositivo exclusivo e gera a chave de autenticação. Escreve informações sobre o dispositivo para o registo de identidade IoT concentrador e a base de dados de DocumentDB solução específicas.
- Gerir as propriedades do dispositivo. Esta ação inclui visualizar as propriedades do existente e atualizar com novas propriedades.
- Envie comandos para um dispositivo.
- Ver o histórico de comando para um dispositivo.
- Activar e desactivar dispositivos.

## <a name="next-steps"></a>Próximos passos

As mensagens do blogue TechNet seguintes fornecem mais detalhes sobre a solução pré-configurado monitorização remota:

- [IoT Suite - em definições avançadas - remoto de monitorização](http://social.technet.microsoft.com/wiki/contents/articles/32941.iot-suite-under-the-hood-remote-monitoring.aspx)
- [Conjunto de aplicações IoT - monitorização remota - adicionar direto e simulados dispositivos](http://social.technet.microsoft.com/wiki/contents/articles/32975.iot-suite-remote-monitoring-adding-live-and-simulated-devices.aspx)

Pode continuar a introdução ao conjunto de aplicações do IoT lendo os artigos seguintes:

- [Ligar o seu dispositivo para a solução pré-configurado monitorização remota][lnk-connect-rm]
- [Permissões no site de azureiotsuite.com][lnk-permissions]

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-iothub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-webjobs]: https://azure.microsoft.com/documentation/articles/websites-webjobs-resources/
[lnk-connect-rm]: iot-suite-connecting-devices.md
[lnk-permissions]: iot-suite-permissions.md