<properties 
   pageTitle="Configurar túnel forçada para ligações de Site para o Site utilizando o modelo de implementação do Gestor de recursos | Microsoft Azure"
   description="Como redirecionar ou 'forçar' todo o tráfego de Internet vinculadas voltar a sua localização no local."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/10/2016"
   ms.author="cherylmc" />

# <a name="configure-forced-tunneling-using-the-azure-resource-manager-deployment-model"></a>Configurar túnel forçada utilizando o modelo de implementação do Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [PowerShell - clássico](vpn-gateway-about-forced-tunneling.md)
- [PowerShell - Gestor de recursos](vpn-gateway-forced-tunneling-rm.md)

Túnel forçada permite-lhe redirecionar ou "forçar" todo o tráfego de Internet vinculadas novamente para sua localização no local através de um túnel de Site para o Site VPN para auditoria e controlo. Isto é um requisito de segurança crítica para a maioria dos enterprise IT políticas.

Sem túnel forçada, o tráfego de Internet vinculadas a partir do seu VMs no Azure sempre irá percorrer à Internet, sem a opção para permitir que inspecionar ou o tráfego de auditoria de infraestrutura de rede Azure diretamente saída. Acesso à Internet não autorizado potencialmente pode levar a divulgação de informações ou outros tipos de quebras de segurança

Este artigo explica o configurar forçada túnel para redes virtuais criadas utilizando o modelo de implementação do Gestor de recursos.

**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

**Modelos de implementação e ferramentas para túnel forçada**

Uma ligação de túnel forçada pode ser configurada para o modelo clássico de implementação e o modelo de implementação do Gestor de recursos. Consulte a tabela seguinte para obter mais informações. Esta tabela poderemos actualizar como novos artigos, modelos de implementação novos e ferramentas adicionais de ficam disponíveis para esta configuração. Quando um artigo estiver disponível, podemos ligar diretamente à mesma a partir da tabela.

[AZURE.INCLUDE [vpn-gateway-table-forced-tunneling](../../includes/vpn-gateway-table-forcedtunnel-include.md)] 


## <a name="about-forced-tunneling"></a>Forçada cerca de túnel


O diagrama seguinte ilustra como forçada funciona túnel. 

![Forçada túnel](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

No exemplo acima, túnel Frontend sub-rede não for forçada. As cargas de trabalho na sub-rede Frontend podem continuar a aceitar e responder a pedidos de cliente da Internet diretamente. As sub-redes camada meados e back-end são forçadas túnel. Quaisquer ligações de saída destas duas sub-redes à Internet vão ser forçadas ou redireccionadas para um site no local através de um dos túneis S2S VPN.

Isto permite-lhe restringir e inspecionar acesso à Internet a partir do seu máquinas virtuais ou cloud services no Azure, enquanto continua ativar a sua arquitetura de várias camadas serviço necessária. Também pode aplicar túnel forçada para as redes virtuais inteiras se não existirem das cargas de trabalho sem acesso à Internet no redes virtuais.

## <a name="requirements-and-considerations"></a>Requisitos e considerações

Túnel forçada no Azure está configurado através do rotas definidas pelo utilizador de rede virtual. Redirecionar o tráfego para um site no local é expresso como uma rota predefinida para o gateway Azure VPN. Para mais informações sobre o encaminhamento de definidas pelo utilizador e redes virtuais, consulte o artigo [definidas pelo utilizador rotas e reencaminhamento IP](../virtual-network/virtual-networks-udr-overview.md).

- Cada sub-rede rede virtual tem uma tabela de encaminhamento incorporado, do sistema. A tabela de encaminhamento de sistema tem os seguintes três grupos de rotas:

    - **Local VNet encaminha:** Diretamente para o destino VMs na mesma rede virtual
    
    - **No local rotas:** Para o gateway VPN do Azure
    
    - **Rota predefinida:** Diretamente à Internet. Pacotes destinados a endereços IP privados não abrangidos pelas anterior duas rotas serão ignorados.

-  Este procedimento utiliza rotas definidas pelo utilizador (UDR) para criar uma tabela de encaminhamento para adicionar uma rota predefinida e, em seguida, associar a tabela de encaminhamento para sua subnet(s) VNet para ativar túnel forçada nesses sub-redes.

- Tem de ser associada a um VNet que tem um gateway VPN com base em encaminhar túnel forçada. Tem de predefinir um "site" entre os sites local da publicação em local ligado à rede virtual.

- ExpressRoute forçada túnel não está configurado através deste mecanismo, mas em vez disso, é activado por publicidade uma rota predefinida através das sessões peering ExpressRoute BGP. Consulte a [Documentação de ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/) para obter mais informações.

## <a name="configuration-overview"></a>Descrição geral de configuração

O procedimento seguinte ajuda-o a criar um grupo de recursos e um VNet. Em seguida, irá criar um gateway VPN e configurar túnel forçada. Este procedimento, a rede virtual "MultiTier VNet" contém 3 sub-redes: *front-end*, *Midtier*e *back-end*, com 4 publicação em várias ligações local: *DefaultSiteHQ*e 3 *ramificações*.

Os passos do procedimento a *DefaultSiteHQ* como a ligação de site predefinida para forçada túnel e configurar o Midtier e sub-redes back-end a utilizar forçada túnel.

    
## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem os seguintes itens antes de iniciar a configuração.

- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).

- Terá de instalar a versão mais recente dos cmdlets do PowerShell de Gestor de recursos do Azure (1.0 ou posteriores). Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="configure-forced-tunneling"></a>Configurar túnel forçada

1. Na consola do PowerShell, inicie sessão na sua conta Azure. Este cmdlet pede-lhe as credenciais de início de sessão para a sua conta Azure. Depois de início de sessão, transfere as definições de conta para que fiquem disponíveis para o Azure PowerShell.

        Login-AzureRmAccount 

2. Obter uma lista das suas subscrições Azure.

        Get-AzureRmSubscription

2. Especifique a subscrição que pretende utilizar. 

        Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"
        
3. Crie um grupo de recursos.

        New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Criar uma rede virtual e especificar sub-redes. 

        $s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
        $s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
        $s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
        $s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
        $vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Crie os gateways de rede local.

        $lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
        $lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
        $lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
        $lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
        
6. Crie a tabela de encaminhamento e regra de encaminhar.

        New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
        $rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
        Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
        Set-AzureRmRouteTable -RouteTable $rt


7. Associe a tabela encaminhar para as sub-redes Midtier e back-end.

        $vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
        Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

8. Crie o Gateway com um site predefinido. Este passo leva algum tempo para concluir, por vezes, 45 minutos ou mais, uma vez que está a criar e configurar o gateway.<br> O `-GatewayDefaultSite` é o parâmetro do cmdlet que permite a configuração de encaminhamento forçada trabalhar, por isso, encarregam-se para configurar esta definição corretamente. Este parâmetro está disponível no PowerShell 1.0 ou posterior.

        $pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
        $gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
        $ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
        New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

9. Estabelece ligações a VPN do Site para o Site.

        $gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
        $lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
        $lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
        $lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
        $lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
        New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

        Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
        



