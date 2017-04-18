<properties 
   pageTitle="Controlar o encaminhamento e utilizar eletrodomésticos virtuais através do PowerShell no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como controlar encaminhamento no VNets através do PowerShell no modelo de implementação clássico"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Controlar o encaminhamento e utilizar eletrodomésticos virtuais (clássico) através do PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

O exemplo Azure PowerShell comandos abaixo esperar um ambiente simple já criado com base no cenário acima. Se pretender executar os comandos, tal como estes são apresentados neste documento, crie o ambiente apresentado na [criar um VNet (clássico) através do PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar UDR para a sub-rede front-end
Para criar a tabela de encaminhamento e encaminhar necessário para a sub-rede front-end com base no cenário acima, siga os passos abaixo.

3. Executar o **`New-AzureRouteTable`** cmdlet para criar uma tabela de encaminhar para a sub-rede front-end.

        New-AzureRouteTable -Name UDR-FrontEnd `
            -Location uswest `
            -Label "Route table for front end subnet"

    Resultado:

        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet

4. Executar o **`Set-AzureRoute`** cmdlet para criar uma rota na tabela rota criado anteriormente para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para **FW1** VM (192.168.0.4).
    
        Get-AzureRouteTable UDR-FrontEnd `
            |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

    Resultado:

        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Executar o **`Set-AzureSubnetRouteTable`** cmdlet para associar a tabela rota criado anteriormente com a sub-rede **front-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName FrontEnd `
            -RouteTableName UDR-FrontEnd
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar UDR para a sub-rede back-end
Para criar a tabela de encaminhamento e encaminhar necessário para a sub-rede back-end com base no cenário acima, siga os passos abaixo.

3. Executar o **`New-AzureRouteTable`** cmdlet para criar uma tabela de encaminhar para a sub-rede back-end.

        New-AzureRouteTable -Name UDR-BackEnd `
            -Location uswest `
            -Label "Route table for back end subnet"

4. Executar o **`Set-AzureRoute`** cmdlet para criar uma rota da tabela rota criado anteriormente para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para **FW1** VM (192.168.0.4).

        Get-AzureRouteTable UDR-BackEnd `
            |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
            -NextHopType VirtualAppliance `
            -NextHopIpAddress 192.168.0.4

5. Executar o **`Set-AzureSubnetRouteTable`** cmdlet para associar a tabela rota criado anteriormente com a sub-rede **back-end** .

        Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
            -SubnetName BackEnd `
            -RouteTableName UDR-BackEnd

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Activar o reencaminhamento de IP na VM FW1
Para ativar IP reencaminhamento de chamadas na FW1 VM, siga os passos abaixo.

1. Executar o **`Get-AzureIPForwarding`** cmdlet para verificar o estado de reencaminhamento de chamadas de IP

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Get-AzureIPForwarding

    Resultado:

        Disabled

2. Executar o **`Set-AzureIPForwarding`** comando para activar o reencaminhamento de IP para *FW1* VM.

        Get-AzureVM -Name FW1 -ServiceName TestRGFW `
            | Set-AzureIPForwarding -Enable
