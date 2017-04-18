<properties
    pageTitle="Criar uma Internet opostas Balanceador de carga com o IPv6 no Gestor de recursos do Azure utilizando o clip do Azure | Microsoft Azure"
    description="Saiba como criar uma Internet opostas Balanceador de carga com o IPv6 no Gestor de recursos do Azure utilizando o clip do Azure"
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

# <a name="create-an-internet-facing-load-balancer-with-ipv6-in-azure-resource-manager-using-the-azure-cli"></a>Criar uma Internet opostas Balanceador de carga com o IPv6 no Gestor de recursos do Azure utilizando o clip do Azure

> [AZURE.SELECTOR]
- [PowerShell](./load-balancer-ipv6-internet-ps.md)
- [Clip Azure](./load-balancer-ipv6-internet-cli.md)
- [Modelo](./load-balancer-ipv6-internet-template.md)

Um balanceador de carga Azure é um balanceador de carga camada-4 (TCP, UDP). O Balanceador de carga fornece elevada disponibilidade através da distribuição de tráfego de entrada entre instâncias do serviço Saudável nos serviços em nuvem ou máquinas virtuais num conjunto de Balanceador de carga. Azure Balanceador de carga também pode apresentar desses serviços no múltiplas portas, vários endereços IP ou ambas.

## <a name="example-deployment-scenario"></a>Cenário de implementação de exemplo

O diagrama seguinte ilustra a solução de balanceamento de carga em que está a ser implementada utilizando o modelo de exemplo é descrito neste artigo.

![Cenário de Balanceador de carga](./media/load-balancer-ipv6-internet-cli/lb-ipv6-scenario-cli.png)

Neste cenário irá criar os seguintes recursos Azure:

- duas máquinas virtuais (VMs)
- uma interface de rede virtual para cada VM com endereços IPv4 e IPv6 atribuídos
- um balanceador de carga de acesso à Internet com um IPv4 e um endereço IP público IPv6
- um conjunto de disponibilidade para que contém as duas VMs
- dois carregar balanceamento regras para mapear VIP público para os pontos finais privados

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementar a solução utilizando o clip do Azure

Os seguintes passos mostram como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure com o clip da Internet. Com o Gestor de recursos do Azure, cada recurso é criado e configurado individualmente e, em seguida, colocar em conjunto para criar um recurso.

Para implementar um balanceador de carga, criar e configurar os objectos seguintes:

- Configuração de IP Front-end - contém endereços IP públicos para o tráfego de rede recebido.
- Conjunto de endereços de back-end - contém interfaces de rede (NIC) para as máquinas virtuais para receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga - contém regras de mapeamento uma porta pública no balanceador de carga à porta no conjunto de endereços de back-end.
- NAT regras de entrada - contém regras mapear uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end.
- Sondas - contém sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end.

Para mais informações, consulte o artigo [De suporte para Balanceador de carga de Gestor de recursos do Azure](load-balancer-arm.md).

## <a name="set-up-your-cli-environment-to-use-azure-resource-manager"></a>Configurar o seu ambiente do clip para utilizar o Gestor de recursos do Azure

Neste exemplo, vamos está a executar as ferramentas de clip numa janela de comando PowerShell. Vamos não está a utilizar os cmdlets do Azure PowerShell mas utilizamos as capacidades de scripting do PowerShell para melhorar a legibilidade e reutilização.

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.

2. Execute o comando **azure config modo** para mudar para modo de Gestor de recursos.

        azure config mode arm

    Saída esperada:

        info:    New mode is arm

3. Inicie sessão no Azure e obter uma lista de subscrições.

        azure login

    Introduza as suas credenciais Azure quando lhe for pedido.

        azure account list

    Selecione a subscrição que pretende utilizar. Tome nota do Id da subscrição para o próximo passo.

4. Configure o PowerShell variáveis para utilização com os comandos do clip.

        ```
        $subscriptionid = "########-####-####-####-############"  # enter subscription id
        $location = "southcentralus"
        $rgName = "pscontosorg1southctrlus09152016"
        $vnetName = "contosoIPv4Vnet"
        $vnetPrefix = "10.0.0.0/16"
        $subnet1Name = "clicontosoIPv4Subnet1"
        $subnet1Prefix = "10.0.0.0/24"
        $subnet2Name = "clicontosoIPv4Subnet2"
        $subnet2Prefix = "10.0.1.0/24"
        $dnsLabel = "contoso09152016"
        $lbName = "myIPv4IPv6Lb"
        ```

