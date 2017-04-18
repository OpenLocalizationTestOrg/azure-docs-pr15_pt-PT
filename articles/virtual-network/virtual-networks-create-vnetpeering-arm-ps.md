<properties
   pageTitle="Criar VNet efectuado utilizar cmdlets do Powershell | Microsoft Azure"
   description="Saiba como criar uma rede virtual através do portal Azure no Gestor de recursos."
   services="virtual-network"
   documentationCenter=""
   authors="NarayanAnnamalai"
   manager="jefco"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2016"
   ms.author="narayanannamalai; annahar"/>

# <a name="create-vnet-peering-using-powershell-cmdlets"></a>Criar VNet efectuado utilizar cmdlets do Powershell

[AZURE.INCLUDE [virtual-networks-create-vnet-selectors-arm-include](../../includes/virtual-networks-create-vnetpeering-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnetpeering-intro-include.md)]

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-basic-include](../../includes/virtual-networks-create-vnetpeering-scenario-basic-include.md)]

Para criar um VNet efectuado utilizando o PowerShell, siga os passos abaixo:

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.

> [AZURE.NOTE] Cmdlet do PowerShell para gerir VNet efectuado é fornecido com [Azure PowerShell 1.6.](http://www.powershellgallery.com/packages/Azure/1.6.0)

2. Ler objectos de rede virtual:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1
        $vnet2 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet2

3. Para estabelecer VNet efectuado, tem de criar duas ligações, uma para cada direção. O passo seguinte irá criar uma ligação de peering VNet para VNet1 para VNet2 pela primeira vez:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $vnet2.Id

    Mostra o resultado:

        Name            : LinkToVNet2
        Id: /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Initiated
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

4. Este passo irá criar uma ligação de peering VNet para VNet2 para VNet1:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $vnet2 -RemoteVirtualNetworkId $vnet1.Id

    Mostra o resultado:

        Name            : LinkToVNet1
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2/virtualNetworkPeerings/LinkToVNet1
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet2
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic   : False
        AllowGatewayTransit : False
        UseRemoteGateways   : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

5. Quando a ligação peering VNet estiver criada, pode ver o estado de ligação abaixo:

        Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name linktovnet2

    Mostra o resultado:

        Name            : LinkToVNet2
        Id              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

    Existem algumas propriedades configuráveis para VNet efectuado:

  	|Opção|Descrição|Predefinido|
  	|:-----|:----------|:------|
  	|AllowVirtualNetworkAccess|Se espaço de ponto a ponto VNet para ser incluídos como parte da etiqueta Virtual_network de endereços|Sim|
  	|AllowForwardedTraffic|Se o tráfego não proveniente de uma VNet dispõe está aceites ou largado|N|
  	|AllowGatewayTransit|Permite que o peer VNet para utilizar o seu gateway VNet|N|
  	|UseRemoteGateways|Utilize o gateway do seu ponto VNet. A ponto a ponto VNet tem de ter um gateway configurado e AllowGatewayTransit selecionado. Não pode utilizar esta opção se tiver um gateway configurado|N|

    Cada ligação no VNet efectuado tem o conjunto de propriedades acima. Por exemplo, pode definir AllowVirtualNetworkAccess como verdadeiro para ligação efectuada VNet VNet1 para VNet2 e defina-o para falso para a ligação peering VNet na direção em outros.

        $LinktoVNet2 = Get-AzureRmVirtualNetworkPeering -VirtualNetworkName vnet1 -ResourceGroupName vnet101 -Name LinkToVNet2
        $LinktoVNet2.AllowForwardedTraffic = $true
        Set-AzureRmVirtualNetworkPeering -VirtualNetworkPeering $LinktoVNet2

    Pode executar Get-AzureRmVirtualNetworkPeering para dar duplo o valor da propriedade após a alteração. A partir de saída, pode ver que allowforwardedtraffic altera conjunto verdadeiro depois de executar os cmdlets acima.

        Name            : LinkToVNet2
        Id          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet1/virtualNetworkPeerings/LinkToVNet2
        Etag            : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ResourceGroupName   : vnet101
        VirtualNetworkName  : vnet1
        PeeringState        : Connected
        ProvisioningState   : Succeeded
        RemoteVirtualNetwork    : {
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/vnet101/providers/Microsoft.Network/virtualNetworks/vnet2"
                                        }
        AllowVirtualNetworkAccess   : True
        AllowForwardedTraffic       : True
        AllowGatewayTransit     : False
        UseRemoteGateways       : False
        RemoteGateways      : null
        RemoteVirtualNetworkAddressSpace : null

    Depois de efectuado é estabelecida neste cenário, deverá conseguir iniciar as ligações a partir de qualquer máquina virtual para qualquer máquina virtual de ambas as VNets. Por predefinição, AllowVirtualNetworkAccess for VERDADEIRO e VNet efectuado irá aprovisionar as ACL inicial para permitir a comunicação entre VNets. Ainda pode aplicar regras de grupo (NSG) de segurança de rede para bloquear a conectividade entre sub-redes específicas ou máquinas virtuais obtenha o controlo de grão fino do access entre duas redes virtuais.  Para mais informações sobre como criar regras NSG, consulte este [artigo](virtual-networks-create-nsg-arm-ps.md).

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-crosssub-include](../../includes/virtual-networks-create-vnetpeering-scenario-crosssub-include.md)]

