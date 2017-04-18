<properties
   pageTitle="Equilibrar seu Cluster com o Azure Service ferro Cluster Gestor de recursos | Microsoft Azure"
   description="Introdução ao balanceamento em seu cluster com o serviço ferro Cluster Gestor de recursos."
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

# <a name="balancing-your-service-fabric-cluster"></a>Equilibrar seu cluster de ferro de serviço
Permite que o Gestor de recursos do serviço ferro Cluster carga dinâmica de elaboração de relatórios, reagindo com as alterações de cluster, corrigir violações de constrangimento e rebalanceamento cluster se for necessário. Mas com que frequência faz estes pontos e o que accionadores-lo? Existem vários controlos relacionados com a esta.

O primeiro conjunto de controlos à volta de balanceamento são um conjunto de temporizadores. Destes temporizadores governem com que frequência o Gestor de recursos Cluster examina o estado do cluster para coisas que precisa de ser resolvidos. Existem três categorias de trabalho, cada uma com os seus próprios temporizador correspondente diferentes. São descritos abaixo:

1.  Posicionamento – nesta fase lida com colocar qualquer réplicas com estado ou instâncias sem estado que estão em falta. Isto abrange novos serviços e processamento réplicas com estado ou instâncias sem estado que tiverem falhado e ser recriadas. Eliminar e largando réplicas ou instâncias são também aqui processada.
2.  Restrição verifica – nesta fase verifica e corrige violações das restrições de posicionamento diferente (regras) no interior do sistema. Exemplos de regras são coisas como assegurar que nós não estão além da capacidade e que forem cumpridas restrições de colocação de um serviço (obter mais informações sobre estes posterior).
3.  Equilibrar – nesta fase verifica ver se rebalanceamento pro-activos for necessária com base em configurado nível de saldo para diferentes métricas pretendido e, em caso afirmativo tenta localizar uma disposição no cluster que é distribuído mais.

## <a name="configuring-cluster-resource-manager-steps-and-timers"></a>Configurar o Gestor de recursos do Cluster passos e temporizadores
Cada um destes tipos diferentes de correções pode tornar o Gestor de recursos de Cluster é controlada por um temporizador diferentes que gere a sua frequência. Por exemplo, se pretender apenas lidar com a colocar o novo serviço das cargas de trabalho no cluster cada hora (para batch-los para cima), mas pretende balanceamento normal verifica em poucos segundos, pode configurar esse comportamento. Quando cada temporizador é acionada, a tarefa é agendada. Por predefinição, o Gestor de recursos analisa o seu estado e aplica-se as atualizações (lotes todas as alterações que ocorreram desde a última pesquisa, como verificando que é um nó para baixo) cada 1/10 de um segundo, conjuntos a posicionamento restrição de verificação e sinalizadores cada segundo e o equilíbrio sinalizar 5 segundos.

ClusterManifest.xml:

``` xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PLBRefreshGap" Value="0.1" />
            <Parameter Name="MinPlacementInterval" Value="1.0" />
            <Parameter Name="MinConstraintCheckInterval" Value="1.0" />
            <Parameter Name="MinLoadBalancingInterval" Value="5.0" />
        </Section>
```

Hoje é apenas fazer uma das seguintes ações cada vez, sequencialmente (razão pela qual a referência é feita estas configurações como "intervalos mínimos")). Este é o modo a que, por exemplo, vamos já tiver respondido a qualquer pedidos pendentes para criar novas réplicas antes do podemos avançar para balanceamento cluster. Como pode ver pelos intervalos de tempo predefinido especificados, podemos pode pesquisar e verificar existência de nada precisamos de muito utiliza com frequência, que significa que o conjunto de alterações oferecemos no final de cada passo é normalmente mais pequeno: Recomendamos não está a analisar através de horas de alterações num cluster e tentar corrigi-los ao mesmo tempo, podemos está a tentar processar coisas mais ou menos à medida que estas aconteçam mas com algumas lotes quando muitas coisas acontecem na linha a mesmo tempo. Isto torna o recurso de serviço ferro Gestor muito capacidade para situações que podem acontecer no cluster.

Enquanto a maior parte destas tarefas são simples (se existirem violações de constrangimento, fix-las, se existirem serviços para ser criado, criá-los), o Gestor de recursos Cluster também precisa de algumas informações adicionais para determinar se o cluster imbalanced. Para que temos dois outros blocos de configuração: *Balanceamento limiares* e *Limiares de atividade*.

