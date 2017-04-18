<properties
   pageTitle="Criar uma sonda personalizada para o Gateway de aplicação ao utilizar o PowerShell no Gestor de recursos | Microsoft Azure"
   description="Saiba como criar uma sonda personalizada para o Gateway de aplicação ao utilizar o PowerShell no Gestor de recursos"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/06/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-by-using-powershell-for-azure-resource-manager"></a>Criar uma sonda personalizada para o Gateway de aplicação do Azure utilizando o PowerShell para o Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clássico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](application-gateway-create-probe-classic-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

### <a name="step-1"></a>Passo 1

Utilize AzureRmAccount de início de sessão para autenticar.

    Login-AzureRmAccount

### <a name="step-2"></a>Passo 2

Verifique as subscrições para a conta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Passo 3

Escolha as suas subscrições Azure para utilizar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Passo 4

Crie um grupo de recursos (ignorar este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. Esta localização é utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicação utilizam o mesmo grupo de recursos.

No exemplo acima, criámos de um grupo de recursos denominado "appgw RG" e a localização "Oeste-nos".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

Os passos seguintes criam uma rede virtual e uma sub-rede para o gateway de aplicação.

### <a name="step-1"></a>Passo 1


Atribua a 10.0.0.0/24 intervalo endereço para uma variável de sub-rede para ser utilizada para criar uma rede virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Passo 2

Crie uma rede virtual com o nome "appgwvnet" no recurso grupo "appgw-rg" utilizando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24 à região ocidental dos EUA.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet


### <a name="step-3"></a>Passo 3

Atribua uma variável de sub-rede para os passos que criar um gateway de aplicação.

    $subnet = $vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end


Crie um recurso IP público "publicIP01" no recurso grupo "appgw-rg" região ocidental dos EUA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name publicIP01 -Location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object-with-a-custom-probe"></a>Criar um objeto de configuração de gateway aplicação com uma sonda personalizada

Configurar todos os itens de configuração antes de criar o gateway de aplicação. Os passos seguintes criam os itens de configuração que são necessários para um recurso de gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração da aplicação gateway IP com o nome "gatewayIP01". Quando inicia o Gateway de aplicação, levantar o auscultador de um endereço IP da sub-rede configurado e encaminhar o tráfego de rede para os endereços IP no conjunto de IP back-end. Tenha em atenção que cada instância leva-o até um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet


### <a name="step-2"></a>Passo 2


Configurar o conjunto de endereços IP de back-end "pool01" com endereços de IP com o nome "134.170.185.46, 134.170.188.221,134.170.185.50". Esses valores são os endereços IP que receber o tráfego de rede que sejam oriundos do ponto final de IP Front-end. Substitua os endereços IP acima para adicionar o seus próprio os pontos finais de endereço do IP de aplicação.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50


### <a name="step-3"></a>Passo 3


A sonda personalizada está configurada neste passo.

Os parâmetros utilizados são:

- **Intervalo** - configura as verificações do sonda intervalo em segundos.
- **Tempo limite** - define o limite de tempo sonda uma verificação de resposta HTTP.
- **-Hostname e o caminho** - caminho de URL completo que é chamado por aplicação Gateway para determinar o estado de funcionamento da instância. Por exemplo, se tiver um Web site http://contoso.com/, em seguida, a sonda personalizada pode ser configurada para "http://contoso.com/path/custompath.htm" para os controlos de sonda a tem uma resposta HTTP com êxito.
- **UnhealthyThreshold** - o número de respostas HTTP falhou necessário para sinalizar a instância de back-end como *danificado*.

<BR>

    $probe = New-AzureRmApplicationGatewayProbeConfig -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/path.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8

### <a name="step-4"></a>Passo 4

Configure a definição de gateway aplicação "poolsetting01" para o tráfego no conjunto de back-end. Este passo também tem uma configuração de limite de tempo que destina-se a resposta do conjunto de dados back-end a um pedido de gateway de aplicação. Quando uma resposta de back-end acertos um limite de tempo, o Gateway aplicação cancela o pedido. Este valor é a diferença entre um limite de tempo de sonda destina-se apenas a resposta de back-end a sonda de controlos.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 80

### <a name="step-5"></a>Passo 5

Configure a front-end porta IP com o nome "frontendport01" para o ponto final de IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01 -Port 80

### <a name="step-6"></a>Passo 6

Criar a configuração de IP Front-end denominado "fipconfig01" e associar o endereço IP público com a configuração de IP Front-end.


    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-7"></a>Passo 7

Crie a escuta listener01"nome" e associar a porta front-end para a configuração de IP Front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-8"></a>Passo 8

Crie a regra encaminhamento de Balanceador de carga com o nome "rule01" que configura o comportamento do Balanceador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-9"></a>Passo 9

Configure o tamanho da instância do gateway aplicação.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2


>[AZURE.NOTE]  O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Criar um gateway aplicação utilizando AzureRmApplicationGateway novo

Crie um gateway aplicação com todos os itens de configuração dos passos acima. Neste exemplo, o gateway aplicação chama-se "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -Probes $probe -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

## <a name="add-a-probe-to-an-existing-application-gateway"></a>Adicionar uma sonda a um gateway de aplicação existente

Tem quatro passos para adicionar uma sonda personalizada a um gateway de aplicação existente.

### <a name="step-1"></a>Passo 1

Carrega o recurso de gateway de aplicação para uma variável de PowerShell utilizando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Passo 2

Adicione uma sonda para a configuração de gateway existente.

    $getgw = Add-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name probe01 -Protocol Http -HostName "contoso.com" -Path "/path/custompath.htm" -Interval 30 -Timeout 120 -UnhealthyThreshold 8


No exemplo, a sonda personalizada está configurada para verificar a existência de URL caminho contoso.com/path/custompath.htm cada 30 segundos. Um limite de tempo limite de 120 segundos está configurado com um número máximo de 8 pedidos de sondagem falha.

### <a name="step-3"></a>Passo 3

Adicionar a sonda para o limite de tempo e de configuração de definição do conjunto de dados back-end utilizando **-Configurar-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol Http -CookieBasedAffinity Disabled -Probe $probe -RequestTimeout 120

### <a name="step-4"></a>Passo 4

Guarde a configuração de gateway aplicação utilizando **AzureRmApplicationGateway conjunto**.

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="remove-a-probe-from-an-existing-application-gateway"></a>Remover uma sonda de um gateway de aplicação existente

Eis os passos para remover uma sonda personalizada a partir de um gateway de aplicação existente.

### <a name="step-1"></a>Passo 1

Carrega o recurso de gateway de aplicação para uma variável de PowerShell utilizando **Get-AzureRmApplicationGateway**.

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg


### <a name="step-2"></a>Passo 2

Remova a configuração de sonda de gateway a aplicação utilizando **AzureRmApplicationGatewayProbeConfig remover**.

    $getgw = Remove-AzureRmApplicationGatewayProbeConfig -ApplicationGateway $getgw -Name $getgw.Probes.name

### <a name="step-3"></a>Passo 3

Atualizar a definição do conjunto de dados back-end para remover a sonda e definição de tempo limite utilizando **-Configurar-AzureRmApplicationGatewayBackendHttpSettings**.


     $getgw = Set-AzureRmApplicationGatewayBackendHttpSettings -ApplicationGateway $getgw -Name $getgw.BackendHttpSettingsCollection.name -Port 80 -Protocol http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Passo 4

Guarde a configuração de gateway aplicação utilizando **AzureRmApplicationGateway conjunto**. 

    Set-AzureRmApplicationGateway -ApplicationGateway $getgw

## <a name="get-application-gateway-dns-name"></a>Obter o nome da aplicação gateway DNS

Quando estiver criado o gateway, o próximo passo é configurar o front-end para a comunicação. Ao utilizar um endereço IP público, o gateway aplicação requer um nome DNS atribuído dinamicamente, que não é compatível com. Para garantir que os utilizadores finais pode premir o gateway aplicação um registo CNAME pode ser utilizada para apontar para o público ponto final do gateway aplicação. [Configurar um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para fazer isto, Obtenha detalhes do gateway aplicação e o nome do IP/DNS associado utilizando o elemento PublicIPAddress anexado para o gateway de aplicação. Nome de DNS do gateway aplicação deverá ser utilizada para criar um registo CNAME, que aponta as aplicações duas web para este nome de DNS. Não é recomendada a utilização de registos de uma vez que o VIP podem ser alteradas no reinício do gateway de aplicação.
    
    Get-AzureRmPublicIpAddress -ResourceGroupName appgw-RG -Name publicIP01
        
    Name                     : publicIP01
    ResourceGroupName        : appgw-RG
    Location                 : westus
    Id                       : /subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/publicIPAddresses/publicIP01
    Etag                     : W/"00000d5b-54ed-4907-bae8-99bd5766d0e5"
    ResourceGuid             : 00000000-0000-0000-0000-000000000000
    ProvisioningState        : Succeeded
    Tags                     : 
    PublicIpAllocationMethod : Dynamic
    IpAddress                : xx.xx.xxx.xx
    PublicIpAddressVersion   : IPv4
    IdleTimeoutInMinutes     : 4
    IpConfiguration          : {
                                 "Id": "/subscriptions/<subscription_id>/resourceGroups/appgw-RG/providers/Microsoft.Network/applicationGateways/appgwtest/frontendIP
                               Configurations/frontend1"
                               }
    DnsSettings              : {
                                 "Fqdn": "00000000-0000-xxxx-xxxx-xxxxxxxxxxxx.cloudapp.net"
                               }

## <a name="next-steps"></a>Próximos passos

Saiba como configurar SSL descarregar acedendo a [Configurar SSL descarregar](application-gateway-ssl-arm.md)

