<properties
   pageTitle="Planear a capacidade de cluster de serviço ferro | Microsoft Azure"
   description="Capacidade de cluster de serviço ferro considerações sobre o planeamento. Camadas de Nodetypes, durabilidade e fiabilidade"
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


# <a name="service-fabric-cluster-capacity-planning-considerations"></a>Capacidade de cluster de serviço ferro considerações sobre o planeamento

Para qualquer implementação de produção, planeamento da capacidade está num passo de importante. Eis alguns dos itens que tenham a considerar como parte do processo.

- O número de tipos de nó que seu cluster precisa de começar com
- As propriedades de cada tipo de nó (tamanho, principal, internet opostas, número de VMs, etc.)
- As características de fiabilidade e durabilidade do cluster

Diga-nos brevemente rever cada um dos seguintes itens.

## <a name="the-number-of-node-types-your-cluster-needs-to-start-out-with"></a>O número de tipos de nó que seu cluster precisa de começar com

Em primeiro lugar, tem de descobriu o que se passa cluster que estiver a criar a ser utilizado para e que tipos de aplicações está a planear implementar para este cluster. Se não seja claro sobre o objetivo do cluster, não são mais provável ainda pronto para introduzir a capacidade de processo de planeamento.

Estabelece o número de tipos de nó que seu cluster precisa de começar com.  Cada tipo de nó é mapeado para um conjunto de escala de Máquina Virtual. Cada tipo de nó, em seguida, pode ser dimensionado para cima ou para baixo de forma independente, têm diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente. Por isso, a decisão do número de tipos de nó essencialmente é fornecido para baixo para as seguintes considerações:

- A sua aplicação tem vários serviços e qualquer uma delas tem de ser público ou ao público de internet? As aplicações típicas contêm um serviço de gateway front-end que recebe a entrada de um cliente e um ou mais serviços back-end que comunicam com os serviços de front-end. Por isso, neste caso, acabar por ter, pelo menos, dois tipos de nó.

- Os serviços (que compõem a sua aplicação) possuo infraestrutura diferentes necessidades como maior RAM ou superiores ciclos de CPU? Por exemplo, diga-nos partem do princípio de que a aplicação que pretende implementar contém um serviço front-end e um serviço de back-end. O serviço de front-end pode ser executados em VMs mais pequenos (tamanhos VM como D2) tem portas abertas à internet.  O serviço de back-end, no entanto, é com um grau elevado de cálculo e precisa de executar no maiores VMs (com tamanhos VM como D4, D6, D15) que não estão internet opostas.

 Neste exemplo, embora pode optar por colocar todos os serviços no tipo de um nó, recomendamos que é colocá-los num cluster com dois tipos de nó.  Esta opção permite-para cada tipo de nó ter propriedades distintas, tais como ligação à internet ou o tamanho da memória virtual. O número de VMs pode ser dimensionado de forma independente, bem como.  

- Uma vez que não pode prever no futuro, aceda com factos a que saber da e decidir no número de tipos de nó as suas aplicações necessitarem começar. Pode sempre adicionar ou remover tipos de nó mais tarde. Um cluster de serviço ferro tem de ter pelo menos um nó tipo.

## <a name="the-properties-of-each-node-type"></a>As propriedades de cada tipo de nó

O **tipo de nó** podem ser vistos como equivalente a funções numa serviços em nuvem. Tipos de nó definem os tamanhos VM, o número de VMs e respectivas propriedades. Cada tipo de nó é definido num cluster de serviço ferro está configurado como um conjunto de escala de Máquina Virtual em separado. Os conjuntos de escala VM são uma Azure calcular pode utilizar para implementar e gerir uma coleção de máquinas virtuais como um conjunto de recursos. A ser definido como conjuntos de escala VM distintos, cada tipo de nó, em seguida, pode ser dimensionado para cima ou para baixo de forma independente, ter diferentes conjuntos de portas abertas e pode ter métricas de capacidade diferente.

Cluster pode ter mais do que um tipo de nó, mas o tipo de nó principal (a primeira parte definidos por si no portal do) tem de ter, pelo menos, cinco VMs para clusters utilizados para cargas de trabalho de produção (ou VMs pelo menos três clusters de teste). Se estiver a criar o cluster utilizando um modelo de Gestor de recursos, irá encontrar um atributo **é principal** da definição do tipo de nó. O tipo de nó primário é o tipo de nó onde são colocados os serviços do sistema ferro de serviço.  

### <a name="primary-node-type"></a>Tipo de nó principal
Para um cluster com vários tipos de nó, terá de selecionar uma das que sejam principal. Eis as características de um tipo de nó primário:

- O tamanho mínimo de VMs para o tipo de nó primário é determinado pela camada durabilidade que escolher. A predefinição para a camada durabilidade é Bronze. Desloque-se para baixo para obter detalhes sobre o que é a camada durabilidade e os valores, pode demorar.  

