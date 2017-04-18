<properties
   pageTitle="Configurar a Firewall de aplicação Web num novo ou existente Gateway de aplicação | Microsoft Azure"
   description="Este artigo fornece orientações sobre como começar a utilizar firewall de aplicação web num gateway de aplicação existentes ou novas."
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
   ms.date="10/25/2016"
   ms.author="gwallace"/>

# <a name="configure-web-application-firewall-on-a-new-or-existing-application-gateway"></a>Configurar a Firewall de aplicação Web num Gateway de aplicação nova ou existente

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-web-application-firewall-powershell.md)

A firewall de aplicação web (WAF) no Azure aplicação Gateway protege aplicações web do comuns ataques baseada na web, como a introdução de SQL, ataques de scripts de publicação em vários sites e contra manipulação de sessão.

Azure de aplicação de Gateway é um balanceador de carga de camada-7. Fornece falha na ligação, pedidos HTTP encaminhamento de desempenho entre servidores diferentes, quer estejam na nuvem ou no local. Aplicação fornece muitas funcionalidades do controlador de entrega de aplicação (ADC) incluindo balanceamento de carga HTTP afinidade sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, o estado de funcionamento personalizado sondas, suporte para múltiplos sites e muitas outras. Para localizar uma lista completa das funcionalidades suportadas, visite descrição geral de Gateway de aplicação

