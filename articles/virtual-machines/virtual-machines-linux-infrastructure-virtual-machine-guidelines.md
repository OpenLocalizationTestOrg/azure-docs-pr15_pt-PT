<properties
    pageTitle="Diretrizes de máquinas virtuais Linux | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para implementar máquinas virtuais de Linux no Azure"
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

# <a name="virtual-machines-guidelines"></a>Diretrizes de máquinas virtuais

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo foca-se em Noções sobre os passos necessários planeamento para criar e gerir máquinas virtuais (VMs) no seu ambiente do Azure.

## <a name="implementation-guidelines-for-vms"></a>Diretrizes de implementação para VMs
Decisões:

- Quantos VMs necessitam do seu várias camadas de aplicação e componentes da sua infraestrutura?
- Quais os recursos memória e CPU o cada VM necessitam e quais são os requisitos de armazenamento?

Tarefas:

- Defina as cargas de trabalho para a aplicação e os recursos que requerem os VMs.
- Alinhe os pedidos de recursos para cada VM com o tipo de tamanho e armazenamento VM adequado.
- Defina os grupos de recursos para os diferentes camadas e componentes da sua infraestrutura.
- Defina o seu Convenção de nomenclatura VM.
- Crie o seu VMs utilizando o clip do Azure, web portal, ou com os modelos de Gestor de recursos.

## <a name="virtual-machines"></a>Máquinas virtuais

Uma das principais componentes do seu ambiente do Azure é provável que VMs. Este é sempre que executar as aplicações, bases de dados, serviços de autenticação, etc.

É importante compreender os [tamanhos VM diferentes](virtual-machines-linux-sizes.md) para dimensionar corretamente o seu ambiente a partir de um desempenho e perspetiva de custo. Se não tiver o seu VMs núcleos CPU ou memória suficiente, o desempenho da sua aplicação sofre, independentemente de como bem são concebido e desenvolvido. Reveja as sugestões cargas de trabalho para cada série VM como ponto de partida como decidir qual VM para utilizar para cada componente sua infraestrutura de tamanho. Pode [alterar o tamanho de uma VM](virtual-machines-linux-change-vm-size.md) após a implementação.

Armazenamento reproduz uma função de chave de desempenho VM. Pode utilizar armazenamento padrão, que utiliza discos girar normal, ou armazenamento Premium para alta e/s das cargas de trabalho e desempenho de pico, que utilize SSD discos. Como com o tamanho da memória virtual, existem são o custo considerações para selecionar o suporte de armazenamento. Pode ler o [artigo de diretrizes de infraestrutura de armazenamento](virtual-machines-linux-infrastructure-storage-solutions-guidelines.md) para compreender como estruturar o armazenamento adequado para um desempenho ideal da sua VMs.


## <a name="resource-groups"></a>Grupos de recursos
Componentes como VMs logicamente são agrupados para facilitar a gestão e utilizar [Grupos de recursos do Azure](../azure-resource-manager/resource-group-overview.md)a manutenção. Ao utilizar grupos de recursos, pode criar, gerir e monitorizar todos os recursos que compõem uma determinada aplicação. Também pode implementar [os controlos de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) para conceder acesso a outras pessoas na sua equipa para apenas os recursos de que necessitam. Demorar a planeie iniciativas seus grupos de recursos e atribuições de funções. Existem diferentes abordagens realmente estruturar e implementar a grupos de recursos, por isso, certifique-se de que leia o [artigo de diretrizes de grupos de recurso](virtual-machines-linux-infrastructure-resource-groups-guidelines.md) para compreender melhor como construir a sua VMs.


## <a name="templates"></a>Modelos 
Pode criar modelos, definidos pelo declarativos ficheiros JSON, para criar o seu VMs. Modelos normalmente também a criar o armazenamento necessário redes, interfaces de rede, endereçamento IP, etc., juntamente com os VMs próprios. Pode utilizar modelos para criar ambientes consistentes e reproduzíveis para o desenvolvimento e testes efeitos a replicação facilmente ambientes de produção e vice versa. Pode ler mais sobre a [criação e utilização de modelos](../azure-resource-manager/resource-group-overview.md#template-deployment) para compreender como os pode utilizar para criar e implementar o seu VMs.


## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 