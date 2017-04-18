<properties
   pageTitle="Ação Testability | Microsoft Azure"
   description="Este artigo fala sobre as ações de testability que se encontram no Microsoft Azure Service ferro."
   services="service-fabric"
   documentationCenter=".net"
   authors="motanv"
   manager="timlt"
   editor="toddabel"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/03/2016"
   ms.author="motanv;heeldin"/>

# <a name="testability-actions"></a>Ações de Testability
Para simular uma infraestrutura não fiável, Azure Service ferro fornece, programador, com formas de simular vários falhas reais e transições de estado. Estas são expostas como ações testability. As ações são as APIs feixe que causam uma introdução falhas específica, transição de estado ou validação. Ao combinar estas ações, pode escrever cenários de teste abrangente para os seus serviços.

Serviço ferro fornece alguns cenários de teste comuns composto por estas ações. Recomendamos vivamente que utilizam estes cenários incorporados, que são cuidadosamente escolhidos para testar comuns transições de estado e casos de falha. No entanto, ações podem ser utilizadas para criar cenários de teste personalizado ao qual pretende adicionar cobertura de cenários que não são abrangidos pelos cenários incorporados ainda ou que se encontram personalizado obtidas especialmente para a sua aplicação.

Implementações c# das ações encontram-se na assemblagem System.Fabric.dll. O módulo sistema ferro PowerShell encontra-se na assemblagem Microsoft.ServiceFabric.Powershell.dll. Como parte da instalação runtime, está instalado o módulo ServiceFabric PowerShell para permitir que para fácil utilização.

## <a name="graceful-vs-ungraceful-fault-actions"></a>Sem problemas vs. ações inesperado de falhas
Ações de Testability são classificadas em dois registos principais:

* Falhas inesperadas: estas falhas simular falhas como máquina reinicia e falhas do processo. Nestes casos de falhas, o contexto de execução do processo deixa de abruptamente. Isto significa que não limpeza do Estado de pode executar antes da aplicação é iniciada novamente.

* Falhas com êxito: estas falhas simular ações com êxito como réplica move o cursor e gotas acionadas ao balanceamento de carga. Nestes casos, o serviço recebe uma notificação de fecho e pode limpar o estado antes de sair.

Para uma melhor validação de qualidade, execute a carga de trabalho do serviço e profissional enquanto induzir vários falhas com êxito e inesperadas. Falhas inesperadas exercem cenários onde o processo de serviço sai abruptamente no meio algumas fluxo de trabalho. Este procedimento testa o caminho de recuperação assim que a réplica do serviço é restaurada pelo serviço ferro. Isto irá ajudar a testar consistência dos dados e se o estado do serviço é mantido corretamente após falhas. Outro conjunto de teste de falhas (as falhas com êxito) que o serviço corretamente reage para réplicas a ser movidas à volta ao serviço ferro. Este procedimento testa processamento de cancelamento no método RunAsync. O serviço necessita verificar o token de cancelamento a ser definido, guardar corretamente o seu estado e sair do método RunAsync.

## <a name="testability-actions-list"></a>Lista de acções Testability

