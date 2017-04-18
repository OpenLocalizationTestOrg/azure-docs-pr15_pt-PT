<properties
 pageTitle="Opções do cluster Linux aos Pack na nuvem | Microsoft Azure"
 description="Saiba mais sobre opções com o Microsoft aos Pack para criar e gerir um Linux alto desempenho computação cluster (HPC) na nuvem Azure"
 services="virtual-machines-linux,cloud-services"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-an-hpc-cluster-in-azure-for-linux-workloads"></a>Opções com aos Pack para criar e gerir um cluster HPC no Azure para Linux cargas de trabalho

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo foca-se em Opções para utilizar o pacote de para executar Linux cargas de trabalho. Também existem opções para executar o [Windows das cargas de trabalho com aos Pack](virtual-machines-windows-hpcpack-cluster-options.md).

## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Executar um cluster de aos Pack no Azure VMs

### <a name="azure-templates"></a>Modelos do Azure


* (Marketplace) [Cluster de aos Pack para Linux cargas de trabalho](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterlinuxcn/)

* (Guia de introdução) [Criar um cluster HPC com Linux calcular nós](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-linux-cn)


### <a name="powershell-deployment-script"></a>Script de implementação do PowerShell

* [Criar um cluster de Linux HPC com o script de implementação aos Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Tutoriais

* [Tutorial: Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md)

* [Tutorial: Executar NAMD com o Microsoft aos Pack na Linux calcular nós no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)

* [Tutorial: Executar OpenFOAM com o Microsoft aos Pack num cluster Linux RDMA no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Tutorial: Executar ESTRELA-CCM + com o Microsoft aos Pack num RDMA Linux cluster no Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)

### <a name="cluster-management"></a>Gestão de cluster

* [Submeter tarefas a um cluster de aos Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestão de projectos aos Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="create-rdma-clusters-for-mpi-workloads"></a>Criar clusters de RDMA para das cargas de trabalho MPI

* [Tutorial: Executar OpenFOAM com o Microsoft aos Pack num cluster Linux RDMA no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)

* [Configurar um cluster de Linux RDMA para executar as aplicações de MPI](virtual-machines-linux-classic-rdma-cluster.md)

