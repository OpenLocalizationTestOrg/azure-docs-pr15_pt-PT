<properties 
   pageTitle="Gerir NSGs através do PowerShell no Gestor de recursos | Microsoft Azure"
   description="Saiba como gerir existente NSGs através do PowerShell no Gestor de recursos"
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
   ms.date="03/14/2016"
   ms.author="jdial" />

# <a name="manage-nsgs-using-powershell"></a>Gerir NSGs através do PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="retrieve-information"></a>Obter informações sobre

Pode ver o seu NSGs existentes, recuperar regras para um NSG existente e saber quais os recursos um NSG está associado a.

### <a name="view-existing-nsgs"></a>Ver NSGs existentes
Para ver todos os NSGs existentes numa subscrição, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo.

    Get-AzureRmNetworkSecurityGroup

Resultado esperado:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
                            
    Name                 : WEB1
    ResourceGroupName    : RG101
    Location             : eastus2
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
                           icrosoft.Network/networkSecurityGroups/WEB1
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]


Para visualizar a lista de NSGs num grupo de recursos específico, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Saída esperada:

    Name                 : NSG-BackEnd
    ResourceGroupName    : RG-NSG
    Location             : westus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-BackEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 :                         
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
    
    Name                 : NSG-FrontEnd
    ResourceGroupName    : RG-NSG
    Location             : eastus
    Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
                           Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
    Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ProvisioningState    : Succeeded
    Tags                 : 
    SecurityRules        : [...]
    DefaultSecurityRules : [...]
    NetworkInterfaces    : [...]
    Subnets              : [...]
         
### <a name="list-all-rules-for-an-nsg"></a>Lista todas as regras para um NSG

Para visualizar as regras de um NSG denominada **NSG FrontEnd**, execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo. 

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Saída esperada:
    
    Name                     : rdp-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow RDP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 3389
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 100
    Direction                : Inbound
    
    Name                     : web-rule
    Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/                        Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
    Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    ProvisioningState        : Succeeded
    Description              : Allow HTTP
    Protocol                 : Tcp
    SourcePortRange          : *
    DestinationPortRange     : 80
    SourceAddressPrefix      : Internet
    DestinationAddressPrefix : *
    Access                   : Allow
    Priority                 : 101
    Direction                : Inbound

>[AZURE.NOTE] Também pode utilizar `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` para as regras de predefinidas a partir do **NSG FrontEnd** NSG da lista.

### <a name="view-nsgs-associations"></a>Vista NSGs associações

Para visualizar o que associar recursos é a NSG **NSG FrontEnd** , execute o `Get-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo.

    Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Procure as propriedades de **NetworkInterfaces** e **sub-redes** conforme apresentado abaixo:

    NetworkInterfaces    : []
    Subnets              : [
                             {
                               "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                               "IpConfigurations": []
                             }
                           ]

No exemplo acima, a NSG não está associada a qualquer interfaces de rede (NIC) e está associado a uma sub-rede denominada **front-end**.

## <a name="manage-rules"></a>Gerir regras

Pode adicionar regras para um NSG existente, editar regras existentes e remover regras.

### <a name="add-a-rule"></a>Adicionar uma regra

Para adicionar uma regra a permitir o tráfego de **entrada** a porta **443** a partir de qualquer computador para **NSG FrontEnd** NSG, siga os passos abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para obter o NSG existente e guardá-la numa variável, conforme apresentado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Add-AzureRmNetworkSecurityRuleConfig` cmdlet, conforme apresentado abaixo.

        Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange * `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Para guardar as alterações efetuadas a NSG, execute o `Set-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Resultado esperado a mostrar apenas as regras de segurança:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "*",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="change-a-rule"></a>Alterar uma regra

