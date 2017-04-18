<properties
    pageTitle="Configure a política de SSL e ponto a ponto SSL com Gateway aplicação | Microsoft Azure"
    description="Este artigo descreve como configurar SSL ponto a ponto com a aplicação de Gateway através do PowerShell de Gestor de recursos do Azure"
    services="application-gateway"
    documentationCenter="na"
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

# <a name="configure-ssl-policy-and-end-to-end-ssl-with-application-gateway-using-powershell"></a>Configure a política de SSL e ponto a ponto SSL com a aplicação de Gateway através do PowerShell

## <a name="overview"></a>Descrição geral

Gateway aplicação suporta a encriptação de ponto a ponto de tráfego. Aplicação Gateway faz isto ao terminar a ligação SSL no gateway da aplicação. O gateway, em seguida, aplica-se as regras de encaminhamento para o tráfego, encripta novamente o pacote e reencaminha o pacote para o back-end adequado com base em regras de encaminhamento definidas. Qualquer resposta do servidor web atravessa o mesmo processo novamente para o utilizador final.

Outro funcionalidade gateway nessa aplicação suporta é desativar determinadas versões de protocolo SSL. Gateway aplicação suporta desativar a versão do protocolo seguintes; TLSv1.0, TLSv1.1 e TLSv1.2.

> [AZURE.NOTE] SSL 2.0 e SSL 3.0 estão desativadas por predefinição e não podem ser activada. Estas são consideradas não seguras e não forem capazes de ser utilizado com o Gateway de aplicação

![imagem do cenário][scenario]

## <a name="scenario"></a>Cenário

Neste cenário, saiba como criar um gateway de aplicação através de SSL ponto a ponto através do PowerShell.

Este cenário irá:

- Criar um grupo de recursos com o nome "appgw rg"
- Crie uma rede virtual com o nome "appgwvnet" com um bloco CIDR reservado de 10.0.0.0/16.
- Crie duas sub-redes denominado "appgwsubnet" e "appsubnet".
- Crie um gateway de aplicação pequena suporte encriptação SSL ponto a ponto que desativa determinados protocolos SSL.

## <a name="before-you-begin"></a>Antes de começar

Para configurar SSL ponto a ponto com um gateway de aplicação, é necessário para o gateway um certificado e os certificados são necessários para os servidores de back-end. O certificado do gateway é utilizado para encriptar e desencriptar o tráfego de enviado para o mesmo através de SSL. O certificado do gateway tem de estar no formato de intercâmbio de informações pessoais (pfx). Este formato de ficheiro permite a chave privada a serem exportadas ferramenta necessária ao gateway de aplicação para efetuar a encriptação e desencriptar de tráfego.

Para encriptação ssl ponto a ponto back-end tem de ser whitelisted com gateway de aplicação. Isto é feito ao carregá o certificado do back-ends público para o gateway de aplicação. Este procedimento assegura que o gateway aplicação apenas comunica com instâncias de back-end conhecidos. Isto protege ainda mais a comunicação ponto a ponto.

Este processo é descrito nos passos seguintes:

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Esta secção explica criar um grupo de recursos, que contém o gateway de aplicação.

### <a name="step-1"></a>Passo 1

Inicie sessão na sua conta Azure.

    Login-AzureRmAccount

### <a name="step-2"></a>Passo 2

Selecione a subscrição a utilizar para este cenário.

    Select-AzureRmsubscription -SubscriptionName "<Subscription name>"

### <a name="step-3"></a>Passo 3

