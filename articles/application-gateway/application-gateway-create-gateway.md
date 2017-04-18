<properties
   pageTitle="Criar, iniciar ou eliminar um gateway aplicação | Microsoft Azure"
   description="Esta página disponibiliza instruções para criar, configurar, iniciar e eliminar um gateway aplicação Azure"
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

# <a name="create-start-or-delete-an-application-gateway"></a>Criar, iniciar ou eliminar um gateway de aplicação

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gestor de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [Clip Azure](application-gateway-create-gateway-cli.md)

Azure de aplicação de Gateway é um balanceador de carga de camada-7. Fornece falha na ligação, pedidos HTTP encaminhamento de desempenho entre servidores diferentes, quer estejam na nuvem ou no local. Aplicação Gateway fornece muitas controlador de entrega de aplicação (ADC) das funcionalidades incluindo balanceamento de carga HTTP afinidade sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, o estado de funcionamento personalizado sondas, suporte para múltiplos sites e muitas outras. Para localizar uma lista completa das funcionalidades suportadas, visite [Descrição geral de Gateway de aplicação](application-gateway-introduction.md)

Este artigo explica os passos para criar, configurar, iniciar e eliminar um gateway de aplicação.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell utilizando o instalador do plataforma Web. Pode transferir e instalar a versão mais recente da secção da [página de transferências do](https://azure.microsoft.com/downloads/) **Windows PowerShell** .
2. Se tiver uma rede virtual existente, selecione uma sub-rede vazia existente ou crie uma nova sub-rede na sua rede existente virtual exclusivamente para utilização ao gateway de aplicação. Não é possível implementar o gateway de aplicação para uma rede virtual diferente os recursos que pretende implementar atrás do gateway de aplicação, a menos que vnet efectuado é utilizado. Para saber mais visite [Vnet efectuado](../virtual-network/virtual-network-peering-overview.md)
3. Certifique-se de que tem uma rede virtual trabalhar com uma sub-rede válida. Certifique-se de que nenhuma máquinas virtuais ou na nuvem implementações estão a utilizar a sub-rede. O gateway aplicação tem de ser por si só, numa sub-rede rede virtual.
3. Os servidores que configurar para utilizar o aplicação gateway tem de existir ou tem os respetivos pontos finais criados na rede virtual ou com um IP público/VIP atribuído.

## <a name="what-is-required-to-create-an-application-gateway"></a>O que é necessário para criar um gateway aplicação?

Quando utiliza o comando **AzureApplicationGateway novo** para criar um gateway de aplicação, nenhuma configuração está definida neste momento e o recurso recentemente criado são configurados através de XML ou um objeto de configuração.

Os valores são:

- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencem à sub-rede rede virtual ou devem ser VIP/IP público.
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Porta front-end:** Esta porta é a pública porta que é aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta front-end, um protocolo (Http ou Https, estes valores são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra vincula automaticamente o serviço de escuta e que o servidor back-end conjunto e define qual conjunto de servidor back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso de gateway de aplicação.
2. Crie um ficheiro de configuração de XML ou um objeto de configuração.
3. Consolide a configuração para o recurso de gateway de aplicação recentemente criado.

>[AZURE.NOTE] Se precisar de configurar uma sonda personalizada para o gateway de aplicação, consulte o artigo [criar um gateway de aplicação com sondas personalizados utilizando o PowerShell](application-gateway-create-probe-classic-ps.md). Veja [as pesquisas personalizadas e os do Estado de funcionamento de monitorização](application-gateway-probe-overview.md) para obter mais informações.

![Exemplo de cenário][scenario]

### <a name="create-an-application-gateway-resource"></a>Criar um recurso de gateway de aplicação

Para criar o gateway, utilize o cmdlet **Novo AzureApplicationGateway** , substituir os valores com a sua própria. Faturação para o gateway não é iniciado neste momento. Faturação começa um passo mais tarde, quando o gateway for iniciado com êxito.

O exemplo seguinte cria um gateway de aplicação, utilizando uma rede virtual denominado "testvnet1" e uma sub-rede designado por "sub-rede-1".


    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Descrição*, *InstanceCount*e *GatewaySize* são parâmetros opcionais.

Para validar que o gateway foi criado, pode utilizar o cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Stopped
    VirtualIPs    : {}
    DnsName       :

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pode escolher entre pequeno, médio e grande.

*VirtualIPs* e *NomeDNS* são mostrados como em branco porque o gateway ainda não tiver iniciado. Estes são criados assim que o gateway está no estado de em execução.

## <a name="configure-the-application-gateway"></a>Configure o gateway de aplicação

Pode configurar o gateway aplicação utilizando XML ou um objeto de configuração.

## <a name="configure-the-application-gateway-by-using-xml"></a>Configurar o gateway aplicação utilizando XML

No exemplo seguinte, utilize um ficheiro XML para configurar todas as definições de gateway de aplicação e consolidá-los para o recurso de gateway de aplicação.  

### <a name="step-1"></a>Passo 1  

Copie o texto que se segue para o bloco de notas.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>(name-of-your-frontend-port)</Name>
                <Port>(port number)</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>(name-of-your-backend-pool)</Name>
                <IPAddresses>
                    <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
                    <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>(backend-setting-name-to-configure-rule)</Name>
                <Port>80</Port>
                <Protocol>[Http|Https]</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>(name-of-the-listener)</Name>
                <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
                <Protocol>[Http|Https]</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>(name-of-load-balancing-rule)</Name>
                <Type>basic</Type>
                <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
                <Listener>(name-of-the-listener)</Listener>
                <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>

Edite os valores entre parênteses para os itens de configuração. Guarde o ficheiro com a extensão. XML.

>[AZURE.IMPORTANT] O item de protocolo Http ou Https é sensível às maiúsculas.

O exemplo seguinte mostra como utilizar um ficheiro de configuração para configurar o gateway de aplicação. A carga de exemplo equilibra o tráfego HTTP na porta público 80 e envia o tráfego de rede à porta de back-end 80 entre dois endereços IP.

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>80</Port>
            </FrontendPort>
        </FrontendPorts>
        <BackendAddressPools>
            <BackendAddressPool>
                <Name>BackendPool1</Name>
                <IPAddresses>
                    <IPAddress>10.0.0.1</IPAddress>
                    <IPAddress>10.0.0.2</IPAddress>
                </IPAddresses>
            </BackendAddressPool>
        </BackendAddressPools>
        <BackendHttpSettingsList>
            <BackendHttpSettings>
                <Name>BackendSetting1</Name>
                <Port>80</Port>
                <Protocol>Http</Protocol>
                <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            </BackendHttpSettings>
        </BackendHttpSettingsList>
        <HttpListeners>
            <HttpListener>
                <Name>HTTPListener1</Name>
                <FrontendPort>FrontendPort1</FrontendPort>
                <Protocol>Http</Protocol>
            </HttpListener>
        </HttpListeners>
        <HttpLoadBalancingRules>
            <HttpLoadBalancingRule>
                <Name>HttpLBRule1</Name>
                <Type>basic</Type>
                <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
                <Listener>HTTPListener1</Listener>
                <BackendAddressPool>BackendPool1</BackendAddressPool>
            </HttpLoadBalancingRule>
        </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


### <a name="step-2"></a>Passo 2

Em seguida, defina o gateway de aplicação. Utilize o cmdlet **Set-AzureApplicationGatewayConfig** com um ficheiro de configuração de XML.


    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="configure-the-application-gateway-by-using-a-configuration-object"></a>Configurar o gateway de aplicação, utilizando um objeto de configuração

O exemplo seguinte mostra como configurar o gateway aplicação utilizando objectos de configuração. Todos os itens de configuração tem de ser configurados individualmente e, em seguida, adicionados a um objeto de configuração de gateway de aplicação. Depois de criar o objeto de configuração, utilize o comando **Definir AzureApplicationGateway** para consolidar a configuração para o recurso de gateway aplicação criada anteriormente.

>[AZURE.NOTE] Antes de atribuir um valor a cada objeto de configuração, terá de declarar que tipo de objeto PowerShell utiliza armazenamento. A primeira linha para criar os itens individuais define que Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model (nome do objeto) são utilizados.

### <a name="step-1"></a>Passo 1

Crie todos os itens individuais de configuração.

Crie o PI front-end, conforme mostrado no seguinte exemplo.

    $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
    $fip.Name = "fip1"
    $fip.Type = "Private"
    $fip.StaticIPAddress = "10.0.0.5"

Crie a porta front-end, conforme mostrado no seguinte exemplo.

    $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
    $fep.Name = "fep1"
    $fep.Port = 80

Crie o conjunto de servidor back-end.

 Defina os endereços IP que são adicionados ao agrupamento de servidor back-end, conforme mostrado no seguinte exemplo.


    $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
    $servers.Add("10.0.0.1")
    $servers.Add("10.0.0.2")

 Utilize o objeto $server para adicionar os valores para o objeto de conjunto de dados back-end ($pool).

    $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
    $pool.BackendServers = $servers
    $pool.Name = "pool1"

Crie a definição do conjunto de dados de servidor back-end.

    $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
    $setting.Name = "setting1"
    $setting.CookieBasedAffinity = "enabled"
    $setting.Port = 80
    $setting.Protocol = "http"

Crie a escuta.

    $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
    $listener.Name = "listener1"
    $listener.FrontendPort = "fep1"
    $listener.FrontendIP = "fip1"
    $listener.Protocol = "http"
    $listener.SslCert = ""

Crie a regra.

    $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
    $rule.Name = "rule1"
    $rule.Type = "basic"
    $rule.BackendHttpSettings = "setting1"
    $rule.Listener = "listener1"
    $rule.BackendAddressPool = "pool1"

### <a name="step-2"></a>Passo 2

Atribua todos os itens de configuração individuais a um objeto de configuração de gateway de aplicação ($appgwconfig).

Adicione o IP Front-end para a configuração.

    $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
    $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
    $appgwconfig.FrontendIPConfigurations.Add($fip)

Adicione a porta front-end para a configuração.

    $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
    $appgwconfig.FrontendPorts.Add($fep)

Adicione o conjunto de servidor back-end para a configuração.

    $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
    $appgwconfig.BackendAddressPools.Add($pool)

Adicione a definição do conjunto de dados back-end para a configuração.

    $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
    $appgwconfig.BackendHttpSettingsList.Add($setting)

Adicione a escuta na configuração.

    $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
    $appgwconfig.HttpListeners.Add($listener)

Adicione a regra para a configuração.

    $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
    $appgwconfig.HttpLoadBalancingRules.Add($rule)

### <a name="step-3"></a>Passo 3

Consolide o objeto de configuração para o recurso de gateway aplicação utilizando **AzureApplicationGatewayConfig conjunto**.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## <a name="start-the-gateway"></a>Iniciar o gateway

Assim que tiver sido configurado o gateway, utilize o cmdlet **AzureApplicationGateway iniciar** para iniciar o gateway. Faturação para um gateway aplicação começa depois do gateway foi iniciado com êxito.

> [AZURE.NOTE] O cmdlet **Iniciar AzureApplicationGateway** poderá demorar até 15-20 minutos a concluir.

    Start-AzureApplicationGateway AppGwTest

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Utilize o cmdlet **Get-AzureApplicationGateway** para verificar o estado do gateway. Se **Iniciar AzureApplicationGateway** foi concluída com êxito no passo anterior, *Estado* deve estar em execução e *Vip* e *NomeDNS* devem ter entradas válidas.

O exemplo seguinte mostra um gateway de aplicação que está para cima, para começar a trabalhar, e pronto para tirar o tráfego destinados `http://<generated-dns-name>.cloudapp.net`.

    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    Vip           : 138.91.170.26
    DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## <a name="delete-an-application-gateway"></a>Eliminar um gateway de aplicação

Para eliminar um gateway de aplicação:

1. Utilize o cmdlet **AzureApplicationGateway parar** para parar o gateway.
2. Utilize o cmdlet **AzureApplicationGateway remover** para remover o gateway.
3. Certifique-se de que o gateway foi removido utilizando o cmdlet **Get-AzureApplicationGateway** .

O exemplo seguinte mostra o cmdlet **Parar AzureApplicationGateway** na primeira linha, seguida de saída.

    Stop-AzureApplicationGateway AppGwTest

    VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
    VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Assim que o gateway aplicação estiver num estado parado, utilize o cmdlet **AzureApplicationGateway remover** para remover o serviço.


    Remove-AzureApplicationGateway AppGwTest

    VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
    VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error
    ----       ----------------     ------------                             ----
    Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para verificar se o serviço foi removido, pode utilizar o cmdlet **Get-AzureApplicationGateway** . Este passo não é necessário.


    Get-AzureApplicationGateway AppGwTest

    VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

    Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
    .....

## <a name="next-steps"></a>Próximos passos

Se pretende configurar a passagem para da SSL, consulte o artigo [configurar um gateway de aplicação para SSL descarregar](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interna, consulte o artigo [criar um gateway de aplicação com um balanceador de carga interna (ILB)](application-gateway-ilb.md).

Se pretender obter mais informações sobre carregar balanceamento de opções geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

[scenario]: ./media/application-gateway-create-gateway/scenario.png