O seguinte artigo mostra como [Adicionar firewall de aplicação web para um gateway de aplicação existente](#add-web-application-firewall-to-an-existing-application-gateway) e [criar um gateway de aplicação que utiliza firewall de aplicação web](#create-an-application-gateway-with-web-application-firewall).

![imagem do cenário][scenario]

## <a name="waf-configuration-differences"></a>Diferenças de configuração de WAF

Se tiver lido [criar um Gateway de aplicação com o PowerShell](application-gateway-create-gateway-arm.md), compreender as definições de SKU para configurar ao criar um gateway de aplicação. WAF fornece definições adicionais para definir quando configurar o SKU num gateway de aplicação. Não existem alterações adicionais que efetuar no gateway aplicação propriamente dita.

**SKU** - um gateway aplicação normal sem WAF suporta **padrão\_pequenas**, **padrão\_médio**, e **padrão\_grande** tamanhos. Com a introdução de WAF, existem dois SKUs adicionais, **WAF\_médio** e **WAF\_grande**. WAF não é suportada no gateways pequenos de aplicações.

**Camada** - os valores disponíveis são **WAF**ou **padrão** . Quando utilizar Firewall de aplicação Web, **WAF** devem ser escolhidas.

**Modo de** -esta definição é o modo de WAF. valores permitidos são **deteção** e **prevenção**. Quando WAF está configurado no modo de detecção, todas as ameaças são guardadas num ficheiro de registo. No modo de prevenção de eventos ainda estão armazenados mas intruso recebe uma resposta não autorizado 403 de gateway de aplicação.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar firewall de aplicação web para um gateway de aplicação existente

Certifique-se de que está a utilizar a versão mais recente do Azure PowerShell. Mais informações estão disponível em [Utilizar o Windows PowerShell com o Gestor de recursos](../powershell-azure-resource-manager.md).

### <a name="step-1"></a>Passo 1

Inicie sessão na sua conta Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Passo 2

Selecione a subscrição a utilizar para este cenário.

    Select-AzureRmSubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Passo 3

Obtenha o gateway que está a adicionar Firewall da aplicação Web para.

    $gw = Get-AzureRmApplicationGateway -Name "AdatumGateway" -ResourceGroupName "MyResourceGroup"


### <a name="step-4"></a>Passo 4

Configure o sku de firewall de aplicação web. Os tamanhos disponíveis são **WAF\_grande** e **WAF\_médio**. Quando a firewall de aplicação web é utilizada a camada tem de ser **WAF**, a capacidade de deve ser confirmada quando definir o sku.

    $gw | Set-AzureRmApplicationGatewaySku -Name WAF_Large -Tier WAF -Capacity 2

### <a name="step-5"></a>Passo 5

Configure as definições de WAF tal como foi definido no exemplo seguinte:

Para a definição de **WafMode** , os valores disponíveis são prevenção e detecção.

    $gw | Set-AzureRmApplicationGatewayWebApplicationFirewallConfiguration -Enabled $true -FirewallMode Prevention

### <a name="step-6"></a>Passo 6

Atualize o gateway aplicação com as definições de definidas no passo anterior.

    Set-AzureRmApplicationGateway -ApplicationGateway $gw

Este comando atualiza o gateway aplicação com Firewall de aplicação Web. Recomenda-se para ver a [Aplicação de Gateway diagnósticos](application-gateway-diagnostics.md) para compreender como ver registos de início para o gateway de aplicação. Devido à natureza segurança WAF, registos de tem de ser revisto regularmente para compreender a postura de segurança das suas aplicações web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Criar um Gateway aplicação com Firewall de aplicação Web

Os passos seguintes levá-lo durante o processo de todo a partir do início ao fim para a criação de um Gateway aplicação com Firewall de aplicação Web.

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

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-4"></a>Passo 4

Crie um grupo de recursos (ignorar este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. Esta localização é utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicação utiliza mesmo grupo de recursos.

No exemplo anterior, criámos de um grupo de recursos denominado "appgw RG" e a localização "Oeste-nos".

>[AZURE.NOTE] Se precisar de configurar uma sonda personalizada para o gateway de aplicação, consulte o artigo [criar um gateway de aplicação com sondas personalizados utilizando o PowerShell](application-gateway-create-probe-ps.md). Veja [as pesquisas personalizadas e os do Estado de funcionamento de monitorização](application-gateway-probe-overview.md) para obter mais informações.

### <a name="step-5"></a>Passo 5

Atribua um intervalo de endereços para a sub-rede ser utilizados para o Gateway aplicação propriamente dita.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Uma sub-rede para uma aplicação deverá ter um mínimo de 28 bits de máscaras de introdução. Este valor deixa 10 endereços disponíveis na sub-rede para instâncias de Gateway de aplicação. Com uma sub-rede mais pequena, não poderá conseguir adicionar mais instância do seu gateway de aplicação no futuro.

### <a name="step-6"></a>Passo 6

Atribua um intervalo de endereço a ser utilizado para o conjunto de endereços de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-7"></a>Passo 7

Criar uma rede virtual com as sub-redes anteriores no grupo de recursos criou no passo: [criar o grupo de recursos](#create-the-resource-group)

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-8"></a>Passo 8

Obter os recursos de rede virtual e recursos de sub-rede para ser utilizado nos passos seguintes:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

### <a name="step-9"></a>Passo 9

Crie um recurso IP público para ser utilizado para o gateway de aplicação. Este endereço IP público é utilizado dos seguintes passos:

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -name 'appgwpip' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Aplicação Gateway não suporta a utilização de um endereço IP público, criado com uma etiqueta de domínio definida. Apenas um endereço IP público com uma etiqueta de domínio dinamicamente criado é suportado. Se introduzir um nome amigável dns para o gateway de aplicação, recomenda-se para utilizar um registo cname como um alias.

### <a name="step-10"></a>Passo 10

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Os passos seguintes criam os itens de configuração que são necessários para um recurso de gateway de aplicação.

Criar uma configuração de IP de gateway de aplicação, este procedimento configura que sub-rede utiliza o Gateway de aplicação. Quando inicia o Gateway de aplicação, levantar o auscultador de um endereço IP da sub-rede configurado e encaminha o tráfego de rede para os endereços IP no conjunto de IP back-end. Tenha em atenção que cada instância leva-o até um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-11"></a>Passo 11

Configure o conjunto de endereços IP back-end com os endereços IP dos servidores web back-end. Estes endereços IP são os endereços IP que recebem o tráfego de rede que sejam oriundos do ponto final de IP Front-end. Substitua os seguintes endereços IP para adicionar o seus próprio os pontos finais de endereço do IP de aplicação.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

### <a name="step-12"></a>Passo 12

Carregue o certificado para ser utilizado nos recursos de conjunto de dados back-end ssl ativado.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile <full path to .cer file>

### <a name="step-13"></a>Passo 13

Configure as definições da aplicação gateway http back-end. Atribua o certificado carregado no passo anterior para as definições de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-14"></a>Passo 14

Configure a porta IP Front-end para o ponto final de IP público. Esta porta é a porta que os utilizadores finais se ligam ao.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-15"></a>Passo 15

Criar uma configuração de IP Front-end, esta definição mapas um endereço ip público ou privado para front-end do gateway aplicação. O passo seguinte associa o endereço IP público no passo anterior, com a configuração de IP Front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-16"></a>Passo 16

Configure o certificado do gateway aplicação. Este certificado é utilizado para desencriptar e voltar a encriptar o tráfego no gateway aplicação.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

### <a name="step-17"></a>Passo 17

Crie a escuta HTTP para o gateway de aplicação. Atribua o certificado de configuração, porta e ssl ip front-end a utilizar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-18"></a>Passo 18

Crie uma regra encaminhamento Balanceador de carga que configura o comportamento do Balanceador de carga. Neste exemplo, é criada uma regra de básicas round robin.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
   
### <a name="step-19"></a>Passo 19

Configure o tamanho da instância do gateway aplicação.

    $sku = New-AzureRmApplicationGatewaySku -Name WAF_Medium -Tier WAF -Capacity 2

>[AZURE.NOTE]  Pode escolher entre **WAF\_médio** e **WAF\_grande**, a camada quando utilizar WAF é sempre **WAF**. Capacidade for qualquer número entre 1 e 10.

### <a name="step-20"></a>Passo 20

Configurar o modo para WAF, são aceites valores **prevenção** e **detecção**.

    $config = New-AzureRmApplicationGatewayWafConfig -Enabled $true -WafMode "Prevention"

### <a name="step-21"></a>Passo, 21

Crie um gateway aplicação com todos os itens de configuração dos passos anteriores. Neste exemplo, o gateway aplicação chama-se "appgwtest".

    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -WebApplicationFirewallConfig $config -SslCertificates $cert -AuthenticationCertificates $authcert

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

Saiba como configurar os registos de diagnóstico, para iniciar sessão os eventos que são detetados ou impedidos com Firewall de aplicação Web visitando [Diagnósticos do Gateway de aplicação](application-gateway-diagnostics.md)


[scenario]: ./media/application-gateway-web-application-firewall-powershell/scenario.png