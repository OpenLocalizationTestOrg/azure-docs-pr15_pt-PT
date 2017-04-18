<properties
   pageTitle="Script PowerShell para implementar cluster Windows HPC | Microsoft Azure"
   description="Executar um script PowerShell para implementar um cluster de Windows aos Pack em máquinas virtuais do Azure"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""
   tags="azure-service-management,hpc-pack"/>
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="big-compute"
   ms.date="07/07/2016"
   ms.author="danlep"/>

# <a name="create-a-windows-high-performance-computing-hpc-cluster-with-the-hpc-pack-iaas-deployment-script"></a>Criar um cluster de computação de alto desempenho (HPC) do Windows com o script de implementação aos Pack IaaS

Execute a implementação de aos Pack IaaS script PowerShell para implementar um cluster HPC completo para das cargas de trabalho do Windows em máquinas virtuais do Azure. Cluster consiste em nó de cabeça façam parte de um Active Directory que executem o Windows Server e Microsoft aos Pack e janelas adicionais calcular recursos que especificar. Se pretende implementar um cluster de aos Pack no Azure para Linux cargas de trabalho, consulte o artigo [criar um cluster de Linux com o script de implementação aos Pack IaaS](virtual-machines-linux-classic-hpcpack-cluster-powershell-script.md). Também pode utilizar um modelo de Gestor de recursos do Azure para implementar um cluster de aos Pack. Para obter exemplos, consulte o artigo [criar um cluster HPC](https://azure.microsoft.com/documentation/templates/create-hpc-cluster/) e [criar um cluster HPC com um personalizado calcular imagem nó](https://azure.microsoft.com/documentation/templates/create-hpc-cluster-custom-image/).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

[AZURE.INCLUDE [virtual-machines-common-classic-hpcpack-cluster-powershell-script](../../includes/virtual-machines-common-classic-hpcpack-cluster-powershell-script.md)]

## <a name="example-configuration-files"></a>Ficheiros de configuração de exemplo

Nos exemplos seguintes, substitua os seus próprios valores para a sua subscrição Id ou nome e os nomes de conta e serviço.

### <a name="example-1"></a>Exemplo 1

O ficheiro de configuração seguintes ser implementada um cluster de aos Pack que tem um nó cabeça com bases de dados locais e calcular cinco nós que executem o sistema operativo do Windows Server 2012 R2. Todos os serviços na nuvem são criados diretamente na localização dos e.u.a. Ocidental. O nó cabeça age como controlador de domínio do floresta de domínios.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionId>08701940-C02E-452F-B0B1-39D50119F267</SubscriptionId>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <Location>West US</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>HeadNodeAsDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%1000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>5</NodeCount>
    <OSVersion>WindowsServer2012R2</OSVersion>
  </ComputeNodes>
</IaaSClusterConfig>
```

### <a name="example-2"></a>Exemplo 2

O ficheiro de configuração seguintes implementa um cluster de aos Pack numa floresta domínio existente. Cluster tem 1 nó cabeça com bases de dados locais e 12 calcular nós com a extensão de BGInfo VM aplicada.
A instalação das atualizações do Windows automática está desativada para todos os VMs na floresta de domínios. Todos os serviços na nuvem são criados diretamente na localização este asiático. Os nós de cluster são criados no três serviços em nuvem e três contas de armazenamento: _MyHPCCN 0001_ para _MyHPCCN 0005_ no _MyHPCCNService01_ e _mycnstorage01_; _MyHPCCN 0006_ para _MyHPCCN0010_ no _MyHPCCNService02_ e _mycnstorage02_; e _MyHPCCN 0011_ para _MyHPCCN 0012_ no _MyHPCCNService03_ e _mycnstorage03_). Os nós de cluster são criados a partir de uma imagem existente privada capturada a partir de um nó cluster. Automaticamente aumentar e diminuir serviço é activado com predefinida aumentar e diminuir intervalos.

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
     <NoWindowsAutoUpdate />
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
  </Certificates>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0001%</VMNamePattern>
<ServiceNamePattern>MyHPCCNService%01%</ServiceNamePattern>
<MaxNodeCountPerService>5</MaxNodeCountPerService>
<StorageAccountNamePattern>mycnstorage%01%</StorageAccountNamePattern>
    <VMSize>Medium</VMSize>
    <NodeCount>12</NodeCount>
    <ImageName HPCPackInstalled=”true”>MyHPCComputeNodeImage</ImageName>
    <VMExtensions>
       <VMExtension>
          <ExtensionName>BGInfo</ExtensionName>
          <Publisher>Microsoft.Compute</Publisher>
          <Version>1.*</Version>
       </VMExtension>
    </VMExtensions>
  </ComputeNodes>
  <AutoGrowShrink>
    <CertificateId>1</CertificateId>
  </AutoGrowShrink>
</IaaSClusterConfig>

```

### <a name="example-3"></a>Exemplo 3

O ficheiro de configuração seguintes implementa um cluster de aos Pack numa floresta domínio existente. Cluster contém um nó cabeça, servidor de base de dados com um disco de dados 500 GB, 2 corretor nós que executem o sistema operativo do Windows Server 2012 R2 e cinco nós de cluster utilizam o sistema operativo do Windows Server 2012 R2. O serviço de nuvem MyHPCCNService é criado no grupo afinidade *MyIBAffinityGroup*e outros serviços na nuvem são criados no grupo afinidade *MyAffinityGroup*. AOS tarefa Scheduler REST API e o portal de web HPC estão ativados no nó cabeça.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>    
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>NewRemoteDB</DBOption>
    <DBVersion>SQLServer2014_Enterprise</DBVersion>
    <DBServer>
      <VMName>MyDBServer</VMName>
      <ServiceName>MyHPCService</ServiceName>
      <VMSize>ExtraLarge</VMSize>
      <DataDiskSizeInGB>500</DataDiskSizeInGB>
    </DBServer>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
    <VMSize>ExtraLarge</VMSize>
    <EnableRESTAPI />
    <EnableWebPortal />
  </HeadNode>
  <ComputeNodes>
    <VMNamePattern>MyHPCCN-%0000%</VMNamePattern>
    <ServiceName>MyHPCCNService</ServiceName>
    <VMSize>A8</VMSize>
<NodeCount>5</NodeCount>
<AffinityGroup>MyIBAffinityGroup</AffinityGroup>
  </ComputeNodes>
  <BrokerNodes>
    <VMNamePattern>MyHPCBN-%0000%</VMNamePattern>
    <ServiceName>MyHPCBNService</ServiceName>
    <VMSize>Medium</VMSize>
    <NodeCount>2</NodeCount>
  </BrokerNodes>
</IaaSClusterConfig>
```



### <a name="example-4"></a>Exemplo 4

O ficheiro de configuração seguintes implementa um cluster de aos Pack numa floresta domínio existente. Cluster tem dois nó cabeça com bases de dados locais, dois nó Azure modelos são criados e três nós do tamanho médio Azure são criados para o modelo de nó Azure _AzureTemplate1_. Um ficheiro de script é executado no nó cabeça depois do nó cabeça está configurado.

```
<?xml version="1.0" encoding="utf-8" ?>
<IaaSClusterConfig>
  <Subscription>
    <SubscriptionName>Subscription-1</SubscriptionName>
    <StorageAccount>mystorageaccount</StorageAccount>
  </Subscription>
  <AffinityGroup>MyAffinityGroup</AffinityGroup>
  <Location>East Asia</Location>  
  <VNet>
    <VNetName>MyVNet</VNetName>
    <SubnetName>Subnet-1</SubnetName>
  </VNet>
  <Domain>
    <DCOption>ExistingDC</DCOption>
    <DomainFQDN>hpc.local</DomainFQDN>
  </Domain>
  <Database>
    <DBOption>LocalDB</DBOption>
  </Database>
  <HeadNode>
    <VMName>MyHeadNode</VMName>
    <ServiceName>MyHPCService</ServiceName>
<VMSize>ExtraLarge</VMSize>
    <PostConfigScript>c:\MyHNPostActions.ps1</PostConfigScript>
  </HeadNode>
  <Certificates>
    <Certificate>
      <Id>1</Id>
      <PfxFile>d:\mytestcert1.pfx</PfxFile>
      <Password>MyPsw!!2</Password>
    </Certificate>
    <Certificate>
      <Id>2</Id>
      <PfxFile>d:\mytestcert2.pfx</PfxFile>
    </Certificate>    
  </Certificates>
  <AzureBurst>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate1</TemplateName>
      <SubscriptionId>bb9252ba-831f-4c9d-ae14-9a38e6da8ee4</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc1</ServiceName>
      <StorageAccount>myteststorage1</StorageAccount>
      <NodeCount>3</NodeCount>
      <RoleSize>Medium</RoleSize>
    </AzureNodeTemplate>
    <AzureNodeTemplate>
      <TemplateName>AzureTemplate2</TemplateName>
      <SubscriptionId>ad4b9f9f-05f2-4c74-a83f-f2eb73000e0b</SubscriptionId>
      <CertificateId>1</CertificateId>
      <ServiceName>mytestsvc2</ServiceName>
      <StorageAccount>myteststorage2</StorageAccount>
      <Proxy>
        <UsesStaticProxyCount>false</UsesStaticProxyCount>     
        <ProxyRatio>100</ProxyRatio>
        <ProxyRatioBase>400</ProxyRatioBase>
      </Proxy>
      <OSVersion>WindowsServer2012</OSVersion>
    </AzureNodeTemplate>
  </AzureBurst>
</IaaSClusterConfig>
```

## <a name="troubleshooting"></a>Resolução de problemas


* **Erro "Não existe VNet"** - se de que executar o script para implementar vários clusters no Azure em simultâneo numa subscrição, um ou mais implementações podem falhar com o erro "VNet *VNet\_nome* não existe".
Se este erro ocorre, execute o script novamente para a implementação falha.

* **Problema ao aceder à Internet da rede virtual Azure** - se criar um cluster com um novo controlador de domínio utilizando o script de implementação ou manualmente promove um nó cabeça VM controlador de domínio, poderá ter problemas ao ligar os VMs à Internet. Este problema pode ocorrer se um servidor de DNS reencaminhador é configurado automaticamente no controlador de domínio e este servidor de DNS reencaminhador não resolve corretamente.

    Para contornar este problema, inicie sessão no controlador de domínio e quer remover a definição de configuração de reencaminhador ou configurar um servidor DNS reencaminhador válida. Para configurar esta definição, no Gestor de servidor, clique em **Ferramentas de** >
    **DNS** para abrir o Gestor de DNS e, em seguida, faça duplo clique em **reencaminhadores**.

* **Problema acedam à rede RDMA a partir com muitos cluster VMs** - se adicionar cluster do Windows Server ou broker VMs nó com um tamanho de RDMA noutros como A8 ou A9, poderá ter problemas esses VMs ligar à rede de aplicação de RDMA. Um motivo que este problema ocorre consiste se a extensão de HpcVmDrivers não está corretamente instalada quando os VMs são adicionados ao cluster. Por exemplo, a extensão poderá estar bloqueada no estado de instalação.

    Para contornar este problema, primeiro verifique o estado da extensão nos VMs. Se a extensão corretamente não estiver instalada, experimente remover os nós do cluster HPC e, em seguida, adicione os nós novamente. Por exemplo, pode adicionar cluster nó VMs ao executar o script de adicionar HpcIaaSNode.ps1 no nó cabeça.
    
## <a name="next-steps"></a>Próximos passos

* Tente executar uma carga de trabalho de teste no cluster. Por exemplo, consulte o artigo o pacote aos [Guia de introdução](https://technet.microsoft.com/library/jj884144).

* Para obter um tutorial script de implementação cluster e executar uma carga de trabalho HPC, consulte o artigo [começar a trabalhar com um cluster de aos Pack no Azure para executar o Excel e SOA das cargas de trabalho](virtual-machines-windows-excel-cluster-hpcpack.md).

* Experimente ferramentas aos Pack para iniciar, parar, adicionar e remover nós de cluster de um cluster de que criar. Consulte o artigo [Gerir calcular nós num cluster aos Pack no Azure](virtual-machines-windows-classic-hpcpack-cluster-node-manage.md).

* Para configurações para submeter as tarefas ao cluster de um computador local, consulte o artigo [submeter aos tarefas a partir de um computador no local a um cluster de aos Pack no Azure](virtual-machines-windows-hpcpack-cluster-submit-jobs.md).
