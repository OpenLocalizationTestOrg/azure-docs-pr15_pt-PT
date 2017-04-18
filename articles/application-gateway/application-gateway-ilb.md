<properties 
   pageTitle="Criar e configurar um Gateway aplicação com Balanceador de carga interna (ILB) numa rede Virtual | Microsoft Azure"
   description="Esta página disponibiliza instruções para configurar um Gateway de aplicação do Azure com um ponto final de balanceamento de carga interno"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="jdial"
   editor="tysonn"/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="08/19/2016"
   ms.author="gwallace"/>

# <a name="create-an-application-gateway-with-an-internal-load-balancer-ilb"></a>Criar um Gateway aplicação com um balanceador de carga interna (ILB)

> [AZURE.SELECTOR]
- [Azure passos clássicos](application-gateway-ilb.md)
- [Gestor de recursos do Powershell passos](application-gateway-ilb-arm.md)

Pode ser configurada Gateway aplicação com um opostas virtual IP da internet ou com ponto final interno não são expostos à internet, também conhecido como ponto final do Balanceador de carga interna (ILB). Configurar o gateway com um ILB é útil para aplicações internas de linha de negócio não expostas à internet. Também é útil para serviços/camadas dentro de uma aplicação de várias camada, que está inserido um limite de segurança não exposto à internet, mas ainda requerem a distribuição de carga round robin, nódoas gordurosas de sessão ou terminação SSL. Este artigo explica os passos para configurar um gateway aplicação com um ILB.

## <a name="before-you-begin"></a>Antes de começar

