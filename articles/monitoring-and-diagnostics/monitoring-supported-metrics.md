<properties
    pageTitle="Métricas de Monitor de Azure - métricas suportados por tipo de recurso | Microsoft Azure"
    description="Lista de métricas disponíveis para cada tipo de recurso com o Azure Monitor."
    authors="johnkemnetz"
    manager="rboucher"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="johnkem"/>

# <a name="supported-metrics-with-azure-monitor"></a>Métricas suportadas com o Monitor do Azure

Azure Monitor fornece várias formas para interagir com métricas, incluindo gráficos-los no portal do, acedê-los através da API REST ou consultá-las utilizando o PowerShell ou clip. Abaixo está uma lista completa de todos os métricas atualmente disponível com pipeline métrica do Azure Monitor.

> [AZURE.NOTE] Outras métricas poderão estar disponíveis no portal do ou utilizando APIs legados. Esta lista inclui apenas métricas de pré-visualização público disponíveis, utilizando a pré-visualização do consolidados Azure Monitor métrica pipeline de pública.

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CoreCount|Contagem de Core|Contar|Total|Número total de tarolos na conta batch|
|TotalNodeCount|Contagem de nós|Contar|Total|Número total de nós na conta batch|
|CreatingNodeCount|Criar nó contagem|Contar|Total|Número de nós ser criado|
|StartingNodeCount|Contagem de nó inicial|Contar|Total|Número de nós inicial|
|WaitingForStartTaskNodeCount|Em espera para contagem de nós de tarefas do início|Contar|Total|Número de nós aguardar a começar a conclusão da tarefa|
|StartTaskFailedNodeCount|Contagem de nós de falha de tarefa de início|Contar|Total|Número de nós onde iniciar tarefa tiver falhado|
|IdleNodeCount|Contagem de nós inactivo|Contar|Total|Número de nós inactivos|
|OfflineNodeCount|Contagem de nós offline|Contar|Total|Número de nós offline|
|RebootingNodeCount|Contagem de nós reiniciar o computador|Contar|Total|Número de nós reiniciar o computador|
|ReimagingNodeCount|Contagem de nós reimaging|Contar|Total|Número de nós reimaging|
|RunningNodeCount|Contagem de nós em execução|Contar|Total|Número de nós de executar|
|LeavingPoolNodeCount|Sair do conjunto de nó contagem|Contar|Total|Número de nós deixar o agrupamento|
|UnusableNodeCount|Contagem de nós inutilizável|Contar|Total|Número de nós inutilizáveis|
|TaskStartEvent|Eventos de início de tarefa|Contar|Total|Número total de tarefas que foram iniciadas|
|TaskCompleteEvent|Eventos de tarefa concluída|Contar|Total|Número total de tarefas que têm de ser concluída|
|TaskFailEvent|Eventos de falhas de tarefa|Contar|Total|Número total de tarefas que concluiu num estado falhado|
|PoolCreateEvent|Criação de conjunto de eventos|Contar|Total|Número total de conjuntos de dados que foram criadas|
|PoolResizeStartEvent|Eventos de início de redimensionamento do conjunto de dados|Contar|Total|Número total de redimensiona de agrupamento que já ter começado|
|PoolResizeCompleteEvent|Eventos de completa de redimensionamento do conjunto de dados|Contar|Total|Número total de redimensiona de conjunto de dados que tiver concluído|
|PoolDeleteStartEvent|Eventos de início de eliminar do conjunto de dados|Contar|Total|Número total de eliminações de agrupamento que já ter começado|
|PoolDeleteCompleteEvent|Conjunto de dados eliminar concluída eventos|Contar|Total|Número total de eliminações de conjunto de dados que tiver concluído|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|connectedclients|Clientes ligados|Contar|Máximo||
|totalcommandsprocessed|Operações de total|Contar|Total||
|cachehits|Acertos na cache|Contar|Total||
|cachemisses|Falhas na cache|Contar|Total||
|getcommands|Obtém|Contar|Total||
|setcommands|Conjuntos|Contar|Total||
|evictedkeys|Teclas retiradas|Contar|Total||
|totalkeys|Total de chaves|Contar|Máximo||
|expiredkeys|Teclas expiradas|Contar|Total||
|usedmemory|Memória utilizada|Bytes|Máximo||
|usedmemoryRss|Memória utilizada RSS|Bytes|Máximo||
|serverLoad|Carga do servidor|Percentagem|Máximo||
|cacheWrite|Cache de escrita|BytesPerSecond|Máximo||
|cacheRead|Cache de leitura|BytesPerSecond|Máximo||
|percentProcessorTime|CPU|Percentagem|Máximo||
|connectedclients0|Clientes ligados (Shard 0)|Contar|Máximo||
|totalcommandsprocessed0|Operações total (Shard 0)|Contar|Total||
|cachehits0|Acertos (Shard 0)|Contar|Total||
|cachemisses0|Falhas na cache (Shard 0)|Contar|Total||
|getcommands0|Obtém (Shard 0)|Contar|Total||
|setcommands0|Conjuntos de (Shard 0)|Contar|Total||
|evictedkeys0|Teclas retiradas (Shard 0)|Contar|Total||
|totalkeys0|Total de chaves (nó 0)|Contar|Máximo||
|expiredkeys0|Teclas expiradas (Shard 0)|Contar|Total||
|usedmemory0|Memória utilizada (Shard 0)|Bytes|Máximo||
|usedmemoryRss0|Memória utilizada RSS (Shard 0)|Bytes|Máximo||
|serverLoad0|Carga do servidor (Shard 0)|Percentagem|Máximo||
|cacheWrite0|Cache de escrita (Shard 0)|BytesPerSecond|Máximo||
|cacheRead0|Cache de leitura (Shard 0)|BytesPerSecond|Máximo||
|percentProcessorTime0|CPU (Shard 0)|Percentagem|Máximo||
|connectedclients1|Clientes ligados (Shard 1)|Contar|Máximo||
|totalcommandsprocessed1|Operações total (Shard 1)|Contar|Total||
|cachehits1|Acertos (Shard 1)|Contar|Total||
|cachemisses1|Falhas na cache (Shard 1)|Contar|Total||
|getcommands1|Obtém (Shard 1)|Contar|Total||
|setcommands1|Conjuntos de (Shard 1)|Contar|Total||
|evictedkeys1|Teclas retiradas (Shard 1)|Contar|Total||
|totalkeys1|Total de chaves (nó 1)|Contar|Máximo||
|expiredkeys1|Teclas expiradas (Shard 1)|Contar|Total||
|usedmemory1|Memória utilizada (Shard 1)|Bytes|Máximo||
|usedmemoryRss1|Memória utilizada RSS (Shard 1)|Bytes|Máximo||
|serverLoad1|Carga do servidor (Shard 1)|Percentagem|Máximo||
|cacheWrite1|Cache de escrita (Shard 1)|BytesPerSecond|Máximo||
|cacheRead1|Cache de leitura (Shard 1)|BytesPerSecond|Máximo||
|percentProcessorTime1|CPU (Shard 1)|Percentagem|Máximo||
|connectedclients2|Clientes ligados (Shard 2)|Contar|Máximo||
|totalcommandsprocessed2|Operações total (Shard 2)|Contar|Total||
|cachehits2|Acertos (Shard 2)|Contar|Total||
|cachemisses2|Falhas na cache (Shard 2)|Contar|Total||
|getcommands2|Obtém (Shard 2)|Contar|Total||
|setcommands2|Conjuntos de (Shard 2)|Contar|Total||
|evictedkeys2|Teclas retiradas (Shard 2)|Contar|Total||
|totalkeys2|Total de chaves (nó 2)|Contar|Máximo||
|expiredkeys2|Teclas expiradas (Shard 2)|Contar|Total||
|usedmemory2|Memória utilizada (Shard 2)|Bytes|Máximo||
|usedmemoryRss2|Memória utilizada RSS (Shard 2)|Bytes|Máximo||
|serverLoad2|Carga do servidor (Shard 2)|Percentagem|Máximo||
|cacheWrite2|Cache de escrita (Shard 2)|BytesPerSecond|Máximo||
|cacheRead2|Cache de leitura (Shard 2)|BytesPerSecond|Máximo||
|percentProcessorTime2|CPU (Shard 2)|Percentagem|Máximo||
|connectedclients3|Clientes ligados (Shard 3)|Contar|Máximo||
|totalcommandsprocessed3|Operações total (Shard 3)|Contar|Total||
|cachehits3|Acertos (Shard 3)|Contar|Total||
|cachemisses3|Falhas na cache (Shard 3)|Contar|Total||
|getcommands3|Obtém (Shard 3)|Contar|Total||
|setcommands3|Conjuntos de (Shard 3)|Contar|Total||
|evictedkeys3|Teclas retiradas (Shard 3)|Contar|Total||
|totalkeys3|Total de chaves (nó 3)|Contar|Máximo||
|expiredkeys3|Teclas expiradas (Shard 3)|Contar|Total||
|usedmemory3|Memória utilizada (Shard 3)|Bytes|Máximo||
|usedmemoryRss3|Memória utilizada RSS (Shard 3)|Bytes|Máximo||
|serverLoad3|Carga do servidor (Shard 3)|Percentagem|Máximo||
|cacheWrite3|Cache de escrita (Shard 3)|BytesPerSecond|Máximo||
|cacheRead3|Cache de leitura (Shard 3)|BytesPerSecond|Máximo||
|percentProcessorTime3|CPU (Shard 3)|Percentagem|Máximo||
|connectedclients4|Clientes ligados (Shard 4)|Contar|Máximo||
|totalcommandsprocessed4|Operações total (Shard 4)|Contar|Total||
|cachehits4|Acertos (Shard 4)|Contar|Total||
|cachemisses4|Falhas na cache (Shard 4)|Contar|Total||
|getcommands4|Obtém (Shard 4)|Contar|Total||
|setcommands4|Conjuntos de (Shard 4)|Contar|Total||
|evictedkeys4|Teclas retiradas (Shard 4)|Contar|Total||
|totalkeys4|Total de chaves (nó 4)|Contar|Máximo||
|expiredkeys4|Teclas expiradas (Shard 4)|Contar|Total||
|usedmemory4|Memória utilizada (Shard 4)|Bytes|Máximo||
|usedmemoryRss4|Memória utilizada RSS (Shard 4)|Bytes|Máximo||
|serverLoad4|Carga do servidor (Shard 4)|Percentagem|Máximo||
|cacheWrite4|Cache de escrita (Shard 4)|BytesPerSecond|Máximo||
|cacheRead4|Cache de leitura (Shard 4)|BytesPerSecond|Máximo||
|percentProcessorTime4|CPU (Shard 4)|Percentagem|Máximo||
|connectedclients5|Clientes ligados (Shard 5)|Contar|Máximo||
|totalcommandsprocessed5|Operações total (Shard 5)|Contar|Total||
|cachehits5|Acertos (Shard 5)|Contar|Total||
|cachemisses5|Falhas na cache (Shard 5)|Contar|Total||
|getcommands5|Obtém (Shard 5)|Contar|Total||
|setcommands5|Conjuntos de (Shard 5)|Contar|Total||
|evictedkeys5|Teclas retiradas (Shard 5)|Contar|Total||
|totalkeys5|Total de chaves (nó 5)|Contar|Máximo||
|expiredkeys5|Teclas expiradas (Shard 5)|Contar|Total||
|usedmemory5|Memória utilizada (Shard 5)|Bytes|Máximo||
|usedmemoryRss5|Memória utilizada RSS (Shard 5)|Bytes|Máximo||
|serverLoad5|Carga do servidor (Shard 5)|Percentagem|Máximo||
|cacheWrite5|Cache de escrita (Shard 5)|BytesPerSecond|Máximo||
|cacheRead5|Cache de leitura (Shard 5)|BytesPerSecond|Máximo||
|percentProcessorTime5|CPU (Shard 5)|Percentagem|Máximo||
|connectedclients6|Clientes ligados (Shard 6)|Contar|Máximo||
|totalcommandsprocessed6|Operações total (Shard 6)|Contar|Total||
|cachehits6|Acertos (Shard 6)|Contar|Total||
|cachemisses6|Falhas na cache (Shard 6)|Contar|Total||
|getcommands6|Obtém (Shard 6)|Contar|Total||
|setcommands6|Conjuntos de (Shard 6)|Contar|Total||
|evictedkeys6|Teclas retiradas (Shard 6)|Contar|Total||
|totalkeys6|Total de chaves (nó 6)|Contar|Máximo||
|expiredkeys6|Teclas expiradas (Shard 6)|Contar|Total||
|usedmemory6|Memória utilizada (Shard 6)|Bytes|Máximo||
|usedmemoryRss6|Memória utilizada RSS (Shard 6)|Bytes|Máximo||
|serverLoad6|Carga do servidor (Shard 6)|Percentagem|Máximo||
|cacheWrite6|Cache de escrita (Shard 6)|BytesPerSecond|Máximo||
|cacheRead6|Cache de leitura (Shard 6)|BytesPerSecond|Máximo||
|percentProcessorTime6|CPU (Shard 6)|Percentagem|Máximo||
|connectedclients7|Clientes ligados (Shard 7)|Contar|Máximo||
|totalcommandsprocessed7|Operações total (Shard 7)|Contar|Total||
|cachehits7|Acertos (Shard 7)|Contar|Total||
|cachemisses7|Falhas na cache (Shard 7)|Contar|Total||
|getcommands7|Obtém (Shard 7)|Contar|Total||
|setcommands7|Conjuntos de (Shard 7)|Contar|Total||
|evictedkeys7|Teclas retiradas (Shard 7)|Contar|Total||
|totalkeys7|Total de chaves (nó 7)|Contar|Máximo||
|expiredkeys7|Teclas expiradas (Shard 7)|Contar|Total||
|usedmemory7|Memória utilizada (Shard 7)|Bytes|Máximo||
|usedmemoryRss7|Memória utilizada RSS (Shard 7)|Bytes|Máximo||
|serverLoad7|Carga do servidor (Shard 7)|Percentagem|Máximo||
|cacheWrite7|Cache de escrita (Shard 7)|BytesPerSecond|Máximo||
|cacheRead7|Cache de leitura (Shard 7)|BytesPerSecond|Máximo||
|percentProcessorTime7|CPU (Shard 7)|Percentagem|Máximo||
|connectedclients8|Clientes ligados (Shard 8)|Contar|Máximo||
|totalcommandsprocessed8|Operações total (Shard 8)|Contar|Total||
|cachehits8|Acertos (Shard 8)|Contar|Total||
|cachemisses8|Falhas na cache (Shard 8)|Contar|Total||
|getcommands8|Obtém (Shard 8)|Contar|Total||
|setcommands8|Conjuntos de (Shard 8)|Contar|Total||
|evictedkeys8|Teclas retiradas (Shard 8)|Contar|Total||
|totalkeys8|Total de chaves (nó 8)|Contar|Máximo||
|expiredkeys8|Teclas expiradas (Shard 8)|Contar|Total||
|usedmemory8|Memória utilizada (Shard 8)|Bytes|Máximo||
|usedmemoryRss8|Memória utilizada RSS (Shard 8)|Bytes|Máximo||
|serverLoad8|Carga do servidor (Shard 8)|Percentagem|Máximo||
|cacheWrite8|Cache de escrita (Shard 8)|BytesPerSecond|Máximo||
|cacheRead8|Cache de leitura (Shard 8)|BytesPerSecond|Máximo||
|percentProcessorTime8|CPU (Shard 8)|Percentagem|Máximo||
|connectedclients9|Clientes ligados (Shard 9)|Contar|Máximo||
|totalcommandsprocessed9|Operações total (Shard 9)|Contar|Total||
|cachehits9|Acertos (Shard 9)|Contar|Total||
|cachemisses9|Falhas na cache (Shard 9)|Contar|Total||
|getcommands9|Obtém (Shard 9)|Contar|Total||
|setcommands9|Conjuntos de (Shard 9)|Contar|Total||
|evictedkeys9|Teclas retiradas (Shard 9)|Contar|Total||
|totalkeys9|Total de chaves (nó 9)|Contar|Máximo||
|expiredkeys9|Teclas expiradas (Shard 9)|Contar|Total||
|usedmemory9|Memória utilizada (Shard 9)|Bytes|Máximo||
|usedmemoryRss9|Memória utilizada RSS (Shard 9)|Bytes|Máximo||
|serverLoad9|Carga do servidor (Shard 9)|Percentagem|Máximo||
|cacheWrite9|Cache de escrita (Shard 9)|BytesPerSecond|Máximo||
|cacheRead9|Cache de leitura (Shard 9)|BytesPerSecond|Máximo||
|percentProcessorTime9|CPU (Shard 9)|Percentagem|Máximo||

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|NumberOfCalls|Número total de chamadas à API|Contar|Total|Número total de chamadas à API.|
|NumberOfFailedCalls|Número total de falhadas API chamadas|Contar|Total|Número total de chamadas à API falhadas.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Percentagem de CPU|Percentagem de CPU|Percentagem|Média|A percentagem de cluster atribuída unidades que se encontram atualmente utilizado pelo Virtual Machine(s)|
|De rede|De rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Virtual Machine(s) (tráfego de receção)|
|Saída de rede|Saída de rede|Bytes|Total|O número de bytes out em todas as interfaces de rede pelo Virtual Machine(s) (tráfego de saída)|
|Bytes de leitura do disco|Bytes de leitura do disco|Bytes|Total|Total de bytes ler a partir do disco durante o período de monitorização|
|Bytes de escrita no disco|Bytes de escrita no disco|Bytes|Total|Total de bytes escritos disco durante o período de monitorização|
|Disco operações de leitura/seg|Disco operações de leitura/seg|CountPerSecond|Média|Leitura do disco IOPS|
|Operações de escrita de disco/seg|Operações de escrita de disco/seg|CountPerSecond|Média|Escrita IOPS no disco|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Percentagem de CPU|Percentagem de CPU|Percentagem|Média|A percentagem de cluster atribuída unidades que se encontram atualmente utilizado pelo Virtual Machine(s)|
|De rede|De rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Virtual Machine(s) (tráfego de receção)|
|Saída de rede|Saída de rede|Bytes|Total|O número de bytes reduzir em todas as interfaces de rede pelo Virtual Machine(s) (tráfego de saída)|
|Bytes de leitura do disco|Bytes de leitura do disco|Bytes|Total|Total de bytes ler a partir do disco durante o período de monitorização|
|Bytes de escrita no disco|Bytes de escrita no disco|Bytes|Total|Total de bytes escritos disco durante o período de monitorização|
|Disco operações de leitura/seg|Disco operações de leitura/seg|CountPerSecond|Média|Leitura do disco IOPS|
|Operações de escrita de disco/seg|Operações de escrita de disco/seg|CountPerSecond|Média|Escrita IOPS no disco|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Percentagem de CPU|Percentagem de CPU|Percentagem|Média|A percentagem de cluster atribuída unidades que se encontram atualmente utilizado pelo Virtual Machine(s)|
|De rede|De rede|Bytes|Total|O número de bytes recebidos em todas as interfaces de rede pela Virtual Machine(s) (tráfego de receção)|
|Saída de rede|Saída de rede|Bytes|Total|O número de bytes reduzir em todas as interfaces de rede pelo Virtual Machine(s) (tráfego de saída)|
|Bytes de leitura do disco|Bytes de leitura do disco|Bytes|Total|Total de bytes ler a partir do disco durante o período de monitorização|
|Bytes de escrita no disco|Bytes de escrita no disco|Bytes|Total|Total de bytes escritos disco durante o período de monitorização|
|Disco operações de leitura/seg|Disco operações de leitura/seg|CountPerSecond|Média|Leitura do disco IOPS|
|Operações de escrita de disco/seg|Operações de escrita de disco/seg|CountPerSecond|Média|Escrita IOPS no disco|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|d2c.telemetry.Ingress.allProtocol|Tentativas de enviar mensagem de telemetria|Contar|Total|Número de dispositivo para o Cloud mensagens de telemetria tentado sejam enviadas para o seu centro IoT|
|d2c.telemetry.Ingress.Success|Telemetria mensagens enviadas|Contar|Total|Número de dispositivo a nuvem telemetria mensagens enviadas com êxito para o seu centro IoT|
|c2d.Commands.egress.Complete.Success|Comandos concluídos|Contar|Total|Número de nuvem aos comandos de dispositivo foi concluída com êxito pelo dispositivo|
|c2d.Commands.egress.Abandon.Success|Comandos abandonados|Contar|Total|Número de nuvem aos comandos de dispositivo abandonadas pelo dispositivo|
|c2d.Commands.egress.Reject.Success|Comandos rejeitados|Contar|Total|Número de nuvem aos comandos de dispositivo rejeitada pelo dispositivo|
|devices.totalDevices|Dispositivos total|Contar|Total|Número de dispositivos registados para o seu centro IoT|
|devices.connectedDevices.allProtocol|Dispositivos ligados|Contar|Total|Número de dispositivos ligados à sua concentrador IoT|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|INREQS|Pedidos recebidos|Contar|Total|Evento concentrador recebida produtividade das mensagens para um espaço de nomes|
|SUCCREQ|Pedidos com êxito|Contar|Total|Total pedidos com êxito um espaço de nomes|
|FAILREQ|Pedidos de falhados|Contar|Total|Total de falha pedidos para um espaço de nomes|
|SVRBSY|Erros ocupado do servidor|Contar|Total|Erros ocupado do total do servidor para um espaço de nomes|
|INTERR|Erros de servidor interno|Contar|Total|Erros de servidor interno total para um espaço de nomes|
|MISCERR|Outros erros|Contar|Total|Total de falha pedidos para um espaço de nomes|
|INMSGS|Mensagens a receber|Contar|Total|Total mensagens recebidas para um espaço de nomes|
|OUTMSGS|Mensagens a enviar|Contar|Total|Total de envio de mensagens para um espaço de nomes|
|EHINMBS|Bytes recebidos por segundo|BytesPerSecond|Total|Evento concentrador recebida produtividade das mensagens para um espaço de nomes|
|EHOUTMBS|Enviadas bytes por segundo|BytesPerSecond|Total|Total de envio de mensagens para um espaço de nomes|
|EHABL|Arquivar mensagens de registo de tarefas pendentes|Contar|Total|Evento concentrador arquivar mensagens no registo para um espaço de nomes de tarefas pendentes|
|EHAMSGS|Arquivar mensagens|Contar|Total|Concentrador de evento arquivados mensagens num espaço de nomes|
|EHAMBS|Produtividade das mensagens arquivo|BytesPerSecond|Total|Produtividade das mensagens arquivadas de Hub do evento num espaço de nomes|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|RunsStarted|Será executado iniciado|Contar|Total|Número de fluxo de trabalho é executado iniciado.|
|RunsCompleted|Será executado concluído|Contar|Total|Número de fluxo de trabalho é executado concluído.|
|RunsSucceeded|Será executado foi concluída com êxito|Contar|Total|Número de fluxo de trabalho é executado bem sucedido.|
|RunsFailed|Ocorreu uma falha de será executado|Contar|Total|Número de fluxo de trabalho é executado falhado.|
|RunsCancelled|Será executado cancelado|Contar|Total|Número de fluxo de trabalho é executado cancelado.|
|RunLatency|Executar a latência|Segundos|Média|Latência de fluxo de trabalho concluída é executado.|
|RunSuccessLatency|Executar o sucesso latência|Segundos|Média|Latência de bem sucedida fluxo de trabalho é executado.|
|RunThrottledEvents|Executar Desacelerados eventos|Contar|Total|Número de ação de fluxo de trabalho ou accionador limitada eventos.|
|RunFailurePercentage|Executar a percentagem de falha|Percentagem|Total|Percentagem de fluxo de trabalho é executado falha.|
|ActionsStarted|Ações iniciadas |Contar|Total|Número de ações de fluxo de trabalho iniciado.|
|ActionsCompleted|Acções concluídas |Contar|Total|Número de ações de fluxo de trabalho concluído.|
|ActionsSucceeded|Ações foi concluída com êxito |Contar|Total|Número de ações de fluxo de trabalho foi concluída com êxito.|
|ActionsFailed|Ocorreu uma falha de ações|Contar|Total|Ocorreu uma falha em número de ações de fluxo de trabalho.|
|ActionsSkipped|Ações ignoradas |Contar|Total|Número de ações de fluxo de trabalho foi ignorada.|
|ActionLatency|Latência de ação |Segundos|Média|Latência das ações de fluxo de trabalho concluído.|
|ActionSuccessLatency|Latência de sucesso de ação |Segundos|Média|Latência das ações de fluxo de trabalho com êxito.|
|ActionThrottledEvents|Ação limitada eventos|Contar|Total|Número de ação de fluxo de trabalho limitada eventos...|
|TriggersStarted|Accionadores iniciados |Contar|Total|Número de fluxo de trabalho accionadores iniciado.|
|TriggersCompleted|Accionadores concluídas |Contar|Total|Número de accionadores de fluxo de trabalho concluído.|
|TriggersSucceeded|Accionadores foi concluída com êxito |Contar|Total|Número de accionadores de fluxo de trabalho foi concluída com êxito.|
|TriggersFailed|Ocorreu uma falha de accionadores |Contar|Total|Ocorreu uma falha em número de accionadores de fluxo de trabalho.|
|TriggersSkipped|Accionadores ignoradas|Contar|Total|Número de accionadores de fluxo de trabalho foi ignorada.|
|TriggersFired|Accionadores chama |Contar|Total|Número de accionadores de fluxo de trabalho chama.|
|TriggerLatency|Latência accionador |Segundos|Média|Latência de avanço do fluxo de trabalho concluído.|
|TriggerFireLatency|Latência de fogo de accionador |Segundos|Média|Latência de em chama accionadores de fluxo de trabalho.|
|TriggerSuccessLatency|Latência de sucesso accionador |Segundos|Média|Latência de em foi bem sucedida accionadores de fluxo de trabalho.|
|TriggerThrottledEvents|Acionar limitada eventos|Contar|Total|Número de accionador de fluxo de trabalho limitada eventos.|
|BillableActionExecutions|Ação cobrar execuções|Contar|Total|Número de execuções de ação de fluxo de trabalho introdução faturada.|
|BillableTriggerExecutions|Acionar cobrar execuções|Contar|Total|Número de execuções do fluxo de trabalho accionador introdução faturada.|
|TotalBillableExecutions|Total execuções cobrar|Contar|Total|Número de execuções do fluxo de trabalho introdução faturada.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|Débito|Débito|BytesPerSecond|Média||

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|SearchLatency|Latência da pesquisa|Segundos|Média|Latência da pesquisa médio para o serviço de pesquisa|
|SearchQueriesPerSecond|Consultas de pesquisa por segundo|CountPerSecond|Média|Consultas de pesquisa por segundo para o serviço de pesquisa|
|ThrottledSearchQueriesPercentage|Percentagem de consultas de pesquisa desacelerado|Percentagem|Média|Percentagem de consultas de pesquisa que foram limitada do serviço de pesquisa|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CPUXNS|Utilização da CPU por espaço de nomes|Percentagem|Máximo|Métrica de utilização do serviço bus premium espaço de nomes CPU|
|WSXNS|Utilização de tamanho de memória por espaço de nomes|Percentagem|Máximo|Métrica a utilização de memória espaço de nomes serviço bus premium|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|
|physical_data_read_percent|Percentagem de es de dados|Percentagem|Média|Percentagem de es de dados|
|log_write_percent|Registo IO percentagem|Percentagem|Média|Registo IO percentagem|
|dtu_consumption_percent|Percentagem DTU|Percentagem|Média|Percentagem DTU|
|armazenamento|Tamanho total da base de dados|Bytes|Máximo|Tamanho total da base de dados|
|connection_successful|Ligações com êxito|Contar|Total|Ligações com êxito|
|connection_failed|Ligações falhadas|Contar|Total|Ligações falhadas|
|blocked_by_firewall|Bloqueado pela Firewall|Contar|Total|Bloqueado pela Firewall|
|bloqueio|Bloqueios|Contar|Total|Bloqueios|
|storage_percent|Percentagem do tamanho da base de dados|Percentagem|Máximo|Percentagem do tamanho da base de dados|
|xtp_storage_percent|Na memória OLTP armazenamento percent(Preview)|Percentagem|Média|Na memória OLTP armazenamento percent(Preview)|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|
|dtu_limit|Limite DTU|Contar|Média|Limite DTU|
|dtu_used|DTU utilizado|Contar|Média|DTU utilizado|
|service_level_objective|Objectivo de nível de serviço da base de dados|Contar|Total|Objectivo de nível de serviço da base de dados|
|dwu_limit|limite de dwu|Contar|Máximo|limite de dwu|
|dwu_consumption_percent|Percentagem DWU|Percentagem|Média|Percentagem DWU|
|dwu_used|DWU utilizado|Contar|Média|DWU utilizado|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|cpu_percent|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|
|physical_data_read_percent|Percentagem de es de dados|Percentagem|Média|Percentagem de es de dados|
|log_write_percent|Registo IO percentagem|Percentagem|Média|Registo IO percentagem|
|dtu_consumption_percent|Percentagem DTU|Percentagem|Média|Percentagem DTU|
|storage_percent|Percentagem de armazenamento|Percentagem|Média|Percentagem de armazenamento|
|workers_percent|Percentagem de trabalhadores|Percentagem|Média|Percentagem de trabalhadores|
|sessions_percent|Percentagem de sessões|Percentagem|Média|Percentagem de sessões|
|eDTU_limit|limite de eDTU|Contar|Média|limite de eDTU|
|storage_limit|Limite de armazenamento|Bytes|Média|Limite de armazenamento|
|eDTU_used|eDTU utilizado|Contar|Média|eDTU utilizado|
|storage_used|Armazenamento utilizado|Bytes|Média|Armazenamento utilizado|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|ResourceUtilization|% De utilização de Oliveira|Percentagem|Máximo|% De utilização de Oliveira|
|InputEvents|Eventos de entradas|Contar|Total|Eventos de entradas|
|InputEventBytes|Bytes evento de entrada|Bytes|Total|Bytes evento de entrada|
|LateInputEvents|Eventos de entrada atrasados|Contar|Total|Eventos de entrada atrasados|
|OutputEvents|Eventos de saída|Contar|Total|Eventos de saída|
|ConversionErrors|Erros de conversão de dados|Contar|Total|Erros de conversão de dados|
|Erros|Erros de tempo de execução|Contar|Total|Erros de tempo de execução|
|DroppedOrAdjustedEvents|Eventos de ordem|Contar|Total|Eventos de ordem|
|AMLCalloutRequests|Pedidos de função|Contar|Total|Pedidos de função|
|AMLCalloutFailedRequests|Pedidos de falha de função|Contar|Total|Pedidos de falha de função|
|AMLCalloutInputEvents|Função eventos|Contar|Total|Função eventos|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuPercentage|Percentagem de CPU|Percentagem|Média|Percentagem de CPU|
|MemoryPercentage|Percentagem de memória|Percentagem|Média|Percentagem de memória|
|DiskQueueLength|Comprimento da fila do disco|Contar|Total|Comprimento da fila do disco|
|HttpQueueLength|Comprimento da fila de HTTP|Contar|Total|Comprimento da fila de HTTP|
|BytesReceived|Dados|Bytes|Total|Dados|
|BytesSent|Dados de saída|Bytes|Total|Dados de saída|

## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|
|Pedidos de|Pedidos de|Contar|Total|Pedidos de|
|BytesReceived|Dados|Bytes|Total|Dados|
|BytesSent|Dados de saída|Bytes|Total|Dados de saída|
|Http2xx|Http 2xx|Contar|Total|Http 2xx|
|Http3xx|Http 3xx|Contar|Total|Http 3xx|
|Http401|HTTP 401|Contar|Total|HTTP 401|
|Http403|HTTP 403|Contar|Total|HTTP 403|
|Http404|HTTP 404|Contar|Total|HTTP 404|
|Http406|HTTP 406|Contar|Total|HTTP 406|
|Http4xx|Http 4xx|Contar|Total|Http 4xx|
|Http5xx|Erros do servidor de HTTP|Contar|Total|Erros do servidor de HTTP|
|MemoryWorkingSet|Conjunto de memória funcional|Bytes|Total|Conjunto de memória funcional|
|AverageMemoryWorkingSet|Memória média conjunto de trabalho|Bytes|Média|Memória média conjunto de trabalho|
|AverageResponseTime|Tempo de resposta médio|Segundos|Média|Tempo de resposta médio|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Métrica|Nome a apresentar métrica|Unidade|Tipo de agregação|Descrição|
|---|---|---|---|---|
|CpuTime|Tempo de CPU|Segundos|Total|Tempo de CPU|
|Pedidos de|Pedidos de|Contar|Total|Pedidos de|
|BytesReceived|Dados|Bytes|Total|Dados|
|BytesSent|Dados de saída|Bytes|Total|Dados de saída|
|Http2xx|Http 2xx|Contar|Total|Http 2xx|
|Http3xx|Http 3xx|Contar|Total|Http 3xx|
|Http401|HTTP 401|Contar|Total|HTTP 401|
|Http403|HTTP 403|Contar|Total|HTTP 403|
|Http404|HTTP 404|Contar|Total|HTTP 404|
|Http406|HTTP 406|Contar|Total|HTTP 406|
|Http4xx|Http 4xx|Contar|Total|Http 4xx|
|Http5xx|Erros do servidor de HTTP|Contar|Total|Erros do servidor de HTTP|
|MemoryWorkingSet|Conjunto de memória funcional|Bytes|Total|Conjunto de memória funcional|
|AverageMemoryWorkingSet|Memória média conjunto de trabalho|Bytes|Média|Memória média conjunto de trabalho|
|AverageResponseTime|Tempo de resposta médio|Segundos|Média|Tempo de resposta médio|

## <a name="next-steps"></a>Próximos passos

- [Leia sobre métricas no Monitor do Azure](./monitoring-overview.md#monitoring-sources)
- [Criar alertas em métricas](./insights-receive-alert-notifications.md)
- [Exportar métricas de armazenamento, concentrador de evento ou a análise de registo](./monitoring-overview-of-diagnostic-logs.md)
