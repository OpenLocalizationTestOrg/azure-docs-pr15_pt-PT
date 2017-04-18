<properties
   pageTitle="Diferenças entre serviços em nuvem e serviço ferro | Microsoft Azure"
   description="Uma descrição geral conceptual da migração de aplicações dos serviços em nuvem para ferro de serviço."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/19/2016"
   ms.author="vturecek"/>

# <a name="learn-about-the-differences-between-cloud-services-and-service-fabric-before-migrating-applications"></a>Saiba mais sobre as diferenças entre serviços em nuvem e serviço ferro antes de migrar aplicações.
Microsoft Azure Service ferro é a plataforma de aplicação próxima geração nuvem para aplicações distribuídas altamente dimensionáveis, altamente fiáveis. Apresenta-muitas funcionalidades novas para embalagem, implementação, atualizar e gerir aplicações de nuvem distribuído. 

Este é um guia de introdução a migrar aplicações dos serviços em nuvem para ferro de serviço. -Foca-se principalmente em arquitetura e diferenças de estrutura entre serviços em nuvem e ferro de serviço.
 
## <a name="applications-and-infrastructure"></a>Aplicações e infraestrutura

Uma diferença fundamental entre serviços em nuvem e serviço ferro é a relação entre VMs, das cargas de trabalho e aplicações. Aqui uma carga de trabalho é definida como o código de escrita de executar uma tarefa específica ou fornecer um serviço.
 
 - **Serviços em nuvem é de cerca de implementação de aplicações como VMs.** O código que escrito está intimamente ao forma para uma instância VM, tal como um Web ou uma função de trabalho. Para implementar uma carga de trabalho serviços em nuvem é implementar um ou mais ocorrências VM que executar a carga de trabalho. Não existe nenhuma separação de aplicações e VMs e, por isso, não existe nenhuma definição formal de uma aplicação. Pode ser considerada uma aplicação como um conjunto de instâncias Web ou função trabalhador dentro de uma implementação dos serviços em nuvem ou como uma implementação de serviços em nuvem inteira. Neste exemplo, uma aplicação é apresentada como um conjunto de instâncias de função.
 
![Aplicações de serviços na nuvem e topologia][1]

 - **Serviço ferro é de cerca de implementação de aplicações para VMs existentes ou computadores com o serviço ferro no Windows ou Linux.** Os serviços que escreve são completamente desacoplados de infraestrutura do subjacente, é recolhida ausente pela plataforma de aplicação de serviço ferro, para que pode ser implementada uma aplicação para vários ambientes. Uma carga de trabalho no serviço ferro chama um "serviço" e um ou mais serviços estão agrupados numa aplicação formal definidas pelo que é executada a plataforma de aplicação de serviço ferro. Podem ser implementadas várias aplicações a um único cluster de serviço ferro.
 
![Aplicações de serviço ferro e topologia][2]
 
Serviço ferro propriamente dito é uma camada de plataforma de aplicação que é executada no Windows ou Linux, Considerando que serviços em nuvem é um sistema para implementar gerido Azure VMs das cargas de trabalho anexadas.
O modelo de aplicação de serviço ferro tem um número das vantagens:

 - Horas de implementação rápida. Criação de instâncias VM pode ser demorado. No serviço ferro, VMs apenas são implementados, uma vez para formar um cluster que aloja a plataforma de aplicação de serviço ferro. A partir desse momento, pacotes de aplicações podem ser implementadas ao cluster muito rapidamente.
 - Densidade de alojamento. Em serviços na nuvem, um VM de função trabalhador aloja uma carga de trabalho. No serviço ferro, aplicações são separadas do VMs executá-los, que significa que pode implementar um grande número de aplicações para um pequeno número de VMs, que pode reduzir custos gerais para implementações maiores.
 - A ferro serviço plataforma pode executar qualquer parte que tem o Windows Server ou Linux máquinas, quer seja Azure ou no local. A plataforma fornece uma camada de produção sobre a infraestrutura de subjacente para que a sua aplicação pode ser executados em ambientes diferentes. 
 - Distribuído gestão de aplicações. Serviço ferro é uma plataforma que não só aloja aplicações distribuídas, mas também o ajuda a gerir o ciclo de vida independentemente dos existentes da VM alojamento ou máquina ciclo de vida.

## <a name="application-architecture"></a>Arquitetura de aplicação

A arquitetura de uma aplicação de serviços em nuvem normalmente inclui vários dependências do serviço externos, como o serviço Bus, tabela do Azure e armazenamento de BLOBs, SQL, Redis e outras pessoas para gerir o estado e os dados de uma aplicação e a comunicação entre o Web e funções de trabalho numa implementação serviços em nuvem. Um exemplo de um pedido de serviços em nuvem completo pode ter este aspeto:  

![Arquitetura de serviços em nuvem][9]

