<properties
   pageTitle="Configurar o tempo limite de idle TCP do Balanceador de carga | Microsoft Azure"
   description="Configurar o tempo limite de idle TCP do Balanceador de carga"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Configurar definições de tempo limite inactivo TCP para Balanceador de carga do Azure

Na sua configuração predefinida, Balanceador de carga Azure tem uma definição de tempo limite inactivo de 4 minutos. Se um período de inatividade for maior que o valor de tempo de espera, há garantias que a sessão TCP ou HTTP são mantidas entre o cliente e o seu serviço de nuvem.

Quando a ligação é fechada, a sua aplicação cliente poderá receber a seguinte mensagem de erro: "a ligação subjacente foi fechada: uma ligação que esperava ser mantido vivo foi fechada pelo servidor."

Uma prática comum é utilizar uma TCP persistentes. Esta prática mantém a ligação ativa durante um período maior. Para obter mais informações, consulte estes [exemplos de .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Com persistentes ativado, os pacotes são enviados durante períodos de inatividade na ligação. Estes pacotes persistentes Certifique-se de que o valor de tempo limite inactivo nunca é atingido e a ligação é mantida durante um longo período.

Esta definição funciona para ligações de entrada apenas. Para evitar perder a ligação, tem de configurar o TCP persistentes com um intervalo inferior a definição de tempo limite idle ou aumentar o valor de tempo limite inactivo. Para suportar destes cenários, adicionámos suporte para um limite de tempo inactivo configurável. Agora pode defini-lo com uma duração de 4 a 30 minutos.

TCP persistentes funciona bem com cenários onde vida de baterias não é uma restrição. Não é recomendado para aplicações móveis. Utilizar um TCP persistentes numa aplicação móvel pode descarregar baterias o dispositivo mais rápido.

![Tempo limite do TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

As secções seguintes descrevem como alterar as definições de tempo limite inactivo em máquinas virtuais e dos serviços em nuvem.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configurar o tempo limite do TCP para o seu endereço IP público de nível da instância a 15 minutos

    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes`é opcional. Se não estiver definida, o tempo limite predefinido é 4 minutos. O intervalo de tempo limite aceitável é 4 a 30 minutos.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Definir o tempo de limite idle ao criar um ponto final Azure numa máquina virtual

Para alterar a definição de tempo limite para um ponto final, utilize o seguinte:

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Para obter a sua configuração de tempo limite idle, utilize o seguinte comando:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Definir o tempo limite do TCP num conjunto de balanceamento de carga ponto final

Se os pontos finais fizerem parte de um conjunto de ponto final de balanceamento de carga, tem de estar definido o tempo limite do TCP no conjunto de balanceamento de carga ponto final. Por exemplo:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## <a name="change-timeout-settings-for-cloud-services"></a>Alterar as definições de tempo limite de serviços em nuvem

Pode utilizar o SDK do Azure para atualizar o seu serviço de nuvem. Criar definições de ponto final para serviços em nuvem no ficheiro, .csdef. Atualizar o tempo limite TCP para a implementação de um serviço na nuvem requer uma atualização de implementação. Uma exceção é se o tempo limite do TCP é especificado apenas para um endereço IP público. Definições de IP público estão no ficheiro .cscfg e pode atualizá-los através da atualização de implementação e atualização.

As alterações de .csdef para as definições de ponto final são:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

As alterações de .cscfg para a definição do limite de tempo no IPs público são:

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
        <InstanceAddress roleName="VMRolePersisted">
        <PublicIPs>
          <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
        </PublicIPs>
        </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## <a name="rest-api-example"></a>Exemplo de REST API

Pode configurar o tempo de limite do TCP idle ao utilizar a API de gestão de serviço. Certifique-se de que o `x-ms-version` cabeçalho estiver definido para a versão `2014-06-01` ou posterior. Actualize a configuração dos especificado balanceamento de carga entradas pontos finais em todas as máquinas virtuais numa implementação.

### <a name="request"></a>Pedido

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Resposta

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
        <LocalPort>local-port-number</LocalPort>
        <Port>external-port-number</Port>
        <LoadBalancerProbe>
          <Path>path-of-probe</Path>
          <Port>port-assigned-to-probe</Port>
          <Protocol>probe-protocol</Protocol>
          <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
          <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
        </LoadBalancerProbe>
        <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
        <Protocol>endpoint-protocol</Protocol>
        <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
        <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
        <EndpointACL>
          <Rules>
            <Rule>
              <Order>priority-of-the-rule</Order>
              <Action>permit-rule</Action>
              <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
              <Description>description-of-the-rule</Description>
            </Rule>
          </Rules>
        </EndpointACL>
      </InputEndpoint>
    </LoadBalancedEndpointList>

## <a name="next-steps"></a>Próximos passos

[Descrição geral do Balanceador de carga interno](load-balancer-internal-overview.md)

[Começar a configurar uma Balanceador de carga de acesso à Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)
