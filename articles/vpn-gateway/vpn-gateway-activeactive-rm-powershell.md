<properties
   pageTitle="Como configurar activo activo S2S VPN ligações com o Azure VPN Gateways utilizando o Gestor de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo explica o configurar ligações de activo activo com o Azure VPN Gateways utilizando o Gestor de recursos do Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="yushwang"
   manager="rossort"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/26/2016"
   ms.author="yushwang"/>

# <a name="configure-active-active-s2s-vpn-connections-with-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Configurar ligações de S2S VPN activo activo com o Azure VPN Gateways utilizando o Gestor de recursos do Azure e PowerShell

Este artigo explica os passos para criar activo activo cruzada local e as ligações de VNet para VNet utilizando o modelo de implementação do Gestor de recursos e PowerShell.


**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-highly-available-cross-premises-connections"></a>Acerca das ligações de publicação em local altamente disponíveis

Para alcançar elevada disponibilidade para publicação em local e a conectividade de VNet para VNet, deve implementar várias gateways VPN e estabelecer múltiplas ligações paralelas entre o seu redes e Azure. Consulte o artigo [altamente disponíveis cruzada local e a conectividade de VNet para VNet](./vpn-gateway-highlyavailable.md) para uma descrição geral das opções de conectividade e de topologia.

Este artigo fornece as instruções para configurar uma ligação de VPN activo activo cruzada local e activo activo ligação entre duas redes virtuais:

