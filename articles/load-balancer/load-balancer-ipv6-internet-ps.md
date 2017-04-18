<properties
    pageTitle="Criar uma Internet opostas Balanceador de carga com o IPv6 através do PowerShell para o Gestor de recursos | Microsoft Azure"
    description="Saiba como criar uma Internet opostas Balanceador de carga com o IPv6 através do PowerShell para o Gestor de recursos"
    services="load-balancer"
    documentationCenter="na"
    authors="sdwheeler"
    manager="carmonm"
    editor=""
    tags="azure-resource-manager"
    keywords="IPv6, Balanceador de carga azure, pilha dupla, endereço ip público, ipv6 nativo, mobile, iot"
/>
<tags
    ms.service="load-balancer"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="09/14/2016"
    ms.author="sewhee"
/>

# <a name="get-started-creating-an-internet-facing-load-balancer-with-ipv6-using-powershell-for-resource-manager"></a>Começar a criar uma Internet opostas Balanceador de carga com o IPv6 através do PowerShell para o Gestor de recursos

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Clip Azure](./load-balancer-ipv6-internet-cli.md)
- [Modelo](./load-balancer-ipv6-internet-template.md)

Um balanceador de carga Azure é um balanceador de carga camada-4 (TCP, UDP). O Balanceador de carga fornece elevada disponibilidade através da distribuição de tráfego de entrada entre instâncias do serviço Saudável nos serviços em nuvem ou máquinas virtuais num conjunto de Balanceador de carga. Azure Balanceador de carga também pode apresentar desses serviços no múltiplas portas, vários endereços IP ou ambas.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama seguinte ilustra o balanceamento de carga solução a ser implementada neste artigo.

![Cenário de Balanceador de carga](./media/load-balancer-ipv6-internet-ps/lb-ipv6-scenario.png)

Neste cenário irá criar os seguintes recursos Azure:

- um balanceador de carga de acesso à Internet com um IPv4 e um endereço IP público IPv6
- dois carregar balanceamento regras para mapear VIP público para os pontos finais privados
- um conjunto de disponibilidade para que contém as duas VMs
- duas máquinas virtuais (VMs)
- uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos

## <a name="deploying-the-solution-using-the-azure-powershell"></a>Implementar a solução utilizando o PowerShell do Azure

Os seguintes passos mostram como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure com PowerShell da Internet. Com o Gestor de recursos do Azure, cada recurso é criado e configurados individualmente, em seguida, colocar em conjunto para criar um recurso.

Para implementar um balanceador de carga, criar e configurar os objectos seguintes:

- Configuração de IP Front-end - contém endereços IP públicos para o tráfego de rede recebido.
- Conjunto de endereços de back-end - contém interfaces de rede (NIC) para as máquinas virtuais para receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga - contém regras de mapeamento uma porta pública no balanceador de carga à porta no conjunto de endereços de back-end.
- NAT regras de entrada - contém regras mapear uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end.
- Sondas - contém sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end.

Para mais informações, consulte o artigo [De suporte para Balanceador de carga de Gestor de recursos do Azure](load-balancer-arm.md).

## <a name="set-up-powershell-to-use-resource-manager"></a>Configurar o PowerShell para utilizar o Gestor de recursos

Certifique-se de que tem a versão mais recente de produção do módulo Azure o Gestor de recursos para PowerShell.

1. Inicie sessão no Azure

        Login-AzureRmAccount

    Introduza as suas credenciais quando lhe for pedido.

2. Verificar as subscrições para a conta

        Get-AzureRmSubscription

3. Escolha as suas subscrições Azure para utilizar.

        Select-AzureRmSubscription -SubscriptionId 'GUID of subscription'

4. Criar um grupo de recursos (ignorar este passo se utilizar um grupo de recursos existente)

        New-AzureRmResourceGroup -Name NRP-RG -location "West US"

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o conjunto de IP Front-end

1. Crie uma rede virtual com uma sub-rede.

        $backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -AddressPrefix 10.0.2.0/24
        $vnet = New-AzureRmvirtualNetwork -Name VNet -ResourceGroupName NRP-RG -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $backendSubnet

