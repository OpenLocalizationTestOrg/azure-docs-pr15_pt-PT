<properties
   pageTitle="Criar um balanceador de carga interno através do PowerShell no modelo de implementação clássica | Microsoft Azure"
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

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Começar a criar um balanceador de carga interna (clássico) através do PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-classic-selectors-include.md](../../includes/load-balancer-get-started-ilb-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](load-balancer-get-started-ilb-arm-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]


[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Criar um balanceador de carga interno definido para máquinas virtuais

Para criar um conjunto de Balanceador de carga interno e os servidores que irão enviar o tráfego para o mesmo, tem de fazer o seguinte procedimento:

1. Crie uma instância do interno balanceamento de carga em que será o ponto final de tráfego de entrada para ser balanceada pelos servidores de um conjunto de balanceamento de carga de carga.

1. Adicione pontos finais correspondente para as máquinas virtuais que irão receber o tráfego de entrada.

1. Configure os servidores que irão enviar o tráfego para ser com a enviar o tráfego para o endereço IP (VIP) virtual da instância interno balanceamento de carga de balanceamento de carga.


### <a name="step-1-create-an-internal-load-balancing-instance"></a>Passo 1: Criar uma instância de balanceamento de carga interno

Para um serviço de nuvem existente ou num serviço na nuvem implementado em rede virtual regional, pode criar uma instância de balanceamento de carga interno com os seguintes comandos do Windows PowerShell:

    $svc="<Cloud Service Name>"
    $ilb="<Name of your ILB instance>"
    $subnet="<Name of the subnet within your virtual network>"
    $IP="<The IPv4 address to use on the subnet-optional>"

    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Note que esta utilização do cmdlet [Adicionar AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell usa o conjunto de parâmetro DefaultProbe. Para mais informações sobre os conjuntos de parâmetro adicional, consulte o artigo [Adicionar AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Passo 2: Adicionar os pontos finais para a instância de balanceamento de carga interno

Eis um exemplo:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $ilb="ilbset"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Passo 3: Configurar os servidores para enviar o respetivo tráfego para o novo balanceamento de carga interno ponto final de

Tem de configurar os servidores cujo tráfego vai ser distribuído para utilizar os novos endereços IP da instância interno balanceamento de carga (o VIP). Este é o endereço no qual está a escutar a instância de balanceamento de carga interno. Na maioria dos casos, tem de acabou de adicionar ou modificar um registo DNS para o VIP da instância balanceamento de carga interno.

Se tiver especificado o endereço IP durante a criação da instância balanceamento de carga interno, já tem o VIP. Caso contrário, pode ver VIP entre os seguintes comandos:

    $svc="<Cloud Service Name>"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Para utilizar estes comandos, preencher os valores e remover a < e >. Eis um exemplo:

    $svc="mytestcloud"
    Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


A partir da apresentação do comando Get-AzureInternalLoadBalancer, tenha em atenção o endereço IP e faça as alterações necessárias ao seu servidores ou registos DNS para garantir que o tráfego é enviado para o VIP.

>[AZURE.NOTE]A plataforma do Microsoft Azure utiliza um endereço de IPv4 estático, publicamente encaminhável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Este endereço IP não deve ser bloqueado por qualquer firewalls, uma vez que pode fazer com que um comportamento inesperado.
>Relativamente às Azure interno balanceamento de carga, este endereço IP é utilizado por sondas a partir do Balanceador de carga de monitorização para determinar o estado de funcionamento para máquinas virtuais num conjunto de balanceamento de carga. Se um grupo de segurança de rede é utilizado para restringir o tráfego para o Azure máquinas virtuais num conjunto de internamente balanceamento de carga ou é aplicado a uma rede sub-rede Virtual, certifique-se de que é adicionada uma regra de segurança de rede para permitir o tráfego de 168.63.129.16.


## <a name="example-of-internal-load-balancing"></a>Exemplo de balanceamento de carga interna

Para orientá-lo durante o processo de fim para fim da criação de um conjunto de balanceamento de carga para duas configurações de exemplo, consulte as secções seguintes.

### <a name="an-internet-facing-multi-tier-application"></a>Um opostas aplicação com várias camadas da Internet

Pretender fornecer um serviço de base de dados de balanceamento de carga para um conjunto de servidores da web acesso à Internet. Os dois conjuntos de servidores estão alojados num serviço de nuvem Azure único. Tem de ser distribuído tráfego de servidor Web à porta TCP 1433 entre duas máquinas virtuais na camada da base de dados. Figura 1 mostra a configuração.

![Conjunto de balanceamento de carga interno para a camada de base de dados](./media/load-balancer-internal-getstarted/IC736321.png)


A configuração consiste em dos seguintes procedimentos:

- O serviço de nuvem existente que aloja as máquinas virtuais é denominado mytestcloud.

- Os dois servidores de base de dados existente são com nome DS1, DB2.

- Servidores Web na camada web ligar aos servidores da base de dados na camada da base de dados utilizando o endereço IP privado. Outra opção é utilizar o seu próprio DNS para a rede virtual e registar um registo a para o conjunto de Balanceador de carga interno manualmente.

Os seguintes comandos configurar uma nova instância de balanceamento de carga interno denominada **ILBset** e adicione os pontos finais a máquinas virtuais correspondente para os servidores de duas bases de dados:

    $svc="mytestcloud"
    $ilb="ilbset"
    Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
    $prot="tcp"
    $locport=1433
    $pubport=1433
    $epname="TCP-1433-1433"
    $lbsetname="lbset"
    $vmname="DB1"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

    $epname="TCP-1433-1433-2"
    $vmname="DB2"
    Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


## <a name="remove-an-internal-load-balancing-configuration"></a>Remover uma configuração de balanceamento de carga interno

Para remover uma máquina virtual como um ponto final de uma instância do Balanceador de carga interno, utilize os comandos seguintes:

    $svc="<Cloud service name>"
    $vmname="<Name of the VM>"
    $epname="<Name of the endpoint>"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para utilizar estes comandos, preencher os valores, remover a < e >.

Eis um exemplo:

    $svc="mytestcloud"
    $vmname="DB1"
    $epname="TCP-1433-1433"
    Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para remover uma instância do Balanceador de carga interno de um serviço na nuvem, utilize os comandos seguintes:

    $svc="<Cloud service name>"
    Remove-AzureInternalLoadBalancer -ServiceName $svc

Para utilizar estes comandos, introduza o valor e remover a < e >.

Eis um exemplo:

    $svc="mytestcloud"
    Remove-AzureInternalLoadBalancer -ServiceName $svc



## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Informações adicionais sobre os cmdlets do Balanceador de carga interno


Para obter informações adicionais sobre os cmdlets do balanceamento de carga interno, execute os seguintes comandos numa linha de comandos do Windows PowerShell:

- Get-help novo AzureInternalLoadBalancerConfig-completo

- Get-help AzureInternalLoadBalancer adicionar-completo

- Obter ajuda Get-AzureInternalLoadbalancer-completo

- Get-help remover AzureInternalLoadBalancer-completo

## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga utilizando afinidade IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)