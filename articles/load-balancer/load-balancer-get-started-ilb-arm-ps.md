<properties
   pageTitle="Criar um balanceador de carga interno através do PowerShell no Gestor de recursos | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno através do PowerShell no Gestor de recursos"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/24/2016"
   ms.author="sewhee" />

# <a name="create-an-internal-load-balancer-using-powershell"></a>Criar um balanceador de carga interno através do PowerShell

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]
<BR>
[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]


Os passos seguintes explicam como criar um balanceador de carga interno utilizar o Gestor de recursos do Azure com PowerShell. Com o Gestor de recursos do Azure, os itens para criar um balanceador de carga interno são configurados individualmente e, em seguida, combinados para criar um balanceador de carga.

Terá de criar e configurar os objetos para implementar um balanceador de carga seguintes:

- Frente configuração de IP end - irá configurar o endereço IP privado para receber tráfego de rede
- Conjunto de endereços de back-end - irá configurar as interfaces de rede que irão receber o tráfego de balanceamento de carga provenientes do conjunto de IP Front-end
- Regras de-origem e de configuração de porta local para o Balanceador de carga de balanceamento de carga.
- Sondas - configura a sonda de estado de funcionamento para as instâncias de Máquina Virtual.
- NAT regras de entrada - configura as regras de portas para aceder diretamente a um das instâncias de Máquina Virtual.

Pode obter mais informações sobre como carregar componentes do Balanceador com o Gestor de recursos Azure no [Azure o Gestor de recursos de suporte para Balanceador de carga](load-balancer-arm.md).

Os passos seguintes explicam como configurar um balanceador de carga entre as duas máquinas virtuais.


## <a name="setup-powershell-to-use-resource-manager"></a>Configurar o PowerShell para utilizar o Gestor de recursos

Certifique-se de que tem a versão de produção mais recente do módulo Azure para PowerShell e ter o programa de configuração do PowerShell corretamente para aceder à sua subscrição do Azure.

### <a name="step-1"></a>Passo 1

        Login-AzureRmAccount

### <a name="step-2"></a>Passo 2

Verificar as subscrições para a conta

        Get-AzureRmSubscription

Vai ser-lhe para autenticar com as suas credenciais.<BR>

### <a name="step-3"></a>Passo 3

Escolha as suas subscrições Azure para utilizar. <BR>


        Select-AzureRmSubscription -Subscriptionid "GUID of subscription"

### <a name="create-resource-group-for-load-balancer"></a>Criar o grupo de recursos para Balanceador de carga

### <a name="step-4"></a>Passo 4

Criar um novo grupo de recursos (ignorar este passo se utilizar um grupo de recursos existente)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

Gestor de recursos do Azure requer que todos os grupos de recursos, especifique uma localização. É utilizada como a localização predefinida para recursos nesse grupo de recursos. Certifique-se de todos os comandos para criar um balanceador de carga irão utilizar o mesmo grupo de recursos.

No exemplo acima criámos de um grupo de recursos denominado "NRP RG" e a localização "Oeste-nos".

## <a name="create-virtual-network-and-a-private-ip-address-for-front-end-ip-pool"></a>Criar um endereço IP privado para conjunto de dados front-end IP e de rede Virtual


### <a name="step-1"></a>Passo 1

Cria uma sub-rede para a rede virtual e atribui a variável $backendSubnet

    $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24

Crie uma rede virtual:

    $vnet= New-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location "West US" -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

Cria a rede virtual e adiciona a sub-rede lb sub-rede ser à rede virtual NRPVNet e atribui a variável de $vnet



## <a name="create-front-end-ip-pool-and-backend-address-pool"></a>Criar o conjunto de dados Front end IP e conjunto de endereços de back-end

Configurar um conjunto IP Front-end para recebidas carga balanceador tráfego e back-end conjunto endereços de rede receber a carga de balanceamento de tráfego.

### <a name="step-1"></a>Passo 1

Crie um conjunto IP de front-end utilizando o endereço IP privado 10.0.2.5 para o 10.0.2.0/24 sub-rede qual será o ponto final tráfego de rede recebidas.

    $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PrivateIpAddress 10.0.2.5 -SubnetId $vnet.subnets[0].Id

### <a name="step-2"></a>passo 2

Configure um conjunto de endereços de back-end utilizado para receber o tráfego de entrada front-end do conjunto de IP:

    $beaddresspool= New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "LB-backend"


## <a name="create-lb-rules-nat-rules-probe-and-load-balancer"></a>Criar regras LB, regras NAT, sonda e Balanceador de carga

Depois de criar o conjunto de IP Front-end e o conjunto de endereços de back-end, terá de criar regras que irão pertencer ao recurso Balanceador de carga:

### <a name="step-1"></a>Passo 1

    $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP1" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

    $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name "RDP2" -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

    $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name "HealthProbe" -RequestPath "HealthProbe.aspx" -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

     $lbrule = New-AzureRmLoadBalancerRuleConfig -Name "HTTP" -FrontendIpConfiguration $frontendIP -BackendAddressPool $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80


O exemplo acima está a criar os seguintes itens:

