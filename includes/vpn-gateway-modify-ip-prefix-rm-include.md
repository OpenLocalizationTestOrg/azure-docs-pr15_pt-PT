### <a name="noconnection"></a>Como adicionar ou remover prefixos - sem ligação do gateway

- **Para adicionar** prefixos de endereço adicionais para um local de rede gateway que criou, mas que não ainda tem uma ligação de gateway, utilize o exemplo abaixo. Certifique-se de que altere os valores para o seu próprio.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

- **Para remover** um prefixo de endereço a partir de um gateway de rede local que não tem uma ligação VPN, utilize o exemplo abaixo. Deixe o os prefixos que já não precisar. Neste exemplo, vamos já não precisa de prefixo 20.0.0.0/24 (a partir do exemplo anterior), por isso Pedimos irá actualizar local gateway de rede e excluir esse prefixo.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### <a name="withconnection"></a>Como adicionar ou remover prefixos - existentes a ligação do gateway

Se tiver criado a sua ligação de gateway e pretende adicionar ou remover os prefixos de endereço IP contidos no seu gateway de rede local, terá de efetuar os seguintes passos por ordem. Isto resultará algum tempo de inatividade para a sua ligação VPN. Ao atualizar o seu prefixos, irá primeiro remover a ligação, modificar os prefixos e, em seguida, criar uma nova ligação. Nos exemplos abaixo, certifique-se de que altere os valores para o seu próprio.

>[AZURE.IMPORTANT] Não elimine o gateway VPN. Se fazê-lo, terá de regressar através dos passos para recriá-la, bem como reconfigurar o seu router no local com as novas definições.
 
1. Remova a ligação.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName

2. Modificar os prefixos de endereço para o gateway de rede local.

    Defina a variável para o LocalNetworkGateway.

        $local = Get-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName

    Modificar os prefixos.

        Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local `
        -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')

4. Crie a ligação. Neste exemplo, vamos está a configurar um tipo de ligação IPsec. Quando criar a sua ligação, utilize o tipo de ligação que é especificado para a configuração. Para tipos de ligação adicionais, consulte a página do [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .

    Defina a variável para o VirtualNetworkGateway.

        $gateway1 = Get-AzureRmVirtualNetworkGateway -Name RMGateway  -ResourceGroupName MyRGName

    Crie a ligação. Tenha em atenção que este exemplo utiliza a variável de $local definida por si no passo anterior.


        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName -Location 'West US' `
        -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local `
        -ConnectionType IPsec `
        -RoutingWeight 10 -SharedKey 'abc123'
