<properties
 pageTitle="Guia do programador - as tarefas | Microsoft Azure"
 description="Guia de programador do Azure IoT concentrador - agendar tarefas para ser executado em múltiplos dispositivos ligado ao seu centro"
 services="iot-hub"
 documentationCenter=".net"
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="09/30/2016" 
 ms.author="juanpere"/>

# <a name="schedule-jobs-on-multiple-devices-preview"></a>Agendar tarefas em múltiplos dispositivos (pré-visualização)

## <a name="overview"></a>Descrição geral

Tal como descrito pelos artigos anterior, Azure IoT concentrador permite que um número de blocos modulares ([duplos propriedades e do dispositivo etiquetas] [ lnk-twin-devguide] e [métodos de dispositivo de nuvem][lnk-dev-methods]).  Normalmente, aplicações de back-end IoT ativar administradores de dispositivo e operadores atualizar e interagir com dispositivos IoT em massa e a uma hora agendada.  Tarefas de incorporar a execução de dispositivo duplos atualizações e métodos de C2D contra um conjunto de dispositivos de cada vez agenda.  Por exemplo, um operador utilizaria uma aplicação de back-end que pretende iniciar e controlar uma tarefa para um conjunto de dispositivos no construir 43 e piso 3, uma vez que não seria desorganização para as operações do edifício reiniciar o computador.

### <a name="when-to-use"></a>Quando utilizar

Considere a utilização de tarefas quando: uma cópia de segurança de solução terminar as necessidades para agendar e controlar o progresso qualquer uma das seguintes atividades num conjunto de dispositivo:

- Atualizar as propriedades do dispositivo duplos pretendido
- Atualizar etiquetas duplos de dispositivo
- Invocar C2D métodos

## <a name="job-lifecycle"></a>Ciclo de vida do projecto

Tarefas são iniciadas por solução back-end e mantidas por IoT concentrador.  Pode iniciar uma tarefa através de um serviço destinado URI (`{iot hub}/jobs/v2/{device id}/methods/<jobID>?api-version=2016-09-30-preview`) e consulta para o progresso de uma tarefa de execução através de um serviço destinado URI (`{iot hub}/jobs/v2/<jobId>?api-version=2016-09-30-preview`).  Assim que uma tarefa é iniciada, consultar para tarefas permitirá a aplicação de back-end atualizar o estado de tarefas em execução.

