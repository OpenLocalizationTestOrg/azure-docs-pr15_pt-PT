## <a name="deploy-the-arm-template-by-using-powershell"></a>Implementar o modelo de processador utilizando o PowerShell

Para implementar o modelo de processador que transferiu utilizando o PowerShell, siga os passos abaixo.

1. Se nunca tiver utilizado o Azure PowerShell, consulte o artigo [como instalar e configurar o Azure PowerShell](../articles/powershell-install-configure.md) e siga as instruções para o fim para iniciar sessão no Azure e selecione a sua subscrição.

3. Se for necessário, execute o **`New-AzureRmResourceGroup`** cmdlet para criar um novo grupo de recursos. O comando abaixo cria um grupo de recursos com o nome *TestRG* na região *Central (EUA)* azure. Para obter mais informações sobre os grupos de recursos, visite [Descrição geral do Gestor de recursos do Azure](../articles/resource-group-overview.md).

        New-AzureRmResourceGroup -Name TestRG -Location centralus
        
    Eis o resultado esperado para o comando acima:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

4. Executar o **`New-AzureRmResourceGroupDeployment`** cmdlet para implementar o novo VNet utilizando o modelo e o parâmetro de ficheiros que transferiu e modificação acima.

        New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
            -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
            
    Eis o resultado esperado para o comando acima:
        
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : 8/14/2015 9:40:00 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
        
        Outputs           :

5. Executar o **`Get-AzureRmVirtualNetwork`** cmdlet para ver as propriedades do novo VNet, conforme apresentado abaixo.


        Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
        
    Eis o resultado esperado para o comando acima:
        
        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]