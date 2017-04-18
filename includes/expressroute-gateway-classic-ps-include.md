Tem de criar um VNet e uma sub-rede do gateway em primeiro lugar, antes das seguintes tarefas a trabalhar. Consulte o artigo [configurar um rede Virtual através do portal clássico](../articles/expressroute/expressroute-howto-vnet-portal-classic.md) para obter mais informações.   

## <a name="add-a-gateway"></a>Adicionar um gateway

Utilize o comando abaixo para criar um gateway. Certifique-se de que todos os valores para o seu próprio substituí-los.

    New-AzureVirtualNetworkGateway -VNetName "MyAzureVNET" -GatewayName "ERGateway" -GatewayType Dedicated -GatewaySKU  Standard

## <a name="verify-the-gateway-was-created"></a>Verifique se que o gateway foi criado

Utilize o comando abaixo para confirmar que o gateway foi criado. Este comando também obtém o ID de gateway, o que precisa para outras operações.

    Get-AzureVirtualNetworkGateway

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Existem um número de [Gateway SKUs](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pode utilizar o seguinte comando para alterar o Gateway SKU em qualquer altura.

>[AZURE.IMPORTANT] Este comando não funciona para UltraPerformance gateway. Para alterar o gateway para um gateway UltraPerformance, primeiro, remova o gateway ExpressRoute existente e, em seguida, crie um novo gateway UltraPerformance. Para alterar o gateway a partir de um gateway UltraPerformance, primeiro, remova o gateway UltraPerformance e, em seguida, crie um novo gateway. 

    Resize-AzureVirtualNetworkGateway -GatewayId <Gateway ID> -GatewaySKU HighPerformance

## <a name="remove-a-gateway"></a>Remover um gateway

Utilize o comando abaixo para remover um gateway

    Remove-AzureVirtualNetworkGateway -GatewayId <Gateway ID>