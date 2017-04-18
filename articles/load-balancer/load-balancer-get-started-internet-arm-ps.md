<properties
   pageTitle="Criar um balanceador de carga de acesso à Internet no Gestor de recursos utilizando o PowerShell | Microsoft Azure"
   description="Saiba como criar um balanceador de carga de acesso à Internet no Gestor de recursos utilizando o PowerShell"
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

# <a name="get-started"></a>Criar um balanceador de carga de acesso à Internet no Gestor de recursos utilizando o PowerShell

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Pode também [saber como criar um balanceador de carga de acesso à Internet, utilizando o modelo de implementação clássica](load-balancer-get-started-internet-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-by-using-azure-powershell"></a>Implementar a solução utilizando o PowerShell do Azure

Os procedimentos seguintes explicam como criar um balanceador de carga de acesso à Internet, utilizando o Gestor de recursos do Azure com PowerShell. Com o Gestor de recursos do Azure, cada recurso é criado e configurado individualmente e, em seguida, colocar em conjunto para criar um balanceador de carga.

Tem de criar e configurar os seguintes objectos para implementar um balanceador de carga:

- Configuração de IP Front-end: contém os endereços de IP (PIP) públicos para o tráfego de rede recebido.
- Conjunto de endereços de back-end: contém interfaces de rede (NIC) para as máquinas virtuais para receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga: contém regras mapeiam uma porta pública no balanceador de carga a uma porta no conjunto de endereços de back-end.
- NAT regras de entrada: contém regras mapeiam uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end.
- Sondas: contém sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquina virtual no conjunto de endereços de back-end.

Para mais informações, consulte o artigo [De suporte para Balanceador de carga de Gestor de recursos do Azure](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para utilizar o Gestor de recursos

Certifique-se de que tem a versão mais recente de produção do módulo Azure o Gestor de recursos para PowerShell:

1. Inicie sessão no Azure.

        Login-AzureRmAccount

    Introduza as suas credenciais quando lhe for pedido.

2. Verifique as subscrições para a conta.

        Get-AzureRmSubscription

3. Escolha as suas subscrições Azure para utilizar.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Crie um grupo de recursos. (Ignorar este passo se estiver a utilizar um grupo de recursos existente.)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o conjunto de IP Front-end

1. Crie uma sub-rede e uma rede virtual.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        New-AzureRmvirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Crie um Azure público recurso de endereço IP, com o nome **PublicIP**, para ser utilizado por um conjunto IP Front-end com o nome DNS **loadbalancernrp.westus.cloudapp.azure.com**. O comando seguinte utiliza o tipo de alocação estática.

        $publicIP = New-AzureRmPublicIpAddress -Name PublicIp -ResourceGroupName NRP-RG -Location 'West US' –AllocationMethod Static -DomainNameLabel loadbalancernrp

    >[AZURE.IMPORTANT]O Balanceador de carga utiliza a etiqueta de domínio do endereço IP público como um prefixo para o FQDN. Este é diferente do modelo clássico de implementação, que utiliza o serviço de nuvem como o FQDN do Balanceador de carga.
    >Neste exemplo, o FQDN é **loadbalancernrp.westus.cloudapp.azure.com**.

## <a name="create-a-front-end-ip-pool-and-a-back-end-address-pool"></a>Criar um conjunto IP Front-end e um conjunto de endereços de back-end

1. Crie um conjunto IP Front-end denominado **LB Frontend** que utiliza o recurso **PublicIp** .

        $frontendIP = New-AzureRmLoadBalancerFrontendIpConfig -Name LB-Frontend -PublicIpAddress $publicIP

2. Crie um conjunto de back-end endereços com nome **LB-back-end**.

        $beaddresspool = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name LB-backend

## <a name="create-nat-rules-a-load-balancer-rule-a-probe-and-a-load-balancer"></a>Criar regras NAT, uma regra de Balanceador de carga, uma sonda e um balanceador de carga

Este exemplo cria os seguintes itens:

- Uma regra NAT para traduzir todo o tráfego de entrada na porta 3441 à porta 3389
- Uma regra NAT para traduzir todo o tráfego de entrada na porta 3442 à porta 3389
- Uma regra de sonda para verificar o estado de funcionamento de uma página com o nome **HealthProbe.aspx**
- Uma regra de Balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 à porta 80 nos endereços no conjunto de back-end
- Um balanceador de carga que utiliza todos os objetos

Utilize estes passos:

1. Crie as regras NAT.

        $inboundNATRule1= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP1 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3441 -BackendPort 3389

        $inboundNATRule2= New-AzureRmLoadBalancerInboundNatRuleConfig -Name RDP2 -FrontendIpConfiguration $frontendIP -Protocol TCP -FrontendPort 3442 -BackendPort 3389

2. Crie uma sonda de estado de funcionamento. Existem duas formas para configurar uma sonda:

    Sonda HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    Sonda TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name HealthProbe -Protocol Tcp -Port 80 -IntervalInSeconds 15 -ProbeCount 2

3. Crie uma regra de Balanceador de carga.

        $lbrule = New-AzureRmLoadBalancerRuleConfig -Name HTTP -FrontendIpConfiguration $frontendIP -BackendAddressPool  $beAddressPool -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 80

4. Crie o Balanceador de carga, utilizando os objetos criados anteriormente.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name NRP-LB -Location 'West US' -FrontendIpConfiguration $frontendIP -InboundNatRule $inboundNATRule1,$inboundNatRule2 -LoadBalancingRule $lbrule -BackendAddressPool $beAddressPool -Probe $healthProbe

## <a name="create-nics"></a>Criar NIC

Crie interfaces de rede (ou modificar os existentes) e, em seguida, associá-las para regras, as regras do Balanceador de carga e sondas NAT:

1. Obter a rede virtual e de sub-rede de uma rede virtual, onde o NIC necessita de ser criada.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Criar uma NIC denominada **lb nic1 ser**e associá-lo a primeira regra NAT e o conjunto de endereços de back-end primeiro (e apenas).

        $backendnic1= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic1-be -Location 'West US' -PrivateIpAddress 10.0.2.6 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[0]

3. Criar uma NIC denominada **lb nic2 ser**e associá-lo a segunda regra NAT e o conjunto de endereços de back-end primeiro (e apenas).

        $backendnic2= New-AzureRmNetworkInterface -ResourceGroupName NRP-RG -Name lb-nic2-be -Location 'West US' -PrivateIpAddress 10.0.2.7 -Subnet $backendSubnet -LoadBalancerBackendAddressPool $nrplb.BackendAddressPools[0] -LoadBalancerInboundNatRule $nrplb.InboundNatRules[1]

4. Verifique a NIC.

        $backendnic1

    Saída esperada:

        Name                 : lb-nic1-be
        ResourceGroupName    : NRP-RG
        Location             : westus
        Id                   : /subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        Etag                 : W/"d448256a-e1df-413a-9103-a137e07276d1"
        ResourceGuid         : 896cac4f-152a-40b9-b079-3e2201a5906e
        ProvisioningState    : Succeeded
        Tags                 :
        VirtualMachine       : null
        IpConfigurations     : [
                            {
                            "Name": "ipconfig1",
                            "Etag": "W/\"d448256a-e1df-413a-9103-a137e07276d1\"",
                            "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/networkInterfaces/lb-nic1-be/ipConfigurations/ipconfig1",
                            "PrivateIpAddress": "10.0.2.6",
                            "PrivateIpAllocationMethod": "Static",
                            "Subnet": {
                                "Id": "/subscriptions/f50504a2-1865-4541-823a-b32842e3e0ee/resourceGroups/NRP-RG/providers/Microsoft.Network/virtualNetworks/NRPVNet/subnets/LB-Subnet-BE"
                            },
                            "ProvisioningState": "Succeeded",
                            "PrivateIpAddressVersion": "IPv4",
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
                            "Primary": true,
                            "ApplicationGatewayBackendAddressPools": []
                            }
                        ]
        DnsSettings          : {
                            "DnsServers": [],
                            "AppliedDnsServers": [],
                            "InternalDomainNameSuffix": "prcwibzcuvie5hnxav0yjks2cd.dx.internal.cloudapp.net"
                        }
        EnableIPForwarding   : False
        NetworkSecurityGroup : null
        Primary              :

5. Utilizar o `Add-AzureRmVMNetworkInterface` cmdlet para atribuir a NIC VMs diferentes.

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Para obter orientações sobre como criar uma máquina virtual e atribuir uma imagem, consulte o artigo [criar um VM Azure através do PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md).

## <a name="add-the-network-interface-to-the-load-balancer"></a>Adicionar a interface de rede para o Balanceador de carga

1. Obtenha o Balanceador de carga a partir do Azure.

    Carrega o recurso de Balanceador de carga para uma variável (se ainda não o tiver feito que ainda). A variável de chama **$lb**. Utilize os mesmos nomes a partir do recurso de Balanceador de carga que criou anteriormente.

        $lb= get-azurermloadbalancer –name NRP-LB -resourcegroupname NRP-RG

2. Carrega a configuração de back-end a uma variável.

        $backend=Get-AzureRmLoadBalancerBackendAddressPoolConfig -name backendpool1 -LoadBalancer $lb

3. Carrega a interface de rede já criado para uma variável. O nome da variável é **$nic**. O nome da interface de rede é a mesma do exemplo anterior.

        $nic =get-azurermnetworkinterface –name lb-nic1-be -resourcegroupname NRP-RG

4. Altere a configuração de back-end na interface de rede.

        $nic.IpConfigurations[0].LoadBalancerBackendAddressPools=$backend

5. Guarde o objeto de interface de rede.

        Set-AzureRmNetworkInterface -NetworkInterface $nic

    Depois de uma interface de rede é adicionada ao agrupamento de back-end do Balanceador de carga, inicia o tráfego de rede com base em regras balanceamento de carga para esse recurso Balanceador de carga a receber.

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

1. Ao utilizar o Balanceador de carga do exemplo anterior, atribua um objeto do Balanceador de carga para a variável **$slb** utilizando `Get-AzureLoadBalancer`.

        $slb = get-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

2. No seguinte exemplo, adicionar uma regra de NAT entrada – utilizando as portas 81 no conjunto de front-end e 8181 para o conjunto de back-end – um balanceador de carga existente.

        $slb | Add-AzureRmLoadBalancerInboundNatRuleConfig -Name NewRule -FrontendIpConfiguration $slb.FrontendIpConfigurations[0] -FrontendPort 81  -BackendPort 8181 -Protocol TCP

3. Guardar a nova configuração utilizando `Set-AzureLoadBalancer`.

        $slb | Set-AzureRmLoadBalancer

## <a name="remove-a-load-balancer"></a>Remover um balanceador de carga

Utilize o comando `Remove-AzureLoadBalancer` eliminar um balanceador de carga criado anteriormente denominado **NRP LB** num grupo de recursos denominado **NRP RG**.

    Remove-AzureRmLoadBalancer -Name NRPLB -ResourceGroupName NRP-RG

>[AZURE.NOTE] Pode utilizar o parâmetro opcional **-Forçar** para evitar a pedir a eliminação.

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