Para criar VNet efectuado entre subscrições através do PowerShell, siga os passos abaixo:

1. Inicie sessão no Azure com privilégios de utilizador da conta para A subscrição e execute o cmdlet seguinte:

        New-AzureRmRoleAssignment -SignInName <UserB ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-A-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet5

    Não se trata de um requisito efectuado pode ser estabelecida mesmo se os utilizadores elevar individualmente efectuados pedidos para os respetivos VNets respetivos desde que os pedidos de correspondem. Adicionar um utilizador com privilégios do outros VNet como um utilizador no local VNet torna mais fácil de fazer a configuração.

2. Inicie sessão no Azure com a conta com privilégios utilizador-B para subscrição-B e execute o cmdlet seguinte:

        New-AzureRmRoleAssignment -SignInName <UserA ID> -RoleDefinitionName "Network Contributor" -Scope /subscriptions/<Subscription-B-ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/VirtualNetworks/VNet3

3. No utilizador A estiverem sessão de início de sessão, execute o cmdlet abaixo:

        $vnet3 = Get-AzureRmVirtualNetwork -ResourceGroupName hr-vnets -Name vnet3

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet5 -VirtualNetwork $vnet3 -RemoteVirtualNetworkId "/subscriptions/<Subscription-B-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet5" -BlockVirtualNetworkAccess

4. Na sessão de início de sessão do utilizador-B, execute o cmdlet abaixo:

        $vnet5 = Get-AzureRmVirtualNetwork -ResourceGroupName vendor-vnets -Name vnet5

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet3 -VirtualNetwork $vnet5 -RemoteVirtualNetworkId "/subscriptions/<Subscriptoin-A-Id>/resourceGroups/<ResourceGroupName>/providers/Microsoft.Network/virtualNetworks/VNet3" -BlockVirtualNetworkAccess

5. Depois de efectuado é estabelecida, qualquer máquina virtual VNet3 deverá conseguir comunicar com qualquer máquina virtual VNet5.

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-transit-include](../../includes/virtual-networks-create-vnetpeering-scenario-transit-include.md)]

1. Neste cenário, pode executar os cmdlets do PowerShell abaixo para estabelecer o VNet efectuado.  Tem de definir a propriedade de AllowForwardedTraffic como VERDADEIRO e ligar VNET1 HubVNet, que permite que o tráfego de entrada de fora do espaço de endereços VNet peering.

        $hubVNet = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name HubVNet
        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

        Add-AzureRmVirtualNetworkPeering -Name LinkToHub -VirtualNetwork $vnet1 -RemoteVirtualNetworkId $HubVNet.Id -AllowForwardedTraffic

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet1 -VirtualNetwork $HubVNet -RemoteVirtualNetworkId $vnet1.Id

