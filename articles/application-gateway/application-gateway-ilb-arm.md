<properties
   pageTitle="Criar e configurar um gateway aplicação com um balanceador de carga interna (ILB) utilizando o Gestor de recursos do Azure | Microsoft Azure"
   description="Esta página disponibiliza instruções para criar, configurar, iniciar e eliminar um gateway de aplicação Azure com Balanceador de carga interna (ILB) para o Gestor de recursos do Azure"
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
   ms.date="08/19/2016"
   ms.author="gwallace"/>


# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb-by-using-azure-resource-manager"></a>Criar um gateway de aplicação com um balanceador de carga interna (ILB) utilizando o Gestor de recursos do Azure

> [AZURE.SELECTOR]
- [Azure passos clássicos](application-gateway-ilb.md)
- [Gestor de recursos do PowerShell passos](application-gateway-ilb-arm.md)

Azure Gateway de aplicação pode ser configurado com uma VIP acesso à Internet ou com um ponto final interno que não é exposto à Internet, também conhecido como um carregamento interno balanceador (ILB) ponto final. Configurar o gateway com um ILB é útil para aplicações internas da linha de negócio que não são expostas à Internet. Também é útil para serviços e camadas dentro de uma aplicação de várias camada que sentar num limite de segurança que não está divulgado na Internet, mas ainda requerem round robin carregar distribuição, nódoas gordurosas de sessão ou taxas de cessação Secure Sockets Layer (SSL).

