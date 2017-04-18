<properties
    pageTitle="Comandos de rede comuns PowerShell para VMs | Microsoft Azure"
    description="Comuns comandos do PowerShell para o ajudar a começar a criação de uma rede virtual e respectivos recursos associados para VMs."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="common-network-azure-powershell-commands-for-vms"></a>Comandos comuns do Azure PowerShell de rede para VMs

Se pretender criar uma máquina virtual, tem de criar uma [rede virtual](../virtual-network/virtual-networks-overview.md) ou saber sobre uma rede virtual existente na qual pode ser adicionada a VM. Normalmente, quando cria uma VM, também tem de considere criar os recursos que é descritos neste artigo.

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a sua subscrição e iniciar sessão na sua conta.

## <a name="create-network-resources"></a>Criar recursos de rede

Tarefa | Comando 
-------------- | -------------------------
Criar sub-rede configurações | $ Sub-rede1 = [Novo AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt619412.aspx) -subnet_name"nome" - AddressPrefix XX. X.X.X/XX<BR>$ Sub-rede2 = novo AzureRmVirtualNetworkSubnetConfig-subnet_name"nome" - AddressPrefix XX. X.X.X/XX<BR><BR>Uma rede típica pode ter uma sub-rede para um [internet opostas Balanceador de carga](../load-balancer/load-balancer-internet-overview.md) e uma sub-rede separada para um [Balanceador de carga interno](../load-balancer/load-balancer-internal-overview.md). |
Criar uma rede virtual | $vnet = [Novo AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603657.aspx) -- ResourceGroupName de "virtual_network_name" nome "resource_group_name"-localização "location_name" - AddressPrefix XX. X.X.X/XX-sub-rede $ Sub-rede1, $ sub-rede2
Teste para um nome de domínio exclusivos | [Teste AzureRmDnsAvailability](https://msdn.microsoft.com/library/mt619419.aspx) - DomainQualifiedName "nome_de_domínio"-localização "location_name"<BR><BR>Pode especificar um nome de domínio para um [recurso IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md), que cria um mapeamento para domainname.location.cloudapp.azure.com para o endereço IP público nos servidores DNS Azure com gestão de DNS. O nome pode conter apenas letras, números e hífenes. O primeiro e último caráter tem de ser uma letra ou número e o nome de domínio tem de ser exclusivos dentro da sua localização Azure. Se for devolvida **Verdadeiro** , o seu nome de proposta é globalmente exclusivo.
Criar um endereço IP público | $pip = [Novo AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt603620.aspx) -nome_de_domínio"nome"ip_address_name"- ResourceGroupName"resource_group_name"- DomainNameLabel"-localização "location_name" - AllocationMethod dinâmicos<BR><BR>O endereço IP público utiliza o nome de domínio testado anteriormente e é utilizado pela configuração frontend do Balanceador de carga.
Criar uma configuração de IP Front-end | $frontendIP = [Novo AzureRmLoadBalancerFrontendIpConfig](https://msdn.microsoft.com/library/mt603510.aspx) -frontend_ip_name"nome" - PublicIpAddress $pip<BR><BR>A configuração de front-end inclui o endereço IP público que criou anteriormente para o tráfego de rede recebido.
Criar um conjunto de endereços de back-end | $beAddressPool = [Novo AzureRmLoadBalancerBackendAddressPoolConfig](https://msdn.microsoft.com/library/mt603791.aspx) -backend_pool_name"nome"<BR><BR>Fornece endereços internos para o back-end do Balanceador de carga que são acedidos através de uma interface de rede.
Criar uma sonda | $healthProbe = [Novo AzureRmLoadBalancerProbeConfig](https://msdn.microsoft.com/library/mt603847.aspx) -probe_name"nome" - RequestPath 'HealthProbe.aspx'-protocolo http-porta 80 - IntervalInSeconds 15 - ProbeCount 2<BR><BR>Contém sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços back-end.
Criar uma regra de balanceamento de carga | $lbRule = [Novo AzureRmLoadBalancerRuleConfig](https://msdn.microsoft.com/library/mt619391.aspx) -nome HTTP - FrontendIpConfiguration $frontendIP - BackendAddressPool $beAddressPool-sonda $healthProbe-protocolo Tcp - FrontendPort 80 - BackendPort 80<BR><BR>Contém regras que atribuir uma porta pública no balanceador de carga a uma porta no conjunto de endereços back-end.
Criar uma regra de NAT entrada | $inboundNATRule = [Novo AzureRmLoadBalancerInboundNatRuleConfig](https://msdn.microsoft.com/library/mt603606.aspx) -rule_name"nome" - FrontendIpConfiguration $frontendIP-protocolo TCP - FrontendPort 3441 - BackendPort 3389<BR><BR>Contém regras de mapear uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços back-end.
Criar um balanceador de carga | $loadBalancer = [Novo AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt619450.aspx) - ResourceGroupName "resource_group_name"-load_balancer_name"nome"-localização "location_name" - FrontendIpConfiguration $frontendIP - InboundNatRule $inboundNATRule - LoadBalancingRule $lbRule - BackendAddressPool $beAddressPool-sonda $healthProbe
Criar uma interface de rede | $nic1 = [Novo AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619370.aspx) - ResourceGroupName "resource_group_name"-Nome_Interface_Rede"nome"-localização "location_name" - PrivateIpAddress XX. X.X.X-sub-rede sub-rede2 - LoadBalancerBackendAddressPool $loadBalancer.BackendAddressPools[0] - LoadBalancerInboundNatRule $loadBalancer.InboundNatRules[0]<BR><BR>Crie uma interface de rede com o endereço IP público e sub-rede de rede virtual que criou anteriormente.
    
## <a name="get-information-about-network-resources"></a>Obter informações sobre recursos de rede

Tarefa | Comando 
-------------- | -------------------------
Lista de redes virtuais | [Get AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt603515.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista todas as redes virtuais no grupo de recursos.
Obter informações sobre uma rede virtual | Get AzureRmVirtualNetwork-- ResourceGroupName de "virtual_network_name" nome "resource_group_name"
Lista sub-redes numa rede virtual | Get AzureRmVirtualNetwork-nome "virtual_network_name" - ResourceGroupName "resource_group_name" & #124; Selecione sub-redes
Obter informações sobre uma sub-rede | [Get AzureRmVirtualNetworkSubnetConfig](https://msdn.microsoft.com/library/mt603817.aspx) -subnet_name"nome" - VirtualNetwork $vnet<BR><BR>Obtém informações sobre a sub-rede na rede virtual especificada. O valor de $vnet representa o objecto devolvido por Get-AzureRmVirtualNetwork.
Endereços IP da lista | [Get AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619342.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista de endereços IP públicos no grupo de recursos.
Balanceadores de carga de lista | [Get AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603668.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista todas as balanceadores de carga no grupo de recursos.
Interfaces de rede de lista | [Get AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) - ResourceGroupName "resource_group_name"<BR><BR>Lista todas as interfaces de rede, no grupo de recursos.
Obter informações sobre uma interface de rede | Get AzureRmNetworkInterface-- ResourceGroupName de "Nome_Interface_Rede" nome "resource_group_name"<BR><BR>Obtém informações sobre uma interface de rede específico.
Obter a configuração de IP de uma interface de rede | [Get AzureRmNetworkInterfaceIPConfig](https://msdn.microsoft.com/library/mt732618.aspx) -ipconfiguration_name"nome" - NetworkInterface $nic<BR><BR>Obtém informações sobre a configuração de IP da interface de rede especificado. O valor de $nic representa o objecto devolvido por Get-AzureRmNetworkInterface.

## <a name="manage-network-resources"></a>Gerir recursos de rede

Tarefa | Comando 
-------------- | -------------------------
Adicionar uma sub-rede a uma rede virtual | [AzureRmVirtualNetworkSubnetConfig adicionar](https://msdn.microsoft.com/library/mt603722.aspx) - AddressPrefix XX. X.X.X/XX-subnet_name"nome" - VirtualNetwork $vnet<BR><BR>Adiciona uma sub-rede a uma rede virtual existente. O valor de $vnet representa o objecto devolvido por Get-AzureRmVirtualNetwork.
Eliminar uma rede virtual | [Remover AzureRmVirtualNetwork](https://msdn.microsoft.com/library/mt619338.aspx) -- ResourceGroupName de "virtual_network_name" nome "resource_group_name"<BR><BR>Remove a rede virtual especificada do grupo de recursos.
Eliminar uma interface de rede | [Remover AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt603836.aspx) -- ResourceGroupName de "Nome_Interface_Rede" nome "resource_group_name"<BR><BR>Remove a interface de rede especificado do grupo de recursos.
Eliminar um balanceador de carga | [Remover AzureRmLoadBalancer](https://msdn.microsoft.com/library/mt603862.aspx) -- ResourceGroupName de "load_balancer_name" nome "resource_group_name"<BR><BR>Remove o Balanceador de carga especificado do grupo de recursos.
Eliminar um endereço IP público | [Remover AzureRmPublicIpAddress](https://msdn.microsoft.com/library/mt619352.aspx)-- ResourceGroupName de "ip_address_name" nome "resource_group_name"<BR><BR>Remove o endereço IP público especificado do grupo de recursos.

## <a name="next-steps"></a>Próximos passos

- Utilizar a interface de rede que que acabou de criar quando [criar uma VM](virtual-machines-windows-ps-create.md).
- Saiba mais sobre como pode [criar uma VM com várias interfaces de rede](../virtual-network/virtual-networks-multiple-nics.md).
