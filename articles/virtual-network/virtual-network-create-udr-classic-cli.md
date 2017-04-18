<properties 
   pageTitle="Controlar o encaminhamento e utilizar eletrodomésticos virtuais utilizando o clip do Azure no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como controlar encaminhamento no VNets utilizando o clip do Azure no modelo de implementação clássico"
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
   ms.date="03/15/2016"
   ms.author="jdial" />

#<a name="control-routing-and-use-virtual-appliances-classic-using-the-azure-cli"></a>Controlar o encaminhamento e utilizar eletrodomésticos virtuais (clássico) utilizando o clip do Azure

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Pode também [controlo encaminhamento e utilizar eletrodomésticos virtuais no modelo de implementação de Gestor de recursos](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Os comandos do Azure clip de exemplo abaixo esperam um ambiente simple já criado com base num cenário acima. Se pretender executar os comandos, tal como estes são apresentados neste documento, crie o ambiente apresentado na [criar um VNet (clássico) utilizando o clip do Azure](virtual-networks-create-vnet-classic-cli.md).

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Criar UDR para a sub-rede front-end
Para criar a tabela de encaminhamento e encaminhar necessário para a sub-rede front-end com base no cenário acima, siga os passos abaixo.

1. Executar o **`azure config mode`** para mudar para modo clássico.

        azure config mode asm

    Resultado:

        info:    New mode is asm

3. Executar o **`azure network route-table create`** comando para criar uma tabela de encaminhar para a sub-rede front-end.

        azure network route-table create -n UDR-FrontEnd -l uswest

    Resultado:

        info:    Executing command network route-table create
        info:    Creating route table "UDR-FrontEnd"
        info:    Getting route table "UDR-FrontEnd"
        data:    Name                            : UDR-FrontEnd
        data:    Location                        : West US
        info:    network route-table create command OK

    Parâmetros:
    - **-l (ou – localização)**. Azure região onde o NSG novo será criado. Para o cenário, *westus*.
    - **-n (ou – nome)**. Nome para o novo NSG. Para o cenário, *NSG FrontEnd*.

4. Executar o **`azure network route-table route set`** comando para criar uma rota na tabela rota criada anteriormente para enviar todo o tráfego destinado à sub-rede de back-end (192.168.2.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

    Resultado:

        info:    Executing command network route-table route set
        info:    Getting route table "UDR-FrontEnd"
        info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    network route-table route set command OK

    Parâmetros:
    - **-r (ou – nome da tabela encaminhar)**. Nome da tabela rota onde a rota será adicionada. Para o cenário, *UDR FrontEnd*.
    - **-a (ou – prefixo de endereço)**. Prefixo para a sub-rede onde pacotes destinam-se para o endereço. Para o cenário, *192.168.2.0/24*.
    - **-t (ou - tipo de salto seguinte)**. Tipo de objeto tráfego será enviado para. Valores possíveis são *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet*ou *nenhum*.
    - **-p (ou --seguinte-salto-endereço ip**). Endereço IP do salto seguinte. Para o cenário, *192.168.0.4*.

5. Executar o **`azure network vnet subnet route-table add`** comando para associar a tabela de rota criada anteriormente com a sub-rede **front-end** .

        azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

    Resultado:

        info:    Executing command network vnet subnet route-table add
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up network configuration
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
        info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
        data:    Route table name                : UDR-FrontEnd
        data:      Location                      : West US
        data:      Routes:
        info:    network vnet subnet route-table add command OK 

    Parâmetros:
    - **-t (ou nome – vnet)**. Nome da VNet onde se encontra a sub-rede. Para o cenário, *TestVNet*.
    - **- n (ou --nome de sub-rede**. Nome da tabela de rotas será adicionada à sub-rede. Para o cenário, *front-end*.
 
## <a name="create-the-udr-for-the-back-end-subnet"></a>Criar UDR para a sub-rede back-end
Para criar a tabela de encaminhamento e encaminhar necessário para a sub-rede back-end com base no cenário acima, siga os passos abaixo.

3. Executar o **`azure network route-table create`** comando para criar uma tabela de encaminhar para a sub-rede back-end.

        azure network route-table create -n UDR-BackEnd -l uswest

4. Executar o **`azure network route-table route set`** comando para criar uma rota na tabela rota criada anteriormente para enviar todo o tráfego destinado à sub-rede de front-end (192.168.1.0/24) para **FW1** VM (192.168.0.4).

        azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Executar o **`azure network vnet subnet route-table add`** comando para associar a tabela de rota criada anteriormente com a sub-rede **back-end** .

        azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

