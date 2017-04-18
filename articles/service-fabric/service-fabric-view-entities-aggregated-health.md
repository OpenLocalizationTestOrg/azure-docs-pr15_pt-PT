<properties
   pageTitle="Como ver entidades de Azure Service ferro agregado estado de funcionamento do | Microsoft Azure"
   description="Descreve como da consulta, ver e avaliar agregado do Estado de funcionamento dos entidades Azure Service ferro, através de consultas de estado de funcionamento e consultas gerais."
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

# <a name="view-service-fabric-health-reports"></a>Ver relatórios de estado de funcionamento do serviço ferro
Azure Service ferro apresenta um [modelo de estado de funcionamento](service-fabric-health-introduction.md) que compreende entidades de estado de funcionamento no qual é o sistema componentes e fiscais podem comunicar condições locais que está a monitorizar. O [Estado de funcionamento do armazenar](service-fabric-health-introduction.md#health-store) agrega todos os dados de estado de funcionamento para determinar se entidades são saudáveis.

Terminar a caixa de, cluster é povoado com relatórios de estado de funcionamento que foi enviados pelos componentes do sistema. Leia mais em [relatórios de estado de funcionamento do sistema utilizar para resolver problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md).

Serviço ferro fornece várias formas para obter o estado de funcionamento agregado das entidades:

- [Serviço ferro Explorer](service-fabric-visualizing-your-cluster.md) ou outras ferramentas de visualização

- Consultas de estado de funcionamento (através do PowerShell, de API ou resto)

- Geral consultas remetente ou uma lista de entidades que têm o estado de funcionamento do que uma das propriedades (através do PowerShell, de API ou resto)

Para demonstrar estas opções, vamos utilizar um cluster local com cinco nós. Junto ao **ferro: / sistema** aplicação (que existe terminar a caixa), são implementadas algumas das outras aplicações. Uma destas aplicações é **ferro: / WordCount**. Esta aplicação contém um serviço com estado configurado com sete réplicas. Uma vez que existem nós apenas cinco, os componentes do sistema mostram um aviso de que a partição for inferior a contagem de destino.

```xml
<Service Name="WordCountService">
    <StatefulService ServiceTypeName="WordCountServiceType" TargetReplicaSetSize="7" MinReplicaSetSize="2">
      <UniformInt64Partition PartitionCount="1" LowKey="1" HighKey="26" />
    </StatefulService>
</Service>
```

## <a name="health-in-service-fabric-explorer"></a>Estado de funcionamento do serviço ferro Explorer
Serviço ferro Explorer disponibiliza uma vista visual do cluster. A imagem abaixo, pode ver que:

- A aplicação **ferro: / WordCount** fique a vermelho (no erro) porque tem um evento de erro comunicado por **MyWatchdog** para a propriedade **disponibilidade**.

- Um dos seus serviços, **ferro: / WordCount/WordCountService** é amarelo (na aviso). Uma vez que o serviço está configurado com sete réplicas, elas não é possível todos ser colocadas, tal como existem nós apenas cinco. Apesar de não é mostrado aqui, a partição de serviço está amarela devido ao relatório do sistema. A amarela partição accionadores o serviço amarelo.

- Cluster está vermelho devido a aplicação vermelha.

A avaliação utiliza políticas predefinidas a partir da manifesto cluster e manifesto da aplicação. São as políticas de estritamente e não tolerar qualquer falha.

Vista do cluster com o Explorador de ferro de serviço:

![Vista do cluster com o Explorador de ferro de serviço.][1]

[1]: ./media/service-fabric-view-entities-aggregated-health/servicefabric-explorer-cluster-health.png


> [AZURE.NOTE] Leia mais sobre o [Serviço ferro Explorer](service-fabric-visualizing-your-cluster.md).

## <a name="health-queries"></a>Consultas de estado de funcionamento
Serviço ferro expõe consultas de estado de funcionamento para cada um dos [tipos de entidade](service-fabric-health-introduction.md#health-entities-and-hierarchy)suportados. Estes podem ser acedidos através da API (os métodos encontrará no **FabricClient.HealthManager**), os cmdlets do PowerShell e restantes. Estas consultas devolvem informações de estado de funcionamento concluída sobre a entidade: o estado de funcionamento agregado, eventos de estado de funcionamento de entidade, Estados-membros subordinado do Estado de funcionamento (quando aplicável) e avaliações danificadas quando a entidade não está em bom estada.

> [AZURE.NOTE] Uma entidade de estado de funcionamento é devolvida quando estiver completamente preenchido na loja do Estado de funcionamento. A entidade tem de estar activo (não eliminados) e ter um relatório de sistema. Respectivas entidades principal da cadeia de hierarquia também tem de ter os relatórios de sistema. Se alguma destas condições não for cumprida, as consultas de estado de funcionamento devolvem uma exceção que mostra por que motivo não é devolvida a entidade.

As consultas de estado de funcionamento tem de passar o identificador de entidade, que depende do tipo de entidade. As consultas de parâmetros de política de estado de funcionamento opcional aceitam. Se não políticas de estado de funcionamento forem especificadas, são utilizadas as [políticas de estado de funcionamento](service-fabric-health-introduction.md#health-policies) do manifesto cluster ou aplicação para avaliação. As consultas também aceitam filtros para devolver apenas crianças parciais ou eventos – aqueles que respeitam filtros especificados.

> [AZURE.NOTE] Os filtros de saída são aplicados do lado do servidor, para que o tamanho de resposta da mensagem é reduzido. Recomendamos que pode utiliza os filtros de saída para limitar os dados devolvidos, em vez de aplica filtros do lado do cliente.

Estado de funcionamento de uma entidade contém:

- Agregado estado de funcionamento da entidade. Calculado pelo arquivo de estado de funcionamento com base em relatórios de estado de funcionamento de entidade, Estados-membros subordinado do Estado de funcionamento (quando aplicável) e políticas de estado de funcionamento. Leia mais sobre a [avaliação de estado de funcionamento de entidade](service-fabric-health-introduction.md#entity-health-evaluation).  

- Os eventos de estado de funcionamento na entidade.

- A colecção de Estados de estado de funcionamento de todos os elementos subordinados para as entidades que podem ter subordinados. Os Estados de estado de funcionamento contêm identificadores entidade e o estado de funcionamento agregado. Para obter o estado de funcionamento completo para subordinado, o estado de funcionamento da consulta de chamadas para o tipo de entidade subordinado e passar o identificador de subordinados.

- Avaliações danificadas que aponte para o relatório que o acionou o estado da entidade, se a entidade não está em bom estada.

## <a name="get-cluster-health"></a>Obter o estado de funcionamento do cluster
Devolve o estado de funcionamento da entidade cluster e contém os Estados de estado de funcionamento de aplicações e nós (crianças do cluster). Introdução:

- [Opcional] A política de estado de funcionamento de cluster utilizada para avaliar os nós e os eventos de cluster.

- [Opcional] O estado de funcionamento política mapa de aplicação, com as políticas de estado de funcionamento utilizadas para substituir as políticas de manifesto de aplicação.

- [Opcional] Filtros para eventos, nós e aplicações que especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos nós de aplicações e são utilizadas para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento do cluster, crie uma `FabricClient` e chamar o método de [GetClusterHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthasync.aspx) no respetivo **HealthManager**.

A chamada seguinte obtém o estado de funcionamento do cluster:

```csharp
ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync();
```

O código seguinte obtém o estado de funcionamento do cluster utilizando uma política de estado de funcionamento de cluster personalizado e filtros para nós e aplicações. Cria [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthquerydescription.aspx), que contém as informações de entrada.

```csharp
var policy = new ClusterHealthPolicy()
{
    MaxPercentUnhealthyNodes = 20
};
var nodesFilter = new NodeHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error | HealthStateFilter.Warning
};
var applicationsFilter = new ApplicationHealthStatesFilter()
{
    HealthStateFilterValue = HealthStateFilter.Error
};
var queryDescription = new ClusterHealthQueryDescription()
{
    HealthPolicy = policy,
    ApplicationsFilter = applicationsFilter,
    NodesFilter = nodesFilter,
};

ClusterHealth clusterHealth = await fabricClient.HealthManager.GetClusterHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do cluster é [Get-ServiceFabricClusterHealth](https://msdn.microsoft.com/library/mt125850.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .

O estado do cluster é cinco nós, a aplicação do sistema e ferro: / WordCount configuradas conforme descrito.

O cmdlet seguinte obtém o estado de funcionamento do cluster utilizando políticas de estado de funcionamento predefinidas. O estado de funcionamento agregado é no aviso, porque o ferro: / WordCount aplicação estiver em aviso. Tenha em atenção como avaliações danificadas fornecem detalhes as condições que o acionou o estado de funcionamento agregado.

```xml
PS C:\> Get-ServiceFabricClusterHealth

AggregatedHealthState   : Warning
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Warning'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=false.


NodeHealthStates        :
                          NodeName              : _Node_2
                          AggregatedHealthState : Ok

                          NodeName              : _Node_0
                          AggregatedHealthState : Ok

                          NodeName              : _Node_1
                          AggregatedHealthState : Ok

                          NodeName              : _Node_3
                          AggregatedHealthState : Ok

                          NodeName              : _Node_4
                          AggregatedHealthState : Ok

ApplicationHealthStates :
                          ApplicationName       : fabric:/System
                          AggregatedHealthState : Ok

                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Warning

HealthEvents            : None
```

O seguinte cmdlet do PowerShell obtém o estado de funcionamento do cluster utilizando uma política de aplicação personalizada. Filtrar resultados para obter apenas erro ou aplicações de aviso e nós. Como resultado, sem nós são devolvidos, tal como estão todas as saudáveis. Apenas o ferro: / WordCount aplicação respeita o filtro de aplicações. Uma vez que a política personalizada Especifica a ter em consideração avisos como erros para o ferro: / WordCount aplicação, a aplicação é avaliada como erro e, pelo que é o cluster.

```powershell
PS c:\> $appHealthPolicy = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicy
$appHealthPolicy.ConsiderWarningAsError = $true
$appHealthPolicyMap = New-Object -TypeName System.Fabric.Health.ApplicationHealthPolicyMap
$appUri1 = New-Object -TypeName System.Uri -ArgumentList "fabric:/WordCount"
$appHealthPolicyMap.Add($appUri1, $appHealthPolicy)
Get-ServiceFabricClusterHealth -ApplicationHealthPolicyMap $appHealthPolicyMap -ApplicationsFilter "Warning,Error" -NodesFilter "Warning,Error"


AggregatedHealthState   : Error
UnhealthyEvaluations    :
                          Unhealthy applications: 100% (1/1), MaxPercentUnhealthyApplications=0%.

                          Unhealthy application: ApplicationName='fabric:/WordCount', AggregatedHealthState='Error'.

                              Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                              Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                  Unhealthy event: SourceId='System.PLB',
                          Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                          ConsiderWarningAsError=true.


NodeHealthStates        : None
ApplicationHealthStates :
                          ApplicationName       : fabric:/WordCount
                          AggregatedHealthState : Error

HealthEvents            : None

```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento do cluster com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707669.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707696.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-node-health"></a>Obter o estado de funcionamento do nó
Devolve o estado de funcionamento de uma entidade nó e contém os eventos de estado de funcionamento comunicados no nó. Introdução:

- [Obrigatório] O nome do nó que identifica o nó.

- [Opcional] As definições de política de estado de funcionamento cluster utilizadas para avaliar o estado de funcionamento.

- [Opcional] Filtros para eventos que especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento do nó através da API, criar uma `FabricClient` e chamar o método de [GetNodeHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getnodehealthasync.aspx) no respetivo HealthManager.

O código seguinte obtém o estado de funcionamento de nó para o nome de nó especificado:

```csharp
NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(nodeName);
```

O código seguinte obtém o estado de funcionamento de nó o nome do nó especificado e atravessa [NodeHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.nodehealthquerydescription.aspx)no filtro de eventos e política personalizada:

```csharp
var queryDescription = new NodeHealthQueryDescription(nodeName)
{
    HealthPolicy = new ClusterHealthPolicy() {  ConsiderWarningAsError = true },
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.Warning },
};

NodeHealth nodeHealth = await fabricClient.HealthManager.GetNodeHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento de nó é [Get-ServiceFabricNodeHealth](https://msdn.microsoft.com/library/mt125937.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .
O cmdlet seguinte obtém o estado de funcionamento de nó utilizando políticas de estado de funcionamento predefinidas:

```powershell
PS C:\> Get-ServiceFabricNodeHealth _Node_1


NodeName              : _Node_1
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 6
                        SentAt                : 3/22/2016 7:47:56 PM
                        ReceivedAt            : 3/22/2016 7:48:19 PM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:48:19 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O cmdlet seguinte obtém o estado de funcionamento de todos os nós no cluster:

```powershell
PS C:\> Get-ServiceFabricNode | Get-ServiceFabricNodeHealth | select NodeName, AggregatedHealthState | ft -AutoSize

NodeName AggregatedHealthState
-------- ---------------------
_Node_2                     Ok
_Node_0                     Ok
_Node_1                     Ok
_Node_3                     Ok
_Node_4                     Ok
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento do nó com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707650.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707665.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-application-health"></a>Obter o estado de funcionamento da aplicação
Devolve o estado de funcionamento de uma entidade de aplicação. Contém os Estados de estado de funcionamento da aplicação implementada e subordinados de serviço. Introdução:

- [Obrigatório] O nome da aplicação (URI) que identifica a aplicação.

- [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir as políticas de manifesto de aplicação.

- [Opcional] Filtros para eventos, serviços e aplicações implementadas que especifiquem quais os movimentos de interesse e devem ser devolvidos no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos, serviços e aplicações implementadas são utilizadas para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento da aplicação, crie uma `FabricClient` e chamar o método de [GetApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getapplicationhealthasync.aspx) no respetivo HealthManager.

O código seguinte obtém o estado de funcionamento de aplicação para o nome da aplicação especificada (URI):

```csharp
ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(applicationName);
```

O código seguinte obtém o estado de funcionamento de aplicação para o nome da aplicação especificada (URI), com os filtros e políticas personalizadas especificadas através do [ApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.applicationhealthquerydescription.aspx).

```csharp
HealthStateFilter warningAndErrors = HealthStateFilter.Error | HealthStateFilter.Warning;
var serviceTypePolicy = new ServiceTypeHealthPolicy()
{
    MaxPercentUnhealthyPartitionsPerService = 0,
    MaxPercentUnhealthyReplicasPerPartition = 5,
    MaxPercentUnhealthyServices = 0,
};
var policy = new ApplicationHealthPolicy()
{
    ConsiderWarningAsError = false,
    DefaultServiceTypeHealthPolicy = serviceTypePolicy,
    MaxPercentUnhealthyDeployedApplications = 0,
};

var queryDescription = new ApplicationHealthQueryDescription(applicationName)
{
    HealthPolicy = policy,
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = warningAndErrors },
    ServicesFilter = new ServiceHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
    DeployedApplicationsFilter = new DeployedApplicationHealthStatesFilter() { HealthStateFilterValue = warningAndErrors },
};

ApplicationHealth applicationHealth = await fabricClient.HealthManager.GetApplicationHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento da aplicação é [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet seguinte devolve o estado de funcionamento da **ferro: / WordCount** aplicação:

```powershell
PS c:\>
PS C:\WINDOWS\system32>  Get-ServiceFabricApplicationHealth fabric:/WordCount


ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Warning
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Warning'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=false.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCountWebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_0
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_3
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : _Node_1
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 360
                                  SentAt                : 3/22/2016 7:56:53 PM
                                  ReceivedAt            : 3/22/2016 7:56:53 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 7:56:53 PM, LastWarning = 1/1/0001 12:00:00 AM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Ok
                                  SequenceNumber        : 131031545225930951
                                  SentAt                : 3/22/2016 9:08:42 PM
                                  ReceivedAt            : 3/22/2016 9:08:42 PM
                                  TTL                   : Infinite
                                  Description           : Availability checked successfully, latency ok
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : Error->Ok = 3/22/2016 8:55:39 PM, LastWarning = 1/1/0001 12:00:00 AM
```

O seguinte cmdlet do PowerShell transmite no políticas personalizadas. Também filtra crianças e eventos.

```powershell
PS C:\> Get-ServiceFabricApplicationHealth -ApplicationName fabric:/WordCount -ConsiderWarningAsError $true -ServicesFilter Error -EventsFilter Error -DeployedApplicationsFilter Error

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy event: SourceId='System.PLB',
                                  Property='ServiceReplicaUnplacedHealth_Secondary_a1f83a35-d6bf-4d39-b90d-28d15f39599b', HealthState='Warning',
                                  ConsiderWarningAsError=true.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCountService
                                  AggregatedHealthState : Error

DeployedApplicationHealthStates : None
HealthEvents                    : None
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento de aplicação com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707681.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707643.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-service-health"></a>Obter o estado de funcionamento do serviço
Devolve o estado de funcionamento de uma entidade de serviço. Contém os Estados de estado de funcionamento partição. Introdução:

- [Obrigatório] O nome do serviço (URI) que identifica o serviço.

- [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir a política de manifesto de aplicação.

- [Opcional] Filtros para eventos e a partições especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos e a partições são utilizadas para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento do serviço através da API, criar uma `FabricClient` e chamar o método de [GetServiceHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getservicehealthasync.aspx) no respetivo HealthManager.

O exemplo seguinte obtém o estado de funcionamento de um serviço com o nome de serviço especificado (URI):

```charp
ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(serviceName);
```

O código seguinte obtém o estado de funcionamento do serviço o nome do serviço especificada (URI), especificando filtros e política personalizada através do [ServiceHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.servicehealthquerydescription.aspx):

```csharp
var queryDescription = new ServiceHealthQueryDescription(serviceName)
{
    EventsFilter = new HealthEventsFilter() { HealthStateFilterValue = HealthStateFilter.All },
    PartitionsFilter = new PartitionHealthStatesFilter() { HealthStateFilterValue = HealthStateFilter.Error },
};

ServiceHealth serviceHealth = await fabricClient.HealthManager.GetServiceHealthAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do serviço é [Get-ServiceFabricServiceHealth](https://msdn.microsoft.com/library/mt125984.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet seguinte obtém o estado de funcionamento do serviço utilizando políticas de estado de funcionamento predefinidas:

```powershell
PS C:\> Get-ServiceFabricServiceHealth -ServiceName fabric:/WordCount/WordCountService


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
                        SequenceNumber        : 131031547693687021
                        SentAt                : 3/22/2016 9:12:49 PM
                        ReceivedAt            : 3/22/2016 9:12:49 PM
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
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento do serviço com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707609.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707646.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-partition-health"></a>Obter o estado de funcionamento do partição
Devolve o estado de funcionamento de uma entidade partição. Contém os Estados de estado de funcionamento de réplica. Introdução:

- [Obrigatório] A partição ID (GUID) que identifica a partição.

- [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir a política de manifesto de aplicação.

- [Opcional] Filtros para eventos e réplicas que especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos e réplicas são utilizadas para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento do partição através da API, criar uma `FabricClient` e chamar o método de [GetPartitionHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getpartitionhealthasync.aspx) no respetivo HealthManager. Para especificar os parâmetros opcionais, crie [PartitionHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.partitionhealthquerydescription.aspx).

```csharp
PartitionHealth partitionHealth = await fabricClient.HealthManager.GetPartitionHealthAsync(partitionId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento partição é [Get-ServiceFabricPartitionHealth](https://msdn.microsoft.com/library/mt125869.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet seguinte obtém o estado de funcionamento para todas as partições do **ferro: / WordCount/WordCountService** serviço:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricPartitionHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.FM', Property='State', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 131031502143040223
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844060
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844059
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844061
                        AggregatedHealthState : Ok

                        ReplicaId             : 131031502346844058
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Warning
                        SequenceNumber        : 76
                        SentAt                : 3/22/2016 7:57:26 PM
                        ReceivedAt            : 3/22/2016 7:57:48 PM
                        TTL                   : Infinite
                        Description           : Partition is below target replica or instance count.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Warning = 3/22/2016 7:57:48 PM, LastOk = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento do partição com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707683.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707680.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-replica-health"></a>Obter o estado de funcionamento do réplica
Devolve o estado de funcionamento de uma réplica com estado do serviço ou uma instância do Estado de serviço. Introdução:

- [Obrigatório] O ID de ID (GUID) e réplica da partição que identifica a réplica.

- [Opcional] Os parâmetros de política de estado de funcionamento aplicação utilizados para substituir as políticas de manifesto de aplicação.

- [Opcional] Filtros para eventos que especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento de réplica através da API, criar uma `FabricClient` e chamar o método de [GetReplicaHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getreplicahealthasync.aspx) no respetivo HealthManager. Para especificar parâmetros avançados, utilize [ReplicaHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.replicahealthquerydescription.aspx).

```csharp
ReplicaHealth replicaHealth = await fabricClient.HealthManager.GetReplicaHealthAsync(partitionId, replicaId);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento de réplica é [Get-ServiceFabricReplicaHealth](https://msdn.microsoft.com/library/mt125808.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .

O cmdlet seguinte obtém o estado de funcionamento da réplica principal para todas as partições do serviço:

```powershell
PS C:\> Get-ServiceFabricPartition fabric:/WordCount/WordCountService | Get-ServiceFabricReplica | where {$_.ReplicaRole -eq "Primary"} | Get-ServiceFabricReplicaHealth


PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
ReplicaId             : 131031502143040223
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 131031502145556748
                        SentAt                : 3/22/2016 7:56:54 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento de réplica com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707673.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707641.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-deployed-application-health"></a>Obter o estado de funcionamento da aplicação implementada
Devolve o estado de funcionamento de uma aplicação do implementado numa entidade nó. Contém os Estados de estado de funcionamento do serviço implementado pacote. Introdução:

- [Obrigatório] O nome da aplicação (URI) e o nó nome (cadeia) que identificam a aplicação implementada.

- [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir as políticas de manifesto de aplicação.

- [Opcional] Filtros para eventos e serviço implementado pacotes que especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos e pacotes de serviço implementado são utilizadas para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento de uma aplicação do implementado num nó através da API, criar uma `FabricClient` e chamar o método de [GetDeployedApplicationHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedapplicationhealthasync.aspx) no respetivo HealthManager. Para especificar os parâmetros opcionais, utilize [DeployedApplicationHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedapplicationhealthquerydescription.aspx).

```csharp
DeployedApplicationHealth health = await fabricClient.HealthManager.GetDeployedApplicationHealthAsync(
    new DeployedApplicationHealthQueryDescription(applicationName, nodeName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento da aplicação implementada é [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) . Para saber onde é implementada uma aplicação, execute [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e observe as crianças a aplicação implementada.

O cmdlet seguinte obtém o estado de funcionamento da **ferro: / WordCount** aplicação implementada no **_Node_2**.

```powershell
PS C:\> Get-ServiceFabricDeployedApplicationHealth -ApplicationName fabric:/WordCount -NodeName _Node_2


ApplicationName                    : fabric:/WordCount
NodeName                           : _Node_2
AggregatedHealthState              : Ok
DeployedServicePackageHealthStates :
                                     ServiceManifestName   : WordCountServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

                                     ServiceManifestName   : WordCountWebServicePkg
                                     NodeName              : _Node_2
                                     AggregatedHealthState : Ok

HealthEvents                       :
                                     SourceId              : System.Hosting
                                     Property              : Activation
                                     HealthState           : Ok
                                     SequenceNumber        : 131031502143710698
                                     SentAt                : 3/22/2016 7:56:54 PM
                                     ReceivedAt            : 3/22/2016 7:57:12 PM
                                     TTL                   : Infinite
                                     Description           : The application was activated successfully.
                                     RemoveWhenExpired     : False
                                     IsExpired             : False
                                     Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento de aplicação implementada com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707644.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707688.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="get-deployed-service-package-health"></a>Obter o estado de funcionamento do serviço implementado pacote
Devolve o estado de funcionamento de uma entidade de pacote implementado serviço. Introdução:

- [Obrigatório] O nome da aplicação (URI), o nome do nó (cadeia) e o serviço de manifesto nome (cadeia) que identifique o pacote de serviço implementada.

- [Opcional] A política de estado de funcionamento da aplicação utilizada para substituir a política de manifesto de aplicação.

- [Opcional] Filtros para eventos que especificar quais as entradas de interesse e devem ser devolvidas no resultado (por exemplo, apenas, erros ou tanto avisos e erros). Todos os eventos são utilizados para avaliar o estado de funcionamento entidade agregado, independentemente do filtro.

### <a name="api"></a>API
Para obter o estado de funcionamento de um pacote de serviço implementada através da API, criar uma `FabricClient` e chamar o método de [GetDeployedServicePackageHealthAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getdeployedservicepackagehealthasync.aspx) no respetivo HealthManager. Para especificar os parâmetros opcionais, utilize [DeployedServicePackageHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.deployedservicepackagehealthquerydescription.aspx).

```csharp
DeployedServicePackageHealth health = await fabricClient.HealthManager.GetDeployedServicePackageHealthAsync(
    new DeployedServicePackageHealthQueryDescription(applicationName, nodeName, serviceManifestName));
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do serviço implementado pacote é [Get-ServiceFabricDeployedServicePackageHealth](https://msdn.microsoft.com/library/mt163525.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) . Para ver onde uma aplicação é implementada, execute [Get-ServiceFabricApplicationHealth](https://msdn.microsoft.com/library/mt125976.aspx) e observe as aplicações implementadas. Para ver qual o pacotes são numa aplicação do serviço, veja subordinados pacote serviço implementado na saída [Get-ServiceFabricDeployedApplicationHealth](https://msdn.microsoft.com/library/mt163523.aspx) .

O cmdlet seguinte obtém o estado de funcionamento do pacote de serviço **WordCountServicePkg** do **ferro: / WordCount** aplicação implementada no **_Node_2**. A entidade tem **System.Hosting** relatórios para o pacote de serviço efetuada com êxito e ativação do ponto de entrada e registo de tipo de serviço com êxito.

```powershell
PS C:\> Get-ServiceFabricDeployedApplication -ApplicationName fabric:/WordCount -NodeName _Node_2 | Get-ServiceFabricDeployedServicePackageHealth -ServiceManifestName WordCountServicePkg


ApplicationName       : fabric:/WordCount
ServiceManifestName   : WordCountServicePkg
NodeName              : _Node_2
AggregatedHealthState : Ok
HealthEvents          :
                        SourceId              : System.Hosting
                        Property              : Activation
                        HealthState           : Ok
                        SequenceNumber        : 131031502301306211
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServicePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : CodePackageActivation:Code:EntryPoint
                        HealthState           : Ok
                        SequenceNumber        : 131031502301568982
                        SentAt                : 3/22/2016 7:57:10 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The CodePackage was activated successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM

                        SourceId              : System.Hosting
                        Property              : ServiceTypeRegistration:WordCountServiceType
                        HealthState           : Ok
                        SequenceNumber        : 131031502314788519
                        SentAt                : 3/22/2016 7:57:11 PM
                        ReceivedAt            : 3/22/2016 7:57:12 PM
                        TTL                   : Infinite
                        Description           : The ServiceType was registered successfully.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : Error->Ok = 3/22/2016 7:57:12 PM, LastWarning = 1/1/0001 12:00:00 AM
```

### <a name="rest"></a>RESTO
Pode obter o estado de funcionamento do serviço implementado pacote com um [pedido de obter](https://msdn.microsoft.com/library/azure/dn707677.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/dn707689.aspx) que inclui políticas de estado de funcionamento descritas no corpo.

## <a name="health-chunk-queries"></a>Consultas de segmento de estado de funcionamento
As consultas de segmento de estado de funcionamento podem devolver crianças a cluster com múltiplos níveis (forma recursiva), por filtros de entrada. Suporta filtros avançados que permitem muita flexibilidade expressar quais crianças específicas a ser devolvido identificadas por sua Identificador exclusivo ou outros identificador de grupo e/ou o estado de funcionamento. Por predefinição, não crianças estão incluídas, por oposição a comandos de estado de funcionamento que incluem sempre subordinados de primeiro nível.

As [consultas de estado de funcionamento](service-fabric-view-entities-aggregated-health.md#health-queries) devolvem apenas de primeiro nível subordinados da entidade especificada por filtros necessários. Para obter os elementos subordinados descendentes, tem de chamar APIs do Estado de funcionamento adicional para cada entidade de interesse. Da mesma forma, para obter o estado de funcionamento de entidades específicas, tem de chamar um Estado de funcionamento API para cada entidade pretendida. A consulta de segmento avançada de filtragem permite-lhe pedir vários itens de interesse numa consulta, minimizar o tamanho da mensagem e o número de mensagens.

O valor da consulta segmento é que pode obter estado de funcionamento para obter mais entidades cluster (potencialmente todos os cluster entidades começando na raiz necessária) numa chamada. Pode express consulta de estado de funcionamento complexa como:

- Devolver apenas as aplicações no erro e para esses aplicações para incluem todos os serviços em aviso | erro. Para os serviços devolvidos, inclua todas as partições.

- Devolve apenas o estado de funcionamento de 4 aplicações, especificado pelos seus nomes.

- Devolve apenas o estado de funcionamento das aplicações de um tipo de aplicação pretendida.

- Devolve implementadas todas as entidades num nó. Devolve todas as aplicações, todas as implementado aplicações no nó especificado e todos os pacotes de serviço implementado nesse nó.

- Devolve todas as réplicas no erro. Devolve todas as aplicações, serviços, partições e apenas réplicas no erro.

- Devolve todas as aplicações. Para um serviço especificado, inclui todas as partições.

Atualmente, a consulta de segmento de estado de funcionamento é exposta em apenas a entidade de cluster. Devolve um bloco de estado de funcionamento cluster, que contém:

- O estado de funcionamento de cluster agregado.

- Lista segmento de estado de funcionamento estado de nós respeitar os filtros de entrada.

- Lista segmento de estado de funcionamento estado das aplicações respeitar os filtros de entrada. Cada segmento de estado de funcionamento de aplicação contém uma lista de blocos com todos os serviços que respeitam os filtros de entrada e uma lista de blocos com todas as aplicações de implementado respeitar os filtros. O mesmo para os filhos dos serviços e aplicações implementadas. Desta forma, todas as entidades no cluster podem ser potencialmente devolvidas se lhe for solicitado, de uma forma hierárquica.

### <a name="cluster-health-chunk-query"></a>Consulta de segmento de estado de funcionamento de cluster
Devolve o estado de funcionamento da entidade cluster e contém os blocos de estado do Estado de funcionamento hierárquico das crianças necessários. Introdução:

- [Opcional] A política de estado de funcionamento de cluster utilizada para avaliar os nós e os eventos de cluster.

- [Opcional] O estado de funcionamento política mapa de aplicação, com as políticas de estado de funcionamento utilizadas para substituir as políticas de manifesto de aplicação.

- [Opcional] Filtros para nós e aplicações que especificar quais as entradas de interesse e devem ser devolvidas no resultado. Os filtros são específicos para um entidade grupo de entidades ou são aplicáveis a todas as entidades nesse nível. A lista de filtros pode conter um filtro geral e/ou filtros para identificadores específicos a entidades fino grão devolvidas pela consulta. Se estiver vazio, os subordinados não são devolvidos por predefinição.
Leia mais sobre os filtros no [NodeHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.nodehealthstatefilter.aspx) e [ApplicationHealthStateFilter](https://msdn.microsoft.com/library/azure/system.fabric.health.applicationhealthstatefilter.aspx). A forma aplicação filtros podem recursiva Especifique filtros avançados para crianças.

O resultado de segmento inclui subordinados que respeitam os filtros.

Atualmente, a consulta de segmento não devolve avaliações danificadas ou eventos de entidade. Que informações extra podem ser obtidas a consulta de estado de funcionamento de cluster existente.

### <a name="api"></a>API
Para obter o fragmento de estado de funcionamento de cluster, crie uma `FabricClient` e chamar o método de [GetClusterHealthChunkAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.healthclient.getclusterhealthchunkasync.aspx) no respetivo **HealthManager**. Pode passar no [ClusterHealthQueryDescription](https://msdn.microsoft.com/library/azure/system.fabric.description.clusterhealthchunkquerydescription.aspx) para descrever as políticas de estado de funcionamento e filtros avançados.

O código seguinte obtém o fragmento de estado de funcionamento de cluster com filtros avançados.

```csharp
var queryDescription = new ClusterHealthChunkQueryDescription();
queryDescription.ApplicationFilters.Add(new ApplicationHealthStateFilter()
    {
        // Return applications only if they are in error
        HealthStateFilter = HealthStateFilter.Error
    });

// Return all replicas
var wordCountServiceReplicaFilter = new ReplicaHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };

// Return all replicas and all partitions
var wordCountServicePartitionFilter = new PartitionHealthStateFilter()
    {
        HealthStateFilter = HealthStateFilter.All
    };
wordCountServicePartitionFilter.ReplicaFilters.Add(wordCountServiceReplicaFilter);

// For specific service, return all partitions and all replicas
var wordCountServiceFilter = new ServiceHealthStateFilter()
{
    ServiceNameFilter = new Uri("fabric:/WordCount/WordCountService"),
};
wordCountServiceFilter.PartitionFilters.Add(wordCountServicePartitionFilter);

// Application filter: for specific application, return no services except the ones of interest
var wordCountApplicationFilter = new ApplicationHealthStateFilter()
    {
        // Always return fabric:/WordCount application
        ApplicationNameFilter = new Uri("fabric:/WordCount"),
    };
wordCountApplicationFilter.ServiceFilters.Add(wordCountServiceFilter);

queryDescription.ApplicationFilters.Add(wordCountApplicationFilter);

var result = await fabricClient.HealthManager.GetClusterHealthChunkAsync(queryDescription);
```

### <a name="powershell"></a>PowerShell
O cmdlet para obter o estado de funcionamento do cluster é [Get-ServiceFabricClusterChunkHealth](https://msdn.microsoft.com/library/mt644772.aspx). Em primeiro lugar, ligar ao cluster utilizando o cmdlet [ServiceFabricCluster ligar](https://msdn.microsoft.com/library/mt125938.aspx) .

O código seguinte obtém nós apenas se forem erro exceto um nó específico, que sempre deve ser devolvido.

```xml
PS C:\> $errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$nodeFilter1 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{HealthStateFilter=$errorFilter}
$nodeFilter2 = New-Object System.Fabric.Health.NodeHealthStateFilter -Property @{NodeNameFilter="_Node_1";HealthStateFilter=$allFilter}
# Create node filter list that will be passed in the cmdlet
$nodeFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.NodeHealthStateFilter]
$nodeFilters.Add($nodeFilter1)
$nodeFilters.Add($nodeFilter2)

Get-ServiceFabricClusterHealthChunk -NodeFilters $nodeFilters

HealthState                  : Error
NodeHealthStateChunks        :
                               TotalCount            : 1

                               NodeName              : _Node_1
                               HealthState           : Ok

ApplicationHealthStateChunks : None
```

O cmdlet seguinte obtém cluster segmento com os filtros de aplicação.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

# All replicas
$replicaFilter = New-Object System.Fabric.Health.ReplicaHealthStateFilter -Property @{HealthStateFilter=$allFilter}

# All partitions
$partitionFilter = New-Object System.Fabric.Health.PartitionHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$partitionFilter.ReplicaFilters.Add($replicaFilter)

# For WordCountService, return all partitions and all replicas
$svcFilter1 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{ServiceNameFilter="fabric:/WordCount/WordCountService"}
$svcFilter1.PartitionFilters.Add($partitionFilter)

$svcFilter2 = New-Object System.Fabric.Health.ServiceHealthStateFilter -Property @{HealthStateFilter=$errorFilter}

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{ApplicationNameFilter="fabric:/WordCount"}
$appFilter.ServiceFilters.Add($svcFilter1)
$appFilter.ServiceFilters.Add($svcFilter2)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)

Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters

HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 1

                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               ServiceHealthStateChunks :
                                   TotalCount            : 1

                                   ServiceName           : fabric:/WordCount/WordCountService
                                   HealthState           : Error
                                   PartitionHealthStateChunks :
                                       TotalCount            : 1

                                       PartitionId           : a1f83a35-d6bf-4d39-b90d-28d15f39599b
                                       HealthState           : Error
                                       ReplicaHealthStateChunks :
                                           TotalCount            : 5

                                           ReplicaOrInstanceId   : 131031502143040223
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844060
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844059
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844061
                                           HealthState           : Ok

                                           ReplicaOrInstanceId   : 131031502346844058
                                           HealthState           : Error
```

O cmdlet seguinte devolve todas as entidades implementadas num nó.

```xml
$errorFilter = [System.Fabric.Health.HealthStateFilter]::Error;
$allFilter = [System.Fabric.Health.HealthStateFilter]::All;

$dspFilter = New-Object System.Fabric.Health.DeployedServicePackageHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$daFilter =  New-Object System.Fabric.Health.DeployedApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter;NodeNameFilter="_Node_2"}
$daFilter.DeployedServicePackageFilters.Add($dspFilter)

$appFilter = New-Object System.Fabric.Health.ApplicationHealthStateFilter -Property @{HealthStateFilter=$allFilter}
$appFilter.DeployedApplicationFilters.Add($daFilter)

$appFilters = New-Object System.Collections.Generic.List[System.Fabric.Health.ApplicationHealthStateFilter]
$appFilters.Add($appFilter)
Get-ServiceFabricClusterHealthChunk -ApplicationFilters $appFilters


HealthState                  : Error
NodeHealthStateChunks        : None
ApplicationHealthStateChunks :
                               TotalCount            : 2

                               ApplicationName       : fabric:/System
                               HealthState           : Ok
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 1

                                       ServiceManifestName   : FAS
                                       HealthState           : Ok



                               ApplicationName       : fabric:/WordCount
                               ApplicationTypeName   : WordCount
                               HealthState           : Error
                               DeployedApplicationHealthStateChunks :
                                   TotalCount            : 1

                                   NodeName              : _Node_2
                                   HealthState           : Ok
                                   DeployedServicePackageHealthStateChunks :
                                       TotalCount            : 2

                                       ServiceManifestName   : WordCountServicePkg
                                       HealthState           : Ok

                                       ServiceManifestName   : WordCountWebServicePkg
                                       HealthState           : Ok
```

### <a name="rest"></a>RESTO
Pode obter o fragmento de estado de funcionamento de cluster com um [pedido de obter](https://msdn.microsoft.com/library/azure/mt656722.aspx) ou um [pedido de mensagem](https://msdn.microsoft.com/library/azure/mt656721.aspx) que inclui as políticas de estado de funcionamento e filtros avançados descritos no corpo.

## <a name="general-queries"></a>Consultas gerais
As consultas gerais devolver uma lista de serviço ferro entidades de um tipo especificado. Estes são expostos através da API (através dos métodos no **FabricClient.QueryManager**), os cmdlets do PowerShell e restantes. Estas consultas agregam subconsultas a partir de vários componentes. Uma delas é o [Estado de funcionamento do arquivo](service-fabric-health-introduction.md#health-store), que preenche o estado de funcionamento agregado para cada resultado de consulta.  

> [AZURE.NOTE] Consultas gerais devolvem o estado de funcionamento agregado da entidade e não contenham dados de estado de funcionamento avançada. Se não for Saudável uma entidade, pode dar seguimento com consultas de estado de funcionamento para obter todas as suas informações de estado de funcionamento, incluindo eventos, subordinado do Estado de funcionamento Estados-membros e avaliações danificadas.

Se o consultas gerais devolvem um Estado de funcionamento desconhecido para uma entidade, é possível que o arquivo de estado de funcionamento não tem dados completos sobre a entidade. Também é possível uma subconsulta para o arquivo de estado de funcionamento não foi bem sucedida (por exemplo, Ocorreu um erro de comunicação ou o arquivo de estado de funcionamento foi limitado). Dar seguimento, com uma consulta de estado de funcionamento da entidade. Se a subconsulta encontrados erros breves, tais como problemas de rede, poderá ter êxito esta consulta seguimento. Poderá também dar-lhe mais detalhes a partir da loja de estado de funcionamento sobre porque é que não sejam exposta à entidade.

As consultas que contêm **HealthState** para entidades são:

- Lista de nós: devolve os nós de lista no cluster (paginado).
  - API: [FabricClient.QueryClient.GetNodeListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getnodelistasync.aspx)
  - PowerShell: Get-ServiceFabricNode
- Lista de aplicações: devolve a lista de aplicações no cluster (paginado).
  - API: [FabricClient.QueryClient.GetApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricApplication
- Lista de serviços: devolve a lista de serviços numa aplicação (paginada).
  - API: [FabricClient.QueryClient.GetServiceListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getservicelistasync.aspx)
  - PowerShell: Get-ServiceFabricService
- Lista de partições: devolve a lista de partições num serviço (paginado).
  - API: [FabricClient.QueryClient.GetPartitionListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getpartitionlistasync.aspx)
  - PowerShell: Get-ServiceFabricPartition
- Lista de réplica: devolve a lista de réplicas uma partição (paginada).
  - API: [FabricClient.QueryClient.GetReplicaListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getreplicalistasync.aspx)
  - PowerShell: Get-ServiceFabricReplica
- Implementado a lista de aplicações: devolve a lista de aplicações implementadas num nó.
  - API: [FabricClient.QueryClient.GetDeployedApplicationListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedapplicationlistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication
- Implementado a lista de pacote de serviço: devolve a lista de pacotes de serviço numa aplicação implementada.
  - API: [FabricClient.QueryClient.GetDeployedServicePackageListAsync](https://msdn.microsoft.com/library/azure/system.fabric.fabricclient.queryclient.getdeployedservicepackagelistasync.aspx)
  - PowerShell: Get-ServiceFabricDeployedApplication

> [AZURE.NOTE] Algumas das consultas devolvem resultados paginados. O retorno destas consultas é uma lista derivada de [PagedList<T>](https://msdn.microsoft.com/library/azure/mt280056.aspx). Se os resultados não se ajusta uma mensagem, é devolvida apenas uma página e um ContinuationToken que controla ponto onde parou enumeração. Deve continuar para ligar para a consulta mesmo e transmitir no token de continuação de notas a partir de consulta anterior para obter resultados seguintes.

### <a name="examples"></a>Exemplos

O código seguinte obtém as aplicações danificadas no cluster:

```csharp
var applications = fabricClient.QueryManager.GetApplicationListAsync().Result.Where(
  app => app.HealthState == HealthState.Error);
```

O cmdlet seguinte obtém os detalhes da aplicação para o ferro: / WordCount aplicação. Repare que o estado de funcionamento é no aviso.

```powershell
PS C:\> Get-ServiceFabricApplication -ApplicationName fabric:/WordCount

ApplicationName        : fabric:/WordCount
ApplicationTypeName    : WordCount
ApplicationTypeVersion : 1.0.0
ApplicationStatus      : Ready
HealthState            : Warning
ApplicationParameters  : { "WordCountWebService_InstanceCount" = "1";
                         "_WFDebugParams_" = "[{"ServiceManifestName":"WordCountWebServicePkg","CodePackageName":"Code","EntryPointType":"Main","Debug
                         ExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {74f7e5d5-71a9-47e2-a8cd-1878ec4734f1} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"},{"ServiceManifestName":"WordCountServicePkg","CodeP
                         ackageName":"Code","EntryPointType":"Main","DebugExePath":"C:\\Program Files (x86)\\Microsoft Visual Studio
                         14.0\\Common7\\Packages\\Debugger\\VsDebugLaunchNotify.exe","DebugArguments":" {2ab462e6-e0d1-4fda-a844-972f561fe751} -p
                         [ProcessId] -tid [ThreadId]","EnvironmentBlock":"_NO_DEBUG_HEAP=1\u0000"}]" }
```

O cmdlet seguinte obtém os serviços com um Estado de funcionamento de aviso:

```powershell
PS C:\> Get-ServiceFabricApplication | Get-ServiceFabricService | where {$_.HealthState -eq "Warning"}


ServiceName            : fabric:/WordCount/WordCountService
ServiceKind            : Stateful
ServiceTypeName        : WordCountServiceType
IsServiceGroup         : False
ServiceManifestVersion : 1.0.0
HasPersistedState      : True
ServiceStatus          : Active
HealthState            : Warning
```

## <a name="cluster-and-application-upgrades"></a>Atualiza os cluster e de aplicações
Durante uma atualização da aplicação e cluster monitorizada, serviço ferro verifica do Estado de funcionamento para se certificar de que tudo permanece saudável. Se uma entidade está danificada, tal como avaliadas, utilizando as políticas de estado de funcionamento configurado, a atualização aplica-se as políticas de atualização específicas para determinar a ação seguinte. A atualização pode ser colocados em pausa para permitir a interação do utilizador (como corrigir condições de erro ou alterar as políticas) ou -poderão automaticamente reverter para a versão de boa anterior.

Durante a actualização de *cluster* , pode obter o estado de actualização de cluster. O estado de actualização inclui avaliações danificadas, que apontam para o que está danificado no cluster. Se a atualização é revertida devido a problemas de estado de funcionamento, o estado de actualização lembra-se a últimos motivos danificados. Esta informação pode ajudar os administradores investigar o que correu mal após a atualização é revertida ou parado.

Da mesma forma, durante uma atualização de *aplicação* , quaisquer avaliações danificadas estão contidas no estado de actualização de aplicação.

A seguinte mostra o estado de atualização da aplicação de um ferro modificado: / WordCount aplicação. Um watchdog comunicou um erro das suas réplicas. A atualização é gradual, porque as verificações do Estado de funcionamento não estão a ser respeitadas.

```powershell
PS C:\> Get-ServiceFabricApplicationUpgrade fabric:/WordCount

ApplicationName               : fabric:/WordCount
ApplicationTypeName           : WordCount
TargetApplicationTypeVersion  : 1.0.0.0
ApplicationParameters         : {}
StartTimestampUtc             : 4/21/2015 5:23:26 PM
FailureTimestampUtc           : 4/21/2015 5:23:37 PM
FailureReason                 : HealthCheck
UpgradeState                  : RollingBackInProgress
UpgradeDuration               : 00:00:23
CurrentUpgradeDomainDuration  : 00:00:00
CurrentUpgradeDomainProgress  : UD1

                                NodeName            : _Node_1
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_2
                                UpgradePhase        : Upgrading

                                NodeName            : _Node_3
                                UpgradePhase        : PreUpgradeSafetyCheck
                                PendingSafetyChecks :
                                EnsurePartitionQuorum - PartitionId: 30db5be6-4e20-4698-8185-4bd7ca744020
NextUpgradeDomain             : UD2
UpgradeDomainsStatus          : { "UD1" = "Completed";
                                "UD2" = "Pending";
                                "UD3" = "Pending";
                                "UD4" = "Pending" }
UnhealthyEvaluations          :
                                Unhealthy services: 100% (1/1), ServiceType='WordCountServiceType', MaxPercentUnhealthyServices=0%.

                                  Unhealthy service: ServiceName='fabric:/WordCount/WordCountService', AggregatedHealthState='Error'.

                                      Unhealthy partitions: 100% (1/1), MaxPercentUnhealthyPartitionsPerService=0%.

                                      Unhealthy partition: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b', AggregatedHealthState='Error'.

                                          Unhealthy replicas: 20% (1/5), MaxPercentUnhealthyReplicasPerPartition=0%.

                                          Unhealthy replica: PartitionId='a1f83a35-d6bf-4d39-b90d-28d15f39599b',
                                  ReplicaOrInstanceId='131031502346844058', AggregatedHealthState='Error'.

                                              Error event: SourceId='DiskWatcher', Property='Disk'.

UpgradeKind                   : Rolling
RollingUpgradeMode            : UnmonitoredAuto
ForceRestart                  : False
UpgradeReplicaSetCheckTimeout : 00:15:00
```

Leia mais sobre a [aplicação de serviço ferro atualizar](service-fabric-application-upgrade.md).

## <a name="use-health-evaluations-to-troubleshoot"></a>Utilizar avaliações de estado de funcionamento para resolver
Sempre que existe um problema com o cluster ou uma aplicação, veja o estado de funcionamento cluster ou aplicação para pinpoint qual é o problema. Avaliações danificadas fornecem detalhes sobre o que acionou o estado atual danificado. Se precisar, pode desagregar em entidades de secundárias danificado para identificar o problema de raiz.

> [AZURE.NOTE] Avaliações danificadas mostram que a razão pela primeira a entidade é avaliado como o estado de funcionamento atual. Poderão existir vários outros eventos acionam este estado, mas não serão refletidas no avaliações. Para obter mais informações, desagregar as entidades de estado de funcionamento descobrir todos os relatórios no cluster danificados.

## <a name="next-steps"></a>Próximos passos
[Utilizar relatórios de estado de funcionamento de sistema para resolução de problemas](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Adicionar relatórios de estado de funcionamento de serviço ferro personalizados](service-fabric-report-health.md)

[Como um relatório e verificar o estado de funcionamento do serviço](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Monitorizar e diagnosticar serviços localmente](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Atualização da aplicação de serviço ferro](service-fabric-application-upgrade.md)
