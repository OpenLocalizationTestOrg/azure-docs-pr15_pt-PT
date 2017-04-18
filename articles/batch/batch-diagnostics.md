<properties
   pageTitle="Registo de diagnóstico lote Azure | Microsoft Azure"
   description="Gravar e analisar os eventos de registo de diagnóstico para recursos de conta Azure lote como conjuntos de dados e tarefas."
   services="batch"
   documentationCenter=""
   authors="mmacy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="multiple"
   ms.workload="big-compute"
   ms.date="10/12/2016"
   ms.author="marsma"/>

# <a name="azure-batch-diagnostic-logging"></a>Registo de diagnóstico lote Azure

Tal como acontece com vários serviços Azure, o serviço do lote emite eventos de registo para determinados recursos durante a vida útil do recurso. Pode ativar registos de diagnóstico do Azure lote para gravar eventos para recursos como conjuntos de dados e de tarefas e, em seguida, utilize os registos de início para a avaliação de diagnóstico e monitorização. Eventos, como o agrupamento de criar, eliminar do conjunto de dados, início da tarefa, tarefa concluída e outras pessoas estão incluídas nos registos de diagnóstico do lote.

>[AZURE.NOTE] Este artigo aborda eventos de registo para o lote conta próprios recursos, não da tarefa e dados de saída de tarefas. Para obter detalhes sobre como armazenar os dados de saída dos seus trabalhos e tarefas, consulte o artigo [persistirem lote Azure saída de projecto e a tarefa](batch-task-output.md).

## <a name="prerequisites"></a>Pré-requisitos

* [Conta lote Azure](batch-account-create-portal.md)

* [Conta de armazenamento Azure](../storage/storage-create-storage-account.md#create-a-storage-account)

  Para persistirem registos de diagnóstico do lote, tem de criar uma conta de armazenamento do Windows Azure onde Azure irá guardar os registos. Especificar esta conta de armazenamento ao [Ativar o registo de diagnóstico](#enable-diagnostic-logging) para a sua conta do lote. A conta de armazenamento que especificar quando ativa a coleção de registo não é o mesmo que uma conta de armazenamento ligadas referida nos artigos de [pacotes de aplicações](batch-application-packages.md) e [persistente de saída da tarefa](batch-task-output.md) .

  >[AZURE.WARNING] Está a **cobrado** para os dados armazenados na sua conta de armazenamento do Windows Azure. Isto inclui os registos de diagnóstico outros fabricantes referidos neste artigo. Tenha isto em conta ao estruturar a [política de retenção do registo](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md).

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnóstico

Registo de diagnóstico não está ativado por predefinição para a sua conta do lote. Tem de ativar explicitamente o registo de diagnóstico para cada conta lote que pretende monitorizar:

[Como ativar o conjunto de registos de diagnóstico](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs)

Recomendamos que leia o artigo [Descrição geral dos registos de diagnóstico Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) completo para compreender de não apenas como ativar a funcionalidade de registo, mas as categorias de registo suportadas pelos vários serviços do Azure. Por exemplo, o lote Azure suporta atualmente uma categoria de registo: **Os registos do serviço**.

## <a name="service-logs"></a>Registos de serviço

Registos de serviço do Azure lote contêm eventos emitidos pelo serviço Azure lote durante o tempo de vida de um recurso de lote como um conjunto de dados ou a tarefa. Cada evento emitido por lote é armazenado na conta de armazenamento especificada no formato JSON. Por exemplo, este é o corpo de uma amostra de **agrupamento de criar o evento**:

```json
{
    "poolId": "myPool1",
    "displayName": "Production Pool",
    "vmSize": "Small",
    "cloudServiceConfiguration": {
        "osFamily": "4",
        "targetOsVersion": "*"
    },
    "networkConfiguration": {
        "subnetId": " "
    },
    "resizeTimeout": "300000",
    "targetDedicated": 2,
    "maxTasksPerNode": 1,
    "vmFillType": "Spread",
    "enableAutoscale": false,
    "enableInterNodeCommunication": false,
    "isAutoPool": false
}
```

Cada corpo de evento se encontra num ficheiro .json na conta de armazenamento do Windows Azure especificada. Se pretender aceder diretamente os registos de início, poderá pretender rever o [esquema dos registos de diagnóstico na conta de armazenamento](../monitoring-and-diagnostics/monitoring-archive-diagnostic-logs.md#schema-of-diagnostic-logs-in-the-storage-account).

## <a name="service-log-events"></a>Eventos de registo de serviço

O serviço do lote atualmente emite os seguintes eventos de registo de serviço. Esta lista pode não ser exaustiva, uma vez que eventos adicionais poderão ter sido adicionados uma vez que este artigo foram atualizados pela última vez.

| **Eventos de registo de serviço** |
| ------------------ |
| [Criar de agrupamento][pool_create] |
| [Iniciar eliminação de agrupamento][pool_delete_start] |
| [Eliminar agrupamento concluída][pool_delete_complete] |
| [Iniciar de redimensionamento do conjunto de dados][pool_resize_start] |
| [Redimensionar agrupamento concluída][pool_resize_complete] |
| [Início da tarefa][task_start] |
| [Tarefa concluída][task_complete] |
| [Falhas de tarefa][task_fail] |

## <a name="next-steps"></a>Próximos passos

Para além de armazenar eventos de registo de diagnóstico numa conta de armazenamento do Windows Azure, também pode transmitir em fluxo eventos de registo de serviço do lote a um [Concentrador de eventos do Azure](../event-hubs/event-hubs-what-is-event-hubs.md)e enviá-las para [A análise de registo do Azure](../log-analytics/log-analytics-overview.md).

* [Transmitir em fluxo Azure registos de diagnóstico para concentradores de evento](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md)

  Passe os eventos de diagnóstico lote para o serviço de penetração dados altamente dimensionáveis, concentradores do evento. Evento concentradores podem ingerir esta última milhões de eventos por segundo, que pode transformar e armazenar utilizando qualquer fornecedor de análise em tempo real.

* [Analisar os registos de diagnóstico Azure utilizando a análise de registo](../log-analytics/log-analytics-azure-storage-json.md)

  Envie registos de diagnóstico para análise de registo, onde pode analisá-los no portal do conjunto de aplicações de gestão de operações (OMS) ou exportá-los para uma análise no Power BI ou no Excel.

[pool_create]: https://msdn.microsoft.com/library/azure/mt743615.aspx
[pool_delete_start]: https://msdn.microsoft.com/library/azure/mt743610.aspx
[pool_delete_complete]: https://msdn.microsoft.com/library/azure/mt743618.aspx
[pool_resize_start]: https://msdn.microsoft.com/library/azure/mt743609.aspx
[pool_resize_complete]: https://msdn.microsoft.com/library/azure/mt743608.aspx
[task_start]: https://msdn.microsoft.com/library/azure/mt743616.aspx
[task_complete]: https://msdn.microsoft.com/library/azure/mt743612.aspx
[task_fail]: https://msdn.microsoft.com/library/azure/mt743607.aspx
