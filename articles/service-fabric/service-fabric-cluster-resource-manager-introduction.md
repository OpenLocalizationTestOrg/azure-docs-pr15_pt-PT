<properties
   pageTitle="Introdução ao Gestor de recursos do serviço ferro Cluster | Microsoft Azure"
   description="Uma introdução para o serviço ferro Cluster Gestor de recursos."
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

# <a name="introducing-the-service-fabric-cluster-resource-manager"></a>Introdução ao Gestor de recursos de cluster ferro de serviço
Gerir tradicionais sistemas de TI ou um conjunto de serviços destina introdução alguns física ou máquinas virtuais dedicadas desses serviços específicos ou sistemas. Muitos serviços principais foram divididos numa camada de "web" e uma camada "dados" ou "armazenamento", talvez com alguns outros componentes especializadas como uma cache. Outros tipos de aplicações teria uma camada mensagens onde os pedidos e reduzir, transferidos ligado a uma camada de trabalho para qualquer análise ou transformação necessária como parte da mensagens. Cada tipo de carga de trabalho tem um máquinas específico dedicado à mesma: a base de dados tem algumas máquinas dedicadas à mesma, os servidores de web por poucas. Se um tipo específico de carga de trabalho causado as máquinas estava no executar demasiado quente, em seguida, adicionado máquinas mais com esse tipo de carga de trabalho configurado para executar no mesmo ou substituído algumas das máquinas com maiores máquinas. Fácil. Se tiver falhado uma máquina, essa parte da aplicação geral ficou com a capacidade inferior até que o computador pode ser restaurado. Ainda bastante fácil (caso não seja necessariamente divertida).

Agora no entanto, vamos apresentamos ter encontrado uma necessidade de dimensionar saída e reencaminhado contentores e/ou do microservice optar. Inesperadamente localizá-se com dezenas, centenas ou mesmo milhares de máquinas, dezenas de diferentes tipos de serviços, talvez centenas de instâncias diferentes desses serviços, cada uma com um ou mais ocorrências ou réplicas para alta disponibilidade (HA).

