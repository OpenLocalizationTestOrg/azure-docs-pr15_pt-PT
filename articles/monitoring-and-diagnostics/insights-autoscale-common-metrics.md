<properties
    pageTitle="Azure Monitor autoscaling comuns métricas. | Microsoft Azure"
    description="Saiba quais métricas mais são utilizadas em autoscaling serviços em nuvem, máquinas virtuais do Web Apps."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/02/2016"
    ms.author="ashwink"/>

# <a name="azure-monitor-autoscaling-common-metrics"></a>Métricas comuns do Azure Monitor autoscaling

Azure Monitor autoscaling permite-lhe dimensionar o número de instâncias em execução, cima ou para baixo, com base em dados de telemetria (Métrica). Este documento descreve métricas comuns que poderá pretender utilizar. No Portal do Azure para serviços em nuvem e os Farms, pode escolher a métrica do recurso para dimensionar por. No entanto, também pode escolher qualquer métrica a partir de um recurso diferente para dimensionar por.

Aqui estão os detalhes sobre como localizar e listar métricas que quer Dimensionar por. A seguinte aplica-se para o dimensionamento também conjuntos de escala de Máquina Virtual.

## <a name="compute-metrics"></a>Calcular métricas
Por predefinição, Azure VM v2 vem com extensão diagnósticos configurado e que tenham as seguintes métricas ativadas.

