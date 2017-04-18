<properties
   pageTitle="Limitação no Gestor de recursos de cluster de serviço ferro | Microsoft Azure"
   description="Saiba como configurar as limitações fornecidas pelo serviço ferro Cluster recurso Gestor do."
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


# <a name="throttling-the-behavior-of-the-service-fabric-cluster-resource-manager"></a>Limitação o comportamento do serviço ferro Cluster Gestor de recursos
Mesmo se configurou o Gestor de recursos Cluster corretamente, pode obter interrompido cluster. Por exemplo, podem existir nó ou falhas domínio falhas em simultâneo - o que deverá acontecer se que tenham ocorrido durante uma atualização? O Gestor de recursos irá tentar o melhor corrigir tudo, mas em vezes da seguinte forma, poderá querer ter em consideração um backstop para que o próprio cluster tem a oportunidade de regularização (os nós que irão vêm execute novamente, as condições da rede heal eles próprios, bits corrigidos implementadas). Para ajudar com estes tipos de situações, o Gestor de recursos do serviço ferro Cluster incluem várias limitações. Note que estes limitações são bastante desorganização e geralmente não deveriam ser utilizadas, a menos que tenha havido alguns cuidado matemática concluída à volta a quantidade de trabalho paralela que realmente pode ser feito cluster, bem como um frequente precisa de responder a estas ordena de (ahem) eventos não planeado reconfiguração macroscópico (AKA: "Muito incorretas dias").

Em geral, recomendamos que evitar dias muito mau através das outras opções (como código normal atualizações e evitar overscheduling cluster para começar) em vez de limitação seu cluster para impedir de recursos a utilizar quando estiver a tentar corrigir propriamente dito). As limitações têm valores predefinidos que tenha encontrámos através de experiência até ser predefinições ok, mas provavelmente deve explorá e sintonizá-los à sua carga real esperada. Enquanto não demasiado restringir ou carregamento que cluster é aconselhável que pode determinar que não existem casos qual (até que pode remediá-los) onde tem de ter algumas limitações num local, mesmo se significa cluster irá demorar mais tempo a estabilidade.

##<a name="configuring-the-throttles"></a>Configurar as limitações
As limitações que estão incluídas por predefinição são:

-   GlobalMovementThrottleThreshold – este controla o número total de movimentos no cluster algum tempo (definido como GlobalMovementThrottleCountingInterval, valor em segundos)
-   MovementPerPartitionThrottleThreshold – este controla o número total de movimentos para qualquer partição de serviço algum tempo (o MovementPerPartitionThrottleCountingInterval, valor em segundos)

``` xml
<Section Name="PlacementAndLoadBalancing">
     <Parameter Name="GlobalMovementThrottleThreshold" Value="1000" />
     <Parameter Name="GlobalMovementThrottleCountingInterval" Value="600" />
     <Parameter Name="MovementPerPartitionThrottleThreshold" Value="50" />
     <Parameter Name="MovementPerPartitionThrottleCountingInterval" Value="600" />
</Section>
```

Tenha em atenção que a maioria das vezes que podemos visualizou os clientes a utilizar estes limitações passaram porque terem sido já num ambiente do recurso restringido (tais como largura de banda de rede limitado para nós individuais ou discos que não foram até os requisitos da réplica paralela constrói a ser colocados nos mesmos) que queria dizer que essas operações não seria teve êxito ou seria lenta mesmo assim.  Nas seguintes situações clientes foram sabendo à vontade com eles potencialmente foram expandir a quantidade de tempo que seria necessário cluster para alcançar um estado estável, incluindo a saber que poderia acabar execução inferior fiabilidade geral do enquanto que foram limitadas.

## <a name="next-steps"></a>Próximos passos
- Para saber mais sobre como o Gestor de recursos Cluster gere e equilibra carga no cluster, consulte o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
- O Gestor de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos dar saída neste artigo [que descreve um cluster de ferro de serviço](service-fabric-cluster-resource-manager-cluster-description.md)