Para alterar a regra que criou acima para permitir o tráfego de entrada a partir da **Internet** apenas, siga os passos abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para obter o NSG existente e guardá-la numa variável, conforme apresentado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Set-AzureRmNetworkSecurityRuleConfig` cmdlet, conforme apresentado abaixo.

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule `
            -Description "Allow HTTPS" `
            -Access Allow `
            -Protocol Tcp `
            -Direction Inbound `
            -Priority 102 `
            -SourceAddressPrefix * `
            -SourcePortRange Internet `
            -DestinationAddressPrefix * `
            -DestinationPortRange 443

3. Para guardar as alterações efetuadas a NSG, execute o `Set-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Resultado esperado a mostrar apenas as regras de segurança:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 },
                                 {
                                   "Name": "https-rule",
                                   "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                   "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
                                   "Description": "Allow HTTPS",
                                   "Protocol": "Tcp",
                                   "SourcePortRange": "*",
                                   "DestinationPortRange": "443",
                                   "SourceAddressPrefix": "Internet",
                                   "DestinationAddressPrefix": "*",
                                   "Access": "Allow",
                                   "Priority": 102,
                                   "Direction": "Inbound",
                                   "ProvisioningState": "Succeeded"
                                 }
                               ]

### <a name="delete-a-rule"></a>Eliminar uma regra

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para obter o NSG existente e guardá-la numa variável, conforme apresentado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Remove-AzureRmNetworkSecurityRuleConfig` cmdlet, conforme apresentado abaixo.

        Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
            -Name https-rule

3. Para guardar as alterações efetuadas a NSG, execute o `Set-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo.

        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

    Resultado esperado a mostrar apenas as regras de segurança, repare a **regra https** já não está listado:

        Name                 : NSG-FrontEnd
        ...
        SecurityRules        : [
                                 {
                                   "Name": "rdp-rule",
                                   ...
                                 },
                                 {
                                   "Name": "web-rule",
                                   ...
                                 }
                               ]

## <a name="manage-associations"></a>Gerir associações

Pode associar uma NSG para sub-redes e NIC. Também pode dissociate uma NSG a partir de qualquer recurso que está associado.

### <a name="associate-an-nsg-to-a-nic"></a>Associar um NSG para uma imagem

Para associar **NSG FrontEnd** NSG para **TestNICWeb1** NIC, siga os passos abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para obter o NSG existente e guardá-la numa variável, conforme apresentado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Get-AzureRmNetworkInterface` cmdlet para obter o NIC existente e guardá-la numa variável, conforme apresentado abaixo.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Defina a propriedade de **NetworkSecurityGroup** da variável **NIC** para o valor de variável **NSG** , conforme apresentado abaixo.

        $nic.NetworkSecurityGroup = $nsg

