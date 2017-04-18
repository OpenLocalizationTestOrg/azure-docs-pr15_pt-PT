<properties 
   pageTitle="Controlar o encaminhamento e utilizar eletrodomésticos virtuais no Gestor de recursos utilizando o clip do Azure | Microsoft Azure"
   description="Saiba como controlar encaminhamento e utilizar eletrodomésticos virtuais utilizando o clip do Azure"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="create-user-defined-routes-udr-in-the-azure-cli"></a>Criar definidas pelo utilizador rotas (UDR) no clip Azure

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Também pode [Criar UDRs no modelo de implementação clássica](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos do Azure clip de exemplo abaixo esperam um ambiente simple já criado com base num cenário acima. Se pretender executar os comandos, tal como estes são apresentados neste documento, primeiro construir o ambiente de teste ao implementar [Este modelo](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), clique em **implementar para Azure**, substitua os valores de parâmetro de predefinido, se necessário e siga as instruções no portal do.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar UDR para a sub-rede front-end
Para criar a tabela de encaminhamento e encaminhar necessário para a sub-rede front-end com base no cenário acima, siga os passos abaixo.

3. Executar o **`azure network route-table create`** comando para criar uma tabela de encaminhar para a sub-rede front-end.

        azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

    Resultado:

        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK

    Parâmetros:
    - **-g (ou - grupo de recursos)**. Nome do grupo de recursos onde o UDR será criado. Para o cenário, *TestRG*.
    - **-l (ou – localização)**. Azure região onde o UDR novo será criado. Para o cenário, *westus*.
    - **-n (ou – nome)**. Nome para o novo UDR. Para o cenário, *UDR FrontEnd*.

4. Executar o **`azure network route-table route create`** comando para criar uma rota na tabela rota criada anteriormente para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

    Resultado:

        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK

    Parâmetros:
    - **-r (ou – nome da tabela encaminhar)**. Nome da tabela rota onde a rota será adicionada. Para o cenário, *UDR FrontEnd*.
    - **-a (ou – prefixo de endereço)**. Prefixo para a sub-rede onde pacotes destinam-se para o endereço. Para o cenário, *192.168.2.0/24*.
    - **-y (ou - tipo de salto seguinte)**. Tipo de objeto tráfego será enviado para. Valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *nenhum*.
    - **-p (ou --seguinte-salto-endereço ip**). Endereço IP do salto seguinte. Para o cenário, *192.168.0.4*.

5. Executar o **`azure network vnet subnet set`** comando para associar a tabela de rota criada anteriormente com a sub-rede **front-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

    Resultado:

        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK

    Parâmetros:
    - **-"e" (ou nome – vnet)**. Nome da VNet onde se encontra a sub-rede. Para o cenário, *TestVNet*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar UDR para a sub-rede back-end
Para criar a tabela de encaminhamento e encaminhar necessário para a sub-rede back-end com base no cenário acima, siga os passos abaixo.

1. Executar o **`azure network route-table create`** comando para criar uma tabela de encaminhar para a sub-rede back-end.

        azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Executar o **`azure network route-table route create`** comando para criar uma rota na tabela rota criada anteriormente para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Executar o **`azure network vnet subnet set`** comando para associar a tabela de rota criada anteriormente com a sub-rede **back-end** .

        azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## <a name="enable-ip-forwarding-on-fw1"></a>Activar o reencaminhamento de IP no FW1
Para ativar IP reencaminhamento na NIC utilizado pelo **FW1**, siga os passos abaixo.

1. Executar o **`azure network nic show`** comando e repare no valor para **reencaminhamento ativar IP**. -Deve ser definido como *Falso*.

        azure network nic show -g TestRG -n NICFW1

    Resultado:
        
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK

2. Executar o **`azure network nic set`** comando para activar o reencaminhamento IP.

        azure network nic set -g TestRG -n NICFW1 -f true

    Resultado:

        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK

    Parâmetros:

    - **-f (ou – o reencaminhamento de ip ativar)**. *Verdadeiro* ou *Falso*.
