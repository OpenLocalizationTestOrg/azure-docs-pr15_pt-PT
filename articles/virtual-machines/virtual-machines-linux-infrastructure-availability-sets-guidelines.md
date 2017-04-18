<properties
    pageTitle="Disponibilidade definir diretrizes | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para implementar conjuntos de disponibilidade nos serviços de infraestrutura Azure."
    documentationCenter=""
    services="virtual-machines-linux"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="availability-sets-guidelines"></a>Diretrizes de conjuntos de disponibilidade

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo foca-se em Noções sobre os passos necessários planeamento para conjuntos de disponibilidade garantir que o seu permanece aplicações acessíveis durante eventos planeados ou não planeados.

## <a name="implementation-guidelines-for-availability-sets"></a>Diretrizes de implementação para conjuntos de disponibilidade

Decisões:

- Quantos conjuntos de disponibilidade precisa para as funções e camadas de vários na sua infraestrutura de aplicação?

Tarefas:

- Defina o número de VMs de cada camada de aplicação que necessita.
- Determine se necessita de ajustar o número de falhas ou atualizar domínios para ser utilizado para a sua aplicação.
- Definir os conjuntos de disponibilidade necessário utilizar o seu Convenção de nomenclatura e o que VMs residem na-los. Uma VM só pode residem na disponibilidade de um conjunto. 

## <a name="availability-sets"></a>Conjuntos de disponibilidade

No Azure, máquinas virtuais (VMs) podem ser colocadas para um agrupamento lógico denominado um conjunto de disponibilidade. Quando cria VMs dentro de um conjunto de disponibilidade, a plataforma Azure distribui o posicionamento desses VMs pela infraestrutura de subjacente. Deverá haver um evento de manutenção planeada para a plataforma Azure ou um hardware subjacente / falhas de infraestrutura, a utilização de conjuntos de disponibilidade garantem que pelo menos uma VM continuará a ser executado.

Como prática recomendada, aplicações devem não se encontram uma única VM. Um conjunto de disponibilidade que contém uma única VM não ganhar qualquer proteção contra planeados ou não planeados eventos dentro da plataforma Azure. O [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines) necessita de dois ou mais VMs dentro de uma disponibilidade definido para permitir que a distribuição dos VMs através de infraestrutura do subjacente.

A infraestrutura subjacente no Azure é dividida em actualizar domínios e de falhas. Estes domínios são definidos por que anfitriões partilhar um ciclo de atualização comuns, ou partilhar semelhantes infraestrutura física como power e funcionamento em rede. Azure distribui automaticamente o seu VMs dentro de uma disponibilidade definido ao longo de domínios para manter a disponibilidade e tolerância a falhas. Dependendo do tamanho da sua aplicação e o número de VMs dentro de um conjunto de disponibilidade, pode ajustar o número de domínios que pretende utilizar. Pode ler mais sobre como [Gerir disponibilidade e a utilização de domínios de atualização e falhas](virtual-machines-linux-manage-availability.md).

Ao estruturar sua infraestrutura de aplicação, também deve planear saída as camadas de aplicação para utilizar. Conjuntos de grupo VMs que servem a mesma finalidade para disponibilidade, tal como uma disponibilidade configurar para a sua VMs front-end a executar o nginx ou Apache. Crie uma disponibilidade separada configurar para a sua VMs back-end a executar o MongoDB ou MySQL. O objetivo é assegurar que cada componente da sua aplicação está protegida por um conjunto de disponibilidade e pelo menos uma vez instância sempre continuará a ser executada.

Podem ser utilizadas balanceadores de carga à frente de cada camada de aplicação para trabalhar juntamente com um conjunto de disponibilidade e certifique-se de que o tráfego pode sempre ser encaminhado para uma instância em execução. Sem um balanceador de carga, seu VMs podem continuar a trabalhar rapidamente ao longo de eventos de manutenção planeados e não, mas os utilizadores finais poderá não conseguir resolvê-las se a VM principal não estiver disponível.


## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 