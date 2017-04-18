<properties
    pageTitle="Ligar Windows VMs num serviço de nuvem | Microsoft Azure"
    description="Ligar máquinas virtuais de Windows criadas com o modelo clássico de implementação para um serviço na nuvem Azure ou rede virtual."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>

# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Ligar máquinas virtuais de Windows criadas com o modelo clássico de implementação com um serviço de rede ou na nuvem virtual

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Máquinas virtuais de Windows criadas com o modelo de implementação clássica são sempre colocadas num serviço de nuvem. O serviço de nuvem age como um contentor e fornece um nome exclusivo de DNS público, um endereço IP público e um conjunto de pontos finais para aceder a máquina virtual através da Internet. O serviço de nuvem pode ser numa rede virtual, mas que não é um requisito. Também pode [Ligar máquinas virtuais de Linux com um serviço de rede ou na nuvem virtual](virtual-machines-linux-classic-connect-vms.md).

Se não uma rede virtual num serviço na nuvem, chama-se um serviço em nuvem *autónomo* . As máquinas virtuais num serviço de nuvem autónomo só podem comunicar com outros máquinas virtuais utilizando nomes DNS públicos das outras máquinas virtuais e o tráfego viaja através da Internet. Se um serviço na nuvem estiver numa rede virtual, as máquinas virtuais nesse serviço na nuvem, pode comunicar com todas as outras máquinas virtuais na rede virtual sem enviar qualquer tráfego através da Internet.

Se colocar o seu máquinas virtuais no serviço de nuvem do mesmo autónomo, ainda pode utilizar balanceamento de carga e conjuntos de disponibilidade. Para obter detalhes, consulte o artigo [balanceamento de carga em máquinas virtuais](virtual-machines-windows-load-balance.md) e [Gerir a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md). No entanto, não pode organizar as máquinas virtuais em sub-redes ou ligar um serviço em nuvem autónomo à sua rede no local. Eis um exemplo:

[AZURE.INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Próximos passos

Depois de criar uma máquina virtual, é uma boa ideia para [Adicionar um disco de dados](virtual-machines-windows-classic-attach-disk.md) para que os seus serviços e das cargas de trabalho tem uma localização para armazenar dados. 