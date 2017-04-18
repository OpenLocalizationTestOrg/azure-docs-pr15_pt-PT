<properties
   pageTitle="Script PowerShell para implementar cluster Linux HPC | Microsoft Azure"
   description="Executar um script PowerShell para implementar um cluster de Linux aos Pack em máquinas virtuais do Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-linux"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-linux-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um Linux alto desempenho computação cluster (HPC) com o script de implementação aos Pack IaaS

Execute a implementação de aos Pack IaaS script PowerShell para implementar um cluster HPC completo para Linux das cargas de trabalho em máquinas virtuais do Azure. Cluster consiste em nó de cabeça façam parte de um Active Directory que executem o Windows Server e Microsoft aos Pack e nós de cluster que execute um das distribuições Linux suportadas aos Pack. Se pretende implementar um cluster de pacote das cargas de trabalho do Azure para Windows, consulte o artigo [criar um cluster de do Windows com o script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md). Também pode utilizar um modelo de Gestor de recursos do Azure para implementar um cluster de aos Pack. Por exemplo, consulte o artigo [criar um cluster HPC com Linux calcular nós](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-linux-cn/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-file"></a>Ficheiro de configuração de exemplo

O ficheiro de configuração seguinte cria um novo controlador de domínio e floresta de domínio e implementa um cluster de pacote aos quais tem 1 nó cabeça com bases de dados locais e 10 nós de cluster Linux. Todos os serviços na nuvem são criados diretamente na localização este asiático. Os nós de cluster Linux são criados no 2 serviços em nuvem e 2 armazenamento contas (ou seja, _MyLnxCN 0001_ para _MyLnxCN 0005_ no _MyLnxCNService01_ e _mylnxstorage01_) e _MyLnxCN 0006_ para _MyLnxCN 0010_ no _MyLnxCNService02_ e _mylnxstorage02_. Os nós de cluster são criados a partir de uma imagem de Linux OpenLogic CentOS versão 7.0. 

Substitua os seus próprios valores para o nome da sua subscrição e os nomes de conta e serviço.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>NewDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
    <DomainController>
      <VMName>MyDCServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>Large</VMSize>
    </DomainController>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <LinuxComputeNodes>
    <VMNamePattern>MyLnxCN-%0001%</VMNamePattern>
    <ServiceNamePattern>MyLnxCNService%01%</ServiceNamePattern>
    <MaxNodeCountPerService>5</MaxNodeCountPerService>
    <StorageAccountNamePattern>mylnxstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>10</NodeCount>
    <ImageName>5112500ae3b842c8b9c604889f8753c3__OpenLogic-CentOS-70-20150325 </ImageName>
  </LinuxComputeNodes>
</IaaSClusterConfig>
```
## <a name="troubleshooting"></a>Resolução de problemas

* **Erro "Não existe VNet"** - se de que executar o script de implementação aos Pack IaaS para implementar vários clusters no Azure em simultâneo numa subscrição, um ou mais implementações podem falhar com o erro "VNet *VNet\_nome* não existe".
Se este erro ocorre, voltar a executar o script para a implementação falha.

* **Problema ao aceder à Internet da rede virtual Azure** - se criar um cluster de aos Pack com um novo controlador de domínio utilizando o script de implementação ou manualmente promove um nó cabeça VM controlador de domínio, poderá ter problemas ao VMs na rede virtual Azure ligar à Internet. Isto pode ocorrer se um servidor de DNS reencaminhador é configurado automaticamente no controlador de domínio e este servidor de DNS reencaminhador não resolve corretamente.

    Para contornar este problema, inicie sessão no controlador de domínio e quer remover a definição de configuração de reencaminhador ou configurar um servidor DNS reencaminhador válida. Para executar esta tarefa, no Gestor de servidor, clique em **Ferramentas de** >
    **DNS** para abrir o Gestor de DNS e, em seguida, faça duplo clique em **reencaminhadores**.
    
## <a name="next-steps"></a>Próximos passos

* Consulte o artigo [Introdução ao Linux cluster nós num cluster no Azure aos Pack](virtual-machines-linux-classic-hpcpack-cluster.md) para obter informações sobre suportadas Linux distribuições, mover dados e submeter as tarefas para um cluster de aos Pack com Linux calcular nós.
* Para a tutoriais que utilizar o script para um cluster de criar e executar uma carga de trabalho Linux HPC, consulte:
    * [Execute NAMD com o Microsoft aos Pack em nós de cluster de Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-namd.md)
    * [Execute OpenFOAM com o Microsoft aos Pack em nós de cluster de Linux no Azure](virtual-machines-linux-classic-hpcpack-cluster-openfoam.md)
    * [Executar ESTRELA-CCM + com o Microsoft aos Pack no Linux calcular nós no Azure](virtual-machines-linux-classic-hpcpack-cluster-starccm.md)