## <a name="balancing-thresholds"></a>Equilibrar limiares
Um determinado limiar balanceamento é o controlo principal para acionar rebalanceamento pro-activos (não se esqueça de que o temporizador encontra-se apenas para o número de vezes que o Gestor de recursos Cluster deve verificar - não significa que irá acontecer nada). O limiar de balanceamento define como imbalanced cluster tem de estar para uma métrica específica na ordem para o Gestor de recursos do Cluster a considerar-imbalanced e balanceamento de accionador.

Limiares de balanceamento são definidos numa base por métrica como uma parte da definição de cluster. Para mais informações sobre métricas de dar saída [deste artigo](service-fabric-cluster-resource-manager-metrics.md).

ClusterManifest.xml

``` xml
    <Section Name="MetricBalancingThresholds">
      <Parameter Name="MetricName1" Value="2"/>
      <Parameter Name="MetricName2" Value="3.5"/>
    </Section>
```

O limiar de balanceamento de uma métrica é um rácio. Se a quantidade de carga no nó mais carregado dividido pela quantidade de carga no nó menos carregado excede este número, em seguida, cluster considera imbalanced e balanceamento será acionado da próxima vez que o Gestor de recursos de Cluster verifica (por predefinição, alguma vez 5 segundos como regulado por MinLoadBalancingInterval, mostrado acima).

![Exemplo de limiar balanceamento][Image1]

Neste exemplo simples cada serviço é utilizado por outros uma unidade de algumas métrica. No exemplo superior, a carga máxima sobre um nó for a 5 e o valor mínimo é 2. Digamos que o limiar de balanceamento para esta métrica é 3. Por conseguinte, no exemplo superior, cluster considera equilíbrio e sem balanceamento será acionado quando verifica o Gestor de recursos Cluster (uma vez que a razão no cluster é de 5/2 = 2,5 e que é menor que o limiar especificado balanceamento de 3).

No exemplo parte inferior, a carga max num nó é 10, enquanto o mínimo é 2, que resulta num rácio de 5. Este procedimento coloca o cluster sobre o limiar de balanceamento concebido de 3 para esse métrica. Como resultado, um executar rebalancing global serão agendada próxima vez que o temporizador balanceamento é acionada. Tenha em atenção que só porque uma pesquisa balanceamento é pontapés não significa nada se irá mover, por vezes o cluster é imbalanced -, mas não pode ser melhorada a situação - mas numa situação como deste (, pelo menos, por predefinição) algumas a carga certamente será distribuída Nó3. Tenha em atenção que uma vez que recomendamos não está a utilizar uma abordagem melão algumas carga pode também ser distribuída para Nó2 desde que resulte na minimização das diferenças entre os nós gerais, mas recomendamos seria de esperar que a maioria da carga seria fluxo para Nó3.

![Ações de exemplo do limiar balanceamento][Image2]

Nota que introdução abaixo do limiar balanceamento não é um objetivo explícito – balanceamento limiares são apenas um *accionador* que indica o Gestor de recursos de Cluster deverá ter o aspecto num cluster para determinar que melhorias pode torná-lo, se existirem.

## <a name="activity-thresholds"></a>Limiares de atividade
Por vezes, embora sejam relativamente imbalanced nós, a quantidade *total* de carga no cluster está baixa. Isto pode ser devido a hora do dia ou porque o cluster há de novo e apenas ao obter bootstrapped. Em ambos os casos, poderá não pretende perder tempo balanceamento cluster porque existe realmente muito pequeno para ser adquirida-irá apenas ser despesas cluster de rede e de recursos para mover itens, sem fazer qualquer absoluto da diferença. Uma vez que queremos evitar este procedimento, existe outro dentro do Gestor de recursos, conhecida como limiares de atividade, que lhe permite especificar algumas vínculo inferior absoluto para atividade – se nenhum nó tem, pelo menos, isto muito carga de controlo, em seguida, balanceamento será não acionado mesmo que o limiar de balanceamento de que existe correspondência.

Por exemplo Digamos que temos relatórios com os totais seguintes para consumo nestes nós. Também Digamos que que recomendamos manter os nossos limiar de balanceamento de 3 para esta métrica, mas agora também temos uma limiar de atividade de 1536. No primeiro caso, enquanto o cluster está imbalanced pelo limiar de balanceamento de nenhum nó cumpre esse limiar mínimo de atividade, para que recomendamos deixe coisas individualmente. No exemplo parte inferior, Nó1 estiver de forma sobre o limiar de atividade, para que balanceamento será executada (uma vez que são excedido o limiar de balanceamento e o limiar de atividade para a métrica)

![Exemplo de limiar de atividade][Image3]

