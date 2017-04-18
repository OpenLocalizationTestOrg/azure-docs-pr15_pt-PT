<properties
   pageTitle="Disponibilidade dos serviços de serviço ferro | Microsoft Azure"
   description="Descreve deteção falhas, activação pós-falha e recuperação para serviços"
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

# <a name="availability-of-service-fabric-services"></a>Disponibilidade dos serviços de ferro de serviço
Serviços de serviço ferro Azure podem ser com estado ou sem estado. Este artigo fornece uma descrição geral de como o serviço ferro mantém a disponibilidade de um serviço trabalho falhas.

## <a name="availability-of-service-fabric-stateless-services"></a>Disponibilidade dos serviços de estado do serviço ferro
Um serviço sem estado é um serviço de aplicação que não tem qualquer [estado persistente local](service-fabric-concepts-state.md).

Criação de um serviço sem estado requer que define uma contagem de instância e o que é o número de instâncias do serviço sem estado que deve estar em execução no cluster. Este é o número de cópias da lógica da aplicação que será criada uma instância do cluster. Aumentar o número de instâncias é a forma recomendada de escalar para cima um serviço sem estado.

Quando uma falha de é detectada em qualquer instância de um serviço sem estado, é criada uma nova instância no outro nó elegível no cluster.

## <a name="availability-of-service-fabric-stateful-services"></a>Disponibilidade dos serviços de estado do serviço ferro
Um serviço com estado tem algumas Estado associado. No serviço ferro, um serviço com estado é considerado um conjunto de réplicas. Cada réplica é uma instância do código do serviço de que tem uma cópia do Estado. Operações de leitura e escrita são executadas numa réplica (denominados a página principal). Alterações de estado a partir de escrita operações ficam de *replicadas* para várias outras réplicas (denominados secundários ativos). A combinação de principais e ativas réplicas secundárias é o conjunto de réplica do serviço.

Podem existir apenas uma réplica principal de manutenção de leitura e escrita pedidos, mas podem existir várias réplicas secundárias ativas. O número de réplicas secundários activas é configurável e um número mais alto de réplicas pode tolerar um maior número de falhas de hardware e software em simultâneo.

Em caso de uma falha (quando acede a réplica principal para baixo), serviço ferro faz com que um das réplicas secundárias ativas a nova réplica principal. Esta réplica secundária ativa já tem a versão actualizada de estado (através do *replicação*) e pode continuar a ler mais de processamento e operações de escrita.

Este conceito – de uma réplica a ser um principal ou ativo secundário – é conhecido como a função de réplica.

### <a name="replica-roles"></a>Funções de réplica
A função de uma réplica é utilizada para gerir o ciclo de vida do Estado a ser gerido pelo que a réplica. Pedidos de leitura de uma réplica cuja função é serviços principais. Também dos serviços pedidos de escrita ao atualizar o seu estado e de replicadas as alterações para os secundários ativos no seu conjunto de réplica. A função de um ativo secundário é receber alterações de estado que foi replicada a réplica principal e atualizar a respetiva vista do Estado.

>[AZURE.NOTE] Modelos de programação complicados como o [quadro intervenientes fiável](service-fabric-reliable-actors-introduction.md) ausente abstract o conceito de função de réplica do programador.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre os conceitos de ferro de serviço, consulte o seguinte:

- [Escalabilidade dos serviços de ferro de serviço](service-fabric-concepts-scalability.md)

- [Criar a partições serviços ferro de serviço](service-fabric-concepts-partitioning.md)

- [Definir e gerir o Estado](service-fabric-concepts-state.md)
