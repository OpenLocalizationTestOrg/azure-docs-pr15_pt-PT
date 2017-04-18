<properties
 pageTitle="Sobre VMs com muitos cluster com Windows | Microsoft Azure"
 description="Obter informações de fundo e considerações para utilizar os tamanhos de cluster-a com um grau elevado H série e A8, A9, A10 e A11 Azure para serviços VMs do Windows e na nuvem"
 services="virtual-machines-windows, cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sobre a série de H e com muitos cluster VMs A série

Eis as informações de fundo e algumas considerações para utilizar a mais recente série H Azure e as anteriores A8, A9, A10 e A11 instâncias, também conhecido como *com muitos cluster* instâncias. Este artigo foca-se sobre como utilizar estes instâncias para VMs do Windows. Este artigo também está disponível para [Linux VMs](virtual-machines-linux-a8-a9-a10-a11-specs.md).


[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA

Pode criar clusters de instâncias do Windows Server RDMA noutros e implementar uma das implementações MPI suportadas para tirar partido da rede Azure RDMA. Esta rede baixa latência, como sendo de alta débito está reservada para o tráfego MPI apenas.

* **Sistema operativo**
    * **Máquinas virtuais** - Windows Server 2012 R2, Windows Server 2012
    * **Serviços em nuvem** - Windows Server 2012 R2, Windows Server 2012, família SO do Windows Server 2008 R2 convidado

* **MPI** - Microsoft MPI (MS-MPI) 2012 R2 ou posterior, Intel MPI biblioteca 5. x

Implementações do MPI suportadas utilizam a interface do Microsoft rede direta para comunicar entre instâncias. Consulte o artigo [configurar um cluster de Windows RDMA com aos Pack para executar as aplicações de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md) e [tarefas com várias instâncias de utilização para executar as aplicações de mensagem prisma Interface (MPI) no Azure lote](../batch/batch-mpi.md) para opções de implementação e passos de configuração de exemplo.


>[AZURE.NOTE]No RDMA noutros com muitos cluster VMs, a extensão de HpcVmDrivers deve ser adicionada às VMs instalar controladores de dispositivo de rede do Windows que são necessários para conectividade RDMA. Na maioria das implementações, a extensão de HpcVmDrivers é adicionada automaticamente. Se precisar de adicionar a extensão de si, consulte o artigo [Gerir VM extensões](virtual-machines-windows-classic-manage-extensions.md).

## <a name="considerations-for-hpc-pack-and-windows"></a>Considerações para HPC Pack e Windows

Para o [Microsoft aos Pack](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuito da Microsoft e a solução de gestão de tarefa, não é necessária para utilizar as instâncias com muitos cluster com o Windows Server. No entanto, é uma opção Criar um cluster de computação no Azure para executar as aplicações de MPI baseados no Windows e outros cargas de trabalho HPC. AOS Pack 2012 R2 e versões posteriores incluem um ambiente do runtime MS MPI que pode utilizar a rede do Azure RDMA quando implementada no VMs RDMA noutros.

Para obter mais informações e listas de verificação para utilizar as instâncias com muitos cluster com aos Pack no Windows Server, consulte o artigo [configurar um cluster de Windows RDMA com aos Pack para executar as aplicações de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).




## <a name="next-steps"></a>Próximos passos

* Para obter detalhes sobre a disponibilidade e preços dos tamanhos de cluster-a com um grau elevado, consulte o artigo [máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows) e [preços de serviços em nuvem](https://azure.microsoft.com/pricing/details/cloud-services/).

* Para as capacidades de armazenamento e detalhes do disco, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

* Para começar a implementar e utilizar com muitos cluster instâncias com aos Pack no Windows, consulte o artigo [configurar um cluster de Windows RDMA com aos Pack para executar as aplicações de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md).

* Para obter informações sobre como utilizar instâncias de A8 e A9 para executar as aplicações de MPI com Azure lote, consulte o artigo [tarefas com várias instâncias de utilização para executar as aplicações de mensagem prisma Interface (MPI) no lote Azure](../batch/batch-mpi.md).