Tal como balanceamento limiares, limiares de atividade são definida por-métrica através da definição do cluster:

ClusterManifest.xml

``` xml
    <Section Name="MetricActivityThresholds">
      <Parameter Name="Memory" Value="1536"/>
    </Section>
```

Note que limiares equilíbrio e atividade são ambos associados à métrica - balanceamento serão apenas acionados se limiares equilíbrio e atividade são excedidos para a métrica do mesmo. Assim, se podemos excederem o limiar de balanceamento de memória e o limiar de atividade CPU, balanceamento irá não desencadear desde que os restantes limiares (balanceamento limiar para CPU) e o limiar de atividade para memória não são excedidos.

## <a name="balancing-services-together"></a>Serviços de balanceamento em conjunto
Algo que é interessante notar é que se cluster é imbalanced ou não é uma decisão de toda a cluster, mas a forma como o podemos aceda sobre como corrigir-está a movê réplicas do serviço individual e instâncias à volta. Isto faz sentido, à direita? Se memória é empilhada num nó, várias réplicas ou instâncias poderão estar a contribuir para a mesma, por isso,-pode requerer mover qualquer uma das réplicas com estado ou instâncias sem estado que utilizam a métrica do afectado, imbalanced.

Ocasionalmente, apesar de um cliente ligarem-no ficheiro ou um convite dizer que um serviço que não foi imbalanced tem movido. Como poderia que acontecer que um serviço é movido à volta de mesmo se foram balanceamento todas métricas desse serviço, mesmo perfeitamente afirmativo, no momento da outros diferença? Vamos ver!

Por exemplo demorar quatro serviços, o serviço 1, o serviço 2, o serviço 3 e o Service4. Serviço 1 relatórios contra métricas Metric1 e Metric2, serviço 2 contra Metric2 e Mmetric3, serviço 3 contra Metric3 e Metric4 e Service4 contra algumas Metric99 métrica. Marido pode ver onde iremos aqui. Temos uma cadeia! Da perspetiva do Gestor de recursos Cluster, não temos realmente quatro serviços independentes, temos um conjunto de serviços que estão relacionados (serviço 1, serviço 2 e serviço 3) e um que está desativada na sua própria.

![Serviços de balanceamento em conjunto][Image4]

Por isso, é possível que uma diferença no Metric1 pode causar réplicas ou instâncias pertencente ao serviço 3 para deslocar-se. Normalmente, estes movimentos são muito limitados, mas pode ser maior dependendo exatamente como imbalanced Metric1 tem e quais as alterações foram necessárias no cluster para poder corrigi-la. Recomendamos também pode dizer com tenha a certeza de que uma diferença no métricas 1, 2 ou 3 nunca causará movimentos na Service4 – não há nenhum ponto desde mover as réplicas ou instâncias que pertencem a Service4 à volta de podem não faça realmente nada impacto o saldo do métricas 1, 2 ou 3.

O Gestor de recursos Cluster automaticamente figuras saída que serviços estão relacionados, uma vez que serviços poderão ter sido adicionados, removido, ou tinham a sua alteração da configuração métrica – por exemplo, entre duas execuções de balanceamento de serviço 2 poderão ter sido reconfigurar para remover Metric2. Isto quebra a cadeia entre o serviço 1 e o serviço 2. Agora em vez de dois grupos dos serviços, tem três:

![Serviços de balanceamento em conjunto][Image5]

## <a name="next-steps"></a>Próximos passos
- Métricas são a forma como o Gestor de recursos do serviço ferro Cluster gere consumo e a capacidade no cluster. Para saber mais sobre-los e como configurá-los consulte [Este artigo](service-fabric-cluster-resource-manager-metrics.md)
- Custo de movimento é uma forma de sinalização o Gestor de recursos do Cluster que determinados serviços estão mais dispendiosos mover que outras pessoas. Para saber mais sobre o custo de movimento, consulte [Este artigo](service-fabric-cluster-resource-manager-movement-cost.md)
- O Gestor de recursos de Cluster tem várias limitações que pode configurar para diminuir a velocidade vasilha no cluster. Não está normalmente necessários, mas se precisar deles pode obter informações sobre os mesmos [aqui](service-fabric-cluster-resource-manager-advanced-throttling.md)


[Image1]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resrouce-manager-balancing-thresholds.png
[Image2]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-threshold-triggered-results.png
[Image3]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-activity-thresholds.png
[Image4]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together1.png
[Image5]:./media/service-fabric-cluster-resource-manager-balancing/cluster-resource-manager-balancing-services-together2.png
