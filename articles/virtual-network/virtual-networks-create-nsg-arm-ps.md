<properties
   pageTitle="Como criar NSGs no Gestor de recursos do Azure utilizando o PowerShell | Microsoft Azure"
   description="Saiba como criar e implementar NSGs no Gestor de recursos do Azure utilizando o PowerShell"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="jdial" />

# <a name="how-to-create-nsgs-in-resource-manager-by-using-powershell"></a>Como criar NSGs no Gestor de recursos utilizando o PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Também pode [Criar NSGs no modelo de implementação clássica](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

O exemplo PowerShell comandos abaixo esperar um ambiente simple já tiver criado com base no cenário acima. Se pretender executar os comandos, tal como estes são apresentados neste documento, primeiro construir o ambiente de teste ao implementar [Este modelo](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), clique em **implementar para Azure**, substitua os valores de parâmetro de predefinido, se necessário e siga as instruções no portal do.

## <a name="how-to-create-the-nsg-for-the-front-end-subnet"></a>Como criar NSG para a sub-rede front-end
Para criar um NSG denominada *NSG FrontEnd* com base no cenário acima, siga os passos abaixo:

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.

2. Crie uma regra de segurança permissão do acesso a porta 3389 da Internet.

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100
            -SourceAddressPrefix Internet -SourcePortRange *
            -DestinationAddressPrefix * -DestinationPortRange 3389

3. Crie uma regra de segurança permissão do acesso a porta 80 da Internet.

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 101
            -SourceAddressPrefix Internet -SourcePortRange * -DestinationAddressPrefix *
            -DestinationPortRange 80

4. Adicione regras criadas acima para uma nova NSG denominada **NSG FrontEnd**.

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus
        -Name "NSG-FrontEnd" -SecurityRules $rule1,$rule2

5. Verifique as regras criadas na NSG.

        $nsg

    Saída a mostrar apenas as regras de segurança:

        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule",
                                   "Description": "Allow RDP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "3389",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 100,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 },
                                 {
                                   "Name": "web-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule",
                                   "Description": "Allow HTTP",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "80",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 101,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

6. Associe NSG criado anteriormente para a sub-rede *front-end* .

                    $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
                    Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd
                        -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

                Output showing only the *FrontEnd* subnet settings, notice the value for the **NetworkSecurityGroup** property:

                    Subnets           : [
                                          {
                                            "Name": "FrontEnd",
                                            "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                            "AddressPrefix": "192.168.1.0/24",
                                            "IpConfigurations": [
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                              },
                                              {
                                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                              }
                                            ],
                                            "NetworkSecurityGroup": {
                                              "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                            },
                                            "RouteTable": null,
                                            "ProvisioningState": "Succeeded"
                                          }

    >[AZURE.WARNING] A exportação para o comando acima mostra o conteúdo para o objeto de configuração de rede virtual qual existe apenas no computador onde está a executar o PowerShell. Que precisa para executar o `Set-AzureRmVirtualNetwork` cmdlet para guardar estas definições Azure.

7. Guarde as novas definições de VNet Azure.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Saída a mostrar apenas a parte NSG:

        "NetworkSecurityGroup": {
          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
        }

## <a name="how-to-create-the-nsg-for-the-back-end-subnet"></a>Como criar NSG para a sub-rede back-end
Para criar um NSG denominada *NSG-back-end* com base no cenário acima, siga os passos abaixo:

1. Crie uma regra de segurança permissão do acesso a partir da sub-rede front-end à porta 1433 (porta predefinida utilizada pelo SQL Server).

        $rule1 = New-AzureRmNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet"
            -Access Allow -Protocol Tcp -Direction Inbound -Priority 100
            -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange *
            -DestinationAddressPrefix * -DestinationPortRange 1433

2. Crie uma regra de segurança a bloquear o acesso à Internet.

        $rule2 = New-AzureRmNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet"
            -Access Deny -Protocol * -Direction Outbound -Priority 200
            -SourceAddressPrefix * -SourcePortRange *
            -DestinationAddressPrefix Internet -DestinationPortRange *

3. Adicione regras criadas acima para uma nova NSG denominada **NSG-back-end**.

        $nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-BackEnd"
            -SecurityRules $rule1,$rule2

4. Associe NSG criado anteriormente para a sub-rede *back-end* .

        Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd
            -AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

    A mostrar apenas as definições de sub-rede *back-end* de saída, repare no valor para a propriedade **NetworkSecurityGroup** :

        Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [...],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

5. Guarde as novas definições de VNet Azure.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet


## <a name="how-to-remove-an-nsg"></a>Como remover uma NSG

Para eliminar uma NSG existente, denominado *NSG Frontend* neste caso, siga o passo abaixo:

Executar a **Remover AzureRmNetworkSecurityGroup** apresentado abaixo e certifique-se de que incluem o grupo de recursos que a NSG está em.

            Remove-AzureRmNetworkSecurityGroup -Name "NSG-FrontEnd" -ResourceGroupName "TestRG"
