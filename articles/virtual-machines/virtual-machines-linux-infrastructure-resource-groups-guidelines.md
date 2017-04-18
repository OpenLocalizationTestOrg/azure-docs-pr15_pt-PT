<properties
    pageTitle="Diretrizes de grupos de recursos | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para implementar o recurso grupos nos serviços de infraestrutura Azure."
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

# <a name="azure-resource-group-guidelines"></a>Diretrizes de grupo de recursos Azure

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-intro](../../includes/virtual-machines-linux-infrastructure-guidelines-intro.md)] 

Este artigo foca-se em Noções básicas sobre como logicamente construir o seu ambiente e agrupar todos os componentes em grupos de recursos.


## <a name="implementation-guidelines-for-resource-groups"></a>Diretrizes de implementação para grupos de recursos

Decisões:

- Vai construir grupos de recursos aos componentes principais do infraestrutura ou ao implementação da aplicação completo?
- É necessário restringir o acesso a grupos de recursos a utilizar os controlos de acesso baseado em funções?

Tarefas:

- Defina o que infraestrutura componentes principais e dedicado grupos de recursos que necessita.
- Reveja como implementar modelos de Gestor de recursos para implementações consistentes e reproduzíveis.
- Defina que funções de acesso do utilizador que necessita para controlar o acesso a grupos de recursos.
- Crie o conjunto de grupos de recursos utilizando Convenção de nomenclatura. Pode utilizar o clip do Azure ou portal.


## <a name="resource-groups"></a>Grupos de recursos

No Azure, logicamente agrupar recursos relacionados, tais como contas de armazenamento, redes virtuais e máquinas virtuais (VMs) para implementar, gerir e mantê-las como uma única entidade. Esta abordagem torna mais fácil para implementação enquanto mantém todos os recursos relacionados em conjunto a partir de uma perspetiva de gestão de aplicações ou para conceder acesso a esse grupo de recursos a outras pessoas. Para uma compreensão mais abrangente de grupos de recursos, pode obter um [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

Uma funcionalidade chave para grupos de recursos é a capacidade de criar o seu ambiente com um ficheiro JSON declara o armazenamento de rede, e calcular recursos. Também pode definir qualquer configurações para aplicar ou scripts personalizados relacionados. Ao utilizar estes modelos JSON, criar implementações consistentes e reproduzíveis para as suas aplicações. Esta abordagem permite-lhe criar um ambiente em desenvolvimento e, em seguida, utilizar esse mesmo modelo para criar uma implementação de produção ou vice versa. Para compreender melhor sobre como utilizar modelos, leia [o tutorial de modelo](../resource-manager-template-walkthrough.md) orienta-o através de cada passo da criação de um modelo de JSON.

Existem duas abordagens diferentes que pode seguir ao estruturar o seu ambiente com grupos de recursos:

- Grupos de recursos para cada implementação da aplicação que combina a contas de armazenamento, redes virtuais e sub-redes, VMs, carregar balanceadores, etc.
- Grupos de recursos centralizada que contêm as suas contas funcionamento em rede e sub-redes ou armazenamento virtuais core. As suas aplicações, em seguida, são os seus próprios grupos de recursos que contenham apenas VMs, balanceadores de carga, interfaces de rede, etc.

À medida que escala de saída, criar grupos de recursos centralizada para o seu funcionamento em rede e sub-redes virtual torna mais fácil criar ligações para opções de conectividade de implementações rede cruzada local. É a abordagem alternativa para cada aplicação para que os seus próprios rede virtual que necessita de configuração e manutenção. [Controlos de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) fornece uma maneira granular para controlar o acesso a grupos de recursos. Para as aplicações de produção, pode controlar os utilizadores que podem aceder aos recursos ou para os recursos de infraestrutura de core pode limitar apenas os técnicos de infraestrutura para trabalhar com os mesmos. Os proprietários de aplicação apenas tem acesso a componentes da aplicação no seu grupo de recursos e não o essencial Azure infraestrutura do seu ambiente. Como estruturar o seu ambiente, considere os utilizadores que requerem acesso aos recursos e estruturar os grupos de recursos, consoante adequado. 


## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [virtual-machines-linux-infrastructure-guidelines-next-steps](../../includes/virtual-machines-linux-infrastructure-guidelines-next-steps.md)] 