1. Instale a versão mais recente dos cmdlets do Azure PowerShell utilizando o instalador do plataforma Web. Pode transferir e instalar a versão mais recente da secção da [página de transferência do](https://azure.microsoft.com/downloads/) **Windows PowerShell** .
2. Certifique-se de que tem uma rede virtual a funcionar com sub-rede válida.
3. Certifique-se de que tem os servidores de back-end a rede virtual ou com um VIP/IP público atribuídas.


Para criar um gateway de aplicação, execute os passos seguintes na ordem listada. 

1. [Criar um gateway de aplicação](#create-a-new-application-gateway)
2. [Configure o gateway](#configure-the-gateway)
3. [Definir a configuração de gateway](#set-the-gateway-configuration)
4. [Iniciar o gateway](#start-the-gateway)
4. [Verifique se o gateway](#verify-the-gateway-status)



## <a name="create-an-application-gateway"></a>Crie um gateway de aplicação:

**Para criar um gateway**, utilizar o `New-AzureApplicationGateway` cmdlet, substituir os valores com a sua própria. Tenha em atenção que a faturação para o gateway não é iniciado neste momento. Faturação começa um passo mais tarde, quando o gateway for iniciado com êxito.

    PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

    VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway 
    VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399

**Para validar** que o gateway foi criado, pode utilizar o `Get-AzureApplicationGateway` cmdlet. 

No exemplo, *Descrição*, *InstanceCount*e *GatewaySize* são parâmetros opcionais. O valor predefinido para *InstanceCount* for 2, com um valor máximo de 10. O valor predefinido para *GatewaySize* é médio. Pequenos e grandes são outros valores disponíveis. *VIP* e *NomeDNS* são mostrados como em branco porque o gateway ainda não tiver iniciado. Estes são criados assim que o gateway está no estado de em execução. 

    PS C:\> Get-AzureApplicationGateway AppGwTest

    VERBOSE: 4:39:39 PM - Begin Operation:
    Get-AzureApplicationGateway VERBOSE: 4:39:40 PM - Completed 
    Operation: Get-AzureApplicationGateway
    Name: AppGwTest 
    Description: 
    VnetName: testvnet1 
    Subnets: {Subnet-1} 
    InstanceCount: 2 
    GatewaySize: Medium 
    State: Stopped 
    VirtualIPs: 
    DnsName:


## <a name="configure-the-gateway"></a>Configure o gateway

Configuração de gateway uma aplicação é composta por vários valores. Podem ser associados os valores em conjunto para construir a configuração.
 
Os valores são:

- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencem à sub-rede VNet ou devem ser VIP/IP público. 
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Frontend porta:** Esta porta é a porta pública aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta de front-end, um protocolo (Http ou Https, estes são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar). 
- **Regra:** A regra vincula automaticamente o serviço de escuta e o conjunto de servidor de back-end e define qual conjunto de servidor de back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta. Atualmente, apenas a regra *básica* é suportada. A regra *básicas* é distribuição de carga round robin.

Pode construir a sua configuração através da criação de um objeto de configuração ou ao utilizar um ficheiro de configuração de XML. Construir a sua configuração utilizando um ficheiro de configuração de XML, utilize o exemplo abaixo.



Tenha em atenção o seguinte procedimento:


- O elemento *FrontendIPConfigurations* descreve os detalhes ILB relevantes para a configuração de Gateway aplicação com um ILB. 

- O *tipo* de front-end IP deve ser definido como "Privado"

- O *StaticIPAddress* deve ser definido para o PI interno pretendido no qual o gateway recebe tráfego. Note que o elemento *StaticIPAddress* é opcional. Se não for definido, uma IP disponíveis interno da sub-rede implementada é escolhido. 

- Deverá ser utilizado o valor do *nome do* elemento especificado no *FrontendIPConfiguration* no elemento de *FrontendIP* a HTTPListener para consultar a FrontendIPConfiguration.

 **Exemplo XML de configuração**

 

        <?xml version="1.0" encoding="utf-8"?>
        <ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
            <FrontendIPConfigurations>
                <FrontendIPConfiguration>
                    <Name>fip1</Name> 
                    <Type>Private</Type> 
                    <StaticIPAddress>10.0.0.10</StaticIPAddress> 
                </FrontendIPConfiguration>
            </FrontendIPConfigurations>
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
                    <FrontendIP>fip1</FrontendIP>
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
    


## <a name="set-the-gateway-configuration"></a>Definir a configuração de gateway

Em seguida, irá configurar o gateway de aplicação. Pode utilizar o `Set-AzureApplicationGatewayConfig` cmdlet com um objeto de configuração, ou com um ficheiro de configuração de XML. 

    PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile D:\config.xml

    VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig 
    VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## <a name="start-the-gateway"></a>Iniciar o gateway

Depois de ter sido configurado o gateway, utilize o `Start-AzureApplicationGateway` cmdlet para iniciar o gateway. Faturação para um gateway aplicação começa depois do gateway foi iniciado com êxito. 


> [AZURE.NOTE] O `Start-AzureApplicationGateway` cmdlet poderá demorar até 15-20 minutos a concluir. 
   
    PS C:\> Start-AzureApplicationGateway AppGwTest 

    VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway 
    VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
    Name       HTTP Status Code     Operation ID                             Error 
    ----       ----------------     ------------                             ----
    Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## <a name="verify-the-gateway-status"></a>Verificar o estado do gateway

Utilizar o `Get-AzureApplicationGateway` cmdlet para verificar o estado do gateway. Se *Iniciar AzureApplicationGateway* foi concluída com êxito no passo anterior, o estado deve estar a *Executar*e Vip e NomeDNS devem ter entradas válidas. Este exemplo mostra o cmdlet na primeira linha, seguido de saída. Neste exemplo, o gateway está a ser executado e está pronto para tirar o tráfego. 

> [AZURE.NOTE] O gateway aplicação está configurado para aceitar o tráfego do configurado ILB ponto final do 10.0.0.10 neste exemplo.

    PS C:\> Get-AzureApplicationGateway AppGwTest 

    VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway 
    VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
    Name          : AppGwTest
    Description   : 
    VnetName      : testvnet1
    Subnets       : {Subnet-1}
    InstanceCount : 2
    GatewaySize   : Medium
    State         : Running
    VirtualIPs    : {10.0.0.10}
    DnsName       : appgw-b2a11563-2b3a-4172-a4aa-226ee4c23eed.cloudapp.net

## <a name="next-steps"></a>Próximos passos


Se pretender obter mais informações sobre carregar balanceamento de opções geral, consulte:

- [Balanceador de carga Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Gestor de tráfego Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)
