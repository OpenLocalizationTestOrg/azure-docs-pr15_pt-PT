<properties
   pageTitle="Criar um balanceador de carga interna para serviços em nuvem no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno através do PowerShell no modelo de implementação clássico"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internal-load-balancer-classic-for-cloud-services"></a>Começar a criar um balanceador de carga interna (clássico) para serviços em nuvem

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

<BR>

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](load-balancer-get-started-ilb-arm-ps.md).


## <a name="configure-internal-load-balancer-for-cloud-services"></a>Configurar Balanceador de carga interna para serviços em nuvem

Balanceador de carga interno é suportada para máquinas virtuais e serviços em nuvem. Um ponto final Balanceador de carga interno criado num serviço na nuvem que se encontra fora de uma rede virtual regional estarão disponíveis apenas dentro do serviço de nuvem.

A configuração do Balanceador de carga interno tem de ser definido durante a criação da primeira implementação num serviço na nuvem, conforme mostrado no exemplo abaixo.

>[AZURE.IMPORTANT] É um pré-requisito para executar os passos abaixo ter uma rede virtual já criou para a implementação na nuvem. Terá do nome de rede virtual e sub-rede para criar balanceamento de carga interno.

### <a name="step-1"></a>Passo 1

Abra o ficheiro de configuração de serviço (.cscfg) para a sua implementação da nuvem no Visual Studio e adicione a secção seguinte para criar o balanceamento de carga interno na última "`</Role>`" item para a configuração de rede.




    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="name of the load balancer">
          <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>


Vamos adicionar os valores para o ficheiro de configuração de rede Mostrar o mesmo aspeto. No exemplo, suponha que criou uma sub-rede chamada "test_vnet" com um 10.0.0.0/24 sub-rede denominado test_subnet e um endereço IP estático 10.0.0.4. O Balanceador de carga nome será testLB.

    <NetworkConfiguration>
      <LoadBalancers>
        <LoadBalancer name="testLB">
          <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
        </LoadBalancer>
      </LoadBalancers>
    </NetworkConfiguration>

Para mais informações acerca do esquema do Balanceador de carga, consulte o artigo [adicionar balanceador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx).

### <a name="step-2"></a>Passo 2


Altere o ficheiro de definição (.csdef) do serviço para adicionar pontos finais de balanceamento de carga interno. O momento em que é criada uma instância de função, o ficheiro de definição de serviço irá adicionar as instâncias de função balanceamento de carga interno.


    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
      </Endpoints>
    </WorkerRole>

Seguir os mesmos valores do exemplo acima, vamos adicionar os valores para o ficheiro de definição de serviço.

    <WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
      </Endpoints>
    </WorkerRole>

O tráfego de rede serão utilizando o Balanceador de carga testLB com porta 80 para pedidos recebidos, enviar para instâncias de função trabalhador também na porta 80 de balanceamento de carga.


## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga utilizando afinidade IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)