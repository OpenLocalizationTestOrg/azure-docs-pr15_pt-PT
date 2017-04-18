<properties
   pageTitle="Capacidade de planeamento para as aplicações de serviço ferro | Microsoft Azure"
   description="Descreve como identificar o número de nós de cluster necessários para uma aplicação de serviço ferro"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="markfuss"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/14/2016"
   ms.author="subramar"/>


# <a name="capacity-planning-for-service-fabric-applications"></a>Capacidade de planeamento para as aplicações de serviço ferro


Este documento ensina-o estimar a quantidade de recursos (CPUs, RAM, armazenamento de disco) que precisa para executar as aplicações do Azure Service ferro. É comuns para os requisitos de recursos alterar ao longo do tempo. Alguns recursos requerem normalmente desenvolver/testar seu serviço e, em seguida, exigir mais recursos à medida que indicam produção e a aplicação cresce popularidade. Quando estrutura da aplicação, pensar os requisitos de longo prazo e escolher permitir que o seu serviço Dimensionar para cumprir o pedido de cliente alta.

 Quando cria um cluster de serviço ferro, pode decidir o que tipos de máquinas virtuais (VMs) constituem o cluster. Cada VM vem com uma quantidade limitada de recursos sob a forma de CPUs (núcleos e velocidade), largura de banda de rede, RAM e armazenamento de disco. À medida que o serviço cresce ao longo do tempo, pode atualizar para o VMs que oferecem recursos maiores e/ou adicionar mais VMs ao seu cluster. Para executar o último, tem Arquitectar seu serviço inicialmente para que-possa tirar partido das novas VMs que dinamicamente ser adicionado ao cluster.

Alguns serviços não gerir pequeno nenhuma dados sobre os VMs próprios. Por conseguinte, planeamento para estes serviços de capacidade deve concentrar-se principalmente no desempenho, o que significa que selecionar CPUs adequadas (núcleos e velocidade) dos VMs. Além disso, deverá tomar em consideração a largura de banda de rede, incluindo a frequência de transferências de rede são ocorrência e está a ser transferido a quantidade de dados. Se o seu serviço necessita para desempenhar bem como aumenta a utilização de serviço, pode adicionar VMs mais para o equilíbrio de cluster e carregar os pedidos de rede ao longo de todo os VMs.

Para os serviços que gerir grandes quantidades de dados nas VMs, planeamento da capacidade deve concentrar-se principalmente no tamanho. Assim sendo, considere cuidadosamente a capacidade de RAM a VM e armazenamento de disco. O sistema de gestão de memória virtual no Windows faz o seguinte aspeto RAM para código da aplicação de espaço em disco. Além disso, o tempo de execução do serviço ferro fornece paginação inteligente manter os dados apenas quentes na memória e mover os dados frios no disco. Aplicações, pelo que podem utilizar mais memória do que está a VM física disponível. Basta ter mais RAM aumenta o desempenho, uma vez que a VM pode manter mais armazenamento do disco na RAM. A VM selecionar deverá ter um disco grande o suficiente para armazenar os dados que pretende na VM. Da mesma forma, a VM deve ter RAM suficiente para fornecer-lhe o desempenho que pretende. Se os dados do seu serviço cresce ao longo do tempo, pode adicionar mais VMs ao cluster e a partições os dados ao longo de todo os VMs.

## <a name="determine-how-many-nodes-you-need"></a>Determinar quantos nós precisar

Criar a partições seu serviço permite-lhe dimensionar dados do seu serviço. Para mais informações sobre partições, consulte o artigo [Criar partições ferro de serviço](service-fabric-concepts-partitioning.md). Cada partição tem de se ajustar dentro de uma única VM, mas múltiplas partições (pequenas) podem ser colocadas numa única VM. Por isso, está a ter mais pequenas partições dá-lhe maior flexibilidade do que está a ter alguns partições maiores. O compromisso é que está a ter muitas a partições aumenta sobrecarga ferro de serviço e não é possível executar operações transaccionadas através de partições. Também existe mais tráfego de rede potenciais se o código do serviço frequentemente aceder a blocos de dados que se encontram-se na partições diferentes. Ao estruturar o seu serviço, considere cuidadosamente estas vantagens e desvantagens para chegar a uma estratégia de criação de partições eficaz.