2. Depois de efectuado é estabelecida, pode referir-se a este [artigo](virtual-network-create-udr-arm-ps.md) e definir uma rota definidos pelo utilizador (UDR) para redirecionar o tráfego de VNet1 através de um virtual aparelho para utilizar as suas capacidades. Quando especificar o endereço salto seguinte a rota, pode defini-lo para o endereço IP do aparelho virtual no peer VNet HubVNet. Segue-se um exemplo:

        $route = New-AzureRmRouteConfig -Name TestNVA -AddressPrefix 10.3.0.0/16 -NextHopType VirtualAppliance -NextHopIpAddress 192.0.1.5

        $routeTable = New-AzureRmRouteTable -ResourceGroupName VNet101 -Location brazilsouth -Name TestRT -Route $route

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName VNet101 -Name VNet1

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet1 -Name subnet-1 -AddressPrefix 10.1.1.0/24 -RouteTable $routeTable

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet1

[AZURE.INCLUDE [virtual-networks-create-vnet-scenario-asmtoarm-include](../../includes/virtual-networks-create-vnetpeering-scenario-asmtoarm-include.md)]

Para criar um VNet efectuado entre uma rede virtual clássica e uma rede virtual do Gestor de recursos do Azure no PowerShell, siga os passos abaixo:

1. Leia o objeto de rede virtual para **VNET1**, a rede virtual do Gestor de recursos do Azure da seguinte forma:

        $vnet1 = Get-AzureRmVirtualNetwork -ResourceGroupName vnet101 -Name vnet1

2. Para estabelecer VNet efectuado neste cenário, é necessário apenas uma ligação, especificamente uma ligação a partir do **VNET1** para **VNET2**. Este passo necessita de saber a ID do recurso. sua VNet clássica O formato de ID de grupo de recursos tem a seguinte apresentação:

        /subscriptions/{SubscriptionID}/resourceGroups/{ResourceGroupName}/providers/Microsoft.ClassicNetwork/virtualNetworks/{VirtualNetworkName}

    Certifique-se de que substituir SubscriptionID, ResourceGroupName e VirtualNetworkName com os nomes adequados.

    Isto pode ser feito pelo seguinte procedimento:

        Add-AzureRmVirtualNetworkPeering -Name LinkToVNet2 -VirtualNetwork $vnet1 -RemoteVirtualNetworkId /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2

3. Uma vez a VNet efectuada ligação é criada, pode ver o estado de ligação conforme apresentado no resultado abaixo:

        Name                             : LinkToVNet2
        Id                               : /subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.Network/virtualNetworks/VNET1/virtualNetworkPeerings/LinkToVNet2
        Etag                             : W/"acecbd0f-766c-46be-aa7e-d03e41c46b16"
        ResourceGroupName                : MyResourceGroup
        VirtualNetworkName               : VNET1
        PeeringState                     : Connected
        ProvisioningState                : Succeeded
        RemoteVirtualNetwork             : {
                                         "Id": "/subscriptions/xxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxx/resourceGroups/MyResourceGroup/providers/Microsoft.ClassicNetwork/virtualNetworks/VNET2"
                                       }
        AllowVirtualNetworkAccess        : True
        AllowForwardedTraffic            : False
        AllowGatewayTransit              : False
        UseRemoteGateways                : False
        RemoteGateways                   : null
        RemoteVirtualNetworkAddressSpace : null

## <a name="remove-vnet-peering"></a>Remover VNet efectuado

1.  Para remover VNet efectuado, o que precisa para executar o cmdlet seguinte:

        Remove-AzureRmVirtualNetworkPeering  

        Remove both links, using the following commands:

        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2
        Remove-AzureRmVirtualNetworkPeering -ResourceGroupName vnet101 -VirtualNetworkName vnet1 -Name linktovnet2

2. Depois de remover uma ligação num VNET efectuado, o estado da ligação de ponto a ponto serão direcionadas para desligado. Neste estado, é possível criar novamente a ligação até se transformar o estado da ligação de ponto a ponto iniciadas. Recomendamos que remova ambas as ligações antes de voltar a criar o VNet efectuado.
