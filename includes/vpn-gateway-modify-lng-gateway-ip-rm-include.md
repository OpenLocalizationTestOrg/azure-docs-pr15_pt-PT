Para modificar o endereço IP gateway, utilize o `New-AzureRmVirtualNetworkGatewayConnection` cmdlet. Manter o nome do gateway rede local exatamente o mesmo que o nome existente, desde que irão substituir as definições. Neste momento, o cmdlet "Definir" não suporta a modificar o endereço IP do gateway.

### <a name="gwipnoconnection"></a>Como modificar o endereço IP do gateway - sem ligação do gateway

Para atualizar o endereço IP de gateway para o gateway de rede local que ainda não tem uma ligação, utilize o exemplo abaixo. Também pode atualizar os prefixos de endereço ao mesmo tempo. As definições que especificar irão substituir as definições de existentes. Certifique-se de que utilize o nome existente do seu gateway de rede local. Caso não pretenda, que está a criar um novo gateway de rede local, não substituir um já existente.

Utilize o exemplo seguinte, substituição de valores para o seu próprio.

    New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
    -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
    -GatewayIpAddress "5.4.3.2" -ResourceGroupName MyRGName


### <a name="gwipwithconnection"></a>Como modificar o endereço IP do gateway - existentes a ligação do gateway

Se o gateway já existe uma ligação, primeiro terá de remover a ligação. Em seguida, pode modificar o endereço IP do gateway e recrie uma nova ligação. Isto resultará algum tempo de inatividade para a sua ligação VPN.


>[AZURE.IMPORTANT] Não elimine o gateway VPN. Se fazê-lo, terá de regressar através dos passos para recriá-la, bem como reconfigurar o seu router no local com o endereço IP forem atribuído ao gateway recentemente criado.
 

1. Remova a ligação. Pode localizar o nome da sua ligação utilizando o `Get-AzureRmVirtualNetworkGatewayConnection` cmdlet.

        Remove-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName `
        -ResourceGroupName MyRGName

2. Modificar o valor de GatewayIpAddress. Também pode modificar prefixos de endereço neste momento, se necessário. Tenha em atenção que isto irá substituir as definições de gateway de rede local existentes. Utilize o nome existente do seu gateway de rede local quando modificar, de modo a que irão substituir as definições. Caso não pretenda, que está a criar um novo gateway de rede local, não modificar um existente.

        New-AzureRmLocalNetworkGateway -Name MyLocalNetworkGWName `
        -Location "West US" -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24') `
        -GatewayIpAddress "104.40.81.124" -ResourceGroupName MyRGName

3. Crie a ligação. Neste exemplo, vamos está a configurar um tipo de ligação IPsec. Quando criar a sua ligação, utilize o tipo de ligação que é especificado para a configuração. Para tipos de ligação adicionais, consulte a página do [cmdlet do PowerShell](https://msdn.microsoft.com/library/mt603611.aspx) .  Para obter o nome de VirtualNetworkGateway, pode executar o `Get-AzureRmVirtualNetworkGateway` cmdlet.

    Defina as variáveis de:

        $local = Get-AzureRMLocalNetworkGateway -Name MyLocalNetworkGWName -ResourceGroupName MyRGName `
        $vnetgw = Get-AzureRmVirtualNetworkGateway -Name RMGateway -ResourceGroupName MyRGName

    Crie a ligação:
    
        New-AzureRmVirtualNetworkGatewayConnection -Name MyGWConnectionName -ResourceGroupName MyRGName `
        -Location "West US" `
        -VirtualNetworkGateway1 $vnetgw `
        -LocalNetworkGateway2 $local `
        -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

