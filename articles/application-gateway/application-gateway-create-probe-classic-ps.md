<properties
   pageTitle="Criar uma sonda personalizada para o Gateway de aplicação ao utilizar o PowerShell no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como criar uma sonda personalizada para o Gateway de aplicação ao utilizar o PowerShell no modelo de implementação clássico"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-a-custom-probe-for-azure-application-gateway-classic-by-using-powershell"></a>Criar uma sonda personalizada para o Gateway de aplicação do Azure (clássico) ao utilizar o PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clássico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](application-gateway-create-probe-ps.md).

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Para criar um gateway de aplicação:

1. Crie um recurso de gateway de aplicação.
2. Crie um ficheiro de configuração de XML ou um objeto de configuração.
3. Consolide a configuração para o recurso de gateway de aplicação recentemente criado.

### <a name="create-an-application-gateway-resource"></a>Criar um recurso de gateway de aplicação

Para criar o gateway, utilize o cmdlet **Novo AzureApplicationGateway** , substituir os valores com a sua própria. Faturação para o gateway não é iniciado neste momento. Faturação começa um passo mais tarde, quando o gateway for iniciado com êxito.

O exemplo seguinte cria um gateway de aplicação, utilizando uma rede virtual denominado "testvnet1" e uma sub-rede designado por "sub-rede-1".

    New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

Para validar que o gateway foi criado, pode utilizar o cmdlet **Get-AzureApplicationGateway** .

    Get-AzureApplicationGateway AppGwTest

>[AZURE.NOTE]  O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pode escolher entre pequeno, médio e grande.

 *VirtualIPs* e *NomeDNS* são mostrados como em branco porque o gateway ainda não tiver iniciado. Estes são criados assim que o gateway está no estado de em execução.

## <a name="configure-an-application-gateway"></a>Configurar um gateway de aplicação

Pode configurar o gateway aplicação utilizando XML ou um objeto de configuração.

## <a name="configure-an-application-gateway-by-using-xml"></a>Configurar um gateway aplicação utilizando XML

No exemplo seguinte, utilize um ficheiro XML para configurar todas as definições de gateway de aplicação e consolidá-los para o recurso de gateway de aplicação.  

### <a name="step-1"></a>Passo 1

Copie o texto que se segue para o bloco de notas.

    <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
    <FrontendIPConfigurations>
        <FrontendIPConfiguration>
            <Name>fip1</Name>
            <Type>Private</Type>
        </FrontendIPConfiguration>
    </FrontendIPConfigurations>    
    <FrontendPorts>
        <FrontendPort>
            <Name>port1</Name>
            <Port>80</Port>
        </FrontendPort>
    </FrontendPorts>
    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
      </Probes>
     <BackendAddressPools>
        <BackendAddressPool>
            <Name>pool1</Name>
            <IPAddresses>
                <IPAddress>1.1.1.1</IPAddress>
                <IPAddress>2.2.2.2</IPAddress>
            </IPAddresses>
        </BackendAddressPool>
    </BackendAddressPools>
    <BackendHttpSettingsList>
        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>
    </BackendHttpSettingsList>
    <HttpListeners>
        <HttpListener>
            <Name>listener1</Name>
            <FrontendIP>fip1</FrontendIP>
        <FrontendPort>port1</FrontendPort>
            <Protocol>Http</Protocol>
        </HttpListener>
    </HttpListeners>
    <HttpLoadBalancingRules>
        <HttpLoadBalancingRule>
            <Name>lbrule1</Name>
            <Type>basic</Type>
            <BackendHttpSettings>setting1</BackendHttpSettings>
            <Listener>listener1</Listener>
            <BackendAddressPool>pool1</BackendAddressPool>
        </HttpLoadBalancingRule>
    </HttpLoadBalancingRules>
    </ApplicationGatewayConfiguration>


Edite os valores entre parênteses para os itens de configuração. Guarde o ficheiro com a extensão. XML.