Gerir o seu ambiente inesperadamente não é tão simple como gerir alguns máquinas dedicadas a única tipos de cargas de trabalho. Os servidores são virtuais e já não têm nomes ( *ter* mudado depois de todos os mindsets da [companhia para animais](http://www.slideshare.net/randybias/architectures-for-open-and-scalable-clouds/20) ). A configuração é menor sobre as máquinas e muito mais sobre os serviços de si. Hardware dedicado é uma coisa do passado e serviços ficaram pequenas sistemas distribuídos, que abrangem vários blocos mais pequenos de hardware do produto importante.

Em consequência dividindo a sua aplicação anteriormente monolítico, em camadas em separado serviços em execução no hardware do produto importante, agora, tem várias combinações mais para lidar com. Quem decidir o que tipos de cargas de trabalho podem ser executados em qual o hardware, ou quantos? Quais das cargas de trabalho funcionam bem no mesmo hardware e que estão em conflito? Quando uma máquina vai para baixo... o que estava mesmo a ser executado aí? Quem é o responsável pelo certificando-se de que carga de trabalho é iniciado executar novamente? Aguarde para o computador (virtual?) voltar atrás ou seu das cargas de trabalho automaticamente falhar sobre a outros máquinas e manter a executar o? É necessária intervenção humana? E atualiza os neste Ordenar do ambiente?

Como os programadores e operadores de vida neste Ordenar do mundo, iremos precisar de ajuda a gerir esta complexidade e obtenha o sentido em que uma binge contratação e tentar sobre a complexidade de papel com pessoas não é a resposta à direita.

O que fazer?

## <a name="introducing-orchestrators"></a>Introdução ao orchestrators
"Orchestrator" é o termo geral para uma peça de software que ajuda a gerir os seguintes tipos de ambientes de administradores. Orchestrators são os componentes que obtêm pedidos como "Optar por 5 cópias deste serviço em execução no meu ambiente", torná-lo for VERDADEIRO e, em seguida (tentar) para mantê-lo dessa forma.

Orchestrators (não humanos) são o que entrar em ação quando uma máquina falha ou uma carga de trabalho termina por algum motivo inesperado. A maior parte dos Orchestrators mais do que apenas lidar com falha, como o ajudar com implementações novas, processamento atualiza os e trabalhar com consumo de recursos, mas todos são bastante sobre como manter que algumas pretender que o estado de configuração no ambiente. Pretende poder dizer um Orchestrator o que pretende e têm-execute a elevatórias grossa. Chronos ou Marathon na parte superior de Mesos, frota, enxame, Kubernetes e serviço ferro se todos os exemplos de Orchestrators (ou as tem incorporado na). Mais estão a ser criados sempre como a complexidade da gestão de implementações do mundo real em diferentes tipos de ambientes e condições aumentar e alterar.

## <a name="orchestration-as-a-service"></a>Orchestration como um serviço
A tarefa de Orchestrator dentro de um cluster de serviço ferro é processada principalmente pelo Gestor de recursos do Cluster. O Gestor de recursos do serviço ferro Cluster é um dos serviços do sistema do serviço ferro e é iniciado automaticamente em cada cluster.  Em geral, a tarefa o Gestor de recursos de Cluster é dividida em três partes:

1. Impor regras
2. Otimizar o seu ambiente
3. Assistir noutros processos

### <a name="what-it-isnt"></a>O que não está
Nas tradicionais N camada-aplicações web havido sempre algumas noção de um "Balanceador de carga", normalmente referida como um balanceador de carga de rede (NLB) ou um balanceador de carga de aplicação (ALB) dependendo de onde Sol na pilha de rede. Algumas balanceadores de carga são baseada em como oferta de BigIP F5 Hardware, outros são software com base como da Microsoft NLB. Em outros ambientes poderá ver algo parecido com HAProxy nesta função. Nestes arquitecturas a tarefa de balanceamento de carga é para se certificar de que todas as máquinas diferentes sem estado front-end ou as máquinas diferentes no cluster (aproximadamente) recebem a mesma quantidade de trabalho. Estratégias para este variado, a partir de enviar cada chamada diferente para um servidor diferente, a sessão afixação/nódoas gordurosas, a alocação estimativa e chamada real com base no seu custo esperado e a carga de máquina atual.

Tenha em atenção que este foi pelo melhor equilíbrio o mecanismo para garantir que a camada web permaneceu aproximadamente. Estratégias para balanceamento a camada de dados foram completamente diferente e dependentes no mecanismo de armazenamento de dados, normalmente Centrar à volta de sharding de dados, colocação em cache, vistas de base de dados gerida e procedimentos armazenados, etc.

Embora algumas destas estratégias são interessantes, o Gestor de recursos do serviço ferro Cluster não é nada como um balanceador de carga de rede ou uma cache. Enquanto um balanceador de carga de rede garante que as extremidades da frente são balanceamento movendo o tráfego para que os serviços estão a ser executado, o Gestor de recursos do serviço ferro Cluster leva-o até uma estratégia de completamente diferente – bastante, serviço ferro move-se a *Serviços* para onde estes fazerem mais sentido (e espera tráfego ou carregar a seguir). Isto pode ser, por exemplo, nós que estão atualmente frias porque os serviços que estão lá não estão a fazer muitas trabalho com momento ou que foram eliminou ou moveu noutro local. Outro exemplo o Gestor de recursos Cluster também foi possível mover um serviço, afastando-o de uma máquina que está prestes a ser actualizado ou que está sobrecarregado devido a um coletor no consumo pelos serviços que estavam em execução no mesmo. Uma vez que o Gestor de recursos de Cluster é responsável por mover serviços em torno (não fornecer o tráfego de rede a onde já estão serviços), contém um conjunto de funcionalidade significativamente diferentes em comparação comparado o que pretende encontrar um balanceador de carga de rede e utiliza estratégias bastante diferentes para assegurar que os recursos de hardware no cluster também são utilizados.

## <a name="next-steps"></a>Próximos passos
- Para obter informações sobre o fluxo de arquitectura e informações dentro o Gestor de recursos de Cluster, consulte [Este artigo](service-fabric-cluster-resource-manager-architecture.md)
- O Gestor de recursos de Cluster tem muitas opções para descrever o cluster. Para saber mais sobre os mesmos dar saída neste artigo [que descreve um cluster de ferro de serviço](service-fabric-cluster-resource-manager-cluster-description.md)
- Para obter mais informações sobre as outras opções disponíveis para a configuração dos serviços de dar saída o tópico sobre as outras configurações do Gestor de recursos de Cluster disponíveis, [Saiba mais sobre como configurar os serviços](service-fabric-cluster-resource-manager-configure-services.md)
- Métricas são a forma como o Gestor de recursos do serviço ferro Cluster gere consumo e a capacidade no cluster. Para saber mais sobre-los e como configurá-los consulte [Este artigo](service-fabric-cluster-resource-manager-metrics.md)
- O Gestor de recursos Cluster funciona com as capacidades da gestão do serviço ferro. Para saber mais sobre essa integração, leia [Este artigo](service-fabric-cluster-resource-manager-management-integration.md)
- Para saber mais sobre como o Gestor de recursos Cluster gere e equilibra carga no cluster, consulte o artigo sobre [balanceamento de carga](service-fabric-cluster-resource-manager-balancing.md)
