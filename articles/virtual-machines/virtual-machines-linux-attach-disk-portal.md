<properties
    pageTitle="Anexar um disco de dados a uma VM Linux | Microsoft Azure"
    description="Como anexar o disco de dados nova ou existente a uma VM Linux no portal do Azure utilizando o modelo de implementação do Gestor de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/06/2016"
    ms.author="cynthn"/>

# <a name="how-to-attach-a-data-disk-to-a-linux-vm-in-the-azure-portal"></a>Como anexar um disco de dados a uma VM Linux no portal do Azure

Este artigo mostra-lhe como anexar discos novos e existentes para uma máquina de virtual Linux através do portal do Azure. Também pode [anexar um disco de dados para uma VM do Windows no portal do Azure](virtual-machines-windows-attach-disk-portal.md). Antes de fazer isto, reveja estas sugestões:

- O tamanho da máquina virtual controlos quantos discos de dados pode anexar. Para obter detalhes, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).
- Para utilizar o armazenamento de Premium, terá uma máquina de virtual DS série ou série s. Pode utilizar discos a partir de contas de armazenamento Premium e padrão com estes máquinas virtuais. Armazenamento de Premium está disponível em determinados regiões. Para obter mais detalhes, consulte o artigo [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).
- Discos ligados a máquinas virtuais são realmente. vhd ficheiros numa conta de armazenamento Azure. Para obter detalhes, consulte o artigo [sobre discos e VHDs para máquinas virtuais](virtual-machines-linux-about-disks-vhds.md).
- Para um novo disco, não precisa de criar primeiro, porque o Azure cria-lo quando anexa-lo.
- Para um disco existente, o ficheiro. vhd tem de estar disponível numa conta de armazenamento Azure. Pode utilizar um VHD que já lá, se não está ligado para outra máquina virtual ou carregar a sua própria. vhd do ficheiro para a conta de armazenamento.


[AZURE.INCLUDE [virtual-machines-common-attach-disk-portal](../../includes/virtual-machines-common-attach-disk-portal.md)]

## <a name="next-steps"></a>Próximos passos

Depois do disco for adicionado, tem de prepará-lo para utilização. Consulte o artigo no sistema operativo da máquina virtual: "como: iniciar um novo disco de dados no Linux" neste [artigo](virtual-machines-linux-classic-attach-disk.md#how-to-initialize-a-new-data-disk-in-linux).