## <a name="create-a-resource-group-a-load-balancer-a-virtual-network-and-subnets"></a>Criar um grupo de recursos, um balanceador de carga, uma rede virtual e sub-redes

1. Criar um grupo de recursos

        azure group create $rgName $location

2. Criar um balanceador de carga

        $lb = azure network lb create --resource-group $rgname --location $location --name $lbName

3. Crie uma rede virtual (VNet).

        $vnet = azure network vnet create  --resource-group $rgname --name $vnetName --location $location --address-prefixes $vnetPrefix

    Crie duas sub-redes este VNet.

        $subnet1 = azure network vnet subnet create --resource-group $rgname --name $subnet1Name --address-prefix $subnet1Prefix --vnet-name $vnetName
        $subnet2 = azure network vnet subnet create --resource-group $rgname --name $subnet2Name --address-prefix $subnet2Prefix --vnet-name $vnetName

## <a name="create-public-ip-addresses-for-the-front-end-pool"></a>Criar endereços IP públicos para o conjunto de dados front-end

1. Configurar as variáveis PowerShell

        $publicIpv4Name = "myIPv4Vip"
        $publicIpv6Name = "myIPv6Vip"

2. Crie um endereço IP público o conjunto de IP Front-end.

        $publicipV4 = azure network public-ip create --resource-group $rgname --name $publicIpv4Name --location $location --ip-version IPv4 --allocation-method Dynamic --domain-name-label $dnsLabel
        $publicipV6 = azure network public-ip create --resource-group $rgname --name $publicIpv6Name --location $location --ip-version IPv6 --allocation-method Dynamic --domain-name-label $dnsLabel

    >[AZURE.IMPORTANT]O Balanceador de carga utiliza a etiqueta de domínio do endereço IP público como respectivo FQDN. Isto uma alteração de implementação clássica, que utiliza o serviço de nuvem nome como o FQDN do Balanceador de carga.
    >Neste exemplo, o FQDN é *contoso09152016.southcentralus.cloudapp.azure.com*.

## <a name="create-front-end-and-back-end-pools"></a>Criar conjuntos de dados front-end e back-end

Este exemplo cria o conjunto de IP Front-end que recebe o tráfego de rede recebidos no balanceador de carga e o conjunto de IP de back-end onde o conjunto de front-end envia o tráfego de rede de balanceamento de carga.

1. Configurar as variáveis PowerShell

        $frontendV4Name = "FrontendVipIPv4"
        $frontendV6Name = "FrontendVipIPv6"
        $backendAddressPoolV4Name = "BackendPoolIPv4"
        $backendAddressPoolV6Name = "BackendPoolIPv6"

2. Crie um conjunto IP Front-end associar o endereço IP público criado no passo anterior e o Balanceador de carga.

        $frontendV4 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV4Name --public-ip-name $publicIpv4Name --lb-name $lbName
        $frontendV6 = azure network lb frontend-ip create --resource-group $rgname --name $frontendV6Name --public-ip-name $publicIpv6Name --lb-name $lbName
        $backendAddressPoolV4 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV4Name --lb-name $lbName
        $backendAddressPoolV6 = azure network lb address-pool create --resource-group $rgname --name $backendAddressPoolV6Name --lb-name $lbName

## <a name="create-the-probe-nat-rules-and-lb-rules"></a>Criar a sonda, regras NAT e LB regras

Este exemplo cria os seguintes itens:

- uma regra de sonda para verificar a existência de conectividade ao porta TCP 80
- uma regra NAT para traduzir todo o tráfego de entrada na porta 3389 à porta 3389 para RDP<sup>1</sup>
- uma regra NAT para traduzir todo o tráfego de entrada na porta 3391 à porta 3389 para RDP<sup>1</sup>
- uma regra de Balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 à porta 80 nos endereços no conjunto de back-end.

<sup>1</sup> regras NAT estão associadas a uma instância de máquina virtual específica atrás Balanceador de carga. O tráfego de rede entregue na porta 3389 é enviado para a porta associadas à regra NAT e máquina virtual específica. Tem de especificar um protocolo (UDP ou TCP) para uma regra NAT. Não podem ser atribuídos ambos os protocolos para a mesma porta.