O exemplo seguinte mostra como utilizar um ficheiro de configuração para configurar o gateway de aplicação para o tráfego de HTTP na porta público 80 de balanceamento de carga e enviar tráfego de rede à porta de back-end 80 entre dois endereços IP utilizando uma sonda personalizada.

>[AZURE.IMPORTANT] O item de protocolo Http ou Https é sensível às maiúsculas.

Um novo item de configuração \<sonda\> é adicionada ao configurar sondas personalizadas.

Os parâmetros de configuração são:

- **Nome** - nome de referência para sonda personalizada.
- **Protocolo** - protocolo utilizado (valores possíveis são HTTP ou HTTPS).
- **Anfitrião** e **caminho** - caminho de URL completo que é chamado pelo gateway de aplicação para determinar o estado de funcionamento da instância. Por exemplo, se tiver um Web site http://contoso.com/, em seguida, a sonda personalizada pode ser configurada para "http://contoso.com/path/custompath.htm" para os controlos de sonda a tem uma resposta HTTP com êxito.
- **Intervalo** - configura as verificações do sonda intervalo em segundos.
- **Tempo limite** - define o limite de tempo sonda uma verificação de resposta HTTP.
- **UnhealthyThreshold** - o número de respostas HTTP falhou necessário para sinalizar a instância de back-end como *danificado*.

O nome de sonda é referenciado na <BackendHttpSettings> configuração para atribuir qual agrupamento de back-end utiliza as definições de sonda personalizado.

## <a name="add-a-custom-probe-configuration-to-an-existing-application-gateway"></a>Adicionar uma configuração sonda personalizado a um gateway de aplicação existente

Alterar a configuração atual de um gateway aplicação requer três passos: obter o ficheiro de configuração de XML atual, modificar ter uma sonda personalizada e configure o gateway de aplicação com as novas definições de XML.

### <a name="step-1"></a>Passo 1

Obter o ficheiro XML utilizando get-AzureApplicationGatewayConfig. Isto exporta a configuração XML ser alterada para adicionar uma definição de sonda.

    Get-AzureApplicationGatewayConfig -Name "<application gateway name>" -Exporttofile "<path to file>"


### <a name="step-2"></a>Passo 2

Abra o ficheiro XML num editor de texto. Adicionar um `<probe>` secção após `<frontendport>`.

    <Probes>
        <Probe>
            <Name>Probe01</Name>
            <Protocol>Http</Protocol>
            <Host>contoso.com</Host>
            <Path>/path/custompath.htm</Path>
            <Interval>15</Interval>
            <Timeout>15</Timeout>
            <UnhealthyThreshold>5</UnhealthyThreshold>
        </Probe>
    </Probes>

Na secção backendHttpSettings de XML, adicione o nome de sonda conforme mostrado no exemplo seguinte:

        <BackendHttpSettings>
            <Name>setting1</Name>
            <Port>80</Port>
            <Protocol>Http</Protocol>
            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
            <RequestTimeout>120</RequestTimeout>
            <Probe>Probe01</Probe>
        </BackendHttpSettings>

Guarde o ficheiro XML.

### <a name="step-3"></a>Passo 3

Atualize a configuração de gateway aplicações com o novo ficheiro XML utilizando o **Conjunto AzureApplicationGatewayConfig**. Este procedimento actualiza gateway de aplicação com a nova configuração.

    Set-AzureApplicationGatewayConfig -Name "<application gateway name>" -Configfile "<path to file>"


## <a name="next-steps"></a>Próximos passos

Se pretende configurar a passagem para da Secure Sockets Layer (SSL), consulte o artigo [configurar um gateway de aplicação para SSL descarregar](application-gateway-ssl.md).

Se pretender configurar um gateway de aplicação para utilizar com um balanceador de carga interna, consulte o artigo [criar um gateway de aplicação com um balanceador de carga interna (ILB)](application-gateway-ilb.md).
