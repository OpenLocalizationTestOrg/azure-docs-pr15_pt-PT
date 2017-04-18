<properties
   pageTitle="Custo do Gestor de recursos do serviço ferro Cluster: movimento | Microsoft Azure"
   description="Descrição geral do custo de movimento para serviços de ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor=""/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/19/2016"
   ms.author="masnider"/>

# <a name="service-movement-cost-for-influencing-cluster-resource-manager-choices"></a>Custo de movimento de serviço para que influenciam a opções de Gestor de recursos de Cluster
Um fator importante a ter em consideração quando está a tentar determinar o que é alterado para efetuar a um cluster e a pontuação de uma solução é o custo geral da realização dessa solução.

Mover o serviço instâncias ou réplicas custos CPU tempo e de rede largura de banda no mínimo. Para os serviços com estado, também custos a quantidade de espaço no disco que precisa para criar uma cópia do Estado antes de encerrar réplicas antigas. Claramente seria pretende minimizar o custo de qualquer solução que o Gestor de recursos do Azure Service ferro Cluster surgir com. Mas também não pretende ignorar soluções que iria melhorar significativamente a alocação de recursos no cluster.

Gestor de recursos do cluster tem duas formas de computação custos e limitá-las, mesmo que tentar gerir o cluster de acordo com os seus objetivos. O primeiro é que quando o Gestor de recursos Cluster está a planear um novo esquema para cluster, conta todas as mover que ficaria. Caso uma simples, se obtiver duas soluções com informações sobre a mesma equilibrar global (pontuação) no final, em seguida, tomar a que o custo mais baixo (número total de move o cursor).

Isto funciona bem muito. Mas tal como acontece com predefinido ou cargas estáticas, é provável em qualquer sistema complexo que move todos os é igual. Alguns estão a ser dispendioso muito mais.

## <a name="changing-a-replicas-move-cost-and-factors-to-consider"></a>Alterar o fatores a ter em conta e o custo de mover uma réplica
Como com relatórios carga (outra funcionalidade do Gestor de recursos de Cluster), que faz o serviço de uma forma de relatórios personalizada como dispendioso o serviço é mover num momento específico.

Código:

```csharp
this.ServicePartition.ReportMoveCost(MoveCost.Medium);
```

MoveCost tiver quatro níveis: Zero, baixa, média e máximo. Estes são relativamente às outras, exceto Zero. Zero significa que a mover uma réplica é gratuita e deve não são contabilizados para a pontuação da solução. Definir o custo de mover para alta é *não* garante que não a irá mover a réplica, apenas a que esta não movida a não ser que exista uma forte razão para.

![Deslocar-se custo com um fator no selecionar réplicas de movimento][Image1]

MoveCost ajuda-o a localizar as soluções que causar a interrupção, pelo menos, geral e são mais fáceis de atingir enquanto ainda a chegar ao saldo equivalente. Conceito de um serviço de custo pode ser relativamente às muitas coisas. Calcular o custo de mover os factores mais comuns são:

- A quantidade de estado ou dados que tenha o serviço para se mover.
- O custo de interrupção de clientes. O custo de mover uma réplica primária é normalmente mais elevado que o custo de mover uma réplica secundária.
- O custo de interromper uma operação em voo. Algumas operações dos dados armazenam nível ou operações realizadas em resposta a uma chamada do cliente são dispendiosas. Após um determinado ponto, que não pretende pará-las, se não tiver de. Por isso, para a duração de operação, pode aumentar o custo para reduzir a probabilidade de réplica do serviço ou instância se irá mover. Quando a operação estiver concluída, colocá-lo novamente ao normal.

## <a name="next-steps"></a>Próximos passos
- Gestor de recursos do serviço ferro Cluster utiliza métricas para gerir o consumo e a capacidade no cluster. Para saber mais sobre métricas e como configurá-los, consulte o artigo [Gerir o consumo de recursos e carregar no ferro serviço com métricas](service-fabric-cluster-resource-manager-metrics.md).
- Para saber mais sobre como o Gestor de recursos Cluster gere e equilibra carga no cluster, consulte o artigo [balanceamento seu cluster de serviço ferro](service-fabric-cluster-resource-manager-balancing.md).

[Image1]:./media/service-fabric-cluster-resource-manager-movement-cost/service-most-cost-example.png