1. Configurar as variáveis PowerShell

        $probeV4V6Name = "ProbeForIPv4AndIPv6"
        $natRule1V4Name = "NatRule-For-Rdp-VM1"
        $natRule2V4Name = "NatRule-For-Rdp-VM2"
        $lbRule1V4Name = "LBRuleForIPv4-Port80"
        $lbRule1V6Name = "LBRuleForIPv6-Port80"

2. Criar a sonda

    O exemplo seguinte cria uma sonda TCP verifica para a conectividade porta 80 TCP back-end cada 15 segundos. -Lo irá marcar o recurso de back-end indisponível após falhas consecutivas da duas.

        $probeV4V6 = azure network lb probe create --resource-group $rgname --name $probeV4V6Name --protocol tcp --port 80 --interval 15 --count 2 --lb-name $lbName

3. Criar regras de entrada na NAT que permitam ligações de RDP para os recursos de back-end

        $inboundNatRuleRdp1 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule1V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3389 --backend-port 3389 --lb-name $lbName
        $inboundNatRuleRdp2 = azure network lb inbound-nat-rule create --resource-group $rgname --name $natRule2V4Name --frontend-ip-name $frontendV4Name --protocol Tcp --frontend-port 3391 --backend-port 3389 --lb-name $lbName

4. Crie um balanceador de carga regras que enviar o tráfego para portas back-end diferentes cálculo consoante a forma que front-end recebeu o pedido

        $lbruleIPv4 = azure network lb rule create --resource-group $rgname --name $lbRule1V4Name --frontend-ip-name $frontendV4Name --backend-address-pool-name $backendAddressPoolV4Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 80 --lb-name $lbName
        $lbruleIPv6 = azure network lb rule create --resource-group $rgname --name $lbRule1V6Name --frontend-ip-name $frontendV6Name --backend-address-pool-name $backendAddressPoolV6Name --probe-name $probeV4V6Name --protocol Tcp --frontend-port 80 --backend-port 8080 --lb-name $lbName

5. Verificar as suas definições

        azure network lb show --resource-group $rgName --name $lbName

    Saída esperada:

        info:    Executing command network lb show
        info:    Looking up the load balancer "myIPv4IPv6Lb"
        data:    Id                              : /subscriptions/########-####-####-####-############/resourceGroups/pscontosorg1southctrlus09152016/providers/Microsoft.Network/loadBalancers/myIPv4IPv6Lb
        data:    Name                            : myIPv4IPv6Lb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : southcentralus
        data:    Provisioning state              : Succeeded
        data:
        data:    Frontend IP configurations:
        data:    Name             Provisioning state  Private IP allocation  Private IP   Subnet  Public IP
        data:    ---------------  ------------------  ---------------------  -----------  ------  ---------
        data:    FrontendVipIPv4  Succeeded           Dynamic                                     myIPv4Vip
        data:    FrontendVipIPv6  Succeeded           Dynamic                                     myIPv6Vip
        data:
        data:    Probes:
        data:    Name                 Provisioning state  Protocol  Port  Path  Interval  Count
        data:    -------------------  ------------------  --------  ----  ----  --------  -----
        data:    ProbeForIPv4AndIPv6  Succeeded           Tcp       80          15        2
        data:
        data:    Backend Address Pools:
        data:    Name             Provisioning state
        data:    ---------------  ------------------
        data:    BackendPoolIPv4  Succeeded
        data:    BackendPoolIPv6  Succeeded
        data:
        data:    Load Balancing Rules:
        data:    Name                  Provisioning state  Load distribution  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    --------------------  ------------------  -----------------  --------  -------------  ------------  ------------------  -----------------------
        data:    LBRuleForIPv4-Port80  Succeeded           Default            Tcp       80             80            false               4
        data:    LBRuleForIPv6-Port80  Succeeded           Default            Tcp       80             8080          false               4
        data:
        data:    Inbound NAT Rules:
        data:    Name                 Provisioning state  Protocol  Frontend port  Backend port  Enable floating IP  Idle timeout in minutes
        data:    -------------------  ------------------  --------  -------------  ------------  ------------------  -----------------------
        data:    NatRule-For-Rdp-VM1  Succeeded           Tcp       3389           3389          false               4
        data:    NatRule-For-Rdp-VM2  Succeeded           Tcp       3391           3389          false               4
        info:    network lb show


