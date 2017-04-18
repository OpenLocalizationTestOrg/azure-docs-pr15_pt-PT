<properties
   pageTitle="Diagnósticos intervenientes e monitorização | Microsoft Azure"
   description="Este artigo descreve as funcionalidades no runtime do serviço ferro fiável intervenientes, incluindo os eventos e contadores de desempenho emitidos por-lo de monitorização de desempenho de diagnóstico e."
   services="service-fabric"
   documentationCenter=".net"
   authors="abhishekram"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/05/2016"
   ms.author="abhisram"/>

# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>De diagnóstico e monitorização de desempenho aos intervenientes fiável
O tempo de execução intervenientes fiável emite [Origemdoevento](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) eventos e [contadores de desempenho](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx). Estes fornecem informações para o modo como o tempo de execução está a funcionar e ajudam para resolução de problemas e monitorização de desempenho.

## <a name="eventsource-events"></a>Origemdoevento eventos
O nome do fornecedor Origemdoevento para o runtime fiável intervenientes é "Intervenientes da ServiceFabric Microsoft". Eventos de origem este evento aparecem na janela de [Eventos de diagnósticos](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) quando a aplicação do ator está a ser [depurado no Visual Studio](service-fabric-debugging-your-application.md).

Exemplos de ferramentas e tecnologias que ajudam a recolher e/ou visualizar os eventos Origemdoevento são [PerfView](http://www.microsoft.com/download/details.aspx?id=28567), [Diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md), [Semântico registo](https://msdn.microsoft.com/library/dn774980.aspx)e a [Biblioteca do Microsoft TraceEvent](http://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Palavras-chave
Todos os eventos que pertencem a Origemdoevento intervenientes fiável estão associados um ou mais palavras-chave. Isto permite a filtragem de eventos que são recolhidos. Os seguintes bits de palavra-chave são definidos.

|Bit|Descrição|
|---|---|
|0x1|Conjunto de eventos importantes que resumem a operação de runtime ferro intervenientes.|
|0x2|Conjunto de eventos que descrevem ator método chamadas. Para mais informações, consulte o [tópico de introdução no intervenientes](service-fabric-reliable-actors-introduction.md#actors).|
|0x4|Conjunto de eventos relacionados com o estado do ator. Para mais informações, consulte o tópico sobre a [Gestão de estado do ator](service-fabric-reliable-actors-state-management.md).|
|0x8|Conjunto de eventos relacionados com a simultaneidade com base em ativar na ator. Para mais informações, consulte o tópico sobre [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency).|

## <a name="performance-counters"></a>Contadores de desempenho
O tempo de execução intervenientes fiável define as seguintes categorias de contador de desempenho.

|Categoria|Descrição|
|---|---|
|Serviço ferro Ator|Contadores específicos para Azure Service ferro intervenientes, por exemplo, tempo redirecionados para guardar o estado do ator|
|Método de Ator ferro do serviço|Contadores específico de métodos por ao intervenientes ferro de serviço, por exemplo, com que frequência um método de ator é chamado|

Cada uma das categorias acima tem um ou mais contadores.

A aplicação de [Monitor de desempenho do Windows](https://technet.microsoft.com/library/cc749249.aspx) que está disponível por predefinição no sistema operativo Windows pode ser utilizada para recolher e visualizar dados de contador de desempenho. [Diagnósticos do Azure](../cloud-services/cloud-services-dotnet-diagnostics.md) é outra opção para recolher dados de contador de desempenho e carregá-lo para tabelas Azure.

### <a name="performance-counter-instance-names"></a>Nomes de instância do contador de desempenho
Um cluster de que tem um grande número de serviços de ator ou a partições de serviço do ator terá um grande número de instâncias de contador de desempenho do ator. Os nomes de ocorrência de contador de desempenho podem ajudar a identificar o método de [partição](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) e ator específico (se aplicável) que está associada a instância de contador de desempenho.

#### <a name="service-fabric-actor-category"></a>Categoria de serviço do Ator ferro
Para a categoria `Service Fabric Actor`, os nomes de instância de contador estão no seguinte formato:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* é a representação de cadeia do ID da partição ferro de serviço que está associada a instância de contador de desempenho. O ID da partição é um GUID e respectiva representação de cadeia é gerada através de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de um número inteiro de 64-bit é gerado pelo runtime ferro intervenientes para a sua utilização interna. Isto é incluído no nome da instância contador de desempenho para garantir a respectiva exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não deverão tentar interpretar esta parte do nome da instância do desempenho contador.

Segue-se um exemplo de um nome da instância contador de um contador que pertence a `Service Fabric Actor` categoria:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

No exemplo acima, `2740af29-78aa-44bc-a20b-7e60fb783264` é a representação de cadeia do ID da partição ferro de serviço, e `635650083799324046` é utilizar o ID de 64-bit é gerado para o tempo de execução estiverem interno.

#### <a name="service-fabric-actor-method-category"></a>Categoria de serviço ferro Ator método
Para a categoria `Service Fabric Actor Method`, os nomes de instância de contador estão no seguinte formato:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* é o nome do método ator que está associada a instância de contador de desempenho. O formato do nome do método é determinado com base em algumas lógica no runtime do ferro intervenientes que equilibra a legibilidade do nome com as restrições o comprimento máximo de nomes de instância do contador de desempenho no Windows.

*ActorsRuntimeMethodId* é a representação de cadeia de um número inteiro de 32 bits é gerado pelo runtime ferro intervenientes para a sua utilização interna. Isto é incluído no nome da instância contador de desempenho para garantir a respectiva exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não deverão tentar interpretar esta parte do nome da instância do desempenho contador.

*ServiceFabricPartitionID* é a representação de cadeia do ID da partição ferro de serviço que está associada a instância de contador de desempenho. O ID da partição é um GUID e respectiva representação de cadeia é gerada através de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) método com o especificador de formato "D".

*ActorRuntimeInternalID* é a representação de cadeia de um número inteiro de 64-bit é gerado pelo runtime ferro intervenientes para a sua utilização interna. Isto é incluído no nome da instância contador de desempenho para garantir a respectiva exclusividade e evitar conflitos com outros nomes de instância do contador de desempenho. Os utilizadores não deverão tentar interpretar esta parte do nome da instância do desempenho contador.

Segue-se um exemplo de um nome da instância contador de um contador que pertence a `Service Fabric Actor Method` categoria:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

No exemplo acima, `ivoicemailboxactor.leavemessageasync` é o nome do método, `2` é o ID de 32 bits gerado para utilização interna do runtime, `89383d32-e57e-4a9b-a6ad-57c6792aa521` é a representação de cadeia do ID da partição ferro de serviço, e `635650083804480486` é utilizar o ID de 64 bits gerado para o tempo de execução estiverem interno.

## <a name="list-of-events-and-performance-counters"></a>Lista de eventos e contadores de desempenho

### <a name="actor-method-events-and-performance-counters"></a>Eventos de método de ator e contadores de desempenho
O tempo de execução intervenientes fiável emite seguintes eventos relacionados com [métodos ator](service-fabric-reliable-actors-introduction.md#actors).

|Nome do evento|ID do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorMethodStart|7|Verboso|0x2|Intervenientes runtime está prestes a invocar um método de ator.|
|ActorMethodStop|8|Verboso|0x2|Um método de ator já terminou em execução. Isto é, chamada de assíncrona o runtime para o método de ator tem devolvido e, a tarefa devolvida pelo método ator já terminou.|
|ActorMethodThrewException|9|Aviso|0x3|Ocorreu uma excepção durante a execução de um método de ator, durante a chamada de assíncrona o runtime para o método de ator ou durante a execução da tarefa devolvido pelo método ator. Este evento indica algum tipo de falha no código do ator que necessita de inquérito.|

O tempo de execução intervenientes fiável publica seguintes contadores de desempenho relacionados com a execução dos métodos de ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Método de Ator ferro do serviço|/ Seg. exe|Número de vezes que o método de serviço do ator é chamado por segundo|
|Método de Ator ferro do serviço|Média milissegundos por invocação de funções|Tempo despendido para executar o método de serviço do ator em milissegundos|
|Método de Ator ferro do serviço|Exceções iniciadas/seg.|Número de vezes que o ator serviço método emitiu uma exceção por segundo|

### <a name="concurrency-events-and-performance-counters"></a>Eventos de simultaneidade e contadores de desempenho
O tempo de execução intervenientes fiável emite seguintes eventos relacionados com [simultaneidade](service-fabric-reliable-actors-introduction.md#concurrency).

|Nome do evento|ID do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorMethodCallsWaitingForLock|12|Verboso|0x8|Este evento é escrito no início de cada novo entregar um ator. Contém o número de pendentes chamadas ator que estão a aguardar para adquirir o bloqueio por ator que imponha simultaneidade com base em ativar.|

O tempo de execução intervenientes fiável publica seguintes contadores de desempenho relacionados com simultaneidade.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço ferro Ator|# de chamadas do ator aguardar ator bloqueio|Número de pendentes do ator chamadas em espera para adquirir o bloqueio por ator que imponha simultaneidade com base em ativar|
|Serviço ferro Ator|Média milissegundos por bloquear esperar|Tempo decorrido (em milissegundos) para adquirir o bloqueio por ator que imponha simultaneidade com base em ativar|
|Serviço ferro Ator|Média milissegundos ator bloqueio mantido|Tempo (em milissegundos) para o qual o bloqueio de por ator é mantido|

### <a name="actor-state-management-events-and-performance-counters"></a>Eventos de gestão de estado do ator e contadores de desempenho
O tempo de execução intervenientes fiável emite seguintes eventos relacionados com a [Gestão de estado do ator](service-fabric-reliable-actors-state-management.md).

|Nome do evento|ID do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorSaveStateStart|10|Verboso|0x4|Intervenientes runtime está prestes a guardar o estado do ator.|
|ActorSaveStateStop|11|Verboso|0x4|Tempo de execução do intervenientes acabou de guardar o estado do ator.|

O tempo de execução intervenientes fiável publica seguintes contadores de desempenho relacionadas com a gestão de estado do ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço ferro Ator|Média milissegundos por guardar a operação de estado|Tempo despendido para guardar o estado de ator em milissegundos|
|Serviço ferro Ator|Média milissegundos por operação de estado de carregamento|Tempo despendido para carregar o estado do ator em milissegundos|

### <a name="events-related-to-actor-replicas"></a>Eventos relacionados com a réplicas do ator
O tempo de execução intervenientes fiável emite seguintes eventos relacionados com [réplicas do ator](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-stateful-actors).

|Nome do evento|ID do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ReplicaChangeRoleToPrimary|1|Informativos|0x1|Réplica do ator alteradas função para principal. Isto significa que os intervenientes para esta partição serão criados dentro esta réplica.|
|ReplicaChangeRoleFromPrimary|2|Informativos|0x1|Réplica do ator alteradas função para que não sejam principal. Isto significa que os intervenientes para esta partição já não serão criados dentro esta réplica. Sem novos pedidos de serão enviados a intervenientes já criadas dentro esta réplica. Os intervenientes serão destruídos depois de todos os pedidos em curso são concluídos.|

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Eventos de ativação e desativação do ator e contadores de desempenho
O tempo de execução intervenientes fiável emite seguintes eventos relacionados com a [ativação do ator e desativação](service-fabric-reliable-actors-lifecycle.md).

|Nome do evento|ID do evento|Nível|Palavra-chave|Descrição|
|---|---|---|---|---|
|ActorActivated|5|Informativos|0x1|Um ator foi ativado.|
|ActorDeactivated|6|Informativos|0x1|Um ator tenha sido desativado.|

O tempo de execução intervenientes fiável publica seguintes contadores de desempenho relacionados com a ativação do ator e desativação.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço ferro Ator|Média OnActivateAsync milissegundos|Tempo despendido para executar o método de OnActivateAsync em milissegundos|

### <a name="actor-request-processing-performance-counters"></a>Processamento do pedido do ator contadores de desempenho
Quando um cliente invoca um método através de um objeto de proxy do ator, resulta numa mensagem de pedido a ser enviada através da rede para o serviço do ator. O serviço processa a mensagem de pedido e envia uma resposta de volta para o cliente. O tempo de execução intervenientes fiável publica seguintes contadores de desempenho relacionados com o processamento de pedidos ator.

|Nome da categoria|Nome do contador|Descrição|
|---|---|---|
|Serviço ferro Ator|# de pedidos pendentes|Número de pedidos de processados no serviço|
|Serviço ferro Ator|Média milissegundos por pedido|Tempo decorrido (em milissegundos) pelo serviço para processar um pedido|
|Serviço ferro Ator|Média milissegundos para desserialização pedido|Tempo decorrido (em milissegundos) para anular a mensagem de pedido de ator serialização quando for recebida no serviço de|
|Serviço ferro Ator|Média em milissegundos para serialização de resposta|Tempo decorrido (em milissegundos) para serializar a mensagem de resposta do ator no serviço de antes da resposta é enviada para o cliente|

## <a name="next-steps"></a>Próximos passos
 - [Como intervenientes fiável utilizar a plataforma ferro de serviço](service-fabric-reliable-actors-platform.md)
 - [Documentação de referência do ator API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
 - [Código de exemplo](https://github.com/Azure/servicefabric-samples)
