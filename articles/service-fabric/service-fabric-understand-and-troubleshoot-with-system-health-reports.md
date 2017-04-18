<properties
   pageTitle="Resolver problemas com relatórios de estado de funcionamento do sistema | Microsoft Azure"
   description="Descreve os relatórios de estado de funcionamento que foi enviados por componentes do Azure Service ferro e a sua utilização para problemas de aplicação ou cluster de resolução de problemas."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/28/2016"
   ms.author="oanapl"/>

# <a name="use-system-health-reports-to-troubleshoot"></a>Utilizar relatórios de estado de funcionamento de sistema para resolução de problemas

Azure Service ferro componentes relatório terminar a caixa de sobre todas as entidades no cluster. O [Estado de funcionamento do armazenar](service-fabric-health-introduction.md#health-store) cria e elimina as entidades com base nos relatórios de sistema. -Lo também organiza-as numa hierarquia que captura interações entidade.

> [AZURE.NOTE] Para compreender os conceitos relacionados com o estado de funcionamento, ler mais no [modelo de estado de funcionamento do serviço ferro](service-fabric-health-introduction.md).

Relatórios de estado de funcionamento do sistema fornecem visibilidade do cluster e funcionalidade da aplicação e problemas de sinalizador através do Estado de funcionamento. Para aplicações e serviços, relatórios de estado de funcionamento do sistema verifique entidades são implementadas e são comporta corretamente da perspetiva ferro de serviço. Os relatórios não fornecem qualquer monitorização do Estado de funcionamento da lógica empresarial do serviço ou de deteção de processos parados. Serviços de utilizador podem enriquecer os dados de estado de funcionamento com informações específicas para os respetivos lógica.

> [AZURE.NOTE] Relatórios de estado de funcionamento fiscais estão visíveis apenas *depois dos* componentes do sistema criam uma entidade. Quando uma entidade é eliminada, o arquivo de estado de funcionamento automaticamente elimina todos os relatórios de estado de funcionamento associados. O mesmo se aplica quando é criada uma nova instância da entidade (por exemplo, é criada uma nova instância de réplica do serviço). Todos os relatórios associados a instância antiga são eliminados e desorganizados; a partir da loja.

O componente de sistema relatórios são identificadas pela origem, que começa com o "**sistema.**" prefixo. Fiscais não podem utilizar o mesmo prefixo para as suas fontes, como relatórios com os parâmetros inválidos são rejeitados.
Vamos olhar alguns relatórios de sistema para compreender o que accionadores-los e como corrigir possíveis problemas que representam.

> [AZURE.NOTE] Serviço ferro continua a adicionar relatórios em condições de interesse que melhoram a visibilidade para o que se passa na cluster e na aplicação.

## <a name="cluster-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de cluster
A entidade de estado de funcionamento de cluster é criada automaticamente na loja do Estado de funcionamento. Se tudo está a funcionar corretamente, não tem um relatório de sistema.

### <a name="neighborhood-loss"></a>Perda de vizinhança
**System.Federation** relatórios um erro quando Deteta uma perda vizinhança. O relatório é a partir de nós individuais e o ID de nó está incluído no nome da propriedade. Se um Vizinhança perde-se em tocar no serviço ferro inteira, que pode esperar normalmente dois eventos (ambos os lados do relatório intervalo: controla). Se perdem-se mais bairros, existem mais eventos.

O relatório Especifica o tempo limite global locação financeira, como a time to live. O relatório é enviado de novo cada metade da duração TTL desde que a condição permanece ativa. O evento automaticamente é removido quando expira. Remova quando comportamento expirado garante que o relatório é desorganizado; a partir da loja do Estado de funcionamento corretamente, mesmo se for o nó elaboração de relatórios para baixo.

- **SourceId**: System.Federation
- **Propriedade**: começa por **Vizinhança** e inclui informações sobre o nó
- **Passos seguintes**: investigar por que motivo é perdida a Vizinhança (por exemplo, verifique a comunicação entre nós de cluster).

## <a name="node-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de nó
**System.FM**, que representa o serviço do Gestor de activação pós-falha, é a autoridade que gere informações sobre os nós de cluster. Cada nó deverá ter um relatório a partir do System.FM a mostrar o seu estado. As entidades de nó são removidas quando o estado de nó é removido (consulte [RemoveNodeStateAsync](https://msdn.microsoft.com/library/azure/mt161348.aspx)).

### <a name="node-updown"></a>Nó para cima/baixo
System.FM relatórios como OK quando o nó adere tocar (está a trabalhar). Reportar um erro, quando o nó partida tocar (é para baixo, um para atualizar ou simplesmente porque tem falhou). A hierarquia de estado de funcionamento criada pelo arquivo de estado de funcionamento assume ação implementadas entidades de correlação com relatórios de nó System.FM. Considerar o nó um elemento principal virtual de todas as entidades implementadas. As entidades implementadas nesse nó são expostas através de consultas se o nó for comunicado como até ao System.FM, com a mesma instância como a instância associada com as entidades. Quando System.FM comunica que o nó for para baixo ou reiniciado (uma nova instância), o arquivo de estado de funcionamento limpa automaticamente as entidades implementadas que podem existir apenas o nó para baixo ou a instância anterior do nó.

- **SourceId**: System.FM
- **Propriedade**: Estado
- **Passos seguintes**: se o nó destina-se para baixo uma atualização,-deve voltar atrás para cima assim que foi atualizada. Neste caso, o estado de funcionamento deve ser mudado voltar a OK. Se o nó não voltar atrás ou falhar, o problema precisa de mais de inquérito.

O exemplo seguinte mostra o evento System.FM com um Estado de funcionamento da OK para nó:

```powershell

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 2
                        SentAt                : 4/24/2015 5:27:33 PM
                        ReceivedAt            : 4/24/2015 5:28:50 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 5:28:50 PM

```


### <a name="certificate-expiration"></a>Expiração de certificado
**System.FabricNode** relatórios um aviso quando os certificados utilizados pelo nó são perto expiração. Existem três certificados por nó: **Certificate_cluster**, **Certificate_server**e **Certificate_default_client**. Quando a data de expiração estiver ausente, pelo menos, duas semanas, o estado de funcionamento de relatório é. Quando estiver a data de expiração nas duas semanas, o tipo de relatório é um aviso. TTL destes eventos é infinito e estes são removidos quando um nó deixa o cluster.

- **SourceId**: System.FabricNode
- **Propriedade**: começa com o **certificado** e contém mais informações sobre o tipo de certificado
- **Passos seguintes**: atualizar os certificados se estiverem perto expiração.

### <a name="load-capacity-violation"></a>Violação de capacidade de carregamento
O Balanceador de carga de ferro serviço relatórios um aviso se detetar uma violação de capacidade de nó.

 - **SourceId**: System.PLB
 - **Propriedade**: começa com **capacidade de**
 - **Passos seguintes**: verificar fornecidos métricas e visualizar a capacidade de corrente no nó.

## <a name="application-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de aplicação
**System.CM**, que representa o serviço Cluster Gestor, é a autoridade que gere a informações sobre uma aplicação.

### <a name="state"></a>Estado
System.CM relatórios como OK quando a aplicação foi criada ou atualizada. Informa o arquivo de estado de funcionamento quando a aplicação foi eliminada, para que pode ser removida da loja.

- **SourceId**: System.CM
- **Propriedade**: Estado
- **Passos seguintes**: se a aplicação foi criada, deve incluir o relatório de estado de funcionamento do Gestor de clusters. Caso contrário, verifique o estado da aplicação ao emitir uma consulta (por exemplo, o cmdlet do PowerShell * *Get-ServiceFabricApplication ApplicationName *applicationName** *).

O exemplo seguinte mostra o evento de estado na **ferro: / WordCount** aplicação:

```powershell
PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount -ServicesFilter None -DeployedApplicationsFilter None

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Ok
ServiceHealthStates             : None
DeployedApplicationHealthStates : None
HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 82
                                  SentAt                : 4/24/2015 6:12:51 PM
                                  ReceivedAt            : 4/24/2015 6:12:51 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/24/2015 6:12:51 PM
```

## <a name="service-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de serviço
**System.FM**, que representa o serviço do Gestor de activação pós-falha, é a autoridade que gere a informações sobre os serviços.

### <a name="state"></a>Estado
System.FM relatórios como OK quando o serviço foi criado. Elimina a entidade a partir da loja do Estado de funcionamento quando o serviço foi eliminado.

- **SourceId**: System.FM
- **Propriedade**: Estado

O exemplo seguinte mostra o evento de estado, o serviço de **ferro: / WordCount/WordCountService**:

```powershell
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService

ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Ok
PartitionHealthStates :
                        PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 3
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:01 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:01 PM
```

### <a name="unplaced-replicas-violation"></a>Violação de réplicas não colocados
**System.PLB** relatórios um aviso se não encontrar um local para uma ou mais réplicas do serviço. O relatório é removido quando expira.

- **SourceId**: System.FM
- **Propriedade**: Estado
- **Passos seguintes**: verificar as restrições de serviço e o estado atual da colocação.

O exemplo seguinte mostra uma violação feita por um serviço configurada com 7 réplicas destino num cluster com 5 nós:

```xml
PS C:\> Get-ServiceFabricServiceHealth fabric:/WordCount/WordCountService


ServiceName           : fabric:/WordCount/WordCountService
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB',
                        Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                        ConsiderWarningAsError=false.

PartitionHealthStates :
                        PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        AggregatedHealthState : Warning

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 10
                        SentAt                : 3/22/2016 7:56:53 PM
                        ReceivedAt            : 3/22/2016 7:57:18 PM
                        TTL                   : Infinite
                        Description           : Service has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:18 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.PLB
                        Property              : ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b
                        HealthState           : Warning
                        SequenceNumber        : 131032232425505477
                        SentAt                : 3/23/2016 4:14:02 PM
                        ReceivedAt            : 3/23/2016 4:14:03 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer was unable to find a placement for one or more of the Service's Replicas:
                        fabric:/WordCount/WordCountService Secondary Partition a1f83a35-d6bf-4d39-b90d-28d15f39599b could not be placed, possibly,
                        due to the following constraints and properties:  
                        Placement Constraint: N/A
                        Depended Service: N/A

                        Constraint Elimination Sequence:
                        ReplicaExclusionStatic eliminated 4 possible node(s) for placement -- 1/5 node(s) remain.
                        ReplicaExclusionDynamic eliminated 1 possible node(s) for placement -- 0/5 node(s) remain.

                        Nodes Eliminated By Constraints:

                        ReplicaExclusionStatic:
                        FaultDomain:fd:/0 NodeName:_Node_0 NodeType:NodeType0 UpgradeDomain:0 UpgradeDomain: ud:/0 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/1 NodeName:_Node_1 NodeType:NodeType1 UpgradeDomain:1 UpgradeDomain: ud:/1 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/3 NodeName:_Node_3 NodeType:NodeType3 UpgradeDomain:3 UpgradeDomain: ud:/3 Deactivation Intent/Status:
                        None/None
                        FaultDomain:fd:/4 NodeName:_Node_4 NodeType:NodeType4 UpgradeDomain:4 UpgradeDomain: ud:/4 Deactivation Intent/Status:
                        None/None

                        ReplicaExclusionDynamic:
                        FaultDomain:fd:/2 NodeName:_Node_2 NodeType:NodeType2 UpgradeDomain:2 UpgradeDomain: ud:/2 Deactivation Intent/Status:
                        None/None


                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="partition-system-health-reports"></a>Relatórios de estado de funcionamento do sistema partição
**System.FM**, que representa o serviço do Gestor de activação pós-falha, é a autoridade que gere a obter informações sobre a partições de serviço.

### <a name="state"></a>Estado
System.FM relatórios como OK quando a partição foi criada e está em bom estada. Elimina a entidade a partir da loja do Estado de funcionamento quando a partição é eliminada.

Se estiver a partições abaixo a contagem de réplica mínimo, relatórios de um erro. Se a partição não for inferior a contagem de réplica mínimo, mas encontra-se abaixo a contagem de réplica de destino, relatórios de um aviso. Se estiver a partições na perda de quórum, System.FM relatórios um erro.

Outros eventos importantes incluem um aviso quando a reconfiguração demora mais tempo do que o esperado e quando a compilação demora mais tempo do que o esperado. O esperado horas para a compilação e reconfiguração são configuráveis com base em cenários de serviço. Por exemplo, se um serviço tem um terabyte de estado, tal como a base de dados SQL, a compilação demora mais de um serviço com uma pequena quantidade de estado.

- **SourceId**: System.FM
- **Propriedade**: Estado
- **Passos seguintes**: se o estado de funcionamento não for OK, é possível que algumas réplicas não foram criadas, abertas ou promovidas a principal ou secundário corretamente. Em muitos casos, a causa de raiz é um erro de serviço na aplicação abrir ou alterar a função.

O exemplo seguinte mostra uma partição Saudável:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/StatelessPiApplication/StatelessPiService | Get-ServiceFabricPartitionHealth
PartitionId           : 29da484c-2c08-40c5-b5d9-03774af9a9bf
AggregatedHealthState : Ok
ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 38
                        SentAt                : 4/24/2015 6:33:10 PM
                        ReceivedAt            : 4/24/2015 6:33:31 PM
                        TTL                   : Infinite
                        Description           : Partition is healthy.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:33:31 PM
```

O exemplo seguinte mostra o estado de funcionamento de uma partição de que está abaixo contagem de réplica de destino. O passo seguinte é obter a descrição da partição, que mostra como está configurado: **MinReplicaSetSize** é dois e **TargetReplicaSetSize** é sete. Obter o número de nós no cluster: cinco. Neste caso, duas réplicas não podem ser colocadas em.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth -ReplicasFilter None

PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   : None
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 37
                        SentAt                : 4/24/2015 6:13:12 PM
                        ReceivedAt            : 4/24/2015 6:13:31 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Ok->Warning = 4/24/2015 6:13:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService

PartitionId            : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
PartitionKind          : Int64Range
PartitionLowKey        : 1
PartitionHighKey       : 26
PartitionStatus        : Ready
LastQuorumLossDuration : 00:00:00
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 7
HealthState            : Warning
DataLossNumber         : 130743727710830900
ConfigurationNumber    : 8589934592


PS C:\> @(Get-ServiceFabricNode).Count
5
```

### <a name="replica-constraint-violation"></a>Violação de restrição de réplica
**System.PLB** relatórios um aviso se detetar uma violação de restrição de réplica e não é possível colocar réplicas da partição.

- **SourceId**: System.PLB
- **Propriedade**: começa por **ReplicaConstraintViolation**

## <a name="replica-system-health-reports"></a>Relatórios de estado de funcionamento do sistema de réplica
**System.RA**, que representa o componente de agente reconfiguração, é a autoridade para o estado de réplica.

### <a name="state"></a>Estado
**System.RA** relatórios como OK quando a réplica foi criada.

- **SourceId**: System.RA
- **Propriedade**: Estado

O exemplo seguinte mostra uma réplica Saudável:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth
PartitionId           : 875a1caa-d79f-43bd-ac9d-43ee89a9891c
ReplicaId             : 130743727717237310
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743727718018580
                        SentAt                : 4/24/2015 6:12:51 PM
                        ReceivedAt            : 4/24/2015 6:13:02 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:02 PM
```

### <a name="replica-open-status"></a>Estado de aberta réplica
A descrição deste relatório de estado de funcionamento contém a hora de início (a hora Universal Coordenada) quando a chamada à API foi chamada.

**System.RA** relatórios um aviso se a réplica abrir demora mais tempo do que o período de configurados (predefinido: 30 minutos). Se a API impactos disponibilidade do serviço, o relatório é emitido muito mais rapidamente (um configurável intervalo, com uma predefinição de 30 segundos). O tempo de medida inclui o tempo despendido para o abrir replicação e o abrir de serviço. A propriedade alterações para OK se completar o abrir.

- **SourceId**: System.RA
- **Propriedade**: **ReplicaOpenStatus**
- **Passos seguintes**: se o estado de funcionamento não OK, investigar por que motivo a réplica abrir demora mais tempo do que o esperado.

### <a name="slow-service-api-call"></a>Chamada de API do serviço lenta
Relatório de **System.RAP** e **System.Replicator** um aviso se uma chamada para o código de serviço de utilizador demora mais tempo do que o período de tempo configurado. O aviso está desmarcado quando concluir a chamada.

- **SourceId**: System.RAP ou System.Replicator
- **Propriedade**: O nome da API do lenta. A descrição fornece mais detalhes sobre o tempo que a API foi pendente.
- **Passos seguintes**: investigar porque é que a chamada demora mais tempo do que o esperado.

O exemplo seguinte mostra uma partição no perda quórum e os passos de inquérito concluídos para descobrir por que motivo. Das réplicas tem um Estado de funcionamento de aviso para obter o seu estado de funcionamento. Mostra que a operação do serviço demora mais tempo que o esperado, um evento comunicado por System.RAP. Após esta informação é recebida, o próximo passo consiste em observe o código de serviço e investigar aí. Neste caso, a aplicação de **RunAsync** do serviço com estado inicia uma exceção não processada. As réplicas são Reciclagem, pelo que não poderá ver quaisquer réplicas no estado de aviso. Pode repetir a obter o estado de funcionamento e procure quaisquer diferenças no ID de réplica do. Em certos casos, o número de tentativas pode dar-lhe pistas.

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful | Get-ServiceFabricPartitionHealth

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
AggregatedHealthState : Error
UnhealthyEvaluations  :
                        Error event: SourceId='System.FM', Property='State'.

ReplicaHealthStates   :
                        ReplicaId             : 130743748372546446
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746168084332
                        AggregatedHealthState : Ok

                        ReplicaId             : 130743746195428808
                        AggregatedHealthState : Warning

                        ReplicaId             : 130743746195428807
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Error
                        SequenceNumber        : 182
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:31 PM
                        TTL                   : Infinite
                        Description           : Partition is in quorum loss.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Warning->Error = 4/24/2015 6:51:31 PM

PS C:\> Get-ServiceFabricPartition fabric:/HelloWorldStatefulApplication/HelloWorldStateful

PartitionId            : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
PartitionKind          : Int64Range
PartitionLowKey        : -9223372036854775808
PartitionHighKey       : 9223372036854775807
PartitionStatus        : InQuorumLoss
LastQuorumLossDuration : 00:00:13
MinReplicaSetSize      : 2
TargetReplicaSetSize   : 3
HealthState            : Error
DataLossNumber         : 130743746152927699
ConfigurationNumber    : 227633266688

PS C:\> Get-ServiceFabricReplica 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

ReplicaId           : 130743746195428808
ReplicaAddress      : PartitionId: 72a0fb3e-53ec-44f2-9983-2f272aca3e38, ReplicaId: 130743746195428808
ReplicaRole         : Primary
NodeName            : Node.3
ReplicaStatus       : Ready
LastInBuildDuration : 00:00:01
HealthState         : Warning

PS C:\> Get-ServiceFabricReplicaHealth 72a0fb3e-53ec-44f2-9983-2f272aca3e38 130743746195428808

PartitionId           : 72a0fb3e-53ec-44f2-9983-2f272aca3e38
ReplicaId             : 130743746195428808
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.RAP', Property='ServiceOpenOperationDuration', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130743756170185892
                        SentAt                : 4/24/2015 7:00:17 PM
                        ReceivedAt            : 4/24/2015 7:00:33 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 7:00:33 PM

                        SourceId              : System.RAP
                        Property              : ServiceOpenOperationDuration
                        HealthState           : Warning
                        SequenceNumber        : 130743756399407044
                        SentAt                : 4/24/2015 7:00:39 PM
                        ReceivedAt            : 4/24/2015 7:00:59 PM
                        TTL                   : Infinite
                        Description           : Start Time (UTC): 2015-04-24 19:00:17.019
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/24/2015 7:00:59 PM
```

Quando iniciar a aplicação com problemas em depurador, as janelas de eventos de diagnóstico mostram a excepção iniciada a partir do RunAsync:

![Visuais Studio 2015 eventos de diagnóstico: falha RunAsync no ferro: / HelloWorldStatefulApplication.][1]

Visuais Studio 2015 eventos de diagnóstico: RunAsync falha na **ferro: / HelloWorldStatefulApplication**.

[1]: ./media/service-fabric-understand-and-troubleshoot-with-system-health-reports/servicefabric-health-vs-runasync-exception.png


### <a name="replication-queue-full"></a>Fila de replicação completa
**System.Replicator** relatórios um aviso se a fila de replicação está cheio. Sobre a página principal, normalmente, isto acontece porque uma ou mais réplicas secundárias são lentas reconhecermos operações. No secundário, normalmente, isto acontece quando o serviço está lento aplicar as operações. O aviso está desmarcado quando já não se encontra completa fila de espera.

- **SourceId**: System.Replicator
- **Propriedade**: **PrimaryReplicationQueueStatus** ou **SecondaryReplicationQueueStatus**, consoante a função de réplica

### <a name="slow-naming-operations"></a>Operações de nomenclatura lentas

**System.NamingService** relatórios do Estado de funcionamento da respectiva réplica principal quando uma operação de nomenclatura demora mais de aceitável. Exemplos de operações de nomenclatura são [CreateServiceAsync](https://msdn.microsoft.com/library/azure/mt124028.aspx) ou [DeleteServiceAsync](https://msdn.microsoft.com/library/azure/mt124029.aspx). Métodos de mais podem ser encontrados em FabricClient, por exemplo em [métodos de gestão de serviço](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.servicemanagementclient.aspx) ou [métodos de gestão de propriedade](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.propertymanagementclient.aspx).

> [AZURE.NOTE] O serviço de nomenclatura resolve nomes de serviço para uma localização no cluster e permite aos utilizadores gerir propriedades e nomes de serviço. É um ferro de serviço a partições persistentes serviço. Uma das partições representa o proprietário de certificação que contém metadados sobre todos os nomes de serviço ferro e serviços. Os nomes de serviço ferro são mapeados para a partições diferentes, denominadas partições proprietário de nomes, pelo que o serviço é extensible. Leia mais sobre o [serviço de atribuição de nomes](service-fabric-architecture.md).

Quando uma operação de nomenclatura demora mais tempo do que o esperado, a operação é sinalizada com um relatório de aviso sobre a *réplica principal da nomenclatura serviço partição que serve a operação*. Se a operação for concluída com êxito, o aviso está desmarcado. Se a operação de completar com um erro, o relatório de estado de funcionamento inclui detalhes sobre o erro.

- **SourceId**: System.NamingService
- **Propriedade**: começa com prefixo **Duration_** e identifica a operação lenta e o nome do hardware de serviço no qual a operação é aplicada. Por exemplo, se criar serviço na ferro nome: / MyApp/Omeuserviço demora demasiado tempo, a propriedade é Duration_AOCreateService.fabric:/MyApp/MyService. TO aponta para a função da partição de nomenclatura para este nome e a operação.
- **Passos seguintes**: verificação porque é que a operação de nomenclatura irá falhar. Cada operação pode ter causas raiz diferente. Por exemplo, elimine serviço poderá estar bloqueado num nó porque o anfitrião da aplicação mantém a falhar num nó devido a um erro de utilizador no código do serviço.

O exemplo seguinte mostra uma operação de serviço de criar. A operação demorou mais do que a duração configurada. TO tentativas e envia trabalho para não. Não foi concluída a operação última com tempo limite. Neste caso, a mesma réplica é principal para o to e funções.

```powershell
PartitionId           : 00000000-0000-0000-0000-000000001000
ReplicaId             : 131064359253133577
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.NamingService', Property='Duration_AOCreateService.fabric:/MyApp/MyService', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131064359308715535
                        SentAt                : 4/29/2016 8:38:50 PM
                        ReceivedAt            : 4/29/2016 8:39:08 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 4/29/2016 8:39:08 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_AOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064359526778775
                        SentAt                : 4/29/2016 8:39:12 PM
                        ReceivedAt            : 4/29/2016 8:39:38 PM
                        TTL                   : 00:05:00
                        Description           : The AOCreateService started at 2016-04-29 20:39:08.677 is taking longer than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM

                        SourceId              : System.NamingService
                        Property              : Duration_NOCreateService.fabric:/MyApp/MyService
                        HealthState           : Warning
                        SequenceNumber        : 131064360657607311
                        SentAt                : 4/29/2016 8:41:05 PM
                        ReceivedAt            : 4/29/2016 8:41:08 PM
                        TTL                   : 00:00:15
                        Description           : The NOCreateService started at 2016-04-29 20:39:08.689 completed with FABRIC_E_TIMEOUT in more than 30.000.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Error->Warning = 4/29/2016 8:39:38 PM, LastOk = 1/1/0001 12:00:00 AM
```

## <a name="deployedapplication-system-health-reports"></a>Relatórios de estado de funcionamento do sistema DeployedApplication
**System.Hosting** é a autoridade em entidades implementadas.

### <a name="activation"></a>Ativação
System.Hosting relatórios como OK quando uma aplicação já foi ativada com êxito no nó. Caso contrário, relate um erro.

- **SourceId**: System.Hosting
- **Propriedade**: ativação, incluindo a versão de implementação
- **Passos seguintes**: se a aplicação está danificada, investigar por que motivo a ativação falha.

O exemplo seguinte mostra activação bem sucedida:

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -NodeName Node.1 -ApplicationName fabric:/WordCount

ApplicationName                    : fabric:/WordCount
NodeName                           : Node.1
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : Node.1
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 130743727751144415
                                     SentAt                : 4/24/2015 6:12:55 PM
                                     ReceivedAt            : 4/24/2015 6:13:03 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Transferir
**System.Hosting** relatórios um erro se a transferência do pacote de aplicação falhar.

- **SourceId**: System.Hosting
- **Propriedade**: * *Transferir:*RolloutVersion***
- **Passos seguintes**: investigar a razão pela qual a transferência falhou no nó.

## <a name="deployedservicepackage-system-health-reports"></a>Relatórios de estado de funcionamento do sistema DeployedServicePackage
**System.Hosting** é a autoridade em entidades implementadas.

### <a name="service-package-activation"></a>Ativação do pacote de serviço
System.Hosting relatórios como OK se a ativação do pacote de serviço no nó é efetuada com êxito. Caso contrário, relate um erro.

- **SourceId**: System.Hosting
- **Propriedade**: ativação
- **Passos seguintes**: investigar por que motivo a ativação falha.

### <a name="code-package-activation"></a>Ativação do pacote de código
**System.Hosting** relatórios como OK para cada pacote de código se a ativação é efetuada com êxito. Se a ativação falhar, relatórios um aviso tal como está configurado. Se **CodePackage** falha ativar ou termina com um erro maior que o configurado **CodePackageHealthErrorThreshold**, aloja relatórios um erro. Se um pacote de serviço contiver vários pacotes de código, é gerado um relatório de ativação para cada um deles.

- **SourceId**: System.Hosting
- **Propriedade**: utiliza o prefixo **CodePackageActivation** e contém o nome do pacote código e o ponto de entrada como * *CodePackageActivation:*CodePackageName*:*SetupEntryPoint/ponto de entrada* ** (por exemplo, **CodePackageActivation:Code:SetupEntryPoint**)

### <a name="service-type-registration"></a>Registo de tipo de serviço
**System.Hosting** relatórios como OK se o tipo de serviço foi registado com êxito. Comunica um erro se o registo não foi feito de hora (tal como está configurado utilizando **ServiceTypeRegistrationTimeout**). Se o tipo de serviço não está registado a partir do nó, é porque o tempo de execução foi fechado. Que aloja relatórios um aviso.

- **SourceId**: System.Hosting
- **Propriedade**: utiliza o prefixo **ServiceTypeRegistration** e que contém o nome do tipo de serviço (por exemplo, **ServiceTypeRegistration:FileStoreServiceType**)

O exemplo seguinte mostra um pacote de serviço implementado Saudável:

```powershell
PS C:\> Get-ServiceFabricDeployedServicePackageHealth -NodeName Node.1 -ApplicationName fabric:/WordCount -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : Node.1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 130743727751456915
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 130743727751613185
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 130743727753644473
                        SentAt                : 4/24/2015 6:12:55 PM
                        ReceivedAt            : 4/24/2015 6:13:03 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/24/2015 6:13:03 PM
```

### <a name="download"></a>Transferir
**System.Hosting** relatórios um erro se a transferência do pacote de serviço falhar.

- **SourceId**: System.Hosting
- **Propriedade**: * *Transferir:*RolloutVersion***
- **Passos seguintes**: investigar a razão pela qual a transferência falhou no nó.

### <a name="upgrade-validation"></a>Validação de atualização
**System.Hosting** relatórios um erro se falhas de validação durante a atualização ou se a atualização falhar no nó.

- **SourceId**: System.Hosting
- **Propriedade**: utiliza o prefixo **FabricUpgradeValidation** e que contém a versão de actualização
- **Descrição**: aponta para o erro encontrado

## <a name="next-steps"></a>Próximos passos
[Ver relatórios de estado de funcionamento do serviço ferro](service-fabric-view-entities-aggregated-health.md)

[Como um relatório e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de serviço ferro](service-fabric-application-upgrade.md)
