<properties 
   pageTitle="Acerca das definições de Gateway de VPN para gateways de rede virtual | Microsoft Azure"
   description="Saiba mais sobre as definições de Gateway de VPN para a rede Virtual Azure."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager,azure-service-management"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="cherylmc" />

# <a name="about-vpn-gateway-settings"></a>Acerca das definições de VPN Gateway

Uma solução de ligação de gateway VPN depende da configuração de vários recursos para poder enviar tráfego de rede entre redes virtuais e no local localizações. Cada recurso contém definições configuráveis. A combinação das definições de recursos e determina o resultado de ligação.

As secções neste artigo abordam as definições que se relacionam com um gateway VPN no modelo de implementação de **Gestor de recursos** e recursos. Poderá ser útil para ver as configurações disponíveis ao utilizar diagramas de topologia de ligação. Pode encontrar as descrições e diagramas de topologia para cada solução de ligação no artigo [Sobre VPN Gateway](vpn-gateway-about-vpngateways.md) . 

## <a name="gwtype"></a>Tipos de gateway

Cada rede virtual só pode ter um gateway de rede virtual de cada tipo. Quando estiver a criar um gateway de rede virtual, deve certificar-se de que o tipo de gateway está correto para a configuração.

Os valores disponíveis para - GatewayType são: 

- VPN
- ExpressRoute

Necessita de um gateway VPN o `-GatewayType` *Vpn*.  

Exemplo:

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn `
    -VpnType RouteBased
 

## <a name="gwsku"></a>SKUs do gateway


[AZURE.INCLUDE [vpn-gateway-gwsku-include](../../includes/vpn-gateway-gwsku-include.md)]

### <a name="configuring-the-gateway-sku"></a>Configurar o gateway SKU

**Especificar o gateway SKU no portal do Azure**

Se utilizar o portal do Azure para criar um gateway de rede virtual do Gestor de recursos, pode selecionar o gateway SKU utilizando no menu pendente. As opções que é apresentadas com correspondem ao tipo de Gateway e tipo de VPN que selecionar.

Por exemplo, se selecionar o tipo de gateway 'VPN' e o tipo 'Política baseado em VPN', verá apenas o SKU 'Básico' porque é que o SKU apenas disponível para PolicyBased VPNs. Se selecionar baseado rota, pode selecionar a partir do Basic, padrão e reversa SKUs. 


**Especificar o gateway SKU através do PowerShell**


O exemplo seguinte do PowerShell Especifica a `-GatewaySku` como *padrão*.

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig -GatewaySku Standard `
    -GatewayType Vpn -VpnType RouteBased

**Alterar um gateway SKU**

Se pretende actualizar o gateway SKU para um SKU mais eficazes (de Basic/padrão para reversa) pode utilizar o `Resize-AzureRmVirtualNetworkGateway` cmdlet do PowerShell. Também pode alterar o tamanho SKU utilizando este cmdlet do gateway.

Exemplo de PowerShell que se segue mostra um gateway SKU a ser redimensionada para reversa.

    $gw = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

### <a name="estimated-aggregate-throughput-by-gateway-sku-and-type"></a>Estimativa débito agregado por gateway SKU e tipo

A tabela seguinte mostra os tipos de gateway e o débito agregação estimado. Esta tabela aplica-se para o Gestor de recursos e de modelos de implementação clássica.

[AZURE.INCLUDE [vpn-gateway-table-gwtype-aggthroughput](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)] 


## <a name="connectiontype"></a>Tipos de ligação

No modelo de implementação do Gestor de recursos, cada configuração requer um tipo de ligação de gateway de rede virtual específica. O PowerShell do Gestor de recursos disponíveis valores para `-ConnectionType` são:

- IPsec
- Vnet2Vnet
- ExpressRoute
- VPNClient

