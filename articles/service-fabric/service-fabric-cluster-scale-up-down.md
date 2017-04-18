<properties
   pageTitle="Dimensionar um cluster de serviço ferro ou reduzir | Microsoft Azure"
   description="Dimensione um cluster de serviço ferro ou reduzir para corresponder ao pedido ao configurar regras de escala automática para cada conjunto de escala de tipo/VM nó. Adicionar ou remover nós a um cluster de ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/09/2016"
   ms.author="chackdan"/>


# <a name="scale-a-service-fabric-cluster-in-or-out-using-auto-scale-rules"></a>Dimensionar um cluster de serviço ferro ou reduzir com regras de escala automática

Os conjuntos de escala de máquina virtual são um recurso de cluster Azure que pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto. Cada tipo de nó é definido num cluster de serviço ferro está configurado como um conjunto de escala VM separado. Cada tipo de nó, em seguida, pode ser dimensionado no ou saída de forma independente, têm diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente. Leia mais acerca do mesmo no documento [nodetypes ferro de serviço](service-fabric-cluster-nodetypes.md) . Uma vez que define a ferro de serviço de tipos de nó no seu cluster são constituídos escala VM ao back-end, tem de configurar regras de escala automática para cada conjunto de escala de tipo/VM nó.

>[AZURE.NOTE] A sua subscrição tem de ter suficiente núcleos para adicionar os novos VMs que compõem este cluster. Não existe nenhuma validação modelo atualmente, para obter uma falha de tempo de implementação, se qualquer um dos limites de quota forem atingidos.

## <a name="choose-the-node-typevm-scale-set-to-scale"></a>Selecione a escala de tipo/VM nó definida com uma escala

Atualmente, não é possível especificar as regras de escala automática para conjuntos de escala VM através do portal, por isso, diga-nos utilize Azure PowerShell (1.0 +) para listar os tipos de nó e, em seguida, adicionar regras de dimensionar automaticamente aos mesmos.

Para obter a lista de conjunto de escala VM que compõem o seu cluster, execute os seguintes cmdlets:

```powershell
Get-AzureRmResource -ResourceGroupName <RGname> -ResourceType Microsoft.Compute/VirtualMachineScaleSets

Get-AzureRmVmss -ResourceGroupName <RGname> -VMScaleSetName <VM Scale Set name>
```

## <a name="set-auto-scale-rules-for-the-node-typevm-scale-set"></a>Definir regras de dimensionar automaticamente para o conjunto de escala de tipo/VM nó

Se o seu cluster tiver vários tipos de nó, em seguida, repita que este procedimento para cada escala de tipos/VM nó define que pretende dimensionar (ou reduzir). Ter em conta o número de nós que tem de ter antes de configurar o dimensionamento automática. O número mínimo de nós que tem de ter para o tipo de nó primário é condicionado pelo nível de fiabilidade que escolheu. Leia mais sobre [níveis de fiabilidade](service-fabric-cluster-capacity.md).

>[AZURE.NOTE]  Dimensionamento para baixo o nó primário escreva para o menor do que a efetuar número mínima cluster instável ou colocá-lo para baixo. Isto poderá resultar na perda de dados para as suas aplicações e para os serviços do sistema.

Atualmente a funcionalidade de escala automática não é condicionada pelas cargas de que as aplicações poderão ser reportar a ferro de serviço. Por isso, neste momento Dimensionar automaticamente que obtém puramente está condicionada pelo desempenho contadores que são emitidas por cada da VM Dimensionar instâncias do conjunto.  

Siga estas instruções [para configurar o Dimensionar automaticamente para cada conjunto de escala VM](../virtual-machine-scale-sets/virtual-machine-scale-sets-autoscale-overview.md).

