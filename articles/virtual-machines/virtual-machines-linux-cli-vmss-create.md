<properties
    pageTitle="O que são escala VM define? | Microsoft Azure"
    description="Saiba mais sobre os conjuntos de escala VM."
    keywords="conjuntos de escala de máquina virtual máquina de virtual Linux," 
    services="virtual-machines-linux"
    documentationCenter=""
    authors="gatneil"
    manager="madhana"
    editor="tysonn"
    tags="azure-resource-manager" />

<tags
    ms.service="virtual-machine-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/24/2016"
    ms.author="gatneil"/>

# <a name="what-are-virtual-machine-scale-sets"></a>O que são máquina virtual escala define?

Conjuntos de escala de máquina virtual permitem-lhe gerir vários VMs como um conjunto. De alto nível, conjuntos de escala têm as seguintes vantagens e desvantagens:

Profissionais:

1. Disponibilidade de alta. Cada conjunto de escala coloca a suas VMs num conjunto de disponibilidade com 5 de falhas de domínios (FDs) e 5 de atualização de domínios (UDs) para se certificar de disponibilidade (para obter mais informações sobre FDs e UDs, ver a [disponibilidade VM](./virtual-machines-linux-manage-availability.md)). 
2. Fácil integração com o Azure Balanceador de carga e aplicação Gateway.
3. Fácil integração com o Azure Autoscale.
4. Simplificar a implementação, gestão e limpar de VMs.
5. Suporta comuns versões do Windows e Linux, bem como imagens personalizadas.

Desvantagens:

1. Não é possível anexar discos de dados para instâncias VM num conjunto de escala. Em vez disso, tem de utilizar armazenamento de BLOBs, ficheiros de Azure, Azure tabelas ou outra solução de armazenamento.

## <a name="quick-create-using-azure-cli"></a>Introdução-criar utilizando o clip do Azure

[AZURE.INCLUDE [cli-vmss-quick-create](../../includes/virtual-machines-linux-cli-vmss-quick-create-include.md)]

## <a name="next-steps"></a>Próximos passos

Para obter informações gerais, consulte o artigo a [página de destino principal para conjuntos de escala](https://azure.microsoft.com/services/virtual-machine-scale-sets/).

Para obter documentação mais, consulte o artigo [conjuntos de página de documentação principal para escala](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md).

Por exemplo modelos de Gestor de recursos utilizando conjuntos de escala, procure "vmss" os [modelos de guia de introdução do Azure github repo](https://github.com/Azure/azure-quickstart-templates).

