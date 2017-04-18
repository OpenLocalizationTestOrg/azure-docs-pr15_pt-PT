<properties
   pageTitle="Como configurar BGP no Azure VPN Gateways utilizando o Gestor de recursos do Azure e PowerShell | Microsoft Azure"
   description="Este artigo explica o configurar BGP com o Azure VPN Gateways utilizando o Gestor de recursos do Azure e PowerShell."
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
   ms.date="04/15/2016"
   ms.author="yushwang"/>

# <a name="how-to-configure-bgp-on-azure-vpn-gateways-using-azure-resource-manager-and-powershell"></a>Como configurar BGP no Azure VPN Gateways utilizando o Gestor de recursos do Azure e PowerShell

Este artigo explica os passos para ativar o BGP uma ligação de Site para Site (S2S) VPN cruzada local e uma ligação de VNet para VNet utilizando o modelo de implementação do Gestor de recursos e PowerShell.


**Sobre modelos de implementação do Azure**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)] 

## <a name="about-bgp"></a>Sobre BGP

BGP é o protocolo de encaminhamento padrão era utilizado frequentemente em da Internet para o encaminhamento e acessibilidade informações entre duas ou mais redes do exchange. BGP permite que os Gateways de VPN do Azure e os dispositivos VPN no local, denominados BGP colegas ou vizinhos para "encaminha" do exchange que irão informá-ambos os gateways sobre a disponibilidade e a acessibilidade para esses prefixos até gateways ou routers envolvido. Também pode ativar BGP trânsito encaminhamento entre várias redes clicando no ícone propagação rotas que um gateway BGP aprende a partir de um ponto BGP para todos os outros colegas BGP.

Consulte o artigo [Descrição geral de BGP com o Azure VPN Gateways](./vpn-gateway-bgp-overview.md) para debate mais sobre as vantagens de BGP e para compreender os requisitos técnicos e considerações de utilização de BGP.

## <a name="getting-started-with-bgp-on-azure-vpn-gateways"></a>Introdução ao BGP no gateways VPN do Azure

Este artigo irá guiá-lo através dos passos para efetuar as seguintes tarefas:

