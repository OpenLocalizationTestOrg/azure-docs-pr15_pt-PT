<properties
   pageTitle="Criar uma rede virtual com uma ligação VPN de Site para o Site utilizando o Gestor de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo orienta-criar um VNet utilizando o modelo de implementação do Gestor de recursos e ligá-lo à sua rede local no local através de uma ligação de gateway S2S VPN."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="cherylmc"/>

# <a name="create-a-vnet-with-a-site-to-site-connection-using-powershell"></a>Criar um VNet com uma ligação de Site para o Site através do PowerShell

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-site-to-site-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-create-site-to-site-rm-powershell.md)
- [Clássico - clássico de Portal](vpn-gateway-site-to-site-create.md)

Este artigo explica o criação de uma rede virtual e uma ligação de gateway VPN de Site para o Site à sua rede no local, utilizando o modelo de implementação do Azure o Gestor de recursos. Ligações de site para o Site podem ser utilizadas para publicação em local e híbrido configurações.

![Diagrama de site para o Site] (./media/vpn-gateway-create-site-to-site-rm-powershell/s2srmps.png "site para o site") 


### <a name="deployment-models-and-methods-for-site-to-site-connections"></a>Modelos de implementação e métodos para ligações do Site para o Site

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)] 

A tabela seguinte mostra os métodos para configurações de Site para o Site e modelos de implementação encontra-se disponível. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela. 

[AZURE.INCLUDE [site-to-site table](../../includes/vpn-gateway-table-site-to-site-include.md)]

#### <a name="additional-configurations"></a>Configurações adicionais

