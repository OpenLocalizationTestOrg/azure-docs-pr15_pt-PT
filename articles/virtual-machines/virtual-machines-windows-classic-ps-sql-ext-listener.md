<properties
    pageTitle="Configurar uma escuta externa sempre no disponibilidade para grupos de | Microsoft Azure"
    description="Neste tutorial irá guiar pelo passos de criação de um sempre no disponibilidade grupo escuta no Azure que esteja acessível externamente, utilizando o endereço de Virtual IP público do serviço em nuvem associado."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="MikeRayMSFT"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/12/2016"
    ms.author="MikeRayMSFT" />

# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar uma escuta externa para sempre no disponibilidade grupos no Azure

> [AZURE.SELECTOR]
- [Escuta interna](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Escuta externa](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Este tópico mostra-lhe como configurar uma escuta para uma sempre no grupo de disponibilidade externamente acessível na internet. Isto é tornado possível associando endereço de **IP Virtual público (VIP)** o serviço de nuvem com a escuta.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


O grupo de disponibilidade podem conter réplicas que estão no local apenas, só, Azure ou do intervalo no local e Azure configurações híbridas. Azure réplicas podem residem dentro da mesma região ou entre várias regiões utilizando várias redes virtuais (VNets). Os passos abaixo partem do pressuposto de que já tiver [configurado um grupo de disponibilidade](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md) , mas não tiver configurado uma escuta.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Directrizes e limitações para listeners externos

Tenha em atenção as seguintes diretrizes sobre a disponibilidade grupo de escuta no Azure quando estiver a implementar o utilizando o endereço VIP da sínfise de serviço na nuvem:

- O serviço de escuta do grupo de disponibilidade é suportado no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.

- A aplicação de cliente tem se encontram um serviço em nuvem diferente daquele que contém o grupo de disponibilidade VMs. Azure não suporta servidor direta ENTER com cliente e servidor no serviço de nuvem do mesmo.

- Por predefinição, os passos neste artigo Mostrar como configurar uma escuta para utilizar o endereço de IP Virtual (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços de VIP do seu serviço de nuvem. Permite-lhe utilizar os passos neste artigo para criar múltiplas listeners associadas cada um VIP diferente. Para obter informações sobre como criar vários endereços de VIP, consulte o artigo [VIP vários por serviço na nuvem](../load-balancer/load-balancer-multivip.md).

- Se estiver a criar uma escuta para um ambiente híbrido, a rede no local tem de ter conectividade à Internet pública para além da VPN do site para o site com a rede virtual Azure. Quando está no Azure sub-rede, o serviço de escuta do grupo de disponibilidade está acessível apenas pelo endereço IP público do serviço de nuvem respetivos.

- Não é suportada para criar uma escuta externa no serviço na nuvem mesmo onde também tem uma escuta interna utilizando o Balanceador de carga interna (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determinar a acessibilidade da escuta

[AZURE.INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo foca-se sobre como criar uma escuta que utiliza **balanceamento de carga externa**. Se pretender uma escuta que for privada à sua rede virtual, consulte a versão deste artigo que fornece os passos para configurar uma [escuta com ILB](virtual-machines-windows-classic-ps-sql-int-listener.md)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar balanceamento de carga os pontos finais VM com servidor direta remetente

Balanceamento de carga externa utiliza o virtual o Virtual endereço IP público do serviço na nuvem que aloja a sua VMs. Por isso, não tem de criar ou configurar o Balanceador de carga neste caso.

[AZURE.INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copie o script do PowerShell abaixo para um editor de texto e defina os valores de variáveis de acordo com o seu ambiente (predefinições foi fornecidas para alguns parâmetros). Tenha em atenção que se o grupo de disponibilidade abranger regiões Azure, tem de executar o script uma vez em cada centro de dados para o serviço de nuvem e nós que residem nesse centro de dados.

        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas

        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }

1. Assim que tiver definido as variáveis, copie o script do editor de texto para a sua sessão Azure PowerShell executá-la. Se o pedido ainda mostra >>, escreva ENTER novamente para se certificar de que o script começa a funcionar.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Certifique-se de que o KB2854082 está instalado se for necessário

[AZURE.INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abrir as portas da firewall em nós do grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Criar a escuta de grupo de disponibilidade

[AZURE.INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para balanceamento de carga externa, tem de obter o endereço IP público virtual do serviço na nuvem que contém o seu réplicas. Inicie sessão no portal do Azure clássico. Navegue para o serviço de nuvem que contém o grupo de disponibilidade VM. Abra a vista de **Dashboard** .

3. Tenha em atenção o endereço mostrado em **endereço IP Virtual público (VIP)**. Se a solução abranger VNets, repita este passo para cada serviço na nuvem que contém uma VM que aloja uma réplica.

4. Das VMs, copie o script do PowerShell abaixo para um editor de texto e definir as variáveis para os valores indicados anteriormente.

        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service

        Import-Module FailoverClusters

        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.

        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255


1. Uma vez que tiver definido as variáveis, abra uma janela do Windows PowerShell elevada, em seguida, copie o script a partir do editor de texto e cole a sua sessão Azure PowerShell executá-la. Se o pedido ainda mostra >>, escreva ENTER novamente para se certificar de que o script começa a funcionar.

1. Repita este procedimento em cada VM. Este script configura o recurso de endereço de IP com o endereço IP do serviço em nuvem e conjuntos de outros parâmetros, como a porta sonda. Quando o recurso de endereço IP estiver online, em seguida, pode responder a inquéritos na porta sonda de ponto final balanceamento de carga criado anteriormente no tutorial.

## <a name="bring-the-listener-online"></a>Trazer a escuta online

[AZURE.INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de seguimento

[AZURE.INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar a escuta de grupo de disponibilidade (dentro do mesmo VNet)

[AZURE.INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testar a escuta de grupo de disponibilidade (através da internet)

Para poder aceder a escuta de fora da rede virtual, tem de utilizar o balanceamento de carga de externo/público (descrito neste tópico) em vez de ILB, que é acessível pelo apenas dentro VNet o mesmo. Na cadeia de ligação, especifique o nome do serviço na nuvem. Por exemplo, se tinha um serviço na nuvem com o nome *mycloudservice*, a declaração de sqlcmd seria da seguinte forma:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, a autenticação de SQL deve ser utilizada, uma vez que o autor da chamada não é possível utilizar a autenticação do windows através da internet. Para obter mais informações, consulte o artigo [sempre no grupo de disponibilidade no Azure VM: cenários de conectividade de cliente](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx). Ao utilizar a autenticação de SQL, certifique-se de que cria o início de sessão do mesmo em ambos os réplicas. Para mais informações sobre os inícios de sessão com grupos de disponibilidade de resolução de problemas, consulte o artigo [como mapear os inícios de sessão ou utilizar contidas utilizador de base de dados SQL para ligar a outras réplicas e mapear a bases de dados de disponibilidade](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas sempre no estão no sub-redes diferentes, clientes tem de especificar **MultisubnetFailover = True** na cadeia de ligação. Isto resulta em tentativas de ligação paralelas para réplicas diferentes sub-redes. Tenha em atenção que este cenário inclui uma implementação sempre no grupo de disponibilidade de publicação em região.

## <a name="next-steps"></a>Próximos passos

[AZURE.INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]