Vamos assumir que a aplicação tem um único serviço com estado que tenha um tamanho de arquivo que esperava para aumentar a DB_Size GB num ano. Está disposto a adicionar mais aplicações (e partições) à medida que se deparar crescimento além desse ano.  Fator de replicação (RF), que determina o número de réplicas do seu serviço de une a DB_Size total. O total DB_Size através de todas as réplicas é fator de replicação multiplicados por DB_Size.  Node_Size representa o disco espaço/de RAM por nó que pretende utilizar para o seu serviço. Para um melhor desempenho, a DB_Size deve se ajustam a memória ao longo do cluster e um Node_Size que está à volta de RAM da VM devem ser escolhidas. Ao atribuir uma Node_Size que é maior do que a capacidade de RAM, são depender a paginação fornecida pelo tempo de execução do serviço ferro. Assim, o desempenho do seu poderá não ser ideal se os seus dados inteiros são considerados como sendo quente (desde, em seguida, os dados são paginados numa/saída). No entanto, para muitos serviços onde está quente apenas uma fração dos dados, é mais rentável.

O número de nós necessários para o desempenho máximo pode ser calculado da seguinte forma:

```
Number of Nodes = (DB_Size * RF)/Node_Size

```


## <a name="account-for-growth"></a>Conta para o crescimento

Pretende calcular o número de nós com base na DB_Size que esperava seu serviço de crescimento, para além de DB_Size que começou com. Em seguida, aumentar o número de nós à medida que aumenta o serviço de modo a que não indevidamente a aprovisionar o número de nós. Mas o número de partições deve ser baseado no número de nós que são necessários quando estiver a executar o seu serviço de crescimento máximo.

É boa ideia têm algumas máquinas extra disponíveis em qualquer altura, de modo a que pode processar qualquer picos inesperados ou falha (por exemplo, se VMs alguns Ir para baixo).  Enquanto a capacidade de extra deve ser determinada utilizando o seu picos esperados, um ponto de partida é reservar alguns VMs extra (5-10 por cento extra).

Anterior assume um único serviço com estado. Se tiver mais de um serviço de estado, tem de adicionar DB_Size associados com outros serviços a equação. Em alternativa, pode calcular o número de nós separadamente para cada serviço com estado.  O serviço poderá ter réplicas ou a partições que não estão balanceamento. Tenha em atenção que partições também podem ter dados mais do que as outras pessoas. Para mais informações sobre partições, consulte o [artigo melhores práticas de criação de partições](service-fabric-concepts-partitioning.md). No entanto, a equação anterior é partição e réplica desconhecido, uma vez que serviço ferro garante que as réplicas são propagadas entre os nós de de uma forma otimizada.


## <a name="use-a-spreadsheet-for-cost-calculation"></a>Utilizar uma folha de cálculo para cálculos de custo

Agora vamos colocar alguns números reais na fórmula. Uma [folha de cálculo de exemplo](https://servicefabricsdkstorage.blob.core.windows.net/publicrelease/SF%20VM%20Cost%20calculator-NEW.xlsx) mostra como planear a capacidade para uma aplicação que contém três tipos de objetos de dados. Para cada objeto, podemos aproximar respetivo tamanho e podemos esperar para que os objectos quantos. Recomendamos também selecionar réplicas quantas queremos de cada tipo de objeto. A folha de cálculo calcula a quantidade total da memória a ser armazenado no cluster.

Em seguida, podemos introduza um tamanho da memória virtual e o custo mensal. Basear o tamanho da memória virtual, a folha de cálculo mostra-lhe o número mínimo de partições que tem de utilizar para dividir os dados para o ajustar física os nós. Poderá pretendem um grande número de partições para acomodar o cálculo específicas da sua aplicação e precisa de tráfego de rede. A folha de cálculo mostra o número de partições que está a gerir os objetos de perfil de utilizador tem aumentado a partir de uma para seis.

Agora, com base no todas estas informações, a folha de cálculo mostra que poderia física obter todos os dados com a partições pretendidas e réplicas num cluster de nó 26. No entanto, este cluster seria ser zonas de elevada embalado, para que pode querer alguns nós adicionais para acomodar falhas de nós e atualiza os. A folha de cálculo também mostra que está a ter mais do que 57 nós fornece nenhum valor adicional porque tem nós vazias. Novamente, poderá querer ir acima 57 nós mesmo assim para acomodar falhas de nós e atualiza os. Pode melhorar usando as a folha de cálculo para que correspondam às necessidades específicas da sua aplicação.   

![Folha de cálculo para cálculos de custo][Image1]



## <a name="next-steps"></a>Próximos passos

Dar saída a [partições ferro serviço serviços] [ 10] para saber mais sobre como criar a partições seu serviço.



<!--Image references-->
[Image1]: ./media/SF-Cost.png

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-concepts-partitioning.md
