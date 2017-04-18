<properties
   pageTitle="Escalabilidade dos serviços de serviço ferro | Microsoft Azure"
   description="Descreve como dimensionar serviços ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="appi101"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/10/2016"
   ms.author="aprameyr"/>

# <a name="scaling-service-fabric-applications"></a>Aplicações de serviço ferro dimensionamento
Azure Service ferro torna mais fácil criar aplicações dimensionáveis balanceamento de carga serviços, partições e réplicas em todos os nós num cluster. Isto permite a utilização de recursos máximo.

Escala elevada para as aplicações de serviço ferro pode ser obtida de duas maneiras:

1. Dimensionamento ao nível do partição

2. Dimensionamento ao nível do nome do serviço

## <a name="scaling-at-the-partition-level"></a>Dimensionamento ao nível do partição
Serviço ferro suporta a criação de um serviço individual partições em múltiplas partições mais pequenas. [Descrição geral da criação de partições](service-fabric-concepts-partitioning.md) fornece informações sobre os tipos de esquemas de criação de partições que são suportadas. As réplicas de cada partição são propagadas em todos os nós num cluster. Considere a hipótese de um serviço que utiliza um esquema de partições ranged com uma chave de baixa de 0, uma chave de alta de 99 e quatro partições. Num cluster de três-node, o serviço poderá ser apresentado com quatro réplicas que partilham os recursos em cada nó, conforme mostrado aqui:

![Esquema de partição com três nós](./media/service-fabric-concepts-scalability/layout-three-nodes.png)

Aumentar o número de nós permite ferro de serviço utilizar os recursos no novos nós movendo algumas das réplicas para nós vazias. Aumentando o número de nós a quatro, o serviço agora tem três réplicas executado em cada nó (diferentes das partições de), tem a permissão para uma melhor utilização de recursos e desempenho.

![Esquema de partições com quatro nós](./media/service-fabric-concepts-scalability/layout-four-nodes.png)

## <a name="scaling-at-the-service-name-level"></a>Dimensionamento ao nível do nome do serviço
Uma instância do serviço é uma instância específica do nome de uma aplicação e um nome de tipo de serviço (consulte [ciclo de vida de aplicação de serviço ferro](service-fabric-application-lifecycle.md)). Durante a criação de um serviço, especifique a partição do esquema (consulte a [partições ferro serviço serviços](service-fabric-concepts-partitioning.md)) para ser utilizado.

É o primeiro nível de dimensionamento por nome de serviço. Pode criar novas instâncias de um serviço, com diferentes níveis de criar a partições, como as mais antigas instâncias do serviço tornam-se ocupado. Esta opção permite-novos consumidores de serviço utilizar instâncias do serviço ocupado menos em vez dos ficheiros.

Uma das opções para aumentar a capacidade, bem como contagens partição crescentes ou decrescentes, é criar uma nova instância do serviço com um novo esquema de partição. Esta ação adiciona complexidade, no entanto, como qualquer necessidade de clientes consome saber quando e como utilizar o serviço de forma diferente com nome.

### <a name="example-scenario-embedded-dates"></a>Cenário de exemplo: incorporado datas
Um cenário possível seria utilizar informações sobre a data como parte do nome do serviço. Por exemplo, pode utilizar uma instância do serviço com um nome específico para todos os clientes que aderiu no 2013 e o outro nome para clientes que aderiu em 2014. Este esquema nomenclatura permite através de programação aumentar os nomes dependendo da data (como abordagens de 2014, a instância do serviço de 2014 pode ser criada a pedido).

No entanto, esta abordagem é baseada os clientes utilizando as informações de atribuição de nomes específicos da aplicação que está fora do âmbito de conhecimento ferro de serviço.

- *Utilizar uma convenção de nomenclatura*: no 2013, quando a aplicação vai direto, cria um serviço denominado ferro: / aplicação/service2013. Junto à segundo trimestre do 2013 criar outro serviço de, denominado ferro: / aplicação/service2014. São ambos um destes serviços do mesmo tipo de serviço. Esta abordagem, o cliente será necessário empregam lógica para construir o nome de serviço apropriado com base num ano.

- *Utilizar um serviço de pesquisa*: outro padrão é proporcionar um serviço de pesquisa secundária, que pode fornecer o nome do serviço para uma chave pretendida. Novas instâncias de serviço, em seguida, podem ser criadas pelo serviço de pesquisa. O próprio serviço de pesquisa não manter quaisquer dados da aplicação, apenas os dados sobre os nomes de serviço que cria. Assim sendo, para que o ano com base no exemplo acima, o cliente seria contactar primeiro o serviço de pesquisa para descobrir o nome do serviço de processamento de dados para um determinado ano e, em seguida, utilizar esse nome de serviço para efetuar a operação real. O resultado de pesquisa primeiro pode ser colocadas em cache.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os conceitos de ferro de serviço, consulte o seguinte:

- [Disponibilidade dos serviços de ferro de serviço](service-fabric-availability-services.md)

- [Criar a partições serviços ferro de serviço](service-fabric-concepts-partitioning.md)

- [Definir e gerir o Estado](service-fabric-concepts-state.md)
