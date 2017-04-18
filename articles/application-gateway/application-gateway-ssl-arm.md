<properties
   pageTitle="Configurar um gateway de aplicação para a passagem para da SSL utilizando o Gestor de recursos do Azure | Microsoft Azure"
   description="Esta página disponibiliza instruções para criar um gateway aplicação com SSL descarregar utilizando o Gestor de recursos do Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-azure-resource-manager"></a>Configurar um gateway de aplicação para a passagem para da SSL utilizando o Gestor de recursos do Azure

> [AZURE.SELECTOR]
-[Azure Portal](application-gateway-ssl-portal.md)
-[Azure Gestor de recursos do PowerShell](application-gateway-ssl-arm.md)
-[Azure PowerShell clássico](application-gateway-ssl.md)

 Azure Gateway de aplicação pode ser configurado para terminar a sessão Secure Sockets Layer (SSL) ao gateway para evitar dispendiosas SSL desencriptação das tarefas de aconteça na linha do web farm. A passagem para da SSL também simplifica a configuração do servidor front-end e gestão da aplicação web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell utilizando o instalador do plataforma Web. Pode transferir e instalar a versão mais recente da secção da [página de transferências do](https://azure.microsoft.com/downloads/) **Windows PowerShell** .
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicação. Certifique-se de que nenhuma máquinas virtuais ou na nuvem implementações estão a utilizar a sub-rede. Aplicação Gateway tem de ser por si só, numa sub-rede rede virtual.
3. Os servidores de configurar para utilizar o aplicação gateway tem de existir ou tem os respetivos pontos finais criados na rede virtual ou com um IP público/VIP atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway aplicação?


- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencem à sub-rede rede virtual ou devem ser VIP/IP público.
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Porta front-end:** Esta porta é a pública porta que é aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta front-end, um protocolo (Http ou Https, estas definições são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra vincula automaticamente o serviço de escuta e que o servidor back-end conjunto e define qual conjunto de servidor back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta. Atualmente, apenas a regra *básica* é suportada. A regra *básicas* é distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo no **HttpListener** deve mudar para *Https* (sensível a maiúsculas e minúsculas). O elemento **SslCertificate** é adicionado à **HttpListener** com o valor da variável configurado para o certificado SSL. A porta front-end deve ser atualizada para 443.

**Para ativar afinidade com base em cookie**: um gateway aplicação pode ser configurado para garantir que um pedido a partir de uma sessão de cliente é sempre direccionado para o mesmo VM na web farm. Este cenário é executado por introdução de um cookie de sessão que permite que o gateway direcionar tráfego corretamente. Para ativar afinidade com base em cookies, defina **CookieBasedAffinity** para *ativado* no elemento de **BackendHttpSettings** .


## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre com o modelo de implementação do Azure clássica e o Gestor de recursos do Azure é a ordem que criar um gateway de aplicação e os itens que têm de ser configurado.

Com o Gestor de recursos, todos os componentes de um gateway de aplicação estão configurados individualmente e, em seguida, colocar em conjunto para criar um recurso de gateway de aplicação.


Eis os passos necessários para criar um gateway de aplicação:

1. Criar um grupo de recursos para Gestor de recursos
2. Criar rede virtual, sub-rede e endereço IP público para o gateway de aplicação
3. Criar um objeto de configuração de gateway de aplicação
4. Criar um recurso de gateway de aplicação


## <a name="create-a-resource-group-for-resource-manager"></a>Criar um grupo de recursos para Gestor de recursos

Certifique-se de que alternar modo de PowerShell para utilizar os cmdlets do Gestor de recursos do Azure. Mais informações estão disponível em [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passo 1

    Login-AzureRmAccount

### <a name="step-2"></a>Passo 2

Verifique as subscrições para a conta.

    Get-AzureRmSubscription

Lhe for pedido para autenticar com as suas credenciais.<BR>

### <a name="step-3"></a>Passo 3

Escolha as suas subscrições Azure para utilizar. <BR>


    Select-AzureRmSubscription -Subscriptionid "GUID of subscription"


### <a name="step-4"></a>Passo 4

Crie um grupo de recursos (ignorar este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. Esta definição é utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicação utiliza mesmo grupo de recursos.

No exemplo acima, criámos de um grupo de recursos denominado "appgw RG" e a localização "Oeste-nos".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte mostra como criar uma rede virtual utilizando o Gestor de recursos:

### <a name="step-1"></a>Passo 1

    $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Este exemplo atribui a 10.0.0.0/24 intervalo endereço para uma variável de sub-rede para ser utilizada para criar uma rede virtual.

### <a name="step-2"></a>Passo 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnet

Este exemplo cria uma rede virtual com o nome "appgwvnet" no recurso grupo "appgw-rg" utilizando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24 à região ocidental dos EUA.

### <a name="step-3"></a>Passo 3

    $subnet = $vnet.Subnets[0]

Este exemplo atribui o objeto de sub-rede a variável $subnet para os passos seguintes.

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name publicIP01 -location "West US" -AllocationMethod Dynamic

Esta fórmula de exemplo cria um recurso IP público "publicIP01" no recurso grupo "appgw-rg" região ocidental dos EUA.


## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração de gateway de aplicação

### <a name="step-1"></a>Passo 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Este exemplo cria uma configuração da aplicação gateway IP com o nome "gatewayIP01". Quando inicia o Gateway de aplicação, levantar o auscultador de um endereço IP da sub-rede configurado e encaminhar o tráfego de rede para os endereços IP no conjunto de IP back-end. Tenha em atenção que cada instância leva-o até um endereço IP.

### <a name="step-2"></a>Passo 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Este exemplo configura o conjunto de endereços IP de back-end "pool01" com endereços de IP com o nome "134.170.185.46, 134.170.188.221,134.170.185.50." Esses valores são os endereços IP que receber o tráfego de rede que sejam oriundos do ponto final de IP Front-end. Substitua os endereços IP do exemplo anterior os endereços IP do seu os pontos finais da aplicação de web.

### <a name="step-3"></a>Passo 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Enabled

Este exemplo configura definição de gateway aplicação "poolsetting01" para o tráfego de rede balanceamento de carga no conjunto de back-end.

### <a name="step-4"></a>Passo 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 443

Este exemplo configura a front-end porta IP com o nome "frontendport01" para o ponto final de IP público.

### <a name="step-5"></a>Passo 5

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path for certificate file> -Password ‘<password>’

Este exemplo configura o certificado utilizado para ligação SSL. O certificado tem de estar no formato. pfx e a palavra-passe tem de estar entre carateres de 4 a 12.

### <a name="step-6"></a>Passo 6

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -PublicIPAddress $publicip

Este exemplo cria a configuração de IP Front-end denominado "fipconfig01" e associa o endereço IP público com a configuração de IP Front-end.

### <a name="step-7"></a>Passo 7

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert


Este exemplo cria o nome "listener01 escuta" e associa a porta front-end para a configuração de IP Front-end e o certificado.

### <a name="step-8"></a>Passo 8

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Este exemplo cria a regra encaminhamento de Balanceador de carga com o nome "rule01" que configura o comportamento do Balanceador de carga.

### <a name="step-9"></a>Passo 9

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Este exemplo configura o tamanho da instância do gateway aplicação.

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway aplicação utilizando AzureApplicationGateway novo

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslCertificates $cert

Esta fórmula de exemplo cria um gateway aplicação com todos os itens de configuração dos passos anteriores. No exemplo, o gateway aplicação chama-se "appgwtest".

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

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interna (ILB), consulte o artigo [criar um gateway de aplicação com um balanceador de carga interna (ILB)](application-gateway-ilb.md).

Se pretender obter mais informações sobre carregar balanceamento de opções geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