Se pretende ligar VNets em conjunto, mas não estiver a criar uma ligação para uma localização no local, consulte o artigo [Configurar uma ligação de VNet para VNet](vpn-gateway-vnet-vnet-rm-ps.md). Se quiser adicionar uma ligação de Site para o Site a um VNet que já tem uma ligação, consulte o artigo [Adicionar uma ligação de S2S a um VNet com uma ligação de gateway VPN existente](vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Certifique-se de que tem os seguintes itens antes de iniciar configuração.

- Um dispositivo VPN compatível e alguém que consiga configurá-lo. Consulte o artigo [sobre os dispositivos VPN](vpn-gateway-about-vpn-devices.md). Se não estiver familiarizado com a configuração do seu dispositivo VPN ou se não estiver familiarizado com o endereço IP intervalos localizados na configuração de rede do seu no local, é necessário estão coordenados com alguém que pode fornecer essas detalhes por si.

- Um externamente oposto endereço IP público para o seu dispositivo VPN. Este endereço IP não pode ser localizado atrás de um NAT.
    
- Uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- A versão mais recente dos cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.


## <a name="Login"></a>1. a ligar à sua subscrição 

Certifique-se de que mudar para o modo de PowerShell para utilizar os cmdlets do Gestor de recursos. Para mais informações, consulte o artigo [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

Abra o seu consola do PowerShell e ligar à sua conta. Utilize o exemplo seguinte para ajudá-lo ligar:

    Login-AzureRmAccount

Verifique as subscrições para a conta.

    Get-AzureRmSubscription 

Especifique a subscrição que pretende utilizar.

    Select-AzureRmSubscription -SubscriptionName "Replace_with_your_subscription_name"

## <a name="VNet"></a>2. Crie uma sub-rede de gateway e uma rede virtual

Os exemplos utilizam uma sub-rede gateway de /28. Enquanto é possível criar uma sub-rede de gateway tão pequenas quanto /29, recomendamos que cria uma sub-rede maior que inclui mais endereços ao selecionar, pelo menos, /28 ou /27. Isto permitirá para endereços suficientes acomodar possíveis configurações adicionais que pode querer no futuro.

Se já tiver uma rede virtual com uma sub-rede de gateway é/29 ou maiores, pode avançar para [Adicionar o seu gateway de rede local](#localnet).


[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)]  

### <a name="to-create-a-virtual-network-and-a-gateway-subnet"></a>Para criar uma rede virtual e uma sub-rede do gateway

Utilize o exemplo seguinte para criar uma rede virtual e de sub-rede um gateway. Substitua valores para o seu próprio. 

Primeiro, crie um grupo de recursos:
    
    New-AzureRmResourceGroup -Name testrg -Location 'West US'

Em seguida, crie a sua rede virtual. Certifique-se de que os espaços de endereço que especificou não se sobrepõe qualquer um dos espaços de endereço tem na sua rede no local.

O exemplo seguinte cria uma rede virtual com o nome *testvnet* e duas sub-redes, chamado um *GatewaySubnet* e o outro designado *Sub-rede1*. É importante criar uma sub-rede denominada especificamente *GatewaySubnet*. Se é-lhe o nome algo, a configuração da ligação irá falhar. 

Defina as variáveis.

    $subnet1 = New-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
    $subnet2 = New-AzureRmVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'

Crie o VNet.

    New-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg `
    -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>Para adicionar uma sub-rede gateway a uma rede virtual que já tiver criado

Este passo é necessário apenas se precisar de adicionar uma sub-rede gateway a um VNet que criou anteriormente.

Pode criar a sua sub-rede aceda gateway utilizando o exemplo seguinte. Certifique-se para atribuir um nome à sub-rede gateway 'GatewaySubnet'. Se é-lhe o nome algo, criar uma sub-rede, mas Azure não processado como uma sub-rede de gateway.

Defina as variáveis.

    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName testrg -Name testvnet

Crie sub-rede o gateway.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Defina a configuração. 

    Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

## 3. <a name="localnet"> </a>adicionar o seu gateway de rede local

Numa rede virtual, o gateway de rede local normalmente refere-se a sua localização no local. É o site dê um nome pelo qual Azure pode referência ao mesmo bem como especificar o prefixo do espaço de endereços para o gateway de rede local. 

Azure utiliza o prefixo do endereço IP que especificar para identificar qual o tráfego para enviar a sua localização no local. Isto significa que tem de especificar cada prefixo endereço que pretende serão associadas ao seu gateway de rede local. Poderá atualizar facilmente estes prefixos se alterar a sua rede no local. 

Ao utilizar os exemplos de PowerShell, tenha em atenção o seguinte procedimento:
    
- O *GatewayIPAddress* é o endereço IP do seu dispositivo VPN no local. Não é possível localizar o seu dispositivo VPN atrás de um NAT. 
- O *AddressPrefix* é o seu espaço de endereços no local.

Para adicionar um gateway de rede local com um prefixo único endereço:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Para adicionar um gateway de rede local com vários prefixos de endereços:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### <a name="to-modify-ip-address-prefixes-for-your-local-network-gateway"></a>Para modificar prefixos de endereços IP para o gateway de rede local

Por vezes, altere o seu prefixos de gateway de rede local. Passos para modificar a sua prefixos de endereço IP dependem de se ter criado uma ligação de gateway VPN. Consulte a secção [prefixos de endereços IP modificar para um gateway de rede local](#modify) deste artigo.


## <a name="PublicIP"></a>4. pedido de um endereço IP público para o gateway VPN

O pedido seguinte, um endereço IP público a serem alocados à sua gateway Azure VNet VPN. Não é o mesmo endereço IP que é atribuído ao seu dispositivo VPN; em vez disso é atribuído para o gateway Azure VPN própria. Não é possível especificar o endereço IP que pretende utilizar. Dinamicamente é atribuído ao seu gateway. Utilize este endereço IP ao configurar o seu dispositivo VPN no local para ligar para o gateway.

O gateway Azure VPN para o modelo de implementação de Gestor de recursos atualmente apenas suporta endereços IP públicos utilizando o método de alocação dinâmicos. No entanto, isso não significa que irá alterar o endereço IP. A única vez que as alterações de endereço IP do Azure VPN gateway é quando o gateway é eliminado e novamente criado. O endereço IP público do gateway não será alterada ao longo do redimensionamento, repor ou outras manutenção/actualizações internas do seu gateway Azure VPN.

Utilize o exemplo seguinte do PowerShell:

    $gwpip= New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## <a name="GatewayIPConfig"></a>5. criar a configuração endereçamento de IP do gateway

A configuração de gateway define a sub-rede e o endereço IP público para utilizar. Utilize o exemplo seguinte para criar a sua configuração de gateway.

    $vnet = Get-AzureRmVirtualNetwork -Name testvnet -ResourceGroupName testrg
    $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## <a name="CreateGateway"></a>6. criar o gateway de rede virtual

Neste passo, crie o gateway de rede virtual. Criar um gateway pode demorar muito tempo a concluir. Muitas vezes 45 minutos ou mais. 

Utilize os seguintes valores:

- O *-GatewayType* para uma configuração de Site para o Site é *Vpn*. O tipo de gateway é sempre específico na configuração que estão a implementar. Por exemplo, outras configurações de gateway precisem - GatewayType ExpressRoute. 

- O *-VpnType* pode ser *RouteBased* (designado por um Gateway dinâmico no algumas documentação), ou *PolicyBased* (designado por um estático Gateway na algumas documentação). Para mais informações sobre tipos de gateway VPN, consulte o artigo [Sobre VPN Gateways](vpn-gateway-about-vpngateways.md#vpntype).
- O *-GatewaySku* pode ser *básica*, *padrão*ou *reversa*.   

        New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
        -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

## <a name="ConfigureVPNDevice"></a>7. configurar o seu dispositivo VPN

Neste momento, precisa do endereço IP público do gateway rede virtual para configurar o seu dispositivo VPN no local. Trabalhar com o fabricante do dispositivo para informações de configuração específicas. Pode referir-se para os [Dispositivos de VPN](vpn-gateway-about-vpn-devices.md) para obter mais informações.

Para localizar o endereço IP público do seu gateway de rede virtual, utilize o exemplo seguinte:

    Get-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName testrg

## <a name="CreateConnection"></a>8. criar a ligação VPN

Em seguida, crie a ligação de Site para o Site VPN entre o seu gateway de rede virtual e o seu dispositivo VPN. Certifique-se de que substitua os valores com o seu próprio. A chave partilhada tem de corresponder o valor utilizado para a configuração de dispositivo VPN. Repare que o `-ConnectionType` de *IPsec*que é o Site para o Site. 

Defina as variáveis.

    $gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    $local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

Crie a ligação.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Depois de um curto enquanto a ligação será estabelecido. 

## <a name="toverify"></a>Para verificar se uma ligação VPN

Existem algumas maneiras diferentes para verificar a ligação VPN.

[AZURE.INCLUDE [vpn-gateway-verify-connection-rm](../../includes/vpn-gateway-verify-connection-rm-include.md)]

## <a name="modify"></a>Para modificar prefixos de endereços IP para um gateway de rede local

Se precisar de alterar os prefixos para o gateway de rede local, utilize as instruções seguintes. Dois conjuntos de instruções são fornecidos. As instruções que escolher dependem de se já tiver criado sua ligação de gateway. 

[AZURE.INCLUDE [vpn-gateway-modify-ip-prefix-rm](../../includes/vpn-gateway-modify-ip-prefix-rm-include.md)]

## <a name="modifygwipaddress"></a>Para modificar o endereço IP de gateway para um gateway de rede local

[AZURE.INCLUDE [vpn-gateway-modify-lng-gateway-ip-rm](../../includes/vpn-gateway-modify-lng-gateway-ip-rm-include.md)]

## <a name="next-steps"></a>Próximos passos

- Pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.

- Para obter informações sobre BGP, consulte a [Descrição geral de BGP](vpn-gateway-bgp-overview.md) e [como configurar BGP](vpn-gateway-bgp-resource-manager-ps.md).

