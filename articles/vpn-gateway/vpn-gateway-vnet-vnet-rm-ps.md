<properties
   pageTitle="Ligar Azure VNets com VPN Gateway e PowerShell | Microsoft Azure"
   description="Este artigo explica o interligar redes virtuais através do Gestor de recursos do Azure e PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/31/2016"
   ms.author="cherylmc"/>

# <a name="configure-a-vnet-to-vnet-connection-for-resource-manager-using-powershell"></a>Configurar uma ligação de VNet para VNet para Gestor de recursos através do PowerShell

> [AZURE.SELECTOR]
- [Gestor de recursos - Azure Portal](vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)
- [Gestor de recursos - PowerShell](vpn-gateway-vnet-vnet-rm-ps.md)
- [Clássico - clássico de Portal](virtual-networks-configure-vnet-to-vnet-connection.md)

Este artigo explica os passos para criar uma ligação entre VNets no modelo de implementação de Gestor de recursos utilizando o VPN Gateway. As redes virtuais podem ser na mesmas ou em diferentes regiões e das mesmas ou em diferentes subscrições.


![Diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="deployment-models-and-methods-for-vnet-to-vnet-connections"></a>Modelos de implementação e métodos para VNet para VNet ligações

[AZURE.INCLUDE [deployment models](../../includes/vpn-gateway-deployment-models-include.md)]

A tabela seguinte mostra os modelos de implementação atualmente disponíveis e os métodos para VNet para VNet configurações. Quando um artigo com os passos de configuração estiver disponível, podemos ligar diretamente à mesma a partir desta tabela.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

#### <a name="vnet-peering"></a>VNet efectuado

[AZURE.INCLUDE [vpn-gateway-vnetpeeringlink](../../includes/vpn-gateway-vnetpeeringlink-include.md)]


## <a name="about-vnet-to-vnet-connections"></a>Acerca das ligações de VNet para VNet

(VNet para VNet) que liga uma rede virtual a outra rede virtual é semelhante a ligar-se uma VNet para uma localização do site no local. Ambos os tipos de conectividade utilizam um gateway Azure VPN para fornecer um túnel seguro através do IPsec/IKE. VNets que ligar pode estar em regiões diferentes. Ou em diferentes subscrições. Ainda pode combinar VNet para VNet comunicação com configurações de múltiplos sites. Permite-estabelecer topologias de rede que combinam cruzada local conectividade com a conectividade de rede entre virtual, conforme mostrado no diagrama seguinte:


![Acerca das ligações](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### <a name="why-connect-virtual-networks"></a>Por que motivo ligar redes virtuais?

Pretende ligar redes virtuais pelos seguintes motivos:

- **Cruzada região geo-redundância e geo presença**
    - Pode configurar o seu próprio geo replicação ou a sincronização com a conectividade segura sem ultrapassar pontos finais de acesso à Internet.
    - Com o Gestor de tráfego do Azure e Balanceador de carga, pode configurar o altamente disponível carga de trabalho com geo redundância entre as regiões Azure múltiplos. Um exemplo importante é configurar o SQL sempre com grupos de disponibilidade propagação entre as regiões Azure múltiplos.

- **Aplicações de várias camadas regionais com isolamento ou limite administrativo**
    - Dentro da mesma região, pode configurar as aplicações com várias camadas com múltiplas redes virtuais interligados devido a isolamento ou requisitos administrativos.


### <a name="vnet-to-vnet-faq"></a>Perguntas mais frequentes do VNet para VNet

[AZURE.INCLUDE [vpn-gateway-vnet-vnet-faq](../../includes/vpn-gateway-vnet-vnet-faq-include.md)] 


## <a name="which-set-of-steps-should-i-use"></a>Qual o conjunto de passos devo utilizar?

Neste artigo, verá duas diferentes conjuntos de passos. Um conjunto de passos para [VNets que residem na mesma subscrição](#samesub)e outro para [VNets que residem na diferentes subscrições](#difsub). A diferença entre os conjuntos de chave é se pode criar e configurar todos os recursos de gateway e de rede virtual dentro da mesma sessão de PowerShell.

Os passos neste artigo utilizam variáveis declarados no início de cada secção. Se já estiver a trabalhar com VNets existente, modifique as variáveis para refletir as definições no seu ambiente. 

![Ambas as ligações](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


## <a name="samesub"></a>Como ligar VNets que estão na mesma subscrição

![Diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vrmps.png)

### <a name="before-you-begin"></a>Antes de começar
    
Antes de começar, tem de instalar os cmdlets do PowerShell do Azure Gestor de recursos. Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter mais informações sobre como instalar os cmdlets do PowerShell.

### <a name="Step1"></a>Passo 1 - Planear a sua intervalos de endereços IP


Nos passos seguintes, podemos criar duas redes virtuais juntamente com as respetivas sub-redes respetivos gateway e configurações. Em seguida, criamos uma ligação VPN entre as duas VNets. É importante planear os intervalos de endereços IP para a configuração de rede. Tenha em atenção que deve certificar-se de que nenhuma das suas VNet intervalos ou intervalos de rede local sobrepor-se de qualquer forma.

Utilizamos os seguintes valores nos exemplos:

**Valores para TestVNet1:**

- Nome de VNet: TestVNet1
- Grupo de recursos: TestRG1
- Localização: Leste dos e.u.a.
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- Back-end: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet1GW
- Endereço IP público: VNet1GWIP
- VPNType: RouteBased
- Connection(1to4): VNet1toVNet4
- Connection(1to5): VNet1toVNet5
- Tipo de ligação: VNet2VNet


**Valores para TestVNet4:**

- Nome de VNet: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- Back-end: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0/27
- Grupo de recursos: TestRG4
- Localização: Oeste dos e.u.a.
- Servidor DNS: 8.8.8.8
- GatewayName: VNet4GW
- Endereço IP público: VNet4GWIP
- VPNType: RouteBased
- Ligação: VNet4toVNet1
- Tipo de ligação: VNet2VNet



### <a name="Step2"></a>Passo 2 - criar e configurar TestVNet1

1. Declarar variáveis

    Comece por declarar variáveis. Este exemplo declara as variáveis utilizando os valores para este exercício. Na maioria dos casos, deve substituir os valores com o seu próprio. No entanto, pode utilizar estas variáveis se estiver a executar os passos para se familiarizar com este tipo de configuração. Modificar as variáveis se for necessário, em seguida, copie e cole-os no seu consola do PowerShell.

        $Sub1 = "Replace_With_Your_Subcription_Name"
        $RG1 = "TestRG1"
        $Location1 = "East US"
        $VNetName1 = "TestVNet1"
        $FESubName1 = "FrontEnd"
        $BESubName1 = "Backend"
        $GWSubName1 = "GatewaySubnet"
        $VNetPrefix11 = "10.11.0.0/16"
        $VNetPrefix12 = "10.12.0.0/16"
        $FESubPrefix1 = "10.11.0.0/24"
        $BESubPrefix1 = "10.12.0.0/24"
        $GWSubPrefix1 = "10.12.255.0/27"
        $DNS1 = "8.8.8.8"
        $GWName1 = "VNet1GW"
        $GWIPName1 = "VNet1GWIP"
        $GWIPconfName1 = "gwipconf1"
        $Connection14 = "VNet1toVNet4"
        $Connection15 = "VNet1toVNet5"

2. Ligar à sua subscrição

    Mudar para o modo de PowerShell para utilizar os cmdlets do Gestor de recursos. Abra o seu consola do PowerShell e ligar à sua conta. Utilize o exemplo seguinte para ajudá-lo ligar:

        Login-AzureRmAccount

    Verifique as subscrições para a conta.

        Get-AzureRmSubscription 

    Especifique a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName $Sub1

3. Criar um novo grupo de recursos

        New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Criar as configurações de sub-rede para TestVNet1

    Este exemplo cria uma denominado TestVNet1 e três sub-redes, GatewaySubnet denominado uma, FrontEnd denominado uma e back-end chamado um de rede, virtual. Quando a substituição de valores, é importante que nome sempre a sua sub-rede aceda gateway especificamente GatewaySubnet. Se é-lhe o nome algo, a criação do seu gateway irá falhar. 

    O exemplo seguinte utiliza as variáveis que definiu anteriormente. Neste exemplo, a sub-rede gateway está a utilizar um /27. Enquanto é possível criar uma sub-rede de gateway tão pequenas quanto /29, recomendamos que cria uma sub-rede maior que inclui mais endereços ao selecionar, pelo menos, /28 ou /27. Isto permitirá para endereços suficientes acomodar possíveis configurações adicionais que pode querer no futuro. 

        $fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
        $besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
        $gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Criar TestVNet1

        New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Pedido de um endereço IP público

    Pedir um endereço IP público a serem atribuídos para o gateway que irá criar para sua VNet. Repare que o AllocationMethod é dinâmicos. Não é possível especificar o endereço IP que pretende utilizar. Dinamicamente é atribuído ao seu gateway. 

        $gwpip1 = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 `
        -Location $Location1 -AllocationMethod Dynamic

7. Criar a configuração de gateway

    A configuração de gateway define a sub-rede e o endereço IP público para utilizar. Utilize o exemplo para criar a sua configuração de gateway. 

        $vnet1 = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
        $subnet1 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
        $gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 `
        -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Criar o gateway para TestVNet1

    Neste passo, crie o gateway de rede virtual para sua TestVNet1. Configurações de VNet para VNet requerem uma RouteBased VpnType. Criar um gateway, pode demorar algum tempo (45 minutos ou mais para concluir).

        New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 `
        -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-3---create-and-configure-testvnet4"></a>Passo 3 - criar e configurar TestVNet4

Depois de ter configurado TestVNet1, crie TestVNet4. Siga os passos abaixo, substituir os valores com o seu próprio quando for necessário. Este passo pode ser efetuado dentro a mesma sessão PowerShell porque está na mesma subscrição.

1. Declarar variáveis

    Certifique-se de que substitua os valores nos que pretende utilizar para a configuração.

        $RG4 = "TestRG4"
        $Location4 = "West US"
        $VnetName4 = "TestVNet4"
        $FESubName4 = "FrontEnd"
        $BESubName4 = "Backend"
        $GWSubName4 = "GatewaySubnet"
        $VnetPrefix41 = "10.41.0.0/16"
        $VnetPrefix42 = "10.42.0.0/16"
        $FESubPrefix4 = "10.41.0.0/24"
        $BESubPrefix4 = "10.42.0.0/24"
        $GWSubPrefix4 = "10.42.255.0/27"
        $DNS4 = "8.8.8.8"
        $GWName4 = "VNet4GW"
        $GWIPName4 = "VNet4GWIP"
        $GWIPconfName4 = "gwipconf4"
        $Connection41 = "VNet4toVNet1"

    Antes de continuar, certifique-se de que ainda está ligado à subscrição 1.

2. Criar um novo grupo de recursos

        New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Criar as configurações de sub-rede para TestVNet4

        $fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
        $besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
        $gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Criar TestVNet4

        New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Pedido de um endereço IP público

        $gwpip4 = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 `
        -Location $Location4 -AllocationMethod Dynamic

6. Criar a configuração de gateway

        $vnet4 = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
        $subnet4 = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
        $gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Criar um gateway TestVNet4

        New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 `
        -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn `
        -VpnType RouteBased -GatewaySku Standard

### <a name="step-4---connect-the-gateways"></a>Passo 4 - ligar os gateways

1. Obter ambos os gateways de rede virtual

    Neste exemplo, uma vez que ambos os gateways estão na mesma subscrição, este passo pode ser concluído na mesma sessão PowerShell.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
        $vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Criar TestVNet1 TestVNet4 ligação

    Neste passo, criar a ligação da TestVNet1 para TestVNet4. Verá uma chave partilhada referenciada nos exemplos. Pode utilizar o seus próprio valores para a chave partilhada. O importante é que a chave partilhada tem de corresponder para ambas as ligações. Criar uma ligação, pode demorar algum tempo breve para concluir.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 `
        -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Criar TestVNet4 TestVNet1 ligação

    Este passo é semelhante a uma acima, exceto que está a criar a ligação a partir do TestVNet4 TestVNet1. Certifique-se de que correspondem as teclas partilhadas.

        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 `
        -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 `
        -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

    A ligação deve ser estabelecida depois de alguns minutos.

4. Verifique se a sua ligação. Consulte a secção [como verificar a sua ligação](#verify).


## <a name="difsub"></a>Como ligar VNets que estão no diferentes subscrições


![Diagrama de v2v](./media/vpn-gateway-vnet-vnet-rm-ps/v2vdiffsub.png)

Neste cenário, podemos ligue TestVNet1 e TestVNet5. TestVNet1 e TestVNet5 residem numa subscrição diferente. Os passos para esta configuração adicionar uma ligação de VNet para VNet adicional para poder ligar TestVNet1 a TestVNet5. 

A diferença aqui é que alguns dos passos de configuração têm de ser efetuados numa sessão PowerShell separada no contexto da subscrição do segundo. Especialmente quando as duas subscrições pertencem a organizações diferentes. 

As instruções continuam dos passos anteriores listados em cima. Tem de concluir o [passo 1](#Step1) e [2 do passo](#Step2) para criar e configurar TestVNet1 e o Gateway de VPN para TestVNet1. Depois de concluir o passo 1 e o passo 2, avance para o passo 5 para criar TestVNet5.

### <a name="step-5---verify-the-additional-ip-address-ranges"></a>Passo 5 - verificar os intervalos de endereços IP adicionais

É importante para se certificar de que o espaço de endereços IP da rede virtual novo, TestVNet5, não se sobrepõe com qualquer um dos seus VNet intervalos ou intervalos de gateway de rede local. 

Neste exemplo, as redes virtuais podem pertencer a organizações diferentes. Para este exercício, pode utilizar os seguintes valores para o TestVNet5:

**Valores para TestVNet5:**

- Nome de VNet: TestVNet5
- Grupo de recursos: TestRG5
- Localização: Japão leste
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- Back-end: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- Servidor DNS: 8.8.8.8
- GatewayName: VNet5GW
- Endereço IP público: VNet5GWIP
- VPNType: RouteBased
- Ligação: VNet5toVNet1
- Tipo de ligação: VNet2VNet

**Valores adicionais para TestVNet1:**

- Ligação: VNet1toVNet5


### <a name="step-6---create-and-configure-testvnet5"></a>Passo 6 - criar e configurar TestVNet5

Este passo deve ser feito no contexto da nova subscrição. Nesta peça pode ser executada pelo administrador de uma organização diferente que é o proprietário da subscrição.

1. Declarar variáveis

    Certifique-se de que substitua os valores nos que pretende utilizar para a configuração.

        $Sub5 = "Replace_With_the_New_Subcription_Name"
        $RG5 = "TestRG5"
        $Location5 = "Japan East"
        $VnetName5 = "TestVNet5"
        $FESubName5 = "FrontEnd"
        $BESubName5 = "Backend"
        $GWSubName5 = "GatewaySubnet"
        $VnetPrefix51 = "10.51.0.0/16"
        $VnetPrefix52 = "10.52.0.0/16"
        $FESubPrefix5 = "10.51.0.0/24"
        $BESubPrefix5 = "10.52.0.0/24"
        $GWSubPrefix5 = "10.52.255.0/27"
        $DNS5 = "8.8.8.8"
        $GWName5 = "VNet5GW"
        $GWIPName5 = "VNet5GWIP"
        $GWIPconfName5 = "gwipconf5"
        $Connection51 = "VNet5toVNet1"

2. Ligar a subscrição 5

    Abra o seu consola do PowerShell e ligar à sua conta. Utilize o exemplo seguinte para ajudá-lo ligar:

        Login-AzureRmAccount

    Verifique as subscrições para a conta.

        Get-AzureRmSubscription 

    Especifique a subscrição que pretende utilizar.

        Select-AzureRmSubscription -SubscriptionName $Sub5

3. Criar um novo grupo de recursos

        New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Criar as configurações de sub-rede para TestVNet4
    
        $fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
        $besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
        $gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Criar TestVNet5

        New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 `
        -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Pedido de um endereço IP público

        $gwpip5 = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 `
        -Location $Location5 -AllocationMethod Dynamic


7. Criar a configuração de gateway

        $vnet5 = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
        $subnet5  = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
        $gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName5 -Subnet $subnet5 -PublicIpAddress $gwpip5

8. Criar um gateway TestVNet5

        New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 `
        -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### <a name="step-7---connecting-the-gateways"></a>Passo 7 - ligar os gateways

Neste exemplo, porque os gateways estão em diferentes subscrições, podemos tenha dividi este passo em duas sessões de PowerShell marcados como [subscrição 1] e [subscrição 5].

1. **[Subscrição 1]** Obter o gateway de rede virtual para 1 de subscrição

    Certifique-se de que inicie sessão no e ligar a 1 de subscrição.

        $vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

    Copie o resultado dos seguintes elementos e envie-os para o administrador de 5 de subscrição através de correio eletrónico ou outro método.

        $vnet1gw.Name
        $vnet1gw.Id

    Estes duas elementos vai ter valores semelhantes a saída de exemplo seguinte:

        PS D:\> $vnet1gw.Name
        VNet1GW
        PS D:\> $vnet1gw.Id
        /subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Subscrição 5]** Obter o gateway de rede virtual para 5 de subscrição

    Certifique-se de que inicie sessão e ligar a 5 da subscrição.

        $vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

    Copie o resultado dos seguintes elementos e envie-os para o administrador de 1 de subscrição através de correio eletrónico ou outro método.

        $vnet5gw.Name
        $vnet5gw.Id

    Estes duas elementos vai ter valores semelhantes a saída de exemplo seguinte:

        PS C:\> $vnet5gw.Name
        VNet5GW
        PS C:\> $vnet5gw.Id
        /subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Subscrição 1]** Criar TestVNet1 TestVNet5 ligação

    Neste passo, criar a ligação da TestVNet1 para TestVNet5. A diferença aqui é que essa vnet5gw $ não pode ser obtido diretamente porque está a ser uma subscrição diferente. Terá de criar um novo objeto do PowerShell com os valores comunicados entre 1 de subscrição nos passos acima. Utilize o exemplo abaixo. Substitua o nome, Id e chave partilhada com os seus próprios valores. O importante é que a chave partilhada tem de corresponder para ambas as ligações. Criar uma ligação, pode demorar algum tempo breve para concluir.

    Certifique-se de que se ligar à subscrição 1. 
    
        $vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet5gw.Name = "VNet5GW"
        $vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
        $Connection15 = "VNet1toVNet5"
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Subscrição 5]** Criar TestVNet5 TestVNet1 ligação

    Este passo é semelhante a uma acima, exceto que está a criar a ligação a partir do TestVNet5 TestVNet1. O mesmo processo de criação de um objeto do PowerShell com base em valores obtidos a partir de 1 de subscrição aplica-se aqui também. Neste passo, certifique-se de que correspondem as teclas partilhadas.

    Certifique-se de que se ligar à subscrição 5.

        $vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
        $vnet1gw.Name = "VNet1GW"
        $vnet1gw.Id = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
        New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

## <a name="verify"></a>Como verificar uma ligação


[AZURE.INCLUDE [vpn-gateway-no-nsg-include](../../includes/vpn-gateway-no-nsg-include.md)]


[AZURE.INCLUDE [verify connection powershell](../../includes/vpn-gateway-verify-connection-ps-rm-include.md)] 


## <a name="next-steps"></a>Próximos passos

- Assim que a sua ligação estiver concluída, pode adicionar máquinas virtuais às suas redes virtuais. Consulte o artigo [criar uma Máquina Virtual](../virtual-machines/virtual-machines-windows-hero-tutorial.md) para obter os passos.
- Para obter informações sobre BGP, consulte a [Descrição geral de BGP](vpn-gateway-bgp-overview.md) e [como configurar BGP](vpn-gateway-bgp-resource-manager-ps.md). 