Aplicações de serviço ferro também podem optar por utilizar os serviços externos mesmo uma aplicação completa. Utilizar este exemplo arquitetura de serviços em nuvem, o caminho de migração mais simples dos serviços em nuvem para serviço ferro é substituir a implementação de serviços em nuvem com uma aplicação de serviço ferro, mantendo a arquitetura geral da mesma. As funções de trabalho e Web podem ser convertidas aos serviços sem estado do serviço ferro com as alterações de código mínimas.

![Arquitetura de serviço ferro após a migração simple][10]

Nesta fase, o sistema deve continuar a trabalhar igual antes. Tirar partido das funcionalidades de estado do serviço ferro, estado externo stores pode ser internalized como com estado dos serviços de onde aplicável. Este é mais complexo que uma migração simple das funções de trabalho e Web para serviços de estado de serviço ferro, como, é necessário escrever serviços personalizados que fornecem uma funcionalidade equivalente à aplicação como os serviços externos faziam anteriormente. Os benefícios da fazê-lo incluem: 

 - Remover dependências externas 
 - Unifying a implementação, gestão e atualização de modelos. 
 
Uma arquitetura de resultante de exemplo do internalizing estes serviços poderia ter este aspeto:

![Arquitetura de serviço ferro após a migração completa][11]

## <a name="communication-and-workflow"></a>Comunicação e fluxo de trabalho

De mais do que uma camada consistir em maior parte das aplicações de serviço na nuvem. Do mesmo modo, uma aplicação de serviço ferro é composta por mais de um serviço (normalmente muitos serviços). Dois modelos de comunicação comuns são direta comunicação e através de um armazenamento resistente externo.

### <a name="direct-communication"></a>Comunicação direta

Com a comunicação direta, podem comunicar camadas diretamente através de ponto final exposto pela cada camada. Nos ambientes sem estado como serviços em nuvem, este meios selecionar uma instância de uma função VM, quer aleatoriamente ou round robin a carga de saldo e estabelecer ligação ao respectivo ponto final diretamente.

![Serviços em nuvem direcionar comunicação][5]

 Comunicação direta é um modelo de comunicação comuns no serviço ferro. A chave diferença entre ferro de serviço e serviços em nuvem é que serviços em nuvem em ligar para uma VM, Considerando que no serviço ferro pode ligar a um serviço. Este é uma distinção importante algumas razões:

 - Serviços no serviço ferro não são vinculados aos VMs que alojam-las; Serviços de pode deslocar-se no cluster e, na verdade, esperados para deslocar-se por diversas razões: recurso balanceamento, activação pós-falha, actualizações de aplicação e infraestrutura e posicionamento ou para carregar restrições. Isto significa que pode alterar o endereço de uma instância de serviço em qualquer altura. 
 - Uma VM no serviço ferro pode alojar vários serviços, cada uma com os pontos finais exclusivos.

Serviço ferro fornece um mecanismo de deteção de serviço, denominado o serviço de atribuição de nomes, que podem ser utilizados para resolver os endereços de ponto final dos serviços. 

![Comunicação direta ferro de serviço][6]

### <a name="queues"></a>Filas

É um comuns mecanismo de comunicação entre camadas nos ambientes sem estado como serviços em nuvem utilizar uma fila de armazenamento externo para armazenar durável tarefas de trabalho a partir de uma camada para outra. Um cenário comum é uma camada de web envia tarefas a um Azure fila ou serviço Bus onde instâncias de função de trabalho podem descarregado e processar as tarefas.

![Comunicação de fila de serviços em nuvem][7]

O mesmo modelo de comunicação pode ser utilizado no serviço ferro. Isto pode ser útil quando uma aplicação de serviços em nuvem existente para o serviço ferro a migrar. 

![Comunicação direta ferro de serviço][8]
 
## <a name="next-steps"></a>Próximos passos

O caminho de migração mais simples dos serviços em nuvem para serviço ferro é substituir a implementação de serviços em nuvem com uma aplicação de serviço ferro, mantendo a arquitetura geral da sua aplicação aproximadamente iguais. O seguinte artigo fornece um guia para o ajudar a converter um Web ou uma função de trabalho para um serviço de estado do serviço ferro.

 - [Migração Simple: converter um Web ou uma função de trabalho para um serviço de estado do serviço ferro](./service-fabric-cloud-services-migration-worker-role-stateless-service.md)

<!--Image references-->
[1]: ./media/service-fabric-cloud-services-migration-differences/topology-cloud-services.png
[2]: ./media/service-fabric-cloud-services-migration-differences/topology-service-fabric.png
[5]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-direct.png
[6]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-direct.png
[7]: ./media/service-fabric-cloud-services-migration-differences/cloud-service-communication-queues.png
[8]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-communication-queues.png
[9]: ./media/service-fabric-cloud-services-migration-differences/cloud-services-architecture.png
[10]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-simple.png
[11]: ./media/service-fabric-cloud-services-migration-differences/service-fabric-architecture-full.png