- O número mínimo de VMs para o tipo de nó primário é determinado pela camada de fiabilidade que escolher. A predefinição para a camada de fiabilidade é prata. Desloque-se para baixo para obter detalhes sobre o que é a camada fiabilidade e os valores, pode demorar.

- Os serviços do sistema ferro de serviço (por exemplo, o serviço do Gestor de Cluster ou um serviço de arquivo de imagem) são colocados no tipo de nó principal e por isso, a fiabilidade e o durabilidade do cluster é determinada pelo valor de camada de fiabilidade e o valor de camada de durabilidade selecionar para o tipo de nó principal.

![Captura de ecrã de um cluster de que tem dois tipos de nó ][SystemServices]


### <a name="non-primary-node-type"></a>Tipo de nó não principal
Para um cluster com vários tipos de nó, existe um tipo de nó principal e o resto dos mesmos são não principal. Eis as características de um tipo de nó não principal:

- O tamanho mínimo de VMs para este tipo de nó é determinado pela camada durabilidade que escolher. A predefinição para a camada durabilidade é Bronze. Desloque-se para baixo para obter detalhes sobre o que é a camada durabilidade e os valores, pode demorar.  

- O número mínimo de VMs para este tipo de nó pode ser uma. No entanto, deve escolher este número com base no número de réplicas as/de serviços de aplicação que pretende executar neste tipo de nó. É possível aumentar o número de VMs num tipo de nó depois de ter implementado cluster.


## <a name="the-durability-characteristics-of-the-cluster"></a>As características durabilidade do cluster

A camada durabilidade é utilizada para indicar ao sistema os privilégios do que o seu VMs tem com infraestrutura do Azure subjacente. Em tipo nó primário, este privilégio permite ferro de serviço colocar em pausa qualquer pedido de infraestrutura de nível de VM (como reinicialização VM, VM reimage ou migração VM) que causam impacto nos requisitos de quórum para os serviços do sistema e os seus serviços de estado. Em tipos de nó não principal, este privilégio permite ferro de serviço colocar em pausa qualquer pedido de infraestrutura nível VM, como reinicialização VM, VM reimage ou migração VM etc., que causam impacto nos requisitos de quórum para os seus serviços com estado a ser executada em-lo.

Este privilégio é expresso nos seguintes valores:

- Ouro - as tarefas de infraestrutura pode ser colocado em pausa durante uma duração das horas 2 por UD

- Prata - as tarefas de infraestrutura pode ser colocado em pausa durante uma duração de 30 minutos por UD (isto atualmente não estiver ativado para utilização. Uma vez activada este estará disponível em todos os VMs padrão de core único e acima).

- Bronze - sem privilégios. Esta é a predefinição.

## <a name="the-reliability-characteristics-of-the-cluster"></a>As características de fiabilidade do cluster

A camada fiabilidade é utilizada para definir o número de réplicas serviços do sistema que pretende executar neste cluster no tipo de nó principal. O mais número de réplicas, mais fiável são os serviços do sistema no seu cluster.  

A camada fiabilidade pode demorar os seguintes valores.

- Platina - execute os serviços de sistema com um alvo de réplica definir a contagem de 9

- Ouro - execute os serviços de sistema com um alvo de réplica definir a contagem de 7

- Prata - execute os serviços de sistema com um alvo de réplica definir a contagem de 5

- Bronze - execute os serviços de sistema com um alvo de réplica definir a contagem de 3

>[AZURE.NOTE] A camada de fiabilidade que escolher determina o número mínimo de nós que tem de ter o tipo de nó principal. A camada de fiabilidade não tem nenhum tendo sobre o tamanho máximo do cluster. Para que possa ter um cluster de 20 nó, que está a ser executado em fiabilidade Bronze.

 Pode optar por atualizar a fiabilidade do seu cluster a partir de uma camada para outra. Este procedimento irá acionar as actualizações de cluster o necessário alterar a réplica de serviços de sistema definir a contagem. Aguarde que a atualização em curso para concluir antes de efetuar quaisquer outras alterações ao cluster, como adicionar nós etc.  Pode monitorizar o progresso da atualização do serviço ferro Explorer ou ao executar [Get-ServiceFabricClusterUpgrade](https://msdn.microsoft.com/library/mt126012.aspx)

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>Próximos passos

Uma vez que o planeamento de capacidade de conclusão e configurar um cluster, leia o seguinte:
- [Segurança do cluster ferro de serviço](service-fabric-cluster-security.md)
- [Introdução de modelo de estado de funcionamento do serviço ferro](service-fabric-health-introduction.md)

<!--Image references-->
[SystemServices]: ./media/service-fabric-cluster-capacity/SystemServices.png
