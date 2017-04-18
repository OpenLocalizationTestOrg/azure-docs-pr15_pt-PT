<properties
 pageTitle="Opções do Windows aos Pack cluster na nuvem | Microsoft Azure"
 description="Saiba mais sobre opções com o Microsoft aos Pack para criar e gerir um desempenho elevado do Windows computação cluster (HPC) na nuvem Azure"
 services="virtual-machines-windows,cloud-services,batch"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-windows"
 ms.workload="big-compute"
 ms.date="09/26/2016"
 ms.author="danlep"/>

# <a name="options-with-hpc-pack-to-create-and-manage-a-windows-hpc-cluster-in-azure"></a>Opções com aos Pack para criar e gerir um cluster de do Windows Azure

[AZURE.INCLUDE [virtual-machines-common-hpcpack-cluster-options](../../includes/virtual-machines-common-hpcpack-cluster-options.md)]

Este artigo foca-se em Opções para criar pacote de aos clusters para executar das cargas de trabalho do Windows. Também existem opções para criar clusters para executar [Linux das cargas de trabalho com aos Pack](virtual-machines-linux-hpcpack-cluster-options.md).


## <a name="run-an-hpc-pack-cluster-in-azure-vms"></a>Executar um cluster de aos Pack no Azure VMs

### <a name="azure-templates"></a>Modelos do Azure

* (Marketplace) [Cluster de aos Pack para das cargas de trabalho do Windows](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/)

* (Marketplace) [Cluster de aos Pack para das cargas de trabalho do Excel](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterexcelcn/)

* (Guia de introdução) [Criar um cluster HPC](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster)

* (Guia de introdução) [Criar um cluster HPC com imagem de nó cluster personalizado](https://github.com/Azure/azure-quickstart-templates/tree/master/create-hpc-cluster-custom-image)

### <a name="azure-vm-images"></a>Imagens VM Azure

* [Pacote HPC no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/)

* [Nó de cluster aos Pack no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodeonwindowsserver2012r2/)

* [Pacote aos calcular nó com o Excel no Windows Server 2012 R2](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2computenodewithexcelonwindowsserver2012r2/)



### <a name="powershell-deployment-script"></a>Script de implementação do PowerShell

* [Criar um cluster HPC com o script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md)

### <a name="tutorials"></a>Tutoriais

* [Tutorial: Começar a trabalhar com um cluster de aos Pack no Azure para executar o Excel e SOA das cargas de trabalho](virtual-machines-windows-excel-cluster-hpcpack.md)



### <a name="manual-deployment-with-the-azure-portal"></a>Implementação manual com o portal do Azure

* [Configurar o nó cabeça de um cluster de pacote a uma VM Azure](virtual-machines-windows-hpcpack-cluster-headnode.md)

### <a name="cluster-management"></a>Gestão de cluster

* [Gerir cluster nós num cluster aos Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md)

* [Aumentar e diminuir Azure cluster recursos num cluster aos Pack](virtual-machines-windows-classic-hpcpack-cluster-node-autogrowshrink.md)

* [Submeter tarefas a um cluster de aos Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md)

* [Gestão de projectos aos Pack](https://technet.microsoft.com/library/jj899585.aspx)


## <a name="add-worker-role-nodes-to-an-hpc-pack-cluster"></a>Adicionar nós de função de trabalho a um cluster de aos Pack


* [Rajada instâncias do Azure trabalhador com aos Pack](https://technet.microsoft.com/library/gg481749.aspx)

* [Tutorial: Configurar um cluster de híbrido com aos Pack no Azure](../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md)

* [Adicionar nós do Azure "rajada" para um nó de cabeça aos Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-burst.md)


## <a name="integrate-with-azure-batch"></a>Integrar com o lote Azure 

* [Rajada lote Azure com HPC Pack](https://technet.microsoft.com/library/mt612877.aspx)

## <a name="create-rdma-clusters-for-mpi-workloads"></a>Criar clusters de RDMA para das cargas de trabalho MPI

* [Configurar um cluster de Windows RDMA com aos Pack para executar as aplicações de MPI](virtual-machines-windows-classic-hpcpack-rdma-cluster.md)