2. Crie o endereço IP público Azure recursos de (PIP) para o conjunto de endereços IP Front-end.

        $publicIPv4 = New-AzureRmPublicIpAddress -Name 'pub-ipv4' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Static -IpAddressVersion IPv4 -DomainNameLabel lbnrpipv4
        $publicIPv6 = New-AzureRmPublicIpAddress -Name 'pub-ipv6' -ResourceGroupName NRP-RG -Location 'West US' -AllocationMethod Dynamic -IpAddressVersion IPv6 -DomainNameLabel lbnrpipv6

    >[AZURE.IMPORTANT] O Balanceador de carga utiliza a etiqueta de domínio do endereço IP público como prefixo para o FQDN. Neste exemplo, os FQDNs são *lbnrpipv4.westus.cloudapp.azure.com* e *lbnrpipv6.westus.cloudapp.azure.com*.

## <a name="create-a-front-end-ip-configurations-and-a-back-end-address-pool"></a>Criar um configurações de front-end IP e um conjunto de endereços de Back-End

1. Crie a configuração de endereço front-end que utiliza os endereços de IP público que criou.

        $FEIPConfigv4 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv4" -PublicIpAddress $publicIPv4
        $FEIPConfigv6 = New-AzureRmLoadBalancerFrontendIpConfig -Name "LB-Frontendv6" -PublicIpAddress $publicIPv6

2. Crie conjuntos de endereços de back-end.

        $backendpoolipv4 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv4"
        $backendpoolipv6 = New-AzureRmLoadBalancerBackendAddressPoolConfig -Name "BackendPoolIPv6"


## <a name="create-lb-rules-nat-rules-a-probe-and-a-load-balancer"></a>Criar regras LB, regras NAT, uma sonda e um balanceador de carga

Este exemplo cria os seguintes itens:

- uma regra NAT para traduzir todo o tráfego de entrada na porta 443 à porta 4443
- uma regra de Balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 à porta 80 nos endereços no conjunto de back-end.
- uma regra de Balanceador de carga para permitir que ligação RDP VMs na porta 3389.
- uma regra de sonda para verificar o estado de funcionamento de uma página com o nome *HealthProbe.aspx* ou um serviço na porta 8080
- um balanceador de carga que utiliza todos os objetos

1. Crie as regras NAT.

        $inboundNATRule1v4 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev4" -FrontendIpConfiguration $FEIPConfigv4 -Protocol TCP -FrontendPort 443 -BackendPort 4443
        $inboundNATRule1v6 = New-AzureRmLoadBalancerInboundNatRuleConfig -Name "NicNatRulev6" -FrontendIpConfiguration $FEIPConfigv6 -Protocol TCP -FrontendPort 443 -BackendPort 4443

2. Crie uma sonda de estado de funcionamento. Existem duas formas para configurar uma sonda:

    Sonda HTTP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -RequestPath 'HealthProbe.aspx' -Protocol http -Port 80 -IntervalInSeconds 15 -ProbeCount 2

    ou sonda TCP

        $healthProbe = New-AzureRmLoadBalancerProbeConfig -Name 'HealthProbe-v4v6' -Protocol Tcp -Port 8080 -IntervalInSeconds 15 -ProbeCount 2
        $RDPprobe = New-AzureRmLoadBalancerProbeConfig -Name 'RDPprobe' -Protocol Tcp -Port 3389 -IntervalInSeconds 15 -ProbeCount 2


    Neste exemplo, vamos utilizar as sondas TCP.

3. Crie uma regra de Balanceador de carga.

        $lbrule1v4 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv4" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $lbrule1v6 = New-AzureRmLoadBalancerRuleConfig -Name "HTTPv6" -FrontendIpConfiguration $FEIPConfigv6 -BackendAddressPool $backendpoolipv6 -Probe $healthProbe -Protocol Tcp -FrontendPort 80 -BackendPort 8080
        $RDPrule = New-AzureRmLoadBalancerRuleConfig -Name "RDPrule" -FrontendIpConfiguration $FEIPConfigv4 -BackendAddressPool $backendpoolipv4 -Probe $RDPprobe -Protocol Tcp -FrontendPort 3389 -BackendPort 3389

4. Crie o Balanceador de carga utilizando os objectos criados anteriormente.

        $NRPLB = New-AzureRmLoadBalancer -ResourceGroupName NRP-RG -Name 'myNrpIPv6LB' -Location 'West US' -FrontendIpConfiguration $FEIPConfigv4,$FEIPConfigv6 -InboundNatRule $inboundNATRule1v6,$inboundNATRule1v4 -BackendAddressPool $backendpoolipv4,$backendpoolipv6 -Probe $healthProbe,$RDPprobe -LoadBalancingRule $lbrule1v4,$lbrule1v6,$RDPrule

