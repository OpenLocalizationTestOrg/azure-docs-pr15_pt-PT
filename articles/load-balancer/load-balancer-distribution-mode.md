<properties
   pageTitle="Configurar o modo de distribuição do Balanceador de carga | Microsoft Azure"
   description="Como configurar o modo de distribuição de Balanceador de carga Azure para suportar afinidade IP de origem"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />


# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configurar o modo de distribuição para Balanceador de carga

## <a name="hash-based-distribution-mode"></a>Modo de distribuição com base em hash

O algoritmo de distribuição predefinido é uma 5-cadeia de identificação (IP, porta de origem, de origem tipo de protocolo IP de destino, porta de destino,) hash para mapear o tráfego para servidores disponíveis. Fornece nódoas gordurosas apenas a partir de uma sessão de transporte. Pacotes na mesma sessão serão direcionados para a mesma instância de IP (DIP) do Centro de dados atrás do ponto final de balanceamento de carga. Quando o cliente inicia uma nova sessão a partir do mesmo IP de origem, da porta de origem muda e faz com que o tráfego Ir para um ponto final de DIP diferente.

![Balanceador de carga hash com base](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1-5-cadeia de identificação distribuição

## <a name="source-ip-affinity-mode"></a>Modo de afinidade do IP de origem

Temos de outro modo de distribuição chamado afinidade de IP de origem (também conhecido como afinidade sessão ou afinidade IP do cliente). Azure Balanceador de carga pode ser configurado para utilizar um 2-cadeia de identificação (IP de origem, IP de destino) ou 3 cadeia de identificação (protocolo IP de origem, IP de destino,) para mapear o tráfego para os servidores disponíveis. Ao utilizar afinidade do IP de origem, ligações iniciadas a partir do mesmo computador cliente vai para o mesmo ponto final de DIP.

O diagrama seguinte ilustra uma configuração de cadeia de identificação de 2. Repare que a forma como a cadeia de identificação 2 é executado através do Balanceador de carga a máquina virtual 1 (VM1) que é, em seguida, cópia de segurança ao VM2 e VM3.

![afinidade sessão](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figura 2-2-cadeia de identificação distribuição

Afinidade IP de origem resolve uma incompatibilidade entre o Azure Balanceador de carga e o Gateway de ambiente de trabalho remoto (RD). Agora pode criar um farm de gateway RD num serviço de nuvem único.

Outra hipótese de utilizar é carregar multimédia onde acontece o carregamento de dados através de UDP mas o plano de controlo é obtido através do TCP:

- Um cliente inicia uma sessão TCP para o endereço de público de balanceamento de carga, obtém direcionado para uma DIP específico, este canal fica ativo para monitorizar o estado de funcionamento de ligação
- Uma nova sessão UDP do mesmo computador cliente é iniciado ao mesmo balanceamento de carga público ponto final, a expetativa aqui é que esta ligação também é direccionada para o mesmo ponto final de DIP tal como a ligação TCP anterior para que carregar multimédia pode ser executada em alto débito mantendo também um canal de controlo através do TCP.

>[AZURE.NOTE] Quando um conjunto de balanceamento de carga é alterado (remover ou adicionar uma máquina virtual), a distribuição dos pedidos de cliente é recomputed. Não é possível dependem novas ligações a partir dos clientes existentes acabarem no mesmo servidor. Para além disso, utilizando o IP de origem modo de distribuição afinidade pode causar uma distribuição desigual de tráfego. Os clientes com atrás proxies podem ser visualizados como uma aplicação de cliente exclusivo.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configurar definições de afinidade IP de origem para Balanceador de carga

Para máquinas virtuais, pode utilizar o PowerShell para alterar as definições de tempo limite:

Adicionar um ponto final Azure para uma Máquina Virtual e configurar o modo de distribuição do Balanceador de carregamento

    Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM

Pode ser definida LoadBalancerDistribution sourceIP 2-cadeia de identificação (IP de origem, IP de destino) balanceamento de carga, sourceIPProtocol para balanceamento de carga em 3-cadeia de identificação (protocolo IP de origem, IP de destino,) ou nenhum se pretender que o comportamento predefinido cadeia de identificação de 5 de balanceamento de carga.

Utilize o seguinte para obter uma configuração de modo distribuição de Balanceador de carga de ponto final:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Se não existir no elemento de LoadBalancerDistribution Balanceador de carga Azure utiliza o algoritmo de cadeia de identificação 5 predefinido.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Configurar o modo de distribuição num conjunto de ponto final de balanceamento de carga

Se os pontos finais fizerem parte de um conjunto de ponto final de balanceamento de carga, tem de estar definido o modo de distribuição no conjunto de ponto final de balanceamento de carga:

    Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuração do serviço na nuvem para alterar o modo de distribuição

Pode tirar partido o SDK do Azure para .NET 2,5 (a ser lançadas em Novembro) para atualizar o seu serviço de nuvem. Definições de ponto final para serviços em nuvem forem efetuadas na .csdef. Para atualizar o modo de distribuição do Balanceador de carga para uma implementação dos serviços em nuvem, é necessária uma atualização de implementação.
Eis um exemplo de alterações de .csdef de definições de ponto final:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
      </Endpoints>
    </WorkerRole>
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

## <a name="api-example"></a>Exemplo de API

Pode configurar a distribuição do Balanceador de carga utilizar a API de gestão de serviço. Certifique-se adicionar o `x-ms-version` cabeçalho estiver definido para a versão `2014-09-01` ou superior.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Actualizar a configuração do conjunto de balanceamento de carga especificado numa implementação

#### <a name="request-example"></a>Pedido de exemplo

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet    x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

O valor do LoadBalancerDistribution pode ser sourceIP para afinidade cadeia de identificação 2, sourceIPProtocol para a cadeia de identificação 3 afinidade ou nenhum (para nenhuma afinidade. Isto é, 5-cadeia de identificação)

#### <a name="response"></a>Resposta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Próximos passos

[Descrição geral do Balanceador de carga interno](load-balancer-internal-overview.md)

[Começar a configurar uma opostas Balanceador de carga da Internet](load-balancer-get-started-internet-arm-ps.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