## <a name="create-nics"></a>Criar NIC

Crie NIC e associá-las para regras, as regras do Balanceador de carga e sondas NAT.

1. Configurar as variáveis PowerShell

        $nic1Name = "myIPv4IPv6Nic1"
        $nic2Name = "myIPv4IPv6Nic2"
        $subnet1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet1Name"
        $subnet2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgName/providers/Microsoft.Network/VirtualNetworks/$vnetName/subnets/$subnet2Name"
        $backendAddressPoolV4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV4Name"
        $backendAddressPoolV6Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/backendAddressPools/$backendAddressPoolV6Name"
        $natRule1V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule1V4Name"
        $natRule2V4Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/loadbalancers/$lbName/inboundNatRules/$natRule2V4Name"

2. Crie uma NIC para cada back-end e adicione uma configuração do IPv6.

        $nic1 = azure network nic create --name $nic1Name --resource-group $rgname --location $location --private-ip-version "IPv4" --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic1IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic1Name

        $nic2 = azure network nic create --name $nic2Name --resource-group $rgname --location $location --subnet-id $subnet1Id --lb-address-pool-ids $backendAddressPoolV4Id --lb-inbound-nat-rule-ids $natRule1V4Id
        $nic2IPv6 = azure network nic ip-config create --resource-group $rgname --name "IPv6IPConfig" --private-ip-version "IPv6" --lb-address-pool-ids $backendAddressPoolV6Id --nic-name $nic2Name

## <a name="create-the-back-end-vm-resources-and-attach-each-nic"></a>Criar os recursos VM back-end e anexar a cada imagem

Para criar VMs, tem de ter uma conta de armazenamento. Para balanceamento de carga, os VMs precisam de ser membros de um conjunto de disponibilidade. Para obter mais informações sobre a criação de VMs, consulte o artigo [criar um VM Azure através do PowerShell](../virtual-machines/virtual-machines-windows-ps-create.md)

1. Configurar as variáveis PowerShell

        $storageAccountName = "ps08092016v6sa0"
        $availabilitySetName = "myIPv4IPv6AvailabilitySet"
        $vm1Name = "myIPv4IPv6VM1"
        $vm2Name = "myIPv4IPv6VM2"
        $nic1Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic1Name"
        $nic2Id = "/subscriptions/$subscriptionid/resourceGroups/$rgname/providers/Microsoft.Network/networkInterfaces/$nic2Name"
        $disk1Name = "WindowsVMosDisk1"
        $disk2Name = "WindowsVMosDisk2"
        $osDisk1Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk1Name.vhd"
        $osDisk2Uri = "https://$storageAccountName.blob.core.windows.net/vhds/$disk2Name.vhd"
        $imageurn "MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest"
        $vmUserName = "vmUser"
        $mySecurePassword = "PlainTextPassword*1"

    >[AZURE.WARNING] Este exemplo utiliza o nome de utilizador e palavra-passe para VMs em texto simples. Cuidados adequados devem ser tomados quando utilizar as credenciais em claro. Para um método mais seguro de processamento de credenciais no PowerShell, consulte o cmdlet [Get-credenciais](https://technet.microsoft.com/library/hh849815.aspx) .

2. Criar o conjunto de conta e disponibilidade de armazenamento

    Pode utilizar uma conta existente do armazenamento quando cria as VMs. O comando seguinte cria uma nova conta de armazenamento.

        $storageAcc = azure storage account create $storageAccountName --resource-group $rgName --location $location --sku-name "LRS" --kind "Storage"

    Em seguida, crie o conjunto de disponibilidade.

        $availabilitySet = azure availset create --name $availabilitySetName --resource-group $rgName --location $location

3. Criar as máquinas virtuais com o NIC associado

        $vm1 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm1Name --nic-id $nic1Id --os-disk-vhd $osDisk1Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn --storage-account-name $storageAccountName --disable-bginfo-extension

        $vm2 = azure vm create --resource-group $rgname --location $location --availset-name $availabilitySetName --name $vm2Name --nic-id $nic2Id --os-disk-vhd $osDisk2Uri --os-type "Windows" --admin-username $vmUserName --admin-password $mySecurePassword --vm-size "Standard_A1" --image-urn $imageurn  --storage-account-name $storageAccountName --disable-bginfo-extension

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