4. Para guardar as alterações efetuadas à NIC, execute o `Set-AzureRmNetworkInterface` cmdlet conforme apresentado abaixo.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Resultado esperado a mostrar apenas a propriedade **NetworkSecurityGroup** :

        NetworkSecurityGroup : {
                                 "SecurityRules": [],
                                 "DefaultSecurityRules": [],
                                 "NetworkInterfaces": [],
                                 "Subnets": [],
                                 "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                               }

### <a name="dissociate-an-nsg-from-a-nic"></a>DISSOCIATE uma NSG a partir de uma imagem

Para dissociar **NSG FrontEnd** NSG a partir do **TestNICWeb1** NIC, siga os passos abaixo.

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para obter o NSG existente e guardá-la numa variável, conforme apresentado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

2. Executar o `Get-AzureRmNetworkInterface` cmdlet para obter o NIC existente e guardá-la numa variável, conforme apresentado abaixo.

        $nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
            -Name TestNICWeb1

3. Defina a propriedade de **NetworkSecurityGroup** da variável **NIC** para **$null**, conforme apresentado abaixo.

        $nic.NetworkSecurityGroup = $null

4. Para guardar as alterações efetuadas à NIC, execute o `Set-AzureRmNetworkInterface` cmdlet conforme apresentado abaixo.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Resultado esperado a mostrar apenas a propriedade **NetworkSecurityGroup** :

        NetworkSecurityGroup : null

### <a name="dissociate-an-nsg-from-a-subnet"></a>DISSOCIATE uma NSG a partir de uma sub-rede

Para dissociar **NSG FrontEnd** NSG da sub-rede **front-end** , siga os passos abaixo.

1. Executar o `Get-AzureRmVirtualNetwork` cmdlet para obter o VNet existente e guardá-la numa variável, conforme apresentado abaixo.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Executar o `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet para obter a sub-rede **front-end** e armazená-lo numa variável, conforme apresentado abaixo.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

3. Defina a propriedade de **NetworkSecurityGroup** da variável **sub-rede** para **$null**, conforme apresentado abaixo.

        $subnet.NetworkSecurityGroup = $null

4. Para guardar as alterações efetuadas à sub-rede, execute o `Set-AzureRmVirtualNetwork` cmdlet conforme apresentado abaixo.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Resultado esperado a mostrar apenas as propriedades da sub-rede **front-end** . Repare que existem não é uma propriedade de **NetworkSecurityGroup**:

            ...
            Subnets           : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
                                      },
                                      {
                                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
                                      }
                                    ],
                                    "ProvisioningState": "Succeeded"
                                  },
                                    ...
                                ]

### <a name="associate-an-nsg-to-a-subnet"></a>Associar um NSG para uma sub-rede

Para associar **NSG FrontEnd** NSG à sub-rede **FronEnd** novamente, siga os passos abaixo.

1. Executar o `Get-AzureRmVirtualNetwork` cmdlet para obter o VNet existente e guardá-la numa variável, conforme apresentado abaixo.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
            -Name TestVNet

2. Executar o `Get-AzureRmVirtualNetworkSubnetConfig` cmdlet para obter a sub-rede **front-end** e armazená-lo numa variável, conforme apresentado abaixo.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
            -Name FrontEnd 

1. Executar o `Get-AzureRmNetworkSecurityGroup` cmdlet para obter o NSG existente e guardá-la numa variável, conforme apresentado abaixo.

        $nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
            -Name NSG-FrontEnd

3. Defina a propriedade de **NetworkSecurityGroup** da variável **sub-rede** para **$null**, conforme apresentado abaixo.

        $subnet.NetworkSecurityGroup = $nsg

4. Para guardar as alterações efetuadas à sub-rede, execute o `Set-AzureRmVirtualNetwork` cmdlet conforme apresentado abaixo.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

    Esperada saída a mostrar apenas a propriedade **NetworkSecurityGroup** da sub-rede **FrontEnd** :

        ...
        "NetworkSecurityGroup": {
                                  "SecurityRules": [],
                                  "DefaultSecurityRules": [],
                                  "NetworkInterfaces": [],
                                  "Subnets": [],
                                  "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                                }
        ...

## <a name="delete-an-nsg"></a>Eliminar um NSG

Só pode eliminar um NSG se não associou a qualquer outro recurso. Para eliminar uma NSG, siga os passos abaixo.

1. Para verificar os recursos associados a um NSG, execute o `azure network nsg show` conforme apresentado na [vista NSGs associações](#View-NSGs-associations).
2. Se o NSG está associado a qualquer NIC, execute o `azure network nic set` conforme [Dissociate uma NSG a partir de uma imagem](#Dissociate-an-NSG-from-a-NIC) para cada NIC. 
3. Se o NSG está associada a qualquer sub-rede, execute o `azure network vnet subnet set` conforme [Dissociate uma NSG a partir de uma sub-rede](#Dissociate-an-NSG-from-a-subnet) para cada sub-rede.
4. Para eliminar o NSG, execute o `Remove-AzureRmNetworkSecurityGroup` cmdlet conforme apresentado abaixo.

        Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

    >[AZURE.NOTE] O **-Forçar** parâmetro garante que não necessita de confirmar a eliminação.

## <a name="next-steps"></a>Próximos passos

- [Ativar o registo](virtual-network-nsg-manage-log.md) para NSGs.