No seguinte exemplo PowerShell, podemos criar uma ligação de S2S que requer o tipo de ligação *IPsec*.

    New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg `
    -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
    -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## <a name="vpntype"></a>Tipos VPN

Quando cria uma configuração de gateway VPN do gateway na rede virtual, tem de especificar um tipo VPN. O tipo VPN que escolher depende da topologia de ligação que pretende criar. Por exemplo, uma ligação de P2S requer um tipo de RouteBased VPN. Um tipo VPN também pode depender do hardware que irá estar a utilizar. Configurações de S2S necessitam de um dispositivo VPN. Alguns dispositivos VPN suportam apenas um determinado tipo VPN.

Selecionar o tipo de VPN satisfazer todos os requisitos de ligação para a solução que pretende criar. Por exemplo, se pretender criar uma ligação de gateway S2S VPN e uma ligação de gateway P2S VPN para a mesma rede virtual, utilizaria o tipo de VPN *RouteBased* porque P2S requer um tipo de RouteBased VPN. Também que precisa para verificar se o seu dispositivo VPN suportada uma ligação RouteBased VPN. 

Assim que um gateway de rede virtual ter sido criado, não é possível alterar o tipo VPN. Tem de eliminar o gateway de rede virtual e crie um novo. Existem dois tipos VPN:

[AZURE.INCLUDE [vpn-gateway-vpntype](../../includes/vpn-gateway-vpntype-include.md)]


O exemplo seguinte do PowerShell Especifica a `-VpnType` como *RouteBased*. Quando estiver a criar um gateway, deve certificar-se de que - VpnType está correto para a configuração. 

    New-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg `
    -Location 'West US' -IpConfigurations $gwipconfig `
    -GatewayType Vpn -VpnType RouteBased

##  <a name="requirements"></a>Requisitos de gateway

[AZURE.INCLUDE [vpn-gateway-table-requirements](../../includes/vpn-gateway-table-requirements-include.md)] 


## <a name="gwsub"></a>Sub-rede do gateway

Para configurar um gateway de rede virtual, primeiro tem de criar uma sub-rede de gateway para a sua VNet. A sub-rede gateway tem de ser nome *GatewaySubnet* funcione corretamente. Este nome permite Azure, sabe que esta sub-rede deve ser utilizada para o gateway.

O tamanho mínimo da sua sub-rede gateway depende completamente na configuração ao qual pretende criar. Apesar de é possível criar uma sub-rede de gateway tão pequenas quanto /29, recomendamos que cria uma sub-rede gateway de /28 polegadas ou superior (/ 28, /27, /26, etc.). 

Criar um tamanho de gateway maior impede que executem contra limitações de tamanho do gateway. Por exemplo, poderá ter criado um gateway de rede virtual com um tamanho de sub-rede /29 gateway para uma ligação de S2S. Pode agora pretende configurar um S2S/ExpressRoute coexistência configuração. Que a configuração requer um tamanho de mínimo de sub-rede do gateway /28. Para criar a sua configuração, tem de modificar a sub-rede de gateway para acomodar os requisitos mínimos para a ligação, que é /28.

O exemplo seguinte do Gestor de recursos do PowerShell mostra uma sub-rede de gateway denominada GatewaySubnet. Pode ver que a notação CIDR Especifica uma /27, que permite endereços IP suficientes para a maior parte das configurações que existam atualmente.

    Add-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/27

[AZURE.INCLUDE [vpn-gateway-no-nsg](../../includes/vpn-gateway-no-nsg-include.md)] 


## <a name="lng"></a>Gateways de rede local

Quando criar uma configuração de gateway VPN, o gateway de rede local representa frequentemente sua localização no local. No modelo clássico de implementação, o gateway de rede local era designado por um Site Local. 

Dê um nome, o endereço IP público do dispositivo VPN no local, ao gateway de rede local e especifique os prefixos de endereços que estão localizados na localização no local. Azure analisa prefixos de endereço de destino para o tráfego de rede, a consulta a configuração que especificou para o gateway de rede local e encaminha pacotes em conformidade. Também pode especificar os gateways de rede local para configurações de VNet para VNet que utilizam uma ligação de gateway VPN.

O exemplo seguinte do PowerShell cria um novo gateway de rede local:

    New-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg `
    -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Por vezes, tem de modificar as definições de gateway de rede local. Por exemplo, quando lhe adicionar ou modificar o intervalo de endereços, ou se altera o endereço IP do dispositivo VPN. Para um VNet clássico, pode alterar estas definições no portal do clássico na página de redes locais. Para o Gestor de recursos, consulte o artigo [Modificar definições de gateway de rede local através do PowerShell](vpn-gateway-modify-local-network-gateway.md).

## <a name="resources"></a>Cmdlets REST APIs e PowerShell

Para recursos de suporte adicionais e requisitos de sintaxe específico ao utilizar REST APIs e os cmdlets do PowerShell para configurações de VPN Gateway, consulte as seguintes páginas:

|**Clássico** | **Gestor de recursos**|
|-----|----|
|[PowerShell](https://msdn.microsoft.com/library/mt270335.aspx)|[PowerShell](https://msdn.microsoft.com/library/mt163510.aspx)|
|[REST API](https://msdn.microsoft.com/library/jj154113.aspx)|[REST API](https://msdn.microsoft.com/library/mt163859.aspx)|


## <a name="next-steps"></a>Próximos passos

Consulte o artigo [Sobre o Gateway de VPN](vpn-gateway-about-vpngateways.md) para obter mais informações sobre as configurações de ligação disponíveis. 







 