Este artigo explica os passos para configurar um gateway aplicação com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell utilizando o instalador do plataforma Web. Pode transferir e instalar a versão mais recente da secção da [página de transferências do](https://azure.microsoft.com/downloads/) **Windows PowerShell** .
2. Criar uma rede virtual e uma sub-rede para o Gateway de aplicação. Certifique-se de que nenhuma máquinas virtuais ou na nuvem implementações estão a utilizar a sub-rede. Aplicação Gateway tem de ser por si só, numa sub-rede rede virtual.
3. Os servidores que configurar para utilizar o aplicação gateway tem de existir ou tem os respetivos pontos finais criados na rede virtual ou com um IP público/VIP atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway aplicação?


- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencer à rede virtual mas na sub-rede diferente para o gateway de aplicação ou devem ser VIP/IP público.
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Porta front-end:** Esta porta é a pública porta que é aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta front-end, um protocolo (Http ou Https, estes são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra vincula automaticamente o serviço de escuta e que o servidor back-end conjunto e define qual conjunto de servidor back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta. Atualmente, apenas a regra *básica* é suportada. A regra *básicas* é distribuição de carga round robin.



## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

A diferença entre a utilizar o Azure clássica e o Gestor de recursos do Azure é a ordem pela qual cria o gateway de aplicação e os itens que têm de ser configurado.
Com o Gestor de recursos, todos os itens que tornam um gateway aplicação for configurados individualmente e, em seguida, em conjunto para criar o recurso de gateway de aplicação.


Eis os passos que são necessários para criar um gateway de aplicação:

1. Criar um grupo de recursos para Gestor de recursos
2. Criar uma rede virtual e uma sub-rede para o gateway de aplicação
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

Crie um novo grupo de recursos (ignorar este passo se estiver a utilizar um grupo de recursos existente).

    New-AzureRmResourceGroup -Name appgw-rg -location "West US"

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. É utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de que todos os comandos para criar um gateway aplicação utiliza mesmo grupo de recursos.

No exemplo acima, criámos de um grupo de recursos denominado "appgw rg" e a localização "Oeste-nos".

## <a name="create-a-virtual-network-and-a-subnet-for-the-application-gateway"></a>Criar uma rede virtual e uma sub-rede para o gateway de aplicação

O exemplo seguinte mostra como criar uma rede virtual utilizando o Gestor de recursos:

### <a name="step-1"></a>Passo 1

    $subnetconfig = New-AzureRmVirtualNetworkSubnetConfig -Name subnet01 -AddressPrefix 10.0.0.0/24

Este procedimento atribui a 10.0.0.0/24 intervalo endereço para uma variável de sub-rede para ser utilizada para criar uma rede virtual.

### <a name="step-2"></a>Passo 2

    $vnet = New-AzureRmVirtualNetwork -Name appgwvnet -ResourceGroupName appgw-rg -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $subnetconfig

Esta ação cria uma rede virtual com o nome "appgwvnet" no recurso grupo "appgw-rg" utilizando o prefixo 10.0.0.0/16 com sub-rede 10.0.0.0/24 à região ocidental dos EUA.

### <a name="step-3"></a>Passo 3

    $subnet = $vnet.subnets[0]

O objecto de sub-rede isto atribui a variável $subnet para os passos seguintes.

## <a name="create-an-application-gateway-configuration-object"></a>Criar um objeto de configuração de gateway de aplicação

### <a name="step-1"></a>Passo 1

    $gipconfig = New-AzureRmApplicationGatewayIPConfiguration -Name gatewayIP01 -Subnet $subnet

Esta ação cria uma configuração da aplicação gateway IP com o nome "gatewayIP01". Quando inicia o Gateway de aplicação, levantar o auscultador de um endereço IP da sub-rede configurado e encaminhar o tráfego de rede para os endereços IP no conjunto de IP back-end. Tenha em atenção que cada instância leva-o até um endereço IP.


### <a name="step-2"></a>Passo 2

    $pool = New-AzureRmApplicationGatewayBackendAddressPool -Name pool01 -BackendIPAddresses 134.170.185.46, 134.170.188.221,134.170.185.50

Este procedimento configura o conjunto de endereços IP de back-end "pool01" com endereços de IP com o nome "134.170.185.46, 134.170.188.221,134.170.185.50". São apresentados os endereços IP que recebem o tráfego de rede que sejam oriundos do ponto final de IP Front-end. Substitua os endereços IP acima para adicionar o seus próprio os pontos finais de endereço do IP de aplicação.

### <a name="step-3"></a>Passo 3

    $poolSetting = New-AzureRmApplicationGatewayBackendHttpSettings -Name poolsetting01 -Port 80 -Protocol Http -CookieBasedAffinity Disabled

Este procedimento configura o tráfego de rede do gateway definição "poolsetting01" para a carga de balanceamento de aplicação no conjunto de back-end.

### <a name="step-4"></a>Passo 4

    $fp = New-AzureRmApplicationGatewayFrontendPort -Name frontendport01  -Port 80

Este procedimento configura a front-end porta IP com o nome "frontendport01" para o ILB.

### <a name="step-5"></a>Passo 5

    $fipconfig = New-AzureRmApplicationGatewayFrontendIPConfig -Name fipconfig01 -Subnet $subnet

Este procedimento cria a configuração de IP Front-end denominada "fipconfig01" e associa-o com um IP privado da sub-rede rede virtual atual.

### <a name="step-6"></a>Passo 6

    $listener = New-AzureRmApplicationGatewayHttpListener -Name listener01  -Protocol Http -FrontendIPConfiguration $fipconfig -FrontendPort $fp

Este procedimento cria a escuta denominada "listener01" e associa a porta front-end para a configuração de IP Front-end.

### <a name="step-7"></a>Passo 7

    $rule = New-AzureRmApplicationGatewayRequestRoutingRule -Name rule01 -RuleType Basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool

Esta ação cria a regra encaminhamento de Balanceador de carga denominada "rule01" que configura o comportamento do Balanceador de carga.

### <a name="step-8"></a>Passo 8

    $sku = New-AzureRmApplicationGatewaySku -Name Standard_Small -Tier Standard -Capacity 2

Este procedimento configura o tamanho da instância do gateway aplicação.

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pode escolher entre Standard_Small, Standard_Medium e Standard_Large.

## <a name="create-an-application-gateway-by-using-new-azureapplicationgateway"></a>Criar um gateway aplicação utilizando AzureApplicationGateway novo

Cria um gateway aplicação com todos os itens de configuração dos passos acima. Neste exemplo, o gateway aplicação chama-se "appgwtest".


    $appgw = New-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Location "West US" -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig  -GatewayIpConfigurations $gipconfig -FrontendPorts $fp -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku

Esta ação cria um gateway aplicação com todos os itens de configuração dos passos acima. No exemplo, o gateway aplicação chama-se "appgwtest".


## <a name="delete-an-application-gateway"></a>Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação, terá de fazer o seguinte na ordem:

1. Utilize o cmdlet **AzureRmApplicationGateway parar** para parar o gateway.
2. Utilize o cmdlet **AzureRmApplicationGateway remover** para remover o gateway.
3. Certifique-se de que o gateway foi removido utilizando o cmdlet **Get-AzureApplicationGateway** .


### <a name="step-1"></a>Passo 1

Obtenha o objeto de gateway application e associá-la a uma variável "$getgw".

    $getgw =  Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

### <a name="step-2"></a>Passo 2

Utilize **AzureRmApplicationGateway parar** para parar o gateway de aplicação. Este exemplo mostra o cmdlet **Parar AzureRmApplicationGateway** na primeira linha, seguido de saída.

    PS C:\> Stop-AzureRmApplicationGateway -ApplicationGateway $getgw  

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Assim que o gateway aplicação estiver num estado parado, utilize o cmdlet **AzureRmApplicationGateway remover** para remover o serviço.


    PS C:\> Remove-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg -Force

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

>[AZURE.NOTE] O **-Forçar** parâmetro pode ser utilizado para suprimir a mensagem de confirmação remover.


Para verificar se o serviço foi removido, pode utilizar o cmdlet **Get-AzureRmApplicationGateway** . Este passo não é necessário.


    PS C:\>Get-AzureRmApplicationGateway -Name appgwtest -ResourceGroupName appgw-rg

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Próximos passos

Se pretende configurar a passagem para da SSL, consulte o artigo [configurar um gateway de aplicação para SSL descarregar](application-gateway-ssl.md).

Se pretender configurar um gateway para utilizar com um ILB de aplicação, consulte o artigo [criar um gateway de aplicação com um balanceador de carga interna (ILB)](application-gateway-ilb.md).

Se pretender obter mais informações sobre carregar balanceamento de opções geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