- [Parte 1 - criar e configurar o gateway da Azure VPN no modo de activo activo](#aagateway)

- [Parte 2 - estabelecer ligações activo activo cruzada local](#aacrossprem)

- [Parte 3 - estabelecer ligações de VNet para VNet activo activo](#aav2v)

- [Parte 4 - Atualize gateway existente entre activo activo e ativo suspensão](#aaupdate)

Pode combinar estes em conjunto para criar uma topologia de rede mais complexas, altamente disponível, consoante as suas necessidades.

>[AZURE.IMPORTANT] Tenha em atenção que o modo de activo activo só funciona no reversa SKU


## <a name ="aagateway"></a>Parte 1 - criar e configurar activo activo VPN gateways

Os passos seguintes irão configurar o seu gateway Azure VPN nos modos de activo activo. As principais diferenças entre os gateways activo activo e de suspensão de ativo:

- Tem de criar duas configurações de IP do Gateway com dois endereços IP públicos
- Precisa de definir o sinalizador de EnableActiveActiveFeature
- O gateway SKU tem de ser reversa

As outras propriedades são os mesmos como os gateways não activo activo. 

### <a name="before-you-begin"></a>Antes de começar

- Certifique-se de que tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Terá de instalar os cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Passo 1 - criar e configurar VNet1

#### <a name="1-declare-your-variables"></a>1. declarar variáveis

Para este exercício, vamos começar por declarar nossas variáveis. O exemplo abaixo declara as variáveis utilizando os valores para este exercício. Certifique-se de que substitua os valores com o seu próprio quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modificar as variáveis e, em seguida, copie e cole o seu consola do PowerShell.

    $Sub1          = "Ross"
    $RG1           = "TestAARG1"
    $Location1     = "West US"
    $VNetName1     = "TestVNet1"
    $FESubName1    = "FrontEnd"
    $BESubName1    = "Backend"
    $GWSubName1    = "GatewaySubnet"
    $VNetPrefix11  = "10.11.0.0/16"
    $VNetPrefix12  = "10.12.0.0/16"
    $FESubPrefix1  = "10.11.0.0/24"
    $BESubPrefix1  = "10.12.0.0/24"
    $GWSubPrefix1  = "10.12.255.0/27"
    $VNet1ASN      = 65010
    $DNS1          = "8.8.8.8"
    $GWName1       = "VNet1GW"
    $GW1IPName1    = "VNet1GWIP1"
    $GW1IPName2    = "VNet1GWIP2"
    $GW1IPconf1    = "gw1ipconf1"
    $GW1IPconf2    = "gw1ipconf2"
    $Connection12  = "VNet1toVNet2"
    $Connection151 = "VNet1toSite5_1"
    $Connection152 = "VNet1toSite5_2"

#### <a name="2-connect-to-your-subscription-and-create-a-new-resource-group"></a>2. se ligar à sua subscrição e criar um novo grupo de recursos

Certifique-se de que mudar para o modo de PowerShell para utilizar os cmdlets do Gestor de recursos. Para mais informações, consulte o artigo [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

Abra o seu consola do PowerShell e ligar à sua conta. Utilize o exemplo seguinte para ajudá-lo ligar:

    Login-AzureRmAccount
    Select-AzureRmSubscription -SubscriptionName $Sub1
    New-AzureRmResourceGroup -Name $RG1 -Location $Location1

#### <a name="3-create-testvnet1"></a>3. criar TestVNet1

O exemplo abaixo cria uma rede virtual com o nome TestVNet1 e três sub-redes, um GatewaySubnet chamado, FrontEnd denominado uma e back-end chamado um. Quando a substituição de valores, é importante que nome sempre a sua sub-rede aceda gateway especificamente GatewaySubnet. Se é-lhe o nome algo, a criação do seu gateway irá falhar. 

    $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
    $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
    $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1

    New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-active-active-mode"></a>Passo 2 - criar o gateway VPN para TestVNet1 com o modo de activo activo

#### <a name="1-create-the-public-ip-addresses-and-gateway-ip-configurations"></a>1. Crie os endereços IP e gateway público configurações IP

Pedir dois endereços IP públicos a serem atribuídos para o gateway que irá criar para sua VNet. Também irá definir as configurações de IP obrigatórias e de sub-rede. 

    $gw1pip1    = New-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    $gw1pip2    = New-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

    $vnet1      = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gw1ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf1 -Subnet $subnet1 -PublicIpAddress $gw1pip1
    $gw1ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW1IPconf2 -Subnet $subnet1 -PublicIpAddress $gw1pip2

#### <a name="2-create-the-vpn-gateway-with-active-active-configuration"></a>2. criar o gateway VPN com configuração activo activo

Crie o gateway de rede virtual para TestVNet1. Note que existem duas entradas GatewayIpConfig e o sinalizador EnableActiveActiveFeature está definido. Modo de activo activo requer um gateway VPN baseada em Encaminhamento de reversa SKU. Criar um gateway, pode demorar algum tempo (30 minutos ou mais para concluir).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gw1ipconf1,$gw1ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN -EnableActiveActiveFeature -Debug

#### <a name="3-obtain-the-gateway-public-ip-addresses-and-the-bgp-peer-ip-address"></a>3. obter os endereços IP públicos do gateway e o endereço IP do BGP ponto a ponto

Quando o gateway estiver criado, terá de obter o endereço de IP de ponto a ponto BGP no Azure VPN Gateway. Este endereço é necessário para configurar o Gateway de VPN Azure como um ponto de BGP para os seus dispositivos VPN no local.

    $gw1pip1 = Get-AzureRmPublicIpAddress -Name $GW1IPName1 -ResourceGroupName $RG1
    $gw1pip2 = Get-AzureRmPublicIpAddress -Name $GW1IPName2 -ResourceGroupName $RG1
    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

Utilize os seguintes cmdlets para mostrar os dois endereços IP públicos atribuídos para o gateway da VPN e os respetivos endereços de IP de ponto a ponto BGP correspondentes para cada instância do gateway:

    PS D:\> $gw1pip1.IpAddress
    40.112.190.5

    PS D:\> $gw1pip2.IpAddress
    138.91.156.129

    PS D:\> $vnet1gw.BgpSettingsText
    {
      "Asn": 65010,
      "BgpPeeringAddress": "10.12.255.4,10.12.255.5",
      "PeerWeight": 0
    }

A ordem do endereço IP público endereços para as instâncias de gateway e os endereços de efectuado de BGP correspondentes são os mesmos. Neste exemplo, o gateway VM com endereço IP público de 40.112.190.5 irá utilizar 10.12.255.4 como o endereço de efectuado BGP e o gateway com 138.91.156.129 irá utilizar 10.12.255.5. Esta informação é necessária, ao configurar o seus dispositivos VPN nos local estabelecer ligação ao gateway activo activo. O gateway é apresentado no diagrama abaixo, com todos os endereços:

![activo activo gateway](./media/vpn-gateway-activeactive-rm-powershell/active-active-gw.png)

Quando o gateway estiver criado, pode utilizar este gateway para estabelecer activo activo cruzada local ou uma ligação de VNet para VNet. As secções seguintes irão guiá-lo através dos passos para concluir o exercício.


## <a name ="aacrossprem"></a>Parte 2 - estabelecer uma ligação activo activo cruzada local

Para estabelecer uma ligação de publicação em local, tem de criar um Gateway de rede Local para representar o seu dispositivo VPN no local e uma ligação para ligar o gateway Azure VPN com o gateway de rede local. Neste exemplo, o gateway Azure VPN está no modo de activo activo. Como resultado, apesar de existir apenas um no local dispositivo VPN (gateway de rede local) e de recursos de uma ligação, ambas as instâncias de gateway Azure VPN estabelecer túneis S2S VPN com o dispositivo no local.

Antes de continuar, certifique-se de que concluiu [parte 1](#aagateway) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passo 1 - criar e configurar o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. declarar variáveis

Este exercício irão continuar a criar a configuração mostrada no diagrama de. Certifique-se de que substitua os valores nos que pretende utilizar para a configuração.

    $RG5           = "TestAARG5"
    $Location5     = "West US"
    $LNGName51     = "Site5_1"
    $LNGPrefix51   = "10.52.255.253/32"
    $LNGIP51       = "131.107.72.22"
    $LNGASN5       = 65050
    $BGPPeerIP51   = "10.52.255.253"

Algumas coisas a tenha em atenção relativamente aos parâmetros de gateway de rede local:

- O gateway de rede local pode ser na mesma ou em diferente localização e grupo de recursos, como o gateway VPN. Este exemplo mostra-las em diferentes grupos de recursos, mas na mesma localização Azure.

- Se existir apenas um dispositivo VPN no local, conforme mostrado acima, a ligação activo activo pode trabalhar com ou sem protocolo BGP. Este exemplo utiliza BGP para a ligação de publicação em local.

- Se BGP estiver ativada, o prefixo que precisa para declarar para o gateway de rede local é o endereço do anfitrião do seu endereço de IP de ponto a ponto BGP no seu dispositivo VPN. Neste caso, é uma /32 prefixo "10.52.255.253/32".

- Como um lembrete, tem de utilizar diferentes BGP ASNs entre o seu redes no local e o Azure VNet. Se são os mesmos, terá de alterar o seu ASN VNet se o seu dispositivo VPN no local que já utiliza o ASN-a-ponto com outros vizinhos BGP.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. criar o gateway de rede local para Site5
    
Antes de continuar, certifique-se de que ainda está ligado à subscrição 1. Crie o grupo de recursos se-ainda não criou.

    New-AzureRmResourceGroup       -Name $RG5 -Location $Location5
    New-AzureRmLocalNetworkGateway -Name $LNGName51 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP51 -AddressPrefix $LNGPrefix51 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP51

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2 – ligue a VNet gateway e gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. a obter dois gateways

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
    $lng5gw1 = Get-AzureRmLocalNetworkGateway   -Name $LNGName51 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Crie TestVNet1 Site5 ligação

Neste passo, irá criar a ligação a partir do TestVNet1 para Site5_1 com "EnableBGP" definido para $True.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection151 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw1 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-on-premises-vpn-device"></a>3. VPN e BGP parâmetros para o seu dispositivo VPN no local

O exemplo abaixo apresenta uma lista de parâmetros que irá introduzir a secção de configuração de BGP no seu dispositivo VPN no local para este exercício:

    - Site5 ASN: 65050
    - Site5 BGP IP: 10.52.255.253
    - Prefixos para anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - Azure VNet ASN: 65010
    - IP de BGP VNet Azure 1: 10.12.255.4 para túnel para 40.112.190.5
    - IP de BGP VNet Azure 2: 10.12.255.5 para túnel para 138.91.156.129
    - Rotas estáticas: destino 10.12.255.4/32, salto seguinte da interface do túnel VPN para 40.112.190.5 destino 10.12.255.5/32, salto seguinte da interface do túnel VPN para 138.91.156.129
    - eBGP Multihop: Certifique-se a opção "multihop" para eBGP é activado no seu dispositivo, se for necessário

A ligação deve ser estabelecida depois de alguns minutos e, a sessão peering BGP será iniciado assim que a ligação IPsec é estabelecida. Este exemplo até ao momento tenha configurado apenas um dispositivo VPN no local, que resulta no diagrama é mostrado abaixo:

![ativo-ativo-crossprem](./media/vpn-gateway-activeactive-rm-powershell/active-active.png)

### <a name="step-3---connect-two-on-premises-vpn-devices-to-the-active-active-vpn-gateway"></a>Passo 3 - ligar dois dispositivos VPN no local para o gateway VPN activo activo

Se tiver dois dispositivos VPN na mesma rede no local, é possível alcançar redundância dupla ligando o gateway Azure VPN para o dispositivo VPN segundo.

#### <a name="1-create-the-second-local-network-gateway-for-site5"></a>1. criar o segundo gateway de rede local para Site5

Tenha em atenção que o endereço IP do gateway, prefixo de endereço e BGP efectuado endereço para o gateway de rede local segundo devem não sobrepor-se com o gateway de rede local anterior para a mesma rede no local. 

    $LNGName52     = "Site5_2"
    $LNGPrefix52   = "10.52.255.254/32"
    $LNGIP52       = "131.107.72.23"
    $BGPPeerIP52   = "10.52.255.254"

    New-AzureRmLocalNetworkGateway -Name $LNGName52 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP52 -AddressPrefix $LNGPrefix52 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP52
 
#### <a name="2-connect-the-vnet-gateway-and-the-second-local-network-gateway"></a>2. ligar o gateway VNet e o segundo gateway de rede local

Criar a ligação de TestVNet1 para Site5_2 com "EnableBGP" definido para $True

    $lng5gw2 = Get-AzureRmLocalNetworkGateway   -Name $LNGName52 -ResourceGroupName $RG5

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection152 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw2 -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP True

#### <a name="3-vpn-and-bgp-parameters-for-your-second-on-premises-vpn-device"></a>3. VPN e BGP parâmetros para o seu dispositivo VPN segundo no local

Da mesma forma, abaixo de listas os parâmetros irá introduzir o segundo dispositivo VPN:

    - Site5 ASN: 65050
    - Site5 BGP IP: 10.52.255.254
    - Prefixos para anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - Azure VNet ASN: 65010
    - IP de BGP VNet Azure 1: 10.12.255.4 para túnel para 40.112.190.5
    - IP de BGP VNet Azure 2: 10.12.255.5 para túnel para 138.91.156.129
    - Rotas estáticas: destino 10.12.255.4/32, salto seguinte da interface do túnel VPN para 40.112.190.5 destino 10.12.255.5/32, salto seguinte da interface do túnel VPN para 138.91.156.129
    - eBGP Multihop: Certifique-se a opção "multihop" para eBGP é activado no seu dispositivo, se for necessário

Assim que a ligação (túneis) são estabelecida, terá de dispositivos VPN redundantes duplos e túneis ligar-se a sua rede no local e o Azure:

![duas-redundância-crossprem](./media/vpn-gateway-activeactive-rm-powershell/dual-redundancy.png)


## <a name ="aav2v"></a>Parte 3 - estabelecer uma ligação de VNet para VNet activo activo

Esta secção cria uma ligação de VNet para VNet activo activo com BGP. 

Continuam as instruções abaixo dos passos anteriores listados em cima. Tem de concluir a [parte 1](#aagateway) para criar e configurar TestVNet1 e o Gateway de VPN com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passo 1 - criar TestVNet2 e o gateway VPN

É importante para se certificar de que o espaço de endereços IP da rede virtual novo, TestVNet2, não se sobrepõe com qualquer um dos seus intervalos VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar o VNet para VNet ligações entre diferentes subscrições; consulte [Configurar uma ligação de VNet para VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para saber mais detalhes. Certifique-se de que adiciona o "-EnableBgp $True" quando criar ligações para ativar BGP.

#### <a name="1-declare-your-variables"></a>1. declarar variáveis

Certifique-se de que substitua os valores nos que pretende utilizar para a configuração.

    $RG2           = "TestAARG2"
    $Location2     = "East US"
    $VNetName2     = "TestVNet2"
    $FESubName2    = "FrontEnd"
    $BESubName2    = "Backend"
    $GWSubName2    = "GatewaySubnet"
    $VNetPrefix21  = "10.21.0.0/16"
    $VNetPrefix22  = "10.22.0.0/16"
    $FESubPrefix2  = "10.21.0.0/24"
    $BESubPrefix2  = "10.22.0.0/24"
    $GWSubPrefix2  = "10.22.255.0/27"
    $VNet2ASN      = 65020
    $DNS2          = "8.8.8.8"
    $GWName2       = "VNet2GW"
    $GW2IPName1    = "VNet2GWIP1"
    $GW2IPconf1    = "gw2ipconf1"
    $GW2IPName2    = "VNet2GWIP2"
    $GW2IPconf2    = "gw2ipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Crie TestVNet2 no novo grupo de recursos

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2

    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-active-active-vpn-gateway-for-testvnet2"></a>3. criar o gateway VPN activo activo para TestVNet2

Pedir dois endereços IP públicos a serem atribuídos para o gateway que irá criar para sua VNet. Também irá definir as configurações de IP obrigatórias e de sub-rede. 

    $gw2pip1    = New-AzureRmPublicIpAddress -Name $GW2IPName1 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic
    $gw2pip2    = New-AzureRmPublicIpAddress -Name $GW2IPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2      = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2    = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gw2ipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf1 -Subnet $subnet2 -PublicIpAddress $gw2pip1
    $gw2ipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GW2IPconf2 -Subnet $subnet2 -PublicIpAddress $gw2pip2

Crie um gateway VPN com o número de como e o sinalizador de "EnableActiveActiveFeature". Tenha em atenção que tem de substituir a predefinição ASN no seu gateways Azure VPN. ASNs para o VNets ligada têm de ser diferentes para activar BGP e encaminhamento de trânsito.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gw2ipconf1,$gw2ipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet2ASN -EnableActiveActiveFeature

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2 - ligar os gateways TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma subscrição. Pode concluir este passo na mesma sessão PowerShell.

#### <a name="1-get-both-gateways"></a>1. a obter ambos os gateways

Certifique-se de que inicie sessão no e ligar a 1 de subscrição.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. Crie ambas as ligações

Neste passo, irá criar a ligação de TestVNet1 para TestVNet2 e a ligação a partir do TestVNet2 TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Certifique-se de que ativar BGP para ambas as ligações.

Depois de concluir estes passos, a ligação estabelecerão no alguns minutos e o BGP efectuado sessão será o assim que a ligação VNet para VNet é concluída com redundância dupla:

![ativo-ativo-v2v](./media/vpn-gateway-activeactive-rm-powershell/vnet-to-vnet.png)

## <a name ="aaupdate"></a>Parte 4 - Atualize gateway existente entre activo activo e ativo suspensão

A última secção irá descrevem como pode configurar um gateway Azure VPN existente a partir do active em espera para o modo de activo activo ou vice versa.

>[AZURE.IMPORTANT] Tenha em atenção que o modo de activo activo só funciona no reversa SKU

### <a name="configure-an-active-standby-gateway-to-active-active-gateway"></a>Configurar um gateway ativo em espera para o gateway activo activo

#### <a name="1-gateway-parameters"></a>1. parâmetros de gateway

O exemplo seguinte converte um gateway suspensão ativo um gateway activo activo. Tem de criar outro endereço IP público, em seguida, adicionar uma segunda configuração de IP do Gateway. Abaixo mostra os parâmetros utilizados:

    $GWName     = "TestVNetAA1GW"
    $VNetName   = "TestVNetAA1"
    $RG         = "TestVPNActiveActive01"
    $GWIPName2  = "gwpip2"
    $GWIPconf2  = "gw1ipconf2"

    $vnet       = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG
    $subnet     = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $location   = $gw.Location

#### <a name="2-create-the-public-ip-address-then-add-the-second-gateway-ip-configuration"></a>2. Crie o endereço IP público, em seguida, adicionar a segunda IP configuração de gateway

    $gwpip2     = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG -Location $location -AllocationMethod Dynamic
    Add-AzureRmVirtualNetworkGatewayIpConfig -VirtualNetworkGateway $gw -Name $GWIPconf2 -Subnet $subnet -PublicIpAddress $gwpip2 

#### <a name="3-enable-active-active-mode-and-update-the-gateway"></a>3. ativar o modo de activo activo e atualize o gateway

Tem de definir o objeto do gateway no PowerShell acionado a atualização real. O SKU do objeto gateway também tem de ser alterado para reversa desde que foi criado anteriormente como padrão.

    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -EnableActiveActiveFeature -GatewaySku HighPerformance

Esta atualização pode demorar 30 a 45 minutos.

### <a name="configure-an-active-active-gateway-to-active-standby-gateway"></a>Configurar um gateway activo activo ao gateway ativo suspensão

#### <a name="1-gateway-parameters"></a>1. parâmetros de gateway

Utilizar os mesmos parâmetros como acima, obter o nome da configuração de IP que pretende remover.

    $GWName     = "TestVNetAA1GW"
    $RG         = "TestVPNActiveActive01"

    $gw         = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    $ipconfname = $gw.IpConfigurations[1].Name

#### <a name="2-remove-the-gateway-ip-configuration-and-disable-the-active-active-mode"></a>2. remover a configuração de IP de gateway e desativar o modo de activo activo

Da mesma forma, tem de definir o objeto do gateway no PowerShell acionado a atualização real.

    Remove-AzureRmVirtualNetworkGatewayIpConfig -Name $ipconfname -VirtualNetworkGateway $gw
    Set-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -DisableActiveActiveFeature

Esta atualização pode demorar até 30 45 minutos.


## <a name="next-steps"></a>Próximos passos

Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.

