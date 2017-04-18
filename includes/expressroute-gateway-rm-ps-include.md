Os passos para esta tarefa utilizam um VNet com base nos valores abaixo. Definições adicionais e nomes de também são destacados esta lista. Não utilizamos esta lista diretamente em qualquer um dos passos, embora Vamos fazer para adicionar variáveis com base nos valores nesta lista. Pode copiar a lista para utilizar como uma referência, substituir os valores com a sua própria.

Lista de referência de configuração:
    
- Nome de rede virtual = "TestVNet"
- Espaço de endereços de rede virtual = 192.168.0.0/16
- Grupo de recursos = "TestRG"
- Nome de Sub-rede1 = "FrontEnd" 
- Espaço de endereços do Sub-rede1 = "192.168.0.0/16"
- Nome do gateway sub-rede: "GatewaySubnet" é necessário nomear sempre uma sub-rede gateway *GatewaySubnet*.
- Espaço de endereços do gateway sub-rede = "192.168.200.0/26"
- Região = "Leste dos EUA"
- Nome do gateway = "GW"
- Nome do gateway IP = "GWIP"
- Configuração de IP do gateway nome = "gwipconf"
-  Tipo = "ExpressRoute" este tipo de é necessário para uma configuração ExpressRoute.
- Nome do gateway público IP = "gwpip"


## <a name="add-a-gateway"></a>Adicionar um gateway

1. Ligar à sua subscrição do Azure. 

        Login-AzureRmAccount
        Get-AzureRmSubscription 
        Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Declare variáveis para este exercício. Este exemplo utilizará a utilizar as variáveis no exemplo abaixo. Certifique-se de que editar esta opção para refletir as definições que pretende utilizar. 
        
        $RG = "TestRG"
        $Location = "East US"
        $GWName = "GW"
        $GWIPName = "GWIP"
        $GWIPconfName = "gwipconf"
        $VNetName = "TestVNet"

3. Armazene o objeto de rede virtual como uma variável.

        $vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Adicione uma sub-rede gateway à sua rede Virtual. A sub-rede gateway deve ter o nome "GatewaySubnet". Que pretende criar um gateway que é /27 polegadas ou superior (/ 26, / 25, etc.).
            
        Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Defina a configuração.

            Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Armazene a sub-rede gateway como uma variável.

        $subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Pedir um endereço IP público. O endereço IP é pedido antes de criar o gateway. Não é possível especificar o endereço IP que pretende utilizar; dinamicamente é atribuída. Irá utilizar este endereço IP na secção configuração seguinte. O AllocationMethod tem de ser dinâmicos.

        $pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic

8. Crie a configuração para o gateway. A configuração de gateway define a sub-rede e o endereço IP público para utilizar. Neste passo, está a especificar a configuração que será utilizada quando criar o gateway. Este passo não realmente a criar o objeto de gateway. Utilize o exemplo abaixo para criar a sua configuração de gateway. 

        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

9. Crie o gateway. Neste passo, o **-GatewayType** é particularmente importante. Tem de utilizar o valor **ExpressRoute**. Note que depois de executar estes cmdlets, o gateway pode demorar 20 minutos ou mais para criar.

        New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute -GatewaySku Standard

## <a name="verify-the-gateway-was-created"></a>Verifique se que o gateway foi criado

Utilize o comando abaixo para confirmar que o gateway foi criado.

    Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG

## <a name="resize-a-gateway"></a>Redimensionar um gateway

Existem um número de [Gateway SKUs](../articles/expressroute/expressroute-about-virtual-network-gateways.md). Pode utilizar o seguinte comando para alterar o Gateway SKU em qualquer altura.

>[AZURE.IMPORTANT] Este comando não funciona para UltraPerformance gateway. Para alterar o gateway para um gateway UltraPerformance, primeiro, remova o gateway ExpressRoute existente e, em seguida, crie um novo gateway UltraPerformance. Para alterar o gateway a partir de um gateway UltraPerformance, primeiro, remova o gateway UltraPerformance e, em seguida, crie um novo gateway.

    $gw = Get-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG
    Resize-AzureRmVirtualNetworkGateway -VirtualNetworkGateway $gw -GatewaySku HighPerformance

## <a name="remove-a-gateway"></a>Remover um gateway

Utilize o comando abaixo para remover um gateway

    Remove-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG  
