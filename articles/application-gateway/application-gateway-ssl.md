<properties
   pageTitle="Configurar um gateway de aplicação para a passagem para da SSL utilizando implementação clássica | Microsoft Azure"
   description="Este artigo fornece instruções para criar um gateway aplicação com SSL descarregar, utilizando o modelo de implementação clássica Azure."
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

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-classic-deployment-model"></a>Configurar um gateway de aplicação para a passagem para da SSL, utilizando o modelo clássico de implementação

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Gestor de recursos do PowerShell](application-gateway-ssl-arm.md)
-[PowerShell clássica do Azure](application-gateway-ssl.md)

Azure Gateway de aplicação pode ser configurado para terminar a sessão Secure Sockets Layer (SSL) ao gateway para evitar dispendiosas tarefas de desencriptação SSL para ocorrer no web farm. A passagem para da SSL também simplifica a configuração do servidor front-end e gestão da aplicação web.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell utilizando o instalador do plataforma Web. Pode transferir e instalar a versão mais recente da secção da [página de transferências do](https://azure.microsoft.com/downloads/) **Windows PowerShell** .
2. Certifique-se de que tem uma rede virtual trabalhar com uma sub-rede válida. Certifique-se de que nenhuma máquinas virtuais ou na nuvem implementações estão a utilizar a sub-rede. O gateway aplicação tem de ser por si só, numa sub-rede rede virtual.
3. Os servidores que configurar para utilizar o aplicação gateway tem de existir ou tem os respetivos pontos finais criados na rede virtual ou com um IP público/VIP atribuído.

Para configurar a passagem para da SSL um gateway de aplicação, efetue os seguintes passos na ordem listada:

1. [Criar um gateway de aplicação](#create-an-application-gateway)
2. [Carregar certificados SSL](#upload-ssl-certificates)
3. [Configure o gateway](#configure-the-gateway)
4. [Definir a configuração de gateway](#set-the-gateway-configuration)
5. [Iniciar o gateway](#start-the-gateway)
6. [Verificar o estado do gateway](#verify-the-gateway-status)


## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar o gateway, utilize o cmdlet **Novo AzureApplicationGateway** , substituir os valores com a sua própria. Faturação para o gateway não é iniciado neste momento. Faturação começa um passo mais tarde, quando o gateway for iniciado com êxito.

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que o gateway foi criado, pode utilizar o cmdlet **Get-AzureApplicationGateway** .

No exemplo, *Descrição*, *InstanceCount*e *GatewaySize* são parâmetros opcionais. O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pequenos e grandes são outros valores disponíveis. *VirtualIPs* e *NomeDNS* são mostrados como em branco, porque o gateway ainda não tiver iniciado. Estes valores são criados assim que o gateway está no estado de em execução.

    Get-AzureApplicationGateway AppGwTest

## <a name="upload-ssl-certificates"></a>Carregar certificados SSL

Utilize **Adicionar AzureApplicationGatewaySslCertificate** para carregar o certificado do servidor no formato de *pfx* para o gateway de aplicação. O nome do certificado é um nome de utilizador-escolhido e têm de ser exclusivo dentro do gateway de aplicação. Este certificado é referido por este nome em todas as operações de gestão de certificado no gateway aplicação.

Este exemplo seguinte mostra o cmdlet, substitua os valores de exemplo com o seu próprio.

    Add-AzureApplicationGatewaySslCertificate  -Name AppGwTest -CertificateName GWCert -Password <password> -CertificateFile <full path to pfx file>

Em seguida, valide o carregamento de certificado. Utilize o cmdlet **Get-AzureApplicationGatewayCertificate** .

Este exemplo mostra o cmdlet na primeira linha, seguido de saída.

    Get-AzureApplicationGatewaySslCertificate AppGwTest

    VERBOSE: 5:07:54 PM - Begin Operation: Get-AzureApplicationGatewaySslCertificate
    VERBOSE: 5:07:55 PM - Completed Operation: Get-AzureApplicationGatewaySslCertificate
    Name           : SslCert
    SubjectName    : CN=gwcert.app.test.contoso.com
    Thumbprint     : AF5ADD77E160A01A6......EE48D1A
    ThumbprintAlgo : sha1RSA
    State..........: Provisioned

>[AZURE.NOTE] A palavra-passe de certificado tem de estar entre carateres, letras ou números 4 a 12. Carateres especiais não são aceites.

## <a name="configure-the-gateway"></a>Configure o gateway

Configuração de gateway uma aplicação é composta por vários valores. Podem ser associados os valores em conjunto para construir a configuração.

Os valores são:

- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencem à sub-rede rede virtual ou devem ser VIP/IP público.
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Porta front-end:** Esta porta é a pública porta que é aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta front-end, um protocolo (Http ou Https, estes valores são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar).
- **Regra:** A regra vincula automaticamente o serviço de escuta e que o servidor back-end conjunto e define qual conjunto de servidor back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta. Atualmente, apenas a regra *básica* é suportada. A regra *básicas* é distribuição de carga round robin.

**Notas de configuração adicionais**

Para a configuração de certificados SSL, o protocolo no **HttpListener** deve mudar para *Https* (sensível a maiúsculas e minúsculas). O elemento **SslCert** é adicionado à **HttpListener** com o valor definido para o mesmo nome que utilizados no carregamento de anterior secção de certificados SSL. A porta front-end deve ser atualizada para 443.

**Para ativar afinidade com base em cookie**: um gateway aplicação pode ser configurado para garantir que um pedido a partir de uma sessão de cliente é sempre direccionado para o mesmo VM na web farm. Este cenário é executado por introdução de um cookie de sessão que permite que o gateway direcionar tráfego corretamente. Para ativar afinidade com base em cookies, defina **CookieBasedAffinity** para *ativado* no elemento de **BackendHttpSettings** .



Pode construir a sua configuração através da criação de um objeto de configuração ou ao utilizar um ficheiro de configuração de XML.
Para construir a sua configuração utilizando um ficheiro de configuração de XML, utilize o exemplo seguinte:

**Exemplo XML de configuração**

    <?xml version="1.0" encoding="utf-8"?>
    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
        <FrontendIPConfigurations />
        <FrontendPorts>
            <FrontendPort>
                <Name>FrontendPort1</Name>
                <Port>443</Port>
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
                <Protocol>Https</Protocol>
                <SslCert>GWCert</SslCert>
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


## <a name="set-the-gateway-configuration"></a>Definir a configuração de gateway

Em seguida, defina o gateway de aplicação. Pode utilizar o cmdlet **Set-AzureApplicationGatewayConfig** com um objeto de configuração ou com um ficheiro de configuração de XML.

    Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

## <a name="start-the-gateway"></a>Iniciar o gateway

Assim que tiver sido configurado o gateway, utilize o cmdlet **AzureApplicationGateway iniciar** para iniciar o gateway. Faturação para um gateway aplicação começa depois do gateway foi iniciado com êxito.


**Nota:** O cmdlet **Iniciar AzureApplicationGateway** poderá demorar até 15-20 minutos a concluir.

    Start-AzureApplicationGateway AppGwTest

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Utilize o cmdlet **Get-AzureApplicationGateway** para verificar o estado do gateway. Se **Iniciar AzureApplicationGateway** foi concluída com êxito no passo anterior, *Estado* deve estar em execução e *VirtualIPs* e *NomeDNS* devem ter entradas válidas.

Este exemplo mostra um gateway de aplicação que para cima, executar, está pronto para tirar o tráfego.

    Get-AzureApplicationGateway AppGwTest

    Name          : AppGwTest2
    Description   :
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {23.96.22.241}
    DnsName       : appgw-4c960426-d1e6-4aae-8670-81fd7a519a43.cloudapp.net


## <a name="next-steps"></a>Próximos passos


Se pretender obter mais informações sobre carregar balanceamento de opções geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)