> [AZURE.NOTE] Quando iniciar uma tarefa, nomes de propriedades e valores só podem a conter US-ASCII imprimível alfanuméricos, exceto qualquer no conjunto de seguinte: ``{'$', '(', ')', '<', '>', '@', ',', ';', ':', '\', '"', '/', '[', ']', '?', '=', '{', '}', SP, HT}``.

## <a name="reference-topics"></a>Tópicos de referência:

Os seguintes tópicos de referência fornecem-lhe mais informações sobre a utilização de tarefas.

## <a name="jobs-to-execute-direct-methods"></a>Tarefas para executar métodos diretos

Segue-se os detalhes do pedido de HTTP 1.1 para executar um [método directo] [ lnk-dev-methods] num conjunto de dispositivos utilizando uma tarefa:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleDirectRequest', 
        cloudToDeviceMethod: {
            methodName: '<methodName>',
            payload: <payload>,                 
            timeoutInSeconds: methodTimeoutInSeconds 
        },
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        
    }
    ```
    
## <a name="jobs-to-update-device-twin-properties"></a>Tarefas para atualizar as propriedades do dispositivo duplos

Os detalhes do pedido de HTTP 1.1 para atualizar as propriedades do duplos dispositivo utilizando uma tarefa é o seguinte:

    ```
    PUT /jobs/v2/<jobId>?api-version=2016-09-30-preview
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>

    {
        jobId: '<jobId>',
        type: 'scheduleTwinUpdate', 
        updateTwin: <patch>                 // Valid JSON object
        queryCondition: '<queryOrDevices>', // if the queryOrDevices parameter is a string
        deviceIds: '<queryOrDevices>',      // if the queryOrDevices parameter is an array
        startTime: <jobStartTime>,          // as an ISO-8601 date string
        maxExecutionTimeInSeconds: <maxExecutionTimeInSeconds>        // format TBD
    }
    ```

## <a name="querying-for-progress-on-jobs"></a>Consultar o progresso de tarefas

Segue-se os detalhes do pedido de HTTP 1.1 para [consultar para tarefas][lnk-query]:

    ```
    GET /jobs/v2/query?api-version=2016-09-30-preview[&jobType=<jobType>][&jobStatus=<jobStatus>][&pageSize=<pageSize>][&continuationToken=<continuationToken>]
    
    Authorization: <config.sharedAccessSignature>
    Content-Type: application/json; charset=utf-8
    Request-Id: <guid>
    User-Agent: <sdk-name>/<sdk-version>
    ```
    
O continuationToken é fornecido pela resposta.  

## <a name="jobs-properties"></a>Propriedades de tarefas

Segue-se uma lista de propriedades e as descrições correspondentes, podem ser utilizadas quando consultar para tarefas ou resultados de tarefa.

| Propriedade | Descrição |
| -------------- | -----------------|
| **jobId** | Aplicação fornecidos ID da tarefa. |
| **hora de início** | Aplicação fornecidos a hora de início (ISO 8601) para a tarefa. |
| **hora de fim** | Concentrador IoT fornecido data (ISO 8601) para a tarefa de concluída. Válido apenas depois da tarefa de atinge o estado 'Concluído'. | 
| **tipo** | Tipos de tarefas: |
| | **scheduledUpdateTwin**: uma tarefa utilizada para atualizar um conjunto de etiquetas ou propriedades de duplos pretendido. |
| | **scheduledDeviceMethod**: uma tarefa utilizada para invocar um método de dispositivo num conjunto de duplos. |
| **Estado** | Estado atual da tarefa. Valores possíveis para Estado: |
| | **pendente** : agendada e a aguardar recolhida pelo serviço tarefa. |
| | **agendada** : agendado para uma hora no futuro. |
| | **em execução** : tarefa atualmente ativa. |
| | **Cancelado** : tarefa foi cancelada. |
| | **Ocorreu uma falha** : Falha na tarefa. |
| | **concluída** : tarefa estiver concluída. |
| **deviceJobStatistics** | Estatísticas sobre a execução da tarefa. |

Durante a pré-visualização, o objeto deviceJobStatistics só está disponível depois a tarefa está concluída.

| Propriedade | Descrição |
| -------------- | -----------------|
| **deviceJobStatistics.deviceCount** | Número de dispositivos da tarefa. |
| **deviceJobStatistics.failedCount** | Número de dispositivos onde a tarefa de falha. |
| **deviceJobStatistics.succeededCount** | Número de dispositivos onde a tarefa foi concluída com êxito. |
| **deviceJobStatistics.runningCount** | Número de dispositivos que estão atualmente em execução da tarefa. |
| **deviceJobStatistics.pendingCount** | Número de dispositivos que estão pendentes para executar a tarefa. |


### <a name="additional-reference-material"></a>Material de referência adicionais

Outros tópicos de referência no Guia do programador incluem:

- [Os pontos finais de IoT concentrador] [ lnk-endpoints] descreve os vários pontos finais que cada concentrador IoT expõe para operações de runtime e gestão.
- [Limitação e quotas] [ lnk-quotas] descreve as quotas de que se aplicam ao serviço de IoT concentrador e o comportamento optimização esperar quando vai utilizar o serviço.
- [Concentrador IoT dispositivo e serviço SDK] [ lnk-sdks] listas o idioma de vários SDK que uma utilização quando desenvolver o dispositivo e serviço aplicações que interagem com IoT concentrador.
- [Idioma de consulta para gémeos, métodos e tarefas de] [ lnk-query] descreve o idioma de consulta que pode utilizar para recuperar informações a partir do IoT Hub sobre as suas gémeos de dispositivo, métodos e tarefas.
- [Suporte de IoT concentrador MQTT] [ lnk-devguide-mqtt] fornece mais informações sobre o suporte de IoT concentrador para o protocolo MQTT.

## <a name="next-steps"></a>Próximos passos

Se gostaria experimentar a alguns dos conceitos descritos neste artigo, poderá estar interessado no tutorial IoT concentrador seguinte:

- [Tarefas de agenda e difusão][lnk-jobs-tutorial]

<!-- links and images -->

[lnk-endpoints]: iot-hub-devguide-endpoints.md
[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-sdks]: iot-hub-devguide-sdks.md
[lnk-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md
[lnk-jobs-tutorial]: iot-hub-schedule-jobs.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-dev-methods]: iot-hub-devguide-direct-methods.md
[lnk-get-started-twin]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-devguide]: iot-hub-devguide-device-twins.md
