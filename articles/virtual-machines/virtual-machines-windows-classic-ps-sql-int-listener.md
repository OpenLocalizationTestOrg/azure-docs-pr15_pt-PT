<properties
    pageTitle="Configurar uma escuta ILB para sempre no grupos de disponibilidade | Microsoft Azure"
    description="Neste tutorial utiliza recursos criados com o modelo clássico de implementação e cria uma sempre no disponibilidade grupo escuta no Azure utilizando um balanceador de carga interno (ILB)."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-ilb-listener-for-always-on-availability-groups-in-azure"></a>Configurar uma escuta ILB para sempre no grupos de disponibilidade no Azure

> [AZURE.SELECTOR]
- [Escuta interna](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Escuta externa](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="overview"></a>Descrição geral

Este tópico mostra-lhe como configurar uma escuta para uma sempre no grupo de disponibilidade utilizando um **Balanceador de carga interna (ILB)**.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para configurar uma escuta ILB para um grupo de disponibilidade sempre no modelo de Gestor de recursos, consulte o artigo [configurar um balanceador de carga interna para um grupo de disponibilidade sempre no Azure](virtual-machines-windows-portal-sql-alwayson-int-listener.md).


O grupo de disponibilidade podem conter réplicas que estão no local apenas, só, Azure ou do intervalo no local e Azure configurações híbridas. Azure réplicas podem residem dentro da mesma região ou entre várias regiões utilizando várias redes virtuais (VNets). Os passos abaixo partem do pressuposto de que já tiver [configurado um grupo de disponibilidade](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , mas não tiver configurado uma escuta.

## <a name="guidelines-and-limitations-for-internal-listeners"></a>Directrizes e limitações para listeners internos
Tenha em atenção as seguintes diretrizes a disponibilidade grupo de escuta no Azure utilizando ILB:

- O serviço de escuta do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- Apenas uma escuta de grupo interno disponibilidade é suportada por serviço na nuvem, uma vez que o está configurado para o ILB e existe apenas uma ILB por serviço na nuvem. No entanto, é possível criar várias listeners externos. Para mais informações, consulte o artigo [Configurar uma escuta externa para sempre no grupos de disponibilidade no Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md).

- Não é suportada para criar uma escuta interna no serviço na nuvem mesmo onde também tem uma escuta externa utilizando público VIP o serviço de nuvem.

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade da escuta

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo foca-se sobre como criar uma escuta que utiliza um **Balanceador de carga interna (ILB)**. Se precisar de uma escuta público/externa, ver a versão deste artigo que fornece os passos para configurar uma [escuta externa](virtual-machines-windows-classic-ps-sql-ext-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar balanceamento de carga os pontos finais VM com servidor direta remetente

Para ILB, primeiro tem de criar o Balanceador de carga interno. Isto é feito no script abaixo.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Para **ILB**, deve atribuir um endereço IP estático. Em primeiro lugar, examine a configuração de VNet actual, executando o seguinte comando:

        (Get-AzureVNetConfig).XMLConfiguration

1. Tenha em atenção o nome de **sub-rede** para a sub-rede que contém os VMs que alojam réplicas. Será utilizada no parâmetro **$SubnetName** no script.

1. Em seguida, tenha em atenção o nome de **VirtualNetworkSite** e as iniciais **AddressPrefix** para a sub-rede que contém os VMs que alojam réplicas. Procure um endereço IP disponível ao passar ambos os valores para o comando de **Teste AzureStaticVNetIP** e examinar a **AvailableAddresses**. Por exemplo, se o VNet era denominado *MyVNet* e tinha um endereço de sub-rede o intervalo que foram iniciadas na *172.16.0.128*, teria o seguinte comando da lista endereços disponíveis:

        (Test-AzureStaticVNetIP -VNetName "MyVNet"-IPAddress 172.16.0.128).AvailableAddresses

1. Selecione um dos endereços disponíveis e utilizá-lo no parâmetro **$ILBStaticIP** do seguinte script.

3. Copie o script do PowerShell abaixo para um editor de texto e defina os valores de variáveis de acordo com o seu ambiente (note que foi fornecidas predefinições para alguns parâmetros). Tenha em atenção que implementações existentes que utilizam grupos de afinidade não é possível adicionar ILB. Para mais informações sobre os requisitos de ILB, consulte o artigo [Interno Balanceador de carga](../load-balancer/load-balancer-internal-overview.md). Além disso, se o grupo de disponibilidade abranger regiões Azure, tem de executar uma vez o script em cada centro de dados para o serviço de nuvem e nós que residem nesse centro de dados.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
        $SubnetName = "<MySubnetName>" # subnet name that the replicas use in the VNet
        $ILBStaticIP = "<MyILBStaticIPAddress>" # static IP address for the ILB in the subnet
        $ILBName = "AGListenerLB" # customize the ILB name or use this default value

        # Create the ILB
        Add-AzureInternalLoadBalancer -InternalLoadBalancerName $ILBName -SubnetName $SubnetName -ServiceName $ServiceName -StaticVNetIPAddress $ILBStaticIP

        # Configure a load balanced endpoint for each node in $AGNodes using ILB
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -LBSetName "ListenerEndpointLB" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ILBName -DirectServerReturn $true | Update-AzureVM
        }

1. Assim que tiver definido as variáveis, copie o script do editor de texto para a sua sessão Azure PowerShell executá-la. Se o pedido ainda mostra >>, escreva ENTER novamente para se certificar de que o script começa a funcionar. Nota

>[AZURE.NOTE] Portal clássico do Azure não suporta o Balanceador de carga interno neste momento, para que não irá visualizar a ILB ou os pontos finais no portal do Azure clássico. No entanto, **Get-AzureEndpoint** devolve um endereço IP interno se estiver a executar o Balanceador de carga no mesmo. Caso contrário, devolve valor nulo.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Certifique-se de que o KB2854082 está instalado se for necessário

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas da firewall em nós do grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar a escuta de grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para ILB, tem de utilizar o endereço IP do interno carga balanceador (ILB) criada anteriormente. Utilize o seguinte script para obter este endereço IP no PowerShell.

        # Define variables
        $ServiceName="<MyServiceName>" # the name of the cloud service that contains the AG nodes
        (Get-AzureInternalLoadBalancer -ServiceName $ServiceName).IPAddress

1. Das VMs, copie o script do PowerShell para o seu sistema operativo para um editor de texto e definir as variáveis para os valores indicados anteriormente.

    Para o Windows Server 2012 ou superior utilize o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
        
    Para o Windows Server 2008 R2 utilize o seguinte script:

        # Define variables
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB)

        Import-Module FailoverClusters

        cluster res $IPResourceName /priv enabledhcp=0 address=$ILBIP probeport=59999  subnetmask=255.255.255.255
    

1. Uma vez que tiver definido as variáveis, abra uma janela do Windows PowerShell elevada, em seguida, copie o script a partir do editor de texto e cole a sua sessão Azure PowerShell executá-la. Se o pedido ainda mostra >>, escreva ENTER novamente para se certificar de que o script começa a funcionar.

2. Repita este procedimento em cada VM. Este script configura o recurso de endereço de IP com o endereço IP do serviço em nuvem e conjuntos de outros parâmetros, como a porta sonda. Quando o recurso de endereço IP estiver online, em seguida, pode responder a inquéritos na porta sonda de ponto final balanceamento de carga criado anteriormente no tutorial.

## <a name="bring-the-listener-online"></a>Trazer a escuta online

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de seguimento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar a escuta de grupo de disponibilidade (dentro do mesmo VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