| Ação | Descrição | API gerida | Cmdlet do PowerShell | Falhas com êxito/inesperado |
|---------|-------------|-------------|-------------------|------------------------------|
|CleanTestState| Remove todos os o estado de teste do cluster em caso de um encerramento incorrecto do controlador de teste. | CleanTestStateAsync | Remover ServiceFabricTestState | Não aplicável |
| InvokeDataLoss | Originar perda de dados para uma partição de serviço. | InvokeDataLossAsync | ServiceFabricPartitionDataLoss invocar | Sem problemas |
| InvokeQuorumLoss | Coloca uma partição de serviço com estado determinado na perda de quórum. | InvokeQuorumLossAsync | ServiceFabricQuorumLoss invocar | Sem problemas |
| Deslocar-se principal | Move a especificado réplica principal de um serviço de estado para o nó do cluster especificado. | MovePrimaryAsync | Mover ServiceFabricPrimaryReplica | Sem problemas |
| Deslocar-se secundário | Move a réplica secundária atual de um serviço de estado para um nó cluster diferente. | MoveSecondaryAsync | Mover ServiceFabricSecondaryReplica | Sem problemas |
| RemoveReplica | Simula uma falha de réplica removendo uma réplica a partir de um cluster. Isto irá fechar a réplica e irá transitar a função 'Nenhum', remover o seu estado do cluster. | RemoveReplicaAsync | Remover ServiceFabricReplica | Sem problemas |
| RestartDeployedCodePackage | Simula uma falha no processo de pacote de código por reiniciar um pacote de código implementado num nó num cluster. Isto interrompe o processo de pacote de código, que irá reiniciar todas as utilizador serviço réplicas alojadas no processo. | RestartDeployedCodePackageAsync | Reiniciar ServiceFabricDeployedCodePackage | Inesperado |
| RestartNode | Simula uma falha do serviço ferro cluster nó por reiniciar um nó. | RestartNodeAsync | Reiniciar ServiceFabricNode | Inesperado |
| RestartPartition | Simula Centro de dados indisponibilidade ou cluster indisponibilidade cenário por reiniciar réplicas alguns ou todos os de uma partição de. | RestartPartitionAsync | Reiniciar ServiceFabricPartition | Sem problemas |
| RestartReplica | Simula uma falha de réplica reiniciar uma réplica persistente num cluster, fechando a réplica e, em seguida, voltar a abri-lo. | RestartReplicaAsync | Reiniciar ServiceFabricReplica | Sem problemas |
| NóInício | Inicia um nó num cluster de que já está parado. | StartNodeAsync | Iniciar ServiceFabricNode | Não aplicável |
| StopNode | Simula uma falha do nó por paragem de um nó num cluster. O nó irá permanecer para baixo até que seja chamado NóInício. | StopNodeAsync | Parar ServiceFabricNode | Inesperado |
| ValidateApplication | Validar a disponibilidade e estado de funcionamento do todos os serviços de serviço ferro dentro de uma aplicação, normalmente após induzir algumas falhas no sistema. | ValidateApplicationAsync | Teste ServiceFabricApplication | Não aplicável |
| ValidateService | Validar a disponibilidade e estado de funcionamento do serviço de serviço ferro, normalmente após induzir algumas falhas no sistema. | ValidateServiceAsync | Teste ServiceFabricService | Não aplicável |

## <a name="running-a-testability-action-using-powershell"></a>Executar uma ação de testability através do PowerShell

Este tutorial mostra como executar uma ação testability utilizando o PowerShell. Vai aprender executar uma ação de testability relativamente a um cluster de local (caixa de um) ou um cluster Azure. Microsoft.Fabric.Powershell.dll – o módulo do serviço ferro PowerShell – é instalado automaticamente quando instala o serviço Microsoft ferro MSI. O módulo é carregado automaticamente quando abre uma linha de comandos do PowerShell.

Segmentos de Iniciação:

