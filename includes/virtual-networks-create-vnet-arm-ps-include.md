## <a name="how-to-create-a-vnet-using-powershell"></a>Como criar um VNet através do PowerShell
Para criar um VNet utilizando o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.
    
2. Se for necessário, crie um novo grupo de recursos, conforme apresentado abaixo. Para o cenário, crie um grupo de recursos denominado *TestRG*. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](../articles/resource-group-overview.md).

        New-AzureRmResourceGroup -Name TestRG -Location centralus

    Saída esperada:
    
        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG   

3. Crie um novo VNet denominada *TestVNet*, conforme apresentado abaixo.

        New-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
            -AddressPrefix 192.168.0.0/16 -Location centralus   
        
    Saída esperada:

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState       : Succeeded
        Tags                    : 
        AddressSpace            : {
                                   "AddressPrefixes": [
                                     "192.168.0.0/16"
                                   ]
                                 }
        DhcpOptions             : {}
        Subnets                 : []
        VirtualNetworkPeerings  : []

4. Armazene o objeto de rede virtual numa variável, conforme apresentado abaixo.

        $vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    
    >[AZURE.TIP] Pode combinar os passos 3 e 4 executando **$vnet = novo AzureRmVirtualNetwork - ResourceGroupName TestRG-nome TestVNet - AddressPrefix 192.168.0.0/16-localização centralus**.

5. Adicione uma sub-rede para a nova variável VNet, conforme apresentado abaixo.

        Add-AzureRmVirtualNetworkSubnetConfig -Name FrontEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
        
    Saída esperada:

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState   : Succeeded
        Tags                :
        AddressSpace        : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions         : {}
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24"
                                  }
                                ]
        VirtualNetworkPeerings  : []

6. Repita o passo 5 acima para cada sub-rede que pretende criar. O comando abaixo cria a sub-rede *back-end* para os nossos cenário.

        Add-AzureRmVirtualNetworkSubnetConfig -Name BackEnd `
            -VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

7. Apesar de criar sub-redes, atualmente apenas existam na variável local utilizada para obter VNet criar no passo 4 acima. Para guardar as alterações ao Azure, execute o cmdlet **Set-AzureRmVirtualNetwork** , conforme apresentado abaixo.

        Set-AzureRmVirtualNetwork -VirtualNetwork $vnet 
        
    Saída esperada:

        Name                : TestVNet
        ResourceGroupName   : TestRG
        Location            : centralus
        Id                  : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag                : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState   : Succeeded
        Tags                :
        AddressSpace        : {
                                  "AddressPrefixes": [
                                    "192.168.0.0/16"
                                  ]
                                }
        DhcpOptions         : {
                                  "DnsServers": []
                                }
        Subnets             : [
                                  {
                                    "Name": "FrontEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                    "AddressPrefix": "192.168.1.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  },
                                  {
                                    "Name": "BackEnd",
                                    "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                    "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                    "AddressPrefix": "192.168.2.0/24",
                                    "IpConfigurations": [],
                                    "ProvisioningState": "Succeeded"
                                  }
                                ]
        VirtualNetworkPeerings : []
