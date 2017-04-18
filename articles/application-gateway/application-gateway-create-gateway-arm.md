<properties
   pageTitle="Criar, iniciar ou eliminar um gateway aplicação utilizando o Gestor de recursos do Azure | Microsoft Azure"
   description="Esta página disponibiliza instruções para criar, configurar, iniciar e eliminar um gateway aplicação Azure utilizando o Gestor de recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="create-start-or-delete-an-application-gateway-by-using-azure-resource-manager"></a>Criar, iniciar ou eliminar um gateway aplicação utilizando o Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gestor de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [Clip Azure](application-gateway-create-gateway-cli.md)

Azure de aplicação de Gateway é um balanceador de carga de camada-7. Fornece falha na ligação, pedidos HTTP encaminhamento de desempenho entre servidores diferentes, quer estejam na nuvem ou no local. Aplicação Gateway fornece muitas funcionalidades do controlador de entrega de aplicação (ADC) incluindo balanceamento de carga HTTP afinidade sessão baseada em cookies, descarregar o Secure Sockets Layer (SSL), sondas de estado de funcionamento personalizado, o suporte para múltiplos sites e muitas outras pessoas. Para localizar uma lista completa das funcionalidades suportadas, visite [Descrição geral de Gateway de aplicação](application-gateway-introduction.md)

Este artigo explica os passos para criar, configurar, iniciar e eliminar um gateway de aplicação.

>[AZURE.IMPORTANT] Antes de trabalhar com recursos Azure, é importante compreender que Azure atualmente tem dois modelos de implementação: Gestor de recursos e clássica. Certifique-se de que compreender [os modelos de implementação e ferramentas](../azure-classic-rm.md) antes de trabalhar com qualquer recurso Azure. Pode ver a documentação para diferentes ferramentas para clicando nos separadores na parte superior deste artigo. Este documento abrange criar um gateway aplicação utilizando o Gestor de recursos do Azure. Para utilizar a versão clássica, aceda a [criar uma implementação clássico de gateway de aplicação ao utilizar o PowerShell](application-gateway-create-gateway.md).


## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell utilizando o instalador do plataforma Web. Pode transferir e instalar a versão mais recente da secção da [página de transferências do](https://azure.microsoft.com/downloads/) **Windows PowerShell** .
2. Se tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou crie uma sub-rede na sua rede existente virtual exclusivamente para utilização ao gateway de aplicação. Não é possível implementar o gateway de aplicação para uma rede virtual diferente os recursos que pretende implementar atrás do gateway de aplicação.
3. Os servidores que configurar para utilizar o aplicação gateway tem de existir ou tem os respetivos pontos finais criados na rede virtual ou com um IP público/VIP atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway aplicação?

- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencem à sub-rede rede virtual ou devem ser VIP/IP público.
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Porta front-end:** Esta porta é a pública porta que é aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta front-end, um protocolo (Http ou Https, estes valores são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra vincula automaticamente o serviço de escuta, o agrupamento de servidor back-end e define qual conjunto de servidor back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre a utilizar o Azure clássica e o Gestor de recursos do Azure é a ordem pela qual cria o gateway de aplicação e os itens que têm de ser configurado.

Com o Gestor de recursos, todos os itens que tornam um gateway de aplicação estão configurados individualmente e, em seguida, colocar em conjunto para criar o recurso de gateway de aplicação.

Seguem-se os passos que são necessários para criar um gateway de aplicação.

## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para Gestor de recursos

Certifique-se de que está a utilizar a versão mais recente do Azure PowerShell. Mais informações estão disponível em [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passo 1

Iniciar sessão no Azure

    Login-AzureRmAccount

Lhe for pedido para autenticar com as suas credenciais.

### <a name="step-2"></a>Passo 2

Verifique as subscrições para a conta.

    Get-AzureRmSubscription

### <a name="step-3"></a>Passo 3

Escolha as suas subscrições Azure para utilizar.

    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="step-4"></a>Passo 4

Crie um grupo de recursos (ignorar este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. Esta localização é utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicação utiliza mesmo grupo de recursos.

No exemplo acima, criámos de um grupo de recursos denominado "appgw RG" e a localização "Oeste-nos".

>[AZURE.NOTE] Se precisar de configurar uma sonda personalizada para o gateway de aplicação, consulte o artigo [criar um gateway de aplicação com sondas personalizados utilizando o PowerShell](application-gateway-create-probe-ps.md). Veja [as pesquisas personalizadas e os do Estado de funcionamento de monitorização](application-gateway-probe-overview.md) para obter mais informações.

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte mostra como criar uma rede virtual utilizando o Gestor de recursos.

### <a name="step-1"></a>Passo 1

Atribua a 10.0.0.0/24 intervalo endereço para a variável de sub-rede para ser utilizado para criar uma rede virtual.

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

### <a name="step-2"></a>Passo 2

Crie uma rede virtual com o nome "appgwvnet" no recurso grupo "appgw-rg" utilizando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24 à região ocidental dos EUA.

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="step-3"></a>Passo 3

Atribua uma variável de sub-rede para os passos que criar um gateway de aplicação.

    $subnet=$vnet.Subnets[0]

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público "publicIP01" no recurso grupo "appgw-rg" região ocidental dos EUA.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic


## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração de gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Os passos seguintes criam os itens de configuração que são necessários para um recurso de gateway de aplicação.

### <a name="step-1"></a>Passo 1

Crie uma configuração da aplicação gateway IP com o nome "gatewayIP01". Quando inicia o Gateway de aplicação, levantar o auscultador de um endereço IP da sub-rede configurado e encaminha o tráfego de rede para os endereços IP no conjunto de IP back-end. Tenha em atenção que cada instância leva-o até um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

### <a name="step-2"></a>Passo 2

Configurar o conjunto de endereços IP de back-end "pool01" com endereços de IP com o nome "134.170.185.46, 134.170.188.221,134.170.185.50". Estes endereços IP são os endereços IP que recebem o tráfego de rede que sejam oriundos do ponto final de IP Front-end. Substitua os endereços IP anteriores para adicionar o seus próprio os pontos finais de endereço do IP de aplicação.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

### <a name="step-3"></a>Passo 3

Configure a definição de gateway aplicação "poolsetting01" para o tráfego de rede balanceamento de carga no conjunto de back-end.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

### <a name="step-4"></a>Passo 4

Configure a front-end porta IP com o nome "frontendport01" para o ponto final de IP público.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

### <a name="step-5"></a>Passo 5

Criar a configuração de IP Front-end denominado "fipconfig01" e associar o endereço IP público com a configuração de IP Front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

### <a name="step-6"></a>Passo 6

Crie a escuta listener01"nome" e associar a porta front-end para a configuração de IP Front-end.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

### <a name="step-7"></a>Passo 7

Crie a regra encaminhamento de Balanceador de carga com o nome "rule01" que configura o comportamento do Balanceador de carga.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-8"></a>Passo 8

Configure o tamanho da instância do gateway aplicação.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azurermapplicationgateway"></a>Criar um gateway aplicação utilizando AzureRmApplicationGateway novo

Crie um gateway aplicação com todos os itens de configuração dos passos anteriores. Neste exemplo, o gateway aplicação chama-se "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

### <a name="step-9"></a>Passo 9

Obtenha detalhes DNS e VIP do gateway aplicação a partir do recurso IP público anexado para o gateway de aplicação.

    Get-AzureRmPublicIpAddress -Name publicIP01 -ResourceGroupName appgw-rg  

## <a name="delete-an-application-gateway"></a>Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação, siga estes passos:

### <a name="step-1"></a>Passo 1

Obtenha o objeto de gateway application e associá-la a uma variável "$getgw".

    $getgw = Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Passo 2

Utilize **AzureRmApplicationGateway parar** para parar o gateway de aplicação.

    Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

Assim que o gateway aplicação estiver num estado parado, utilize o cmdlet **AzureRmApplicationGateway remover** para remover o serviço.

    Remove-AzureRmApplicationGateway -Name $appgwtest -ResourceGroupName appgw-rg -Force

>[AZURE.NOTE] O **-Forçar** parâmetro pode ser utilizado para suprimir a mensagem de confirmação remover.

Para verificar se o serviço foi removido, pode utilizar o cmdlet **Get-AzureRmApplicationGateway** . Este passo não é necessário.

    Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

## <a name="get-application-gateway-dns-name"></a>Obter o nome da aplicação gateway DNS

Quando o gateway estiver criado, o próximo passo é configurar o front-end para a comunicação. Ao utilizar um endereço IP público, o gateway de aplicação requer um nome DNS atribuído dinamicamente, que não é compatível com. Para garantir que os utilizadores finais pode premir o gateway aplicação um registo CNAME pode ser utilizada para apontar para o público ponto final do gateway aplicação. [Configurar um nome de domínio personalizado para no Azure](../cloud-services/cloud-services-custom-domain-name-portal.md). Para fazer isto, Obtenha detalhes do gateway aplicação e o nome do IP/DNS associado utilizando o elemento PublicIPAddress anexado para o gateway de aplicação. Nome de DNS do gateway aplicação deverá ser utilizada para criar um registo CNAME, que aponta as aplicações duas web para este nome de DNS. Não é recomendada a utilização de registos de uma vez que o VIP podem ser alteradas no reinício do gateway de aplicação.
    
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

Se pretende configurar a passagem para da SSL, consulte o artigo [configurar um gateway de aplicação para SSL descarregar](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interna, consulte o artigo [criar um gateway de aplicação com um balanceador de carga interna (ILB)](application-gateway-ilb.md).

Se pretender obter mais informações sobre carregar balanceamento de opções geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