- Regra de NAT que todo o tráfego de entrada à porta 3441 serão direcionadas para porta 3389.
- uma segunda NAT regra que todo o tráfego de entrada à porta 3442 serão direcionadas para porta 3389.
- uma regra de Balanceador de carga que irá carregar equilibrar tráfego de todos os entrada na porta público 80 à porta local 80 no conjunto de endereços back-end.
- uma regra de sonda qual vai verificar o estado de funcionamento da caminho "HealthProbe.aspx"



### <a name="step-2"></a>Passo 2

Crie o Balanceador de carga adicionar todos os objetos (regras NAT, as regras do Balanceador de carga, sonda configurações) em conjunto:

    $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName "NRP-RG" -Name "NRP-LB" -Location "West US" -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe


## <a name="create-network-interfaces"></a>Criar interfaces de rede

Depois de criar o Balanceador de carga interno, necessita de definir as interfaces de rede irão receber o tráfego de rede de balanceamento de carga, regras NAT e sonda recebidas. Neste caso a interface de rede está configurada individualmente e pode ser atribuída a uma máquina virtual mais tarde.


### <a name="step-1"></a>Passo 1


Obter a rede virtual do recurso e sub-rede para criar interfaces de rede:

    $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG

    $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet


Este passo cria uma interface de rede que pertencem ao agrupamento de back-end do Balanceador de carga e associar a primeira regra NAT para RDP para esta interface de rede:

    $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic1-be -Location "West US" -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

### <a name="step-2"></a>Passo 2

Criar uma segunda interface de rede denominada LB Nic2 ser:

Este passo cria uma segunda interface de rede, atribuir ao mesmo conjunto de back-end do Balanceador de carga e associação a segunda regra NAT criada para RDP:

     $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName "NRP-RG" -Name lb-nic2-be -Location "West US" -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

O resultado final irá aparecer o seguinte procedimento:

    $backendnic1

Saída esperada:

    Name                 : lb-nic1-be
    ResourceGroupName    : NRP-RG
    Location             : westus
    Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
    Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
    ProvisioningState    : Succeeded
    Tags                 :
    VirtualMachine       : null
    IpConfigurations     : [
                         {
                           "PrivateIpAddress": "10.0.2.6",
                           "PrivateIpAllocationMethod": "Static",
                           "Subnet": {
                             "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                           },
                           "PublicIpAddress": {
                             "Id": null
                           },
                           "LoadBalancerBackendAddressPools": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/backendAddressPools/LB-backend"
                             }
                           ],
                           "LoadBalancerInboundNatRules": [
                             {
                               "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/loadBalancers/NRPlb/inboundNatRules/RDP1"
                             }
                           ],
                           "ProvisioningState": "Succeeded",
                           "Name": "ipconfig1",
                           "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                           "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1"
                         }
                       ]
    DnsSettings          : {
                         "DnsServers": [],
                         "AppliedDnsServers": []
                       }
    AppliedDnsSettings   :
    NetworkSecurityGroup : null
    Primary              : False



### <a name="step-3"></a>Passo 3

Utilize o comando Adicionar AzureRmVMNetworkInterface para atribuir a NIC para uma máquina virtual.

Pode encontrar as instruções passo a passo para criar uma máquina virtual e atribuir uma NIC seguinte documentação: [criar um VM Azure através do PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

Se já tiver uma máquina de virtual criada, pode adicionar a interface de rede com os seguintes passos:

#### <a name="step-1"></a>Passo 1

Carrega o recurso de Balanceador de carga para uma variável (se ainda não o tiver feito que ainda). Variável utilizada chama $lb e utilize os mesmos nomes a partir do recurso de Balanceador de carga criado anteriormente.

    $lb= Get-AzureRmLoadBalancer –name NRP-LB -resourcegroupname NRP-RG

#### <a name="step-2"></a>Passo 2

Carrega a configuração de back-end a uma variável.

    $backend= Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

#### <a name="step-3"></a>Passo 3

Carrega a interface de rede já criado para uma variável. o nome da variável utilizado é NIC $. O nome da interface de rede utilizado é a mesma do exemplo acima.

    $nic=Get-AzureRmNetworkInterface –name lb-nic1-be -resourcegroupname NRP-RG

#### <a name="step-4"></a>Passo 4

Altere a configuração de back-end na interface de rede.

    $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

#### <a name="step-5"></a>Passo 5

Guarde o objeto de interface de rede.

    Set-AzureRmNetworkInterface -NetworkInterface $nic

Depois de uma interface de rede é adicionada ao agrupamento de back-end do Balanceador de carga, inicia o tráfego de rede com base nas regras para esse recurso Balanceador de carga de balanceamento de carga a receber.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente


### <a name="step-1"></a>Passo 1

Utilizar o Balanceador de carga do exemplo acima, atribuir objeto do Balanceador de carga a variável $slb utilizando Get-AzureRmLoadBalancer

    $slb=get-azureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

### <a name="step-2"></a>Passo 2

No exemplo seguinte, irá adicionar uma nova regra de entrada NAT com as portas 81 no front-end e 8181 para o conjunto de back-end Balanceador de carga de existente

    $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol Tcp


### <a name="step-3"></a>Passo 3

Guardar a configuração da nova utilizando conjunto AzureLoadBalancer

    $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Utilize o comando AzureRmLoadBalancer remover para eliminar um balanceador de carga previamente criado com o nome "NRP-LB" num grupo de recursos denominado "NRP RG"

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Pode utilizar o opcional mudar - forçar para evitar a pedir a eliminação.



## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)