>[AZURE.NOTE] Numa escala de cores para baixo cenário, a menos que o seu tipo de nó tem um nível de durabilidade de ouro ou prata é necessário ligar o [cmdlet remover ServiceFabricNodeState](https://msdn.microsoft.com/library/azure/mt125993.aspx) com o nome de nó apropriado.

## <a name="manually-add-vms-to-a-node-typevm-scale-set"></a>Adicionar manualmente VMs a um conjunto de escala de tipo/VM nó

Siga as instruções/exemplo na [Galeria de modelos de guia de introdução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs em cada tipo de nó. 

>[AZURE.NOTE] Adição de VMs demora algum tempo, para que não esperava adições para ser instantânea. Por isso, planeia adicionar capacidade corretamente no tempo, para permitir a mais de 10 minutos antes da capacidade VM está disponível para as réplicas / instâncias obter colocado de serviço.

## <a name="manually-remove-vms-from-the-primary-node-typevm-scale-set"></a>Remover manualmente VMs do conjunto de escala tipo/VM nó principal

>[AZURE.NOTE] Os serviços de sistema do serviço ferro executar no tipo de nó primário no seu cluster. Para que nunca deve encerrar ou dimensionar para baixo o número de instâncias em que tipos de nó menor que que a camada de fiabilidade merece. Consulte [os detalhes na camadas de fiabilidade aqui](service-fabric-cluster-capacity.md). 

Tem de executar a seguinte passos uma VM instância de cada vez. Esta opção permite-para os serviços do sistema (e os seus serviços de estado) encerrar correctamente na instância VM que está a remover e novas réplicas criadas em outros nós.

1. Execute a [Desativar ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) com intenção 'RemoveNode' para desativar o nó que vai remover (a instância mais alta nesse tipo de nó).

2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para se certificar de que o nó tem facto que transitaram para desativado. Caso contrário, aguarde até que o nó está desativado. Não é possível hurry este passo.

2. Siga as instruções/exemplo na [Galeria de modelos de guia de introdução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs por uma nesse tipo de nó. A instância removida está a instância VM mais alta. 

3. Repita os passos 1 a 3, conforme necessário, mas nunca Dimensionar para baixo o número de instâncias nos nó primário tipos menor que o que a camada de fiabilidade merece. Consulte [os detalhes na camadas de fiabilidade aqui](service-fabric-cluster-capacity.md). 

## <a name="manually-remove-vms-from-the-non-primary-node-typevm-scale-set"></a>Remova manualmente VMs do conjunto de escala de tipo/VM de nós de que não sejam principal

>[AZURE.NOTE] Para um serviço com estado, é necessário um determinado número de nós para ser sempre excesso para manter a disponibilidade e preservar o estado do seu serviço. Em muito mínimo, tem o número de nós igual à contagem de conjunto de réplica destino do partição/serviço. 

Tem de executar a seguinte passos uma VM instância de cada vez. Esta opção permite-para os serviços do sistema (e os seus serviços de estado) encerrar correctamente na instância VM que está a remover e novas réplicas criada onde pessoa.

1. Execute a [Desativar ServiceFabricNode](https://msdn.microsoft.com/library/mt125852.aspx) com intenção 'RemoveNode' para desativar o nó que vai remover (a instância mais alta nesse tipo de nó).

2. Execute [Get-ServiceFabricNode](https://msdn.microsoft.com/library/mt125856.aspx) para se certificar de que o nó tem facto que transitaram para desativado. Se não esperar até que o nó está desativado. Não é possível hurry este passo.

2. Siga as instruções/exemplo na [Galeria de modelos de guia de introdução](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing) para alterar o número de VMs por uma nesse tipo de nó. Agora, isto irá remover a instância VM mais alta. 

3. Repita os passos 1 a 3, conforme necessário, mas nunca Dimensionar para baixo o número de instâncias nos nó primário tipos menor que o que a camada de fiabilidade merece. Consulte [os detalhes na camadas de fiabilidade aqui](service-fabric-cluster-capacity.md).

## <a name="behaviors-you-may-observe-in-service-fabric-explorer"></a>Comportamentos que poderá observar no serviço ferro Explorer

Quando dimensionar um cluster o Explorador de ferro serviço vão refletir as o número de nós (escala VM definido instâncias) que fazem parte de cluster.  No entanto, quando dimensionar um cluster para baixo irá ver a instância de nó removido/VM apresentada em mau estado, a menos que as chamadas [Remover ServiceFabricNodeState cmd](https://msdn.microsoft.com/library/mt125993.aspx) com o nome de nó apropriado.   

Eis a explicação para este comportamento.

Os nós listada no Explorador do serviço ferro são um reflexo que o serviço ferro dos serviços do sistema (FM especificamente) sabe sobre o número de nós cluster tinha/tem. Quando dimensionar a escala VM definida para baixo, a VM foi eliminada mas ainda serviço do sistema FM pensa que o nó (que foi mapeado para a VM que foi eliminada) será voltar atrás. Por isso, serviço ferro Explorer continua a apresentar que o nó (apesar do Estado de funcionamento poderá estar erro ou desconhecido).

Para se certificar de que um nó é removido quando uma VM é removida, tem duas opções:

1) Escolha um nível de durabilidade de ouro ou prata (disponível mais cedo) para os tipos de nó no seu cluster, que dá-lhe a integração de infraestrutura. Qual irá, em seguida, remover automaticamente os nós da nossa estado do sistema serviços (FM) quando dimensionar para baixo.
Consultar [os detalhes sobre os níveis de durabilidade aqui](service-fabric-cluster-capacity.md)

2) Assim que a instância VM foi dimensionada para baixo, tem de ligar para o [cmdlet ServiceFabricNodeState remover](https://msdn.microsoft.com/library/mt125993.aspx).

>[AZURE.NOTE] Serviço ferro clusters necessitam de um determinado número de nós de estar sempre para manter a disponibilidade e preservar estado - designado "manutenção de quórum." para cima Por isso, é normalmente não segura para encerrar todos os computadores no cluster, a menos que tiver executado em primeiro lugar uma [cópia de segurança completa do Estado](service-fabric-reliable-services-backup-restore.md).

## <a name="next-steps"></a>Próximos passos
Leia o seguinte para também saber mais sobre planear a capacidade de cluster, actualizar um cluster e criar a partições serviços:

- [Planear a sua capacidade de cluster](service-fabric-cluster-capacity.md)
- [Actualizações de cluster](service-fabric-cluster-upgrade.md)
- [Serviços de estado de partição para escala máxima](service-fabric-concepts-partitioning.md)

<!--Image references-->
[BrowseServiceFabricClusterResource]: ./media/service-fabric-cluster-scale-up-down/BrowseServiceFabricClusterResource.png
[ClusterResources]: ./media/service-fabric-cluster-scale-up-down/ClusterResources.png