- [Métricas de convidado para Windows VM v2](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métricas de convidado para Linux VM v2](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Pode utilizar o `Get MetricDefinitions` PoSH/API/clip para ver as métricas disponíveis para o seu recurso VMSS. 

Se estiver a utilizar o VM conjuntos de escala e não vir uma determinada métrica listada, em seguida, é provável que *desativado* na sua extensão de diagnóstico.

Se uma determinada métrica não está a ser amostra ou transferidos com frequência que pretende, pode atualizar a configuração de diagnóstico.

Se qualquer um dos casos acima for VERDADEIRO, em seguida, reveja a [Utilizar o PowerShell para activar diagnósticos do Azure numa máquina virtual a executar o Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) sobre o PowerShell para configurar e atualizar a sua extensão Azure VM diagnóstico para ativar a métrica do. Esse artigo também inclui um ficheiro de configuração de diagnósticos de exemplo.

### <a name="compute-metrics-for-windows-vm-v2-as-a-guest-os"></a>Calcular métricas para Windows VM v2 como convidado SO

Quando criar uma nova VM (v2) no Azure, diagnósticos são activado utilizando a extensão de diagnóstico.

Pode gerar uma lista de métricas utilizando o seguinte comando no PowerShell.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode criar um alerta para as seguintes métricas.

|Métrica nome|   Unidade|
|---|---|
|\Processor(_Total)\% processador de tempo    |Percentagem|
|\Processor(_Total)\% privilégios de tempo   |Percentagem|
|\Processor(_Total)\% tempo do utilizador |Percentagem|
|Frequência de \Processor \Processor informações ( total) |Contar|
|\System\Processes| Contar|
|Contagem de \Thread \Processo ( total)| Contar|
|Contagem de \Handle \Processo ( total)  |Contar|
|\Memory.\% consolidada Bytes utilizado   |Percentagem|
|\Memory\Available bytes|   Bytes|
|\Memory\Committed bytes    |Bytes|
|Limite de \Memory\Commit|  Bytes|
|\Memory\Pool paginada Bytes|  Bytes|
|\Memory\Pool nonpaged Bytes|   Bytes|
|\PhysicalDisk(_Total)\% tempo do disco| Percentagem|
|\PhysicalDisk(_Total)\% tempo de leitura do disco|    Percentagem|
|\PhysicalDisk(_Total)\% tempo de escrita do disco|   Percentagem|
|Transferência de \Disk \PhysicalDisk ( total) / seg   |CountPerSecond|
|\PhysicalDisk ( total) \Disk lê/sec   |CountPerSecond|
|\PhysicalDisk ( total) \Disk escritas/seg  |CountPerSecond|
|\PhysicalDisk ( total) \Disk bytes/seg   |BytesPerSecond|
|\Disk \PhysicalDisk ( total) Bytes lidos/seg.| BytesPerSecond|
|\PhysicalDisk ( total) \Disk escrita Bytes/seg |BytesPerSecond|
|\Avg \PhysicalDisk ( total). Comprimento da fila do disco|  Contar|
|\Avg \PhysicalDisk ( total). Comprimento de fila de leitura do disco| Contar|
|\Avg \PhysicalDisk ( total). Comprimento de fila de escrita de disco |Contar|
|\LogicalDisk(_Total)\% espaço livre| Percentagem|
|\LogicalDisk ( total) \Free megabytes|   Contar|



### <a name="compute-metrics-for-linux-vm-v2-as-a-guest-os"></a>Calcular métricas para Linux VM v2 como convidado SO

Quando cria uma nova VM (v2) no Azure, diagnósticos está ativado por predefinição, utilizando a extensão de diagnóstico.

Pode gerar uma lista de métricas utilizando o seguinte comando no PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Pode criar um alerta para as seguintes métricas.

|Métrica nome                            |Unidade|
|---|---|
|\Memory\AvailableMemory                |Bytes|
|\Memory\PercentAvailableMemory         |Percentagem|
|\Memory\UsedMemory                     |Bytes|
|\Memory\PercentUsedMemory              |Percentagem|
|\Memory\PercentUsedByCache             |Percentagem|
|\Memory\PagesPerSec                    |CountPerSecond|
|\Memory\PagesReadPerSec                |CountPerSecond|
|\Memory\PagesWrittenPerSec             |CountPerSecond|
|\Memory\AvailableSwap                  |Bytes|
|\Memory\PercentAvailableSwap           |Percentagem|
|\Memory\UsedSwap                       |Bytes|
|\Memory\PercentUsedSwap                |Percentagem|
|\Processor\PercentIdleTime             |Percentagem|
|\Processor\PercentUserTime             |Percentagem|
|\Processor\PercentNiceTime             |Percentagem|
|\Processor\PercentPrivilegedTime       |Percentagem|
|\Processor\PercentInterruptTime        |Percentagem|
|\Processor\PercentDPCTime              |Percentagem|
|\Processor\PercentProcessorTime        |Percentagem|
|\Processor\PercentIOWaitTime           |Percentagem|
|\PhysicalDisk\BytesPerSecond           |BytesPerSecond|
|\PhysicalDisk\ReadBytesPerSecond       |BytesPerSecond|
|\PhysicalDisk\WriteBytesPerSecond      |BytesPerSecond|
|\PhysicalDisk\TransfersPerSecond       |CountPerSecond|
|\PhysicalDisk\ReadsPerSecond           |CountPerSecond|
|\PhysicalDisk\WritesPerSecond          |CountPerSecond|
|\PhysicalDisk\AverageReadTime          |Segundos|
|\PhysicalDisk\AverageWriteTime         |Segundos|
|\PhysicalDisk\AverageTransferTime      |Segundos|
|\PhysicalDisk\AverageDiskQueueLength   |Contar|
|\NetworkInterface\BytesTransmitted     |Bytes|
|\NetworkInterface\BytesReceived        |Bytes|
|\NetworkInterface\PacketsTransmitted   |Contar|
|\NetworkInterface\PacketsReceived      |Contar|
|\NetworkInterface\BytesTotal           |Bytes|
|\NetworkInterface\TotalRxErrors        |Contar|
|\NetworkInterface\TotalTxErrors        |Contar|
|\NetworkInterface\TotalCollisions      |Contar|




## <a name="commonly-used-web-server-farm-metrics"></a>Utilizadas mais frequentemente métricas de Web (Farm de servidores)

Também pode executar autoscale com base na comuns métricas de servidor web como o comprimento da fila Http. Nome métrica é **HttpQueueLength**.  A secção seguinte lista métricas de farm (Web Apps) do servidor disponível.

### <a name="web-apps-metrics"></a>Métricas de aplicações Web

Pode gerar uma lista de métricas de aplicações Web utilizando o seguinte comando no PowerShell.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Pode alertar sobre ou dimensionar por estas métricas.

|Métrica nome        |Unidade|
|---                |---|
|CpuPercentage      |Percentagem|
|MemoryPercentage   |Percentagem|
|DiskQueueLength    |Contar|
|HttpQueueLength    |Contar|
|BytesReceived      |Bytes|
|BytesSent          |Bytes|


## <a name="commonly-used-storage-metrics"></a>Utilizadas mais frequentemente métricas de armazenamento
É possível dimensionar por comprimento de fila de armazenamento, que é o número de mensagens na fila de armazenamento. Comprimento da fila de armazenamento é uma métrica especial e limiar aplicado será o número de mensagens por instância. Isto significa que se existem duas instâncias e se o limiar de estiver definido como 100, irá dimensionar quando o número total de mensagens na fila de espera está 200. Por exemplo, 100 mensagens por instância.

Pode configurar este é no Portal do Azure pá as **Definições** . Para conjuntos de escala VM, pode atualizar a definição de Autoscale no modelo de processador para utilizar *metricName* como *ApproximateMessageCount* e passar o ID da fila armazenamento como *metricResourceUri*.

Por exemplo, com uma conta de armazenamento clássico de metricTrigger de definição de autoscale deverá incluir:

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

Para uma conta de armazenamento (que não sejam clássico), o metricTrigger deverá incluir:

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.Storage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
```

## <a name="commonly-used-service-bus-metrics"></a>Métricas de serviço Bus utilizadas mais frequentemente

É possível dimensionar por comprimento da fila Bus de serviço, que é o número de mensagens na fila de espera Bus de serviço. Comprimento da fila serviço Bus é uma métrica especial e o limiar especificado aplicado será o número de mensagens por instância. Isto significa que se existem duas instâncias e se o limiar de estiver definido como 100, irá dimensionar quando o número total de mensagens na fila de espera está 200. Por exemplo, 100 mensagens por instância.

Para conjuntos de escala VM, pode atualizar a definição de Autoscale no modelo de processador para utilizar *metricName* como *ApproximateMessageCount* e passar o ID da fila armazenamento como *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Para Bus de serviço, não existe o conceito de grupo de recursos mas Gestor de recursos do Azure cria um grupo de recursos predefinido por região. O grupo de recursos é normalmente no formato de 'Predefinido - ServiceBus-[Região]'. Por exemplo, 'Predefinido-ServiceBus EastUS', 'Predefinido-ServiceBus WestUS', 'Predefinido-ServiceBus-AustraliaEast' etc.
