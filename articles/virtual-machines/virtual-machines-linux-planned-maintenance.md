<properties
    pageTitle="Manutenção planeada para Linux VMs | Microsoft Azure"
    description="Compreender a que a manutenção planeada Azure é e como afeta-máquinas virtuais Linux em execução no Azure"
    services="virtual-machines-linux"
    documentationCenter=""
    authors="drewm"
    manager="timlt"
    editor=""
    tags="azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="04/26/2016"
    ms.author="drewm"/>

# <a name="planned-maintenance-for-linux-virtual-machines-in-azure"></a>Manutenção planeada para máquinas virtuais de Linux no Azure

Compreenda o que manutenção planeada Azure é e como pode afectar a disponibilidade de máquinas virtuais Linux. Este artigo também está disponível para [máquinas de virtuais do Windows](virtual-machines-windows-planned-maintenance.md). 

Este artigo fornece um fundo do processo de manutenção planeada Azure. Se são pretendem resolva problemas em que a VM reiniciado, pode [Ler neste blogue mensagem com detalhes sobre a visualização VM reinicie registos](https://azure.microsoft.com/blog/viewing-vm-reboot-logs/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

## <a name="why-azure-performs-planned-maintenance"></a>Por que motivo o Azure executa a manutenção planeada

Microsoft Azure executa periodicamente atualizações em todo o mundo para melhorar a fiabilidade, desempenho e segurança subjacente ao máquinas virtuais infraestrutura do anfitrião. Muitos destas atualizações são executados sem qualquer impacto ao seu máquinas virtuais ou serviços em nuvem, incluindo a preservação de memória atualizações.

No entanto, algumas actualizações requerem um reinício a sua máquinas virtuais para aplicar as atualizações necessárias a infraestrutura do. As máquinas virtuais são encerrar enquanto correcção estamos a infraestrutura de e, em seguida, as máquinas virtuais são reiniciadas.

Tenha em atenção que não existem dois tipos de manutenção que podem ter impacto a disponibilidade de máquinas virtuais: planeados e não. Esta página descreve como Microsoft Azure executa a manutenção planeada. Para mais informações sobre manutenção imprevistas, consulte o artigo [entender planeado versus manutenção imprevistas](virtual-machines-linux-manage-availability.md).

[AZURE.INCLUDE [virtual-machines-common-planned-maintenance](../../includes/virtual-machines-common-planned-maintenance.md)]
