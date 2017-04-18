<properties
 pageTitle="Sobre VMs com muitos cluster com Linux | Microsoft Azure"
 description="Obter informações de fundo e considerações para utilizar os tamanhos de cluster-a com um grau elevado H série e A8, A9, A10 e A11 para Linux VMs"
 services="virtual-machines-linux"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management"/>
<tags
ms.service="virtual-machines-linux"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-linux"
 ms.workload="infrastructure-services"
 ms.date="09/21/2016"
 ms.author="danlep"/>

# <a name="about-h-series-and-compute-intensive-a-series-vms"></a>Sobre a série de H e com muitos cluster VMs A série 

Eis as informações de fundo e algumas considerações para utilizar a mais recente série H Azure e os anteriores A8, A9, A10 e A11 tamanhos, também conhecido como *com muitos cluster* instâncias. Este artigo foca-se sobre como utilizar estes tamanhos para Linux VMs. Este artigo também está disponível para [VMs do Windows](virtual-machines-windows-a8-a9-a10-a11-specs.md).




[AZURE.INCLUDE [virtual-machines-common-a8-a9-a10-a11-specs](../../includes/virtual-machines-common-a8-a9-a10-a11-specs.md)]

## <a name="access-to-the-rdma-network"></a>Acesso à rede RDMA

Pode criar clusters de RDMA noutros Linux VMs que execute um dos seguintes suportadas distribuições Linux HPC e uma implementação MPI suportada para tirar partido da rede Azure RDMA. Consulte o artigo [configurar um cluster de Linux RDMA para executar as aplicações de MPI](virtual-machines-linux-classic-rdma-cluster.md) para opções de implementação e passos de configuração de exemplo.

* **As distribuições** - tem de implementar VMs do Linux do RDMA noutros SUSE Server Enterprise (SLES) ou com base em OpenLogic CentOS HPC imagens no Azure Marketplace. As seguintes imagens Marketplace suportam os controladores Linux RDMA necessários:

    * SLES 12 SP1 para HPC, SLES 12 SP1 para HPC (Premium)
    
    * SLES 12 para HPC, SLES 12 para HPC (Premium)
    
    * Com base em centOS 7.1 HPC
    
    * Com base em centOS HPC 6.5
    
    >[AZURE.NOTE]Para VMs série H, recomendamos que quer um SP1 de 12 SLES para imagem HPC ou imagem HPC 7.1 baseada em CentOS.
    >
    >Em imagens com base em CentOS HPC, atualizações de kernel estão desactivadas no ficheiro de configuração de **yum** . Isto acontece porque os controladores Linux RDMA são distribuídos como um pacote de rotações/minuto e atualizações do controlador poderão não funcionar se o kernel é atualizado.