Crie um grupo de recursos (ignorar este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -Location "West US"

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte cria uma rede virtual e duas sub-redes. Uma sub-rede é utilizada para colocar em espera o gateway de aplicação. A outros sub-rede é utilizada para o back-ends que aloja a aplicação web.

### <a name="step-1"></a>Passo 1

Atribua um intervalo de endereços para a sub-rede ser utilizados para o Gateway aplicação propriamente dita.

    $gwSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -AddressPrefix 10.0.0.0/24

> [AZURE.NOTE] Deverão ser corretamente dimensionados sub-redes configurados para o gateway de aplicação. Um gateway aplicação pode ser configurado para até 10 instâncias. Cada instância leva-o até 1 IP address da sub-rede. Demasiado pequeno de uma sub-rede adverso pode afetar escalar para fora de um gateway de aplicação.

### <a name="step-2"></a>Passo 2

Atribua um intervalo de endereço a ser utilizado para o conjunto de endereços de back-end.

    $nicSubnet = New-AzureRmVirtualNetworkSubnetConfig  -Name 'appsubnet' -AddressPrefix 10.0.2.0/24

### <a name="step-3"></a>Passo 3

Crie uma rede virtual com as sub-redes definidas nos passos anteriores.

    $vnet = New-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet, $nicSubnet

### <a name="step-4"></a>Passo 4

Obter os recursos de rede virtual e recursos de sub-rede para ser utilizado nos passos seguintes:

    $vnet = Get-AzureRmvirtualNetwork -Name 'appgwvnet' -ResourceGroupName appgw-rg
    $gwSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appgwsubnet' -VirtualNetwork $vnet
    $nicSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'appsubnet' -VirtualNetwork $vnet

## <a name="create-a-public-ip-address-for-the-front-end-configuration"></a>Criar um endereço IP público para a configuração de front-end

Crie um recurso IP público para ser utilizado para o gateway de aplicação. Este endereço IP público é utilizado um passo seguinte.

    $publicip = New-AzureRmPublicIpAddress -ResourceGroupName appgw-rg -Name 'publicIP01' -Location "West US" -AllocationMethod Dynamic

> [AZURE.IMPORTANT] Aplicação Gateway não suporta a utilização de um endereço IP público, criado com uma etiqueta de domínio definida. Apenas um endereço IP público com uma etiqueta de domínio dinamicamente criado é suportado. Se introduzir um nome amigável dns para o gateway de aplicação, recomenda-se para utilizar um registo cname como um alias.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração de gateway de aplicação

Tem de configurar todos os itens de configuração antes de criar o gateway de aplicação. Os passos seguintes criam os itens de configuração que são necessários para um recurso de gateway de aplicação.

### <a name="step-1"></a>Passo 1

Criar uma configuração de IP de gateway de aplicação, esta definição configura que sub-rede utiliza o gateway de aplicação. Quando inicia o gateway de aplicação, levantar o auscultador de um endereço IP da sub-rede configurado e encaminha o tráfego de rede para os endereços IP no conjunto de IP back-end. Tenha em atenção que cada instância leva-o até um endereço IP.

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name 'gwconfig' -Subnet $gwSubnet

### <a name="step-2"></a>Passo 2

Criar uma configuração de IP Front-end, esta definição mapas um endereço ip público ou privado para front-end do gateway aplicação. O passo seguinte associa o endereço IP público no passo anterior, com a configuração de IP Front-end.

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name 'fip01' -PublicIPAddress $publicip

### <a name="step-3"></a>Passo 3

Configure o conjunto de endereços IP back-end com os endereços IP dos servidores web back-end. Estes endereços IP são os endereços IP que recebem o tráfego de rede que sejam oriundos do ponto final de IP Front-end. Substitua os seguintes endereços IP para adicionar o seus próprio os pontos finais de endereço do IP de aplicação.

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name 'pool01' -BackendIPAddresses 1.1.1.1, 2.2.2.2, 3.3.3.3

> [AZURE.NOTE] Um nome de domínio completamente qualificado (FQDN) também é um valor válido em vez de um endereço de ip para os servidores de back-end utilizando o parâmetro - BackendFqdns.

### <a name="step-4"></a>Passo 4

Configure a porta IP Front-end para o ponto final de IP público. Esta porta é a porta que os utilizadores finais ligar.

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name 'port01'  -Port 443

### <a name="step-5"></a>Passo 5

Configure o certificado do gateway aplicação. Este certificado é utilizado para desencriptar e voltar a encriptar o tráfego no gateway aplicação.

    $cert = New-AzureRmApplicationGatewaySslCertificate -Name cert01 -CertificateFile <full path to .pfx file> -Password <password for certificate file>

> [AZURE.NOTE] Este exemplo configura o certificado utilizado para ligação SSL. O certificado tem de estar no formato. pfx e a palavra-passe tem de estar entre carateres de 4 a 12.

### <a name="step-6"></a>Passo 6

Crie a escuta HTTP para o gateway de aplicação. Atribua o certificado de configuração, porta e ssl ip front-end a utilizar.

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01 -Protocol Https -FrontendIPConfiguration $fipconfig -FrontendPort $fp -SslCertificate $cert

### <a name="step-7"></a>Passo 7

Carregue o certificado para ser utilizado nos recursos de conjunto de dados back-end ssl ativado.

> [AZURE.NOTE] A sonda de predefinido obtém a chave pública da enlace SSL **predefinido** no endereço IP do back-end e compara o valor de chave pública que receber para o valor de chave pública fornecidos aqui. A chave pública obtida não pode ser necessariamente o local pretendido para o qual o tráfego irá fluxo **se** estiver a utilizar os cabeçalhos de anfitrião e SNI no back-end. Em caso de dúvida, visite https://127.0.0.1/ nas extremidades de anterior para confirmar que certificado é utilizado para a ligação de SSL **predefinido** . Utilize a chave pública a partir desse pedido nesta secção. Se estiver a utilizar cabeçalhos de anfitrião e SNI num enlaces HTTPS e que não recebe uma resposta e o certificado de um pedido de manual browser para https://127.0.0.1/ nas extremidades de anterior, tem de configurar um enlace de SSL predefinido nas extremidades de anterior. Se não o fizer, irão falhar sondas e back-end não estará whitelisted.

    $authcert = New-AzureRmApplicationGatewayAuthenticationCertificate -Name 'whitelistcert1' -CertificateFile C:\users\gwallace\Desktop\cert.cer

> [AZURE.NOTE] O certificado fornecido neste passo, deve ser chave pública do certificado de pfx presente no back-end. Exporte o certificado (não o certificado de raiz) instalado no servidor de back-end na. CER formatar e utilizá-la neste passo. Este listas de brancas do passo back-end com o gateway de aplicação.

### <a name="step-8"></a>Passo 8

Configure as definições da aplicação gateway http back-end. Atribua o certificado carregado no passo anterior para as definições de http.

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name 'setting01' -Port 443 -Protocol Https -CookieBasedAffinity Enabled -AuthenticationCertificates $authcert

### <a name="step-9"></a>Passo 9

Crie uma regra encaminhamento Balanceador de carga que configura o comportamento do Balanceador de carga. Neste exemplo, é criada uma regra de básicas round robin.

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name 'rule01' -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

### <a name="step-10"></a>Passo 10

Configure o tamanho da instância do gateway aplicação.  Os tamanhos disponíveis são **padrão\_pequenas**, **padrão\_médio**, e **padrão\_grande**.  Para capacidade, os valores disponíveis são 1 a 10.

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

>[AZURE.NOTE] Pode ser escolhida uma contagem de instância de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é abrangido pela SLA e, por conseguinte, que não são recomendadas. Gateways pequenas estão a ser utilizado para Dev Center teste e não para fins de produção.

### <a name="step-11"></a>Passo 11

Configure a política SSL para ser utilizado no Gateway aplicação. Gateway aplicação suporta a capacidade de desativar determinadas versões de protocolo SSL.

Os seguintes valores são uma lista de versões de protocolo que pode ser desativada.

- **TLSv1_0**
- **TLSv1_1**
- **TLSv1_2**

O exemplo seguinte desativa TLSv1\_0.

    $sslPolicy = New-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0

## <a name="create-the-application-gateway"></a>Criar um Gateway de aplicação

Utilizando os passos anteriores, crie o Gateway de aplicação. A criação do gateway é um processo longo em execução.

    $appgw = New-AzureRmApplicationGateway -Name appgateway -SslCertificates $cert -ResourceGroupName "appgw-rg" -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicy -AuthenticationCertificates $authcert -Verbose

## <a name="disable-ssl-protocol-versions-on-an-existing-application-gateway"></a>Desativar versões de protocolo SSL num Gateway de aplicação existente

Os passos anteriores levá-lo através de criar uma aplicação com ponto a ponto ssl e desativar determinadas versões de protocolo SSL. O exemplo seguinte desativa determinadas políticas de SSL num gateway de aplicação existente.

### <a name="step-1"></a>Passo 1

Obtenha o gateway de aplicação para atualizar.

    $gw = Get-AzureRmApplicationGateway -Name AdatumAppGateway -ResourceGroupName AdatumAppGatewayRG

### <a name="step-2"></a>Passo 2

Defina uma política SSL. No exemplo seguinte, TLSv1.0 e TLSv1.1 são desativados.

    Set-AzureRmApplicationGatewaySslPolicy -DisabledSslProtocols TLSv1_0, TLSv1_1 -ApplicationGateway $gw

### <a name="step-3"></a>Passo 3

Por fim, atualize o gateway. É importante ter em atenção que este último passo é uma tarefa longa. Quando concluir, ponto a ponto ssl está configurado no gateway aplicação.

    $gw | Set-AzureRmApplicationGateway

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

Saiba mais sobre protecção a segurança das suas aplicações web com a Firewall do aplicação Web através de Gateway aplicação acedendo a [Descrição geral de Firewall de aplicações Web](application-gateway-webapplicationfirewall-overview.md)

[scenario]: ./media/application-gateway-end-to-end-ssl-powershell/scenario.png