## <a name="create-nics-for-the-back-end-vms"></a>Criar NIC para os VMs back-end

1. Obter a rede Virtual e Virtual sub-rede da rede, onde o NIC necessita de ser criada.

        $vnet = Get-AzureRmVirtualNetwork -Name NRPVNet -ResourceGroupName NRP-RG
        $backendSubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name LB-Subnet-BE -VirtualNetwork $vnet

2. Crie as configurações de IP e NIC para os VMs.

        $nic1IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4 -LoadBalancerInboundNatRule $inboundNATRule1v4
        $nic1IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6 -LoadBalancerInboundNatRule $inboundNATRule1v6
        $nic1 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic0' -IpConfiguration $nic1IPv4,$nic1IPv6 -ResourceGroupName NRP-RG -Location 'West US'

        $nic2IPv4 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv4IPConfig" -PrivateIpAddressVersion "IPv4" -Subnet $backendSubnet -LoadBalancerBackendAddressPool $backendpoolipv4
        $nic2IPv6 = New-AzureRmNetworkInterfaceIpConfig -Name "IPv6IPConfig" -PrivateIpAddressVersion "IPv6" -LoadBalancerBackendAddressPool $backendpoolipv6
        $nic2 = New-AzureRmNetworkInterface -Name 'myNrpIPv6Nic1' -IpConfiguration $nic2IPv4,$nic2IPv6 -ResourceGroupName NRP-RG -Location 'West US'

## <a name="create-virtual-machines-and-assign-the-newly-created-nics"></a>Criar máquinas virtuais e atribuir a NIC recentemente criado

Para obter mais informações sobre a criação de uma VM, consulte o artigo [criar e pré-configurar uma máquina de Virtual do Windows com o Gestor de recursos e Azure PowerShell](..\virtual-machines\virtual-machines-windows-ps-create.md)

1. Criar uma conta do conjunto de disponibilidade e armazenamento

        New-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG -location 'West US'
        $availabilitySet = Get-AzureRmAvailabilitySet -Name 'myNrpIPv6AvSet' -ResourceGroupName NRP-RG
        New-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct' -Location 'West US' -SkuName $LRS
        $CreatedStorageAccount = Get-AzureRmStorageAccount -ResourceGroupName NRP-RG -Name 'mynrpipv6stacct'

2. Crie cada VM e atribua anterior criado NIC

        $mySecureCredentials= Get-Credential -Message “Type the username and password of the local administrator account.”

        $vm1 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM0' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm1 = Set-AzureRmVMOperatingSystem -VM $vm1 -Windows -ComputerName 'myNrpIPv6VM0' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm1 = Set-AzureRmVMSourceImage -VM $vm1 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm1 = Add-AzureRmVMNetworkInterface -VM $vm1 -Id $nic1.Id -Primary
        $osDisk1Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM0osdisk.vhd"
        $vm1 = Set-AzureRmVMOSDisk -VM $vm1 -Name 'myNrpIPv6VM0osdisk' -VhdUri $osDisk1Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm1

        $vm2 = New-AzureRmVMConfig -VMName 'myNrpIPv6VM1' -VMSize 'Standard_G1' -AvailabilitySetId $availabilitySet.Id
        $vm2 = Set-AzureRmVMOperatingSystem -VM $vm2 -Windows -ComputerName 'myNrpIPv6VM1' -Credential $mySecureCredentials -ProvisionVMAgent -EnableAutoUpdate
        $vm2 = Set-AzureRmVMSourceImage -VM $vm2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
        $vm2 = Add-AzureRmVMNetworkInterface -VM $vm2 -Id $nic2.Id -Primary
        $osDisk2Uri = $CreatedStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/myNrpIPv6VM1osdisk.vhd"
        $vm2 = Set-AzureRmVMOSDisk -VM $vm2 -Name 'myNrpIPv6VM1osdisk' -VhdUri $osDisk2Uri -CreateOption FromImage
        New-AzureRmVM -ResourceGroupName NRP-RG -Location 'West US' -VM $vm2

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