* **MPI** - Intel MPI biblioteca 5. x

    Dependendo da imagem Marketplace escolher, instalação de licenciamento, separada, ou configuração da Intel MPI pode ser necessárias, da seguinte forma: 
    
    * **SLES 12 SP1 para imagem HPC** - instalar os pacotes de Intel MPI distribuídos na VM executando o seguinte comando:
    
            sudo rpm -v -i --nodeps /opt/intelMPI/intel_mpi_packages/*.rpm

    * **SLES 12 para imagem HPC** - tem de registar em separado para transferir e instalar Intel MPI. Consulte o [Guia de instalação Intel MPI biblioteca](https://software.intel.com/sites/default/files/managed/7c/2c/intelmpi-2017-installguide-linux.pdf).
    
    * **Imagens com base em centOS HPC** - Intel MPI 5.1 já está instalado.  

    Configuração do sistema adicionais é necessária para executar tarefas MPI no VMs agrupadas. Por exemplo, num cluster de VMs, é necessário estabelecer a fidedignidade entre os nós de cluster. Para definições típicas, consulte o artigo [configurar um cluster de Linux RDMA para executar MPI aplicações](virtual-machines-linux-classic-rdma-cluster.md).


## <a name="considerations-for-hpc-pack-and-linux"></a>Considerações para HPC Pack e Linux

Para o [Pacote de aos](https://technet.microsoft.com/library/jj899572.aspx), cluster HPC gratuito da Microsoft e a solução de gestão de tarefa, fornece uma opção utilizar as instâncias com muitos cluster com Linux. As versões mais recentes de suporte aos Pack 2012 R2 diversas Linux distribuições para executar calcular nós implementada no Azure VMs, gerido por um nó de cabeça do Windows Server. Connosco de cluster Linux RDMA noutros executar Intel MPI, aos Pack pode agendar e executar Linux MPI aplicações que acedam à rede RDMA. Para começar, consulte o artigo [Introdução ao Linux cluster nós num cluster aos Pack no Azure](virtual-machines-linux-classic-hpcpack-cluster.md).

## <a name="network-topology-considerations"></a>Considerações de topologia da rede

* Com capacidade de RDMA Linux VMs no Azure Eth1 está reservado RDMA tráfego de rede. Não altere quaisquer definições Eth1 ou quaisquer informações no ficheiro de configuração que fazem referência a esta rede. Eth0 está reservada para o tráfego de rede Azure normal.

* No Azure, IP sobre InfiniBand i (b) não é suportada. Apenas RDMA sobre i b é suportada.

## <a name="rdma-driver-updates-for-sles-12"></a>Atualizações de controlador RDMA de SLES 12

Depois de criar uma VM com base numa imagem SLES 12 aos, poderá ter de atualizar os controladores RDMA no VMs para a conectividade da rede RDMA. 

>[AZURE.IMPORTANT]Este passo é **necessário** para SLES 12 para implementações HPC VM em todas as regiões Azure. 
>Este passo não é necessário se implementar uma SP1 de 12 SLES para HPC, com base em CentOS HPC 7.1 ou com base em CentOS HPC 6.5 VM. 

Antes de atualizar os controladores, pare todos os processos de **zypper** ou qualquer processos que bloquear as bases de dados de repo SUSE na VM. Caso contrário, os controladores poderão não são atualizadas corretamente.  

Para atualizar os controladores Linux RDMA no cada VM, execute um dos seguintes conjuntos de comandos do Azure clip do seu computador cliente.

**SLES 12 para HPC VM aprovisionado no modelo de implementação clássico**

```
azure config mode asm

azure vm extension set <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

**SLES 12 para HPC VM aprovisionado no modelo de implementação de Gestor de recursos**

```
azure config mode arm

azure vm extension set <resource-group> <vm-name> RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1
```

>[AZURE.NOTE]Pode demorar algum tempo para instalar os controladores e o comando devolve sem saída. Após a atualização, o seu VM irá reiniciar e deve estar pronto para utilização em vários minutos.

### <a name="sample-script-for-driver-updates"></a>Exemplo de script existência de actualizações de controlador

Se tiver um cluster de SLES 12 para aos VMs, pode executar scripts a actualização do controlador através de todos os nós no seu cluster. Por exemplo, o seguinte script atualiza os controladores num cluster nó 8.

```

#!/bin/bash -x

# Define a prefix naming scheme for compute nodes, e.g., cluster11, cluster12, etc.

vmname=cluster

# Plug in appropriate numbers in the for loop below.

for (( i=11; i<19; i++ )); do

# For VMs created in the classic deployment model, use the following command in your script.

azure vm extension set $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

# For VMs created in the Resource Manager deployment model, use the following command in your script.

# azure vm extension set <resource-group> $vmname$i RDMAUpdateForLinux Microsoft.OSTCExtensions 0.1

done

```


## <a name="next-steps"></a>Próximos passos

* Para obter detalhes sobre a disponibilidade e preços dos tamanhos de cluster-a com um grau elevado, consulte o artigo [máquinas virtuais preços](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux).

* Para as capacidades de armazenamento e detalhes do disco, consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-linux-sizes.md).

* Para começar a implementar e utilizar com muitos cluster tamanhos com RDMA no Linux, consulte o artigo [configurar um cluster de Linux RDMA para executar MPI aplicações](virtual-machines-linux-classic-rdma-cluster.md).