- [Executar uma ação contra a um cluster de uma caixa](#run-an-action-against-a-one-box-cluster)
- [Executar uma ação contra a um cluster Azure](#run-an-action-against-an-azure-cluster)

### <a name="run-an-action-against-a-one-box-cluster"></a>Executar uma ação contra a um cluster de uma caixa

Para executar uma ação testability relativamente a um cluster local, ligar ao cluster e abra a linha de comandos do PowerShell no modo de administrador. Observe a ação **Reiniciar ServiceFabricNode** diga-nos.

```powershell
Restart-ServiceFabricNode -NodeName Node1 -CompletionMode DoNotVerify
```

Aqui, a ação **Reiniciar ServiceFabricNode** está a ser executada num nó com o nome "Nó1". O modo de conclusão Especifica que-deve não verificar se a ação de nó reiniciar realmente foi concluída com êxito. Especifica o modo de conclusão, tal como "Verificar" originará-o para verificar se a ação reiniciar realmente foi concluída com êxito. Em vez de diretamente especificando o nó pelo seu nome, pode especificá-lo através de uma chave de partição e o tipo de réplica, da seguinte forma:

```powershell
Restart-ServiceFabricNode -ReplicaKindPrimary  -PartitionKindNamed -PartitionKey Partition3 -CompletionMode Verify
```


```powershell
$connection = "localhost:19000"
$nodeName = "Node1"

Connect-ServiceFabricCluster $connection
Restart-ServiceFabricNode -NodeName $nodeName -CompletionMode DoNotVerify
```

**Reiniciar ServiceFabricNode** deve ser utilizada para reiniciar um nó de serviço ferro num cluster. Isto irá parar o processo de Fabric.exe, que irá reiniciar todas as sistema serviço e utilizador serviço réplicas alojadas nesse nó. Utilizar esta API para testar o seu serviço de ajuda-o a descobrir erros ao longo dos caminhos de recuperação activação pós-falha. É útil simular falhas de nós no cluster.

A seguinte captura de ecrã mostra o comando de testability **ServiceFabricNode reiniciar** em ação.

![](media/service-fabric-testability-actions/Restart-ServiceFabricNode.png)

O resultado da primeira **Get-ServiceFabricNode** (um cmdlet do módulo serviço ferro PowerShell) mostra que o cluster local tem cinco nós: Node.1 para Node.5. Depois da ação de testability (cmdlet) **Reiniciar ServiceFabricNode** é executada no nó, com o nome Node.4, Vemos que o tempo de utilização do nó tiver sido reposto.

### <a name="run-an-action-against-an-azure-cluster"></a>Executar uma ação contra a um cluster Azure

Executar uma ação testability (ao utilizar o PowerShell) relativamente a um cluster Azure é semelhante ao executar a ação contra a um cluster local. A única diferença é que antes de poder executar a ação, em vez de ligar ao cluster local, é necessário ligar ao Azure cluster pela primeira vez.

## <a name="running-a-testability-action-using-c35"></a>Executar uma ação de testability através do C# 35;

Para executar uma ação testability utilizando c#, primeiro é necessário ligar ao cluster utilizando FabricClient. Em seguida, obtenha os parâmetros necessários para executar a ação. Diferentes parâmetros podem ser utilizados para executar a ação mesmo.
Consultar a ação RestartServiceFabricNode, é uma forma de executá-la, utilizando as informações de nó (nome do nó e ID da instância nó) no cluster.

```csharp
RestartNodeAsync(nodeName, nodeInstanceId, completeMode, operationTimeout, CancellationToken.None)
```

Explicação de parâmetro:

- **CompleteMode** Especifica que o modo não deve verificar se a ação reiniciar realmente foi concluída com êxito. Especifica o modo de conclusão, tal como "Verificar" originará-o para verificar se a ação reiniciar realmente foi concluída com êxito.  
- **OperationTimeout** define a quantidade de tempo para a operação de terminar antes de uma exceção TimeoutException é devolvida.
- **CancellationToken** permite uma chamada pendente para ser cancelada.

Em vez de diretamente especificando o nó pelo seu nome, poderá especificá-la através de uma chave de partição e o tipo de réplica.

Para obter mais informações, consulte o artigo [PartitionSelector e ReplicaSelector](#partition_replica_selector).


```csharp
// Add a reference to System.Fabric.Testability.dll and System.Fabric.dll
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.Fabric.Testability;
using System.Fabric;
using System.Threading;
using System.Numerics;

class Test
{
    public static int Main(string[] args)
    {
        string clusterConnection = "localhost:19000";
        Uri serviceName = new Uri("fabric:/samples/PersistentToDoListApp/PersistentToDoListService");
        string nodeName = "N0040";
        BigInteger nodeInstanceId = 130743013389060139;

        Console.WriteLine("Starting RestartNode test");
        try
        {
            //Restart the node by using ReplicaSelector
            RestartNodeAsync(clusterConnection, serviceName).Wait();

            //Another way to restart node is by using nodeName and nodeInstanceId
            RestartNodeAsync(clusterConnection, nodeName, nodeInstanceId).Wait();
        }
        catch (AggregateException exAgg)
        {
            Console.WriteLine("RestartNode did not complete: ");
            foreach (Exception ex in exAgg.InnerExceptions)
            {
                if (ex is FabricException)
                {
                    Console.WriteLine("HResult: {0} Message: {1}", ex.HResult, ex.Message);
                }
            }
            return -1;
        }

        Console.WriteLine("RestartNode completed.");
        return 0;
    }

    static async Task RestartNodeAsync(string clusterConnection, Uri serviceName)
    {
        PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);
        ReplicaSelector primaryofReplicaSelector = ReplicaSelector.PrimaryOf(randomPartitionSelector);

        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(primaryofReplicaSelector, CompletionMode.Verify);
    }

    static async Task RestartNodeAsync(string clusterConnection, string nodeName, BigInteger nodeInstanceId)
    {
        // Create FabricClient with connection and security information here
        FabricClient fabricclient = new FabricClient(clusterConnection);
        await fabricclient.FaultManager.RestartNodeAsync(nodeName, nodeInstanceId, CompletionMode.Verify);
    }
}
```

## <a name="partitionselector-and-replicaselector"></a>PartitionSelector e ReplicaSelector

### <a name="partitionselector"></a>PartitionSelector
PartitionSelector é uma auxiliar exposta na testability e é utilizado para selecionar uma partição específica na qual pretende efetuar nenhuma das ações testability. Pode ser utilizado para selecionar uma partição específica, se o ID da partição for conhecido prévia. Ou pode fornecer a chave de partição e a operação resolverá o ID da partição internamente. Também tem a opção de selecionar uma partição aleatória.

Para utilizar este programa auxiliar, crie o objecto PartitionSelector e selecione a partição utilizando um dos métodos Select *. Em seguida, passe no objeto PartitionSelector à API que necessite dele. Se nenhuma opção estiver selecionada, esta assume a predefinição uma partição aleatória.

```csharp
Uri serviceName = new Uri("fabric:/samples/InMemoryToDoListApp/InMemoryToDoListService");
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
string partitionName = "Partition1";
Int64 partitionKeyUniformInt64 = 1;

// Select a random partition
PartitionSelector randomPartitionSelector = PartitionSelector.RandomOf(serviceName);

// Select a partition based on ID
PartitionSelector partitionSelectorById = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);

// Select a partition based on name
PartitionSelector namedPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionName);

// Select a partition based on partition key
PartitionSelector uniformIntPartitionSelector = PartitionSelector.PartitionKeyOf(serviceName, partitionKeyUniformInt64);
```

### <a name="replicaselector"></a>ReplicaSelector
ReplicaSelector é uma auxiliar exposta na testability e é utilizada para ajudar a seleccionar uma réplica na qual pretende efetuar nenhuma das ações testability. Pode ser utilizado para selecionar uma réplica específica, se o ID de réplica for conhecido prévia. Além disso, tem a opção de selecionar uma réplica principal ou um secundário aleatório. ReplicaSelector deriva da PartitionSelector, pelo que necessita para selecionar a réplica e a partições no qual pretende efectuar a operação de testability.

Para utilizar este programa auxiliar, crie um objeto de ReplicaSelector e defina da forma que pretende para selecionar a réplica e a partição. Em seguida, pode passar para a API que necessite dele. Se nenhuma opção estiver selecionada, esta assume a predefinição uma réplica aleatória e partição aleatória.

```csharp
Guid partitionIdGuid = new Guid("8fb7ebcc-56ee-4862-9cc0-7c6421e68829");
PartitionSelector partitionSelector = PartitionSelector.PartitionIdOf(serviceName, partitionIdGuid);
long replicaId = 130559876481875498;

// Select a random replica
ReplicaSelector randomReplicaSelector = ReplicaSelector.RandomOf(partitionSelector);

// Select the primary replica
ReplicaSelector primaryReplicaSelector = ReplicaSelector.PrimaryOf(partitionSelector);

// Select the replica by ID
ReplicaSelector replicaByIdSelector = ReplicaSelector.ReplicaIdOf(partitionSelector, replicaId);

// Select a random secondary replica
ReplicaSelector secondaryReplicaSelector = ReplicaSelector.RandomSecondaryOf(partitionSelector);
```

## <a name="next-steps"></a>Próximos passos

- [Cenários de Testability](service-fabric-testability-scenarios.md)
- Como testar o seu serviço
   - [Simular falhas durante das cargas de trabalho do serviço](service-fabric-testability-workload-tests.md)
   - [Falhas de comunicação de serviço de serviço](service-fabric-testability-scenarios-service-communication.md)