- [Parte 1 - ativar BGP no seu gateway VPN do Azure](#enablebgp)

- [Parte 2 - estabelecer uma ligação de publicação em local com BGP](#crossprembgp)

- [Parte 3 - estabelecer uma ligação de VNet para VNet com BGP](#v2vbgp)

Cada parte das instruções de formulários um bloco modular básico para ativação BGP na conectividade de rede. Se concluir todas as três partes, irá criar a topologia de conforme o diagrama seguinte:

![Topologia BGP](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

Pode combinar estes em conjunto para criar uma rede de trânsito mais complexas, com várias esperança, corresponder às suas necessidades.

## <a name ="enablebgp"></a>Parte 1 - Configurar BGP no Gateway Azure VPN

Os seguintes passos de configuração configurará os parâmetros BGP do gateway Azure VPN conforme o diagrama seguinte:

![BGP Gateway](./media/vpn-gateway-bgp-resource-manager-ps/bgp-gateway.png)

### <a name="before-you-begin"></a>Antes de começar

- Certifique-se de que tem uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode ativá sua [benefícios de subscritor MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) ou inicie sessão para cima de uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/).
    
- Terá de instalar os cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="step-1---create-and-configure-vnet1"></a>Passo 1 - criar e configurar VNet1 

#### <a name="1-declare-your-variables"></a>1. declarar variáveis

Para este exercício, vamos começar por declarar nossas variáveis. O exemplo abaixo declara as variáveis utilizando os valores para este exercício. Certifique-se de que substitua os valores com o seu próprio quando configurar para produção. Pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modificar as variáveis e, em seguida, copie e cole o seu consola do PowerShell.

    $Sub1          = "Replace_With_Your_Subcription_Name"
    $RG1           = "TestBGPRG1"
    $Location1     = "East US"
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
    $GWIPName1     = "VNet1GWIP"
    $GWIPconfName1 = "gwipconf1"
    $Connection12  = "VNet1toVNet2"
    $Connection15  = "VNet1toSite5"

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

### <a name="step-2---create-the-vpn-gateway-for-testvnet1-with-bgp-parameters"></a>Passo 2 - criar o Gateway de VPN para TestVNet1 com parâmetros BGP

#### <a name="1-create-the-ip-and-subnet-configurations"></a>1. Crie as configurações de IP e de sub-rede

Pedir um endereço IP público a serem atribuídos para o gateway que irá criar para sua VNet. Também irá definir as configurações de IP obrigatórias e de sub-rede. 

    $gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic
    
    $vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
    $subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
    $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

#### <a name="2-create-the-vpn-gateway-with-the-as-number"></a>2. Crie o gateway VPN com o número como

Crie o gateway de rede virtual para TestVNet1. Repare que BGP requer um gateway VPN baseada em encaminhar e o parâmetro de adição, - Asn, para definir o ASN (tal como número) para TestVNet1. Criar um gateway, pode demorar algum tempo (30 minutos ou mais para concluir).

    New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku HighPerformance -Asn $VNet1ASN

#### <a name="3-obtain-the-azure-bgp-peer-ip-address"></a>3. obter o endereço IP do Azure BGP ponto a ponto

Quando o gateway estiver criado, terá de obter o endereço de IP de ponto a ponto BGP no Azure VPN Gateway. Este endereço é necessário para configurar o Gateway de VPN Azure como um ponto de BGP para os seus dispositivos VPN no local.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet1gw.BgpSettingsText

O último comando irá apresentar as configurações de BGP correspondentes no Azure VPN Gateway; Por exemplo:

    $vnet1gw.BgpSettingsText
    {
        "Asn": 65010,
        "BgpPeeringAddress": "10.12.255.30",
        "PeerWeight": 0
    }

Quando o gateway estiver criado, pode utilizar este gateway para estabelecer ligação cruzada local ou uma ligação de VNet para VNet com BGP. As secções seguintes irão guiá-lo através dos passos para concluir o exercício.

## <a name ="crossprembbgp"></a>Parte 2 - estabelecer uma ligação de publicação em local com BGP

Para estabelecer uma ligação de publicação em local, tem de criar um Gateway de rede Local para representar o seu dispositivo VPN no local e uma ligação para ligar o gateway Azure VPN com o gateway de rede local. A diferença entre as instruções neste artigo é as propriedades adicionais necessárias para especificar os parâmetros de configuração de BGP.

![BGP para publicação em local](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crossprem.png)

Antes de continuar, certifique-se de que concluiu [parte 1](#enablebgp) deste exercício.

### <a name="step-1---create-and-configure-the-local-network-gateway"></a>Passo 1 - criar e configurar o gateway de rede local

#### <a name="1-declare-your-variables"></a>1. declarar variáveis

Este exercício irão continuar a criar a configuração mostrada no diagrama de. Certifique-se de que substitua os valores nos que pretende utilizar para a configuração.

    $RG5           = "TestBGPRG5"
    $Location5     = "East US 2"
    $LNGName5      = "Site5"
    $LNGPrefix50   = "10.52.255.254/32"
    $LNGIP5        = "Your_VPN_Device_IP"
    $LNGASN5       = 65050
    $BGPPeerIP5    = "10.52.255.254"

Algumas coisas a tenha em atenção relativamente aos parâmetros de gateway de rede local:

- O gateway de rede local pode ser na mesma ou em diferente localização e grupo de recursos, como o gateway VPN. Este exemplo mostra-os em grupos de recurso diferente em diferentes localizações.

- O prefixo mínimo que necessário para declarar para o gateway de rede local é o endereço do anfitrião do seu endereço de IP de ponto a ponto BGP no seu dispositivo VPN. Neste caso, é uma /32 prefixo "10.52.255.254/32".

- Como um lembrete, tem de utilizar diferentes BGP ASNs entre o seu redes no local e o Azure VNet. Se são os mesmos, terá de alterar o seu ASN VNet se o seu dispositivo VPN no local que já utiliza o ASN-a-ponto com outros vizinhos BGP.
    
Antes de continuar, certifique-se de que ainda está ligado à subscrição 1.

#### <a name="2-create-the-local-network-gateway-for-site5"></a>2. criar o gateway de rede local para Site5

Certifique-se de que criar o grupo de recursos se de que não é criado, antes de criar o gateway de rede local. Repare os dois parâmetros adicionais para o gateway de rede local: Asn e BgpPeerAddress.

    New-AzureRmResourceGroup -Name $RG5 -Location $Location5

    New-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5 -Location $Location5 -GatewayIpAddress $LNGIP5 -AddressPrefix $LNGPrefix50 -Asn $LNGASN5 -BgpPeeringAddress $BGPPeerIP5

### <a name="step-2---connect-the-vnet-gateway-and-local-network-gateway"></a>Passo 2 – ligue a VNet gateway e gateway de rede local

#### <a name="1-get-the-two-gateways"></a>1. a obter dois gateways

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1  -ResourceGroupName $RG1
        $lng5gw  = Get-AzureRmLocalNetworkGateway -Name $LNGName5 -ResourceGroupName $RG5

#### <a name="2-create-the-testvnet1-to-site5-connection"></a>2. Crie TestVNet1 Site5 ligação

Neste passo, irá criar a ligação a partir do TestVNet1 para Site5. Tem de especificar "-EnableBGP $True" para activar BGP para esta ligação. Como debatido anteriormente, é possível ter BGP e que não sejam BGP ligações para o mesmo Gateway de VPN Azure. A menos que BGP está ativado na propriedade de ligação, Azure não permitirá BGP para esta ligação apesar de parâmetros BGP já estão configurados em ambos os gateways.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -LocalNetworkGateway2 $lng5gw -Location $Location1 -ConnectionType IPsec -SharedKey 'AzureA1b2C3' -EnableBGP $True


O exemplo abaixo apresenta uma lista de parâmetros que irá introduzir a secção de configuração de BGP no seu dispositivo VPN no local para este exercício:

    - Site5 ASN: 65050
    - Site5 BGP IP: 10.52.255.254
    - Prefixos para anunciar: (por exemplo) 10.51.0.0/16 e 10.52.0.0/16
    - Azure VNet ASN: 65010
    - Azure VNet BGP IP: 10.12.255.30
    - Rota estática: adicionar uma rota para 10.12.255.30/32, com salto seguinte a ser a interface de túnel VPN no seu dispositivo
    - eBGP Multihop: Certifique-se a opção "multihop" para eBGP é activado no seu dispositivo, se for necessário

A ligação deve ser estabelecida depois de alguns minutos e, a sessão peering BGP será iniciado assim que a ligação IPsec é estabelecida.
 
## <a name ="v2vbgp"></a>Parte 3 - estabelecer uma ligação de VNet para VNet com BGP

Esta secção adiciona uma ligação de VNet para VNet com BGP, conforme mostrado no diagrama abaixo. 

![BGP para VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-vnet2vnet.png)

Continuam as instruções abaixo dos passos anteriores listados em cima. Tem de concluir [parte I](#enablebgp) para criar e configurar TestVNet1 e o Gateway de VPN com BGP. 

### <a name="step-1---create-testvnet2-and-the-vpn-gateway"></a>Passo 1 - criar TestVNet2 e o gateway VPN

É importante para se certificar de que o espaço de endereços IP da rede virtual novo, TestVNet2, não se sobrepõe com qualquer um dos seus intervalos VNet.

Neste exemplo, as redes virtuais pertencem à mesma subscrição. Pode configurar VNet para VNet ligações entre diferentes subscrições; consulte [Configurar uma ligação de VNet para VNet](./vpn-gateway-vnet-vnet-rm-ps.md) para saber mais detalhes. Certifique-se de que adiciona o "-EnableBgp $True" quando criar ligações para ativar BGP.

#### <a name="1-declare-your-variables"></a>1. declarar variáveis

Certifique-se de que substitua os valores nos que pretende utilizar para a configuração.

    $RG2           = "TestBGPRG2"
    $Location2     = "West US"
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
    $GWIPName2     = "VNet2GWIP"
    $GWIPconfName2 = "gwipconf2"
    $Connection21  = "VNet2toVNet1"
    $Connection12  = "VNet1toVNet2"

#### <a name="2-create-testvnet2-in-the-new-resource-group"></a>2. Crie TestVNet2 no novo grupo de recursos

    New-AzureRmResourceGroup -Name $RG2 -Location $Location2
    
    $fesub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName2 -AddressPrefix $FESubPrefix2
    $besub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName2 -AddressPrefix $BESubPrefix2
    $gwsub2 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName2 -AddressPrefix $GWSubPrefix2

    New-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2 -Location $Location2 -AddressPrefix $VNetPrefix21,$VNetPrefix22 -Subnet $fesub2,$besub2,$gwsub2

#### <a name="3-create-the-vpn-gateway-for-testvnet2-with-bgp-parameters"></a>3. criar o gateway VPN para TestVNet2 com parâmetros BGP

Pedir um endereço IP público a serem atribuídos para o gateway que irá criar para sua VNet. Também irá definir as configurações de IP obrigatórias e de sub-rede. 

    $gwpip2    = New-AzureRmPublicIpAddress -Name $GWIPName2 -ResourceGroupName $RG2 -Location $Location2 -AllocationMethod Dynamic

    $vnet2     = Get-AzureRmVirtualNetwork -Name $VNetName2 -ResourceGroupName $RG2
    $subnet2   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet2
    $gwipconf2 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName2 -Subnet $subnet2 -PublicIpAddress $gwpip2

Crie um gateway VPN com o número como. Tenha em atenção que tem de substituir a predefinição ASN no seu gateways Azure VPN. ASNs para o VNets ligada têm de ser diferentes para activar BGP e encaminhamento de trânsito.

    New-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2 -Location $Location2 -IpConfigurations $gwipconf2 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard -Asn $VNet2ASN

### <a name="step-2---connect-the-testvnet1-and-testvnet2-gateways"></a>Passo 2 - ligar os gateways TestVNet1 e TestVNet2

Neste exemplo, ambos os gateways estão na mesma subscrição. Pode concluir este passo na mesma sessão PowerShell.

#### <a name="1-get-both-gateways"></a>1. a obter ambos os gateways

Certifique-se de que iniciar sessão e ligar a 1 de subscrição.

    $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
    $vnet2gw = Get-AzureRmVirtualNetworkGateway -Name $GWName2 -ResourceGroupName $RG2
    
#### <a name="2-create-both-connections"></a>2. Crie ambas as ligações

Neste passo, irá criar a ligação de TestVNet1 para TestVNet2 e a ligação a partir do TestVNet2 TestVNet1.

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection12 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet2gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

    New-AzureRmVirtualNetworkGatewayConnection -Name $Connection21 -ResourceGroupName $RG2 -VirtualNetworkGateway1 $vnet2gw -VirtualNetworkGateway2 $vnet1gw -Location $Location2 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3' -EnableBgp $True

>[AZURE.IMPORTANT] Certifique-se de que ativar BGP para ambas as ligações.

Depois de concluir estes passos, a ligação será estabelecida de alguns minutos e a sessão peering BGP serão para cima de uma vez a ligação VNet para VNet está concluída.

Se tiver concluído todas as três partes de neste exercício, irá estabelecidos uma topologia da rede conforme apresentado abaixo:

![BGP para VNet para VNet](./media/vpn-gateway-bgp-resource-manager-ps/bgp-crosspremv2v.png)

## <a name="next-steps"></a>Próximos passos

Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.

