<properties
   pageTitle="Criar uma Internet opostas Balanceador de carga no Gestor de recursos utilizando o clip do Azure | Microsoft Azure"
   description="Saiba como criar uma Internet opostas Balanceador de carga no Gestor de recursos utilizando o clip do Azure"
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

# <a name="creating-an-internal-load-balancer-using-the-azure-cli"></a>Criar um balanceador de carga interno utilizando o clip do Azure

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação do Gestor de recursos. Também pode [aprender a criar uma Internet opostas Balanceador de carga utilizando implementação clássica](load-balancer-get-started-internet-classic-portal.md)


[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="deploying-the-solution-using-the-azure-cli"></a>Implementar a solução utilizando o clip do Azure

Os seguintes passos mostram como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure com o clip da Internet. Gestor de recursos do Azure com cada recurso é criado e configurados individualmente, em seguida, colocar em conjunto para criar um recurso.

Tem de criar e configurar os seguintes objectos para implementar um balanceador de carga:

- Configuração de IP Front-end - contém endereços IP públicos para o tráfego de rede recebido.
- Conjunto de endereços de back-end - contém interfaces de rede (NIC) para as máquinas virtuais para receber o tráfego de rede de Balanceador de carga.
- Regras de balanceamento de carga - contém regras de mapeamento uma porta pública no balanceador de carga à porta no conjunto de endereços de back-end.
- NAT regras de entrada - contém regras mapear uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end.
- Sondas - contém sondas de estado de funcionamento utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end.

Para obter mais informações consulte o artigo [De suporte para Balanceador de carga de Gestor de recursos do Azure](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar o clip para utilizar o Gestor de recursos

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md) e siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.

2. Execute o comando de **modo azure config** para mudar para modo de Gestor de recursos, conforme apresentado abaixo.

        azure config mode arm

    Saída esperada:

        info:    New mode is arm

## <a name="create-a-virtual-network-and-a-public-ip-address-for-the-front-end-ip-pool"></a>Criar uma rede virtual e um endereço IP público para o conjunto de IP Front-end

1. Crie uma rede virtual (VNet) com o nome *NRPVnet* na localização Leste dos EUA utilizar um grupo de recursos com o nome *NRPRG*.

        azure network vnet create NRPRG NRPVnet eastUS -a 10.0.0.0/16

    Crie uma sub-rede denominada *NRPVnetSubnet* com um bloco CIDR de 10.0.0.0/24 no *NRPVnet*.

        azure network vnet subnet create NRPRG NRPVnet NRPVnetSubnet -a 10.0.0.0/24

2. Crie um endereço IP público com o nome *NRPPublicIP* para ser utilizado por um conjunto IP Front-end com DNS nome *loadbalancernrp.eastus.cloudapp.azure.com*. O comando abaixo utiliza o tipo de alocação estática e tempo limite inactivo de 4 minutos.

        azure network public-ip create -g NRPRG -n NRPPublicIP -l eastus -d loadbalancernrp -a static -i 4

    >[AZURE.IMPORTANT]O Balanceador de carga irá utilizar a etiqueta de domínio do endereço IP público como respectivo FQDN. Isto uma alteração de implementação clássica, que utiliza na nuvem serviço, tal como o Balanceador de carga completamente qualificado nome de domínio (FQDN).
    >Neste exemplo, o FQDN é *loadbalancernrp.eastus.cloudapp.azure.com*.

## <a name="create-a-load-balancer"></a>Criar um balanceador de carga

O comando seguinte cria um balanceador de carga denominado *NRPlb* no grupo de recursos *NRPRG* nos *Leste dos EUA* Azure localização.

    azure network lb create NRPRG NRPlb eastus

## <a name="create-a-front-end-ip-pool-and-a-backend-address-pool"></a>Criar um conjunto IP Front-end e um conjunto de endereços de back-end

Este exemplo demonstra como criar o conjunto de IP Front-end que recebe o tráfego de rede recebidos no balanceador de carga e o conjunto de IP de back-end onde o conjunto de front-end envia o tráfego de rede de balanceamento de carga.

1. Crie um conjunto IP Front-end associar o endereço IP público criado no passo anterior e o Balanceador de carga.

        azure network lb frontend-ip create nrpRG NRPlb NRPfrontendpool -i nrppublicip

2. Configure um conjunto de back-end endereços utilizado para receber o tráfego de entrada a partir do conjunto IP Front-end.

        azure network lb address-pool create NRPRG NRPlb NRPbackendpool

## <a name="create-lb-rules-nat-rules-and-probe"></a>Criar regras LB, regras NAT e sonda

Este exemplo cria os seguintes itens.

- uma regra NAT para traduzir todo o tráfego de entrada na porta 21 à porta 22<sup>1</sup>
- uma regra NAT para traduzir todo o tráfego de entrada na porta 23 à porta 22
- uma regra de Balanceador de carga para equilibrar todo o tráfego de entrada na porta 80 à porta 80 nos endereços no conjunto de back-end.
- uma regra de sonda para verificar o estado de funcionamento de uma página com o nome *HealthProbe.aspx*.

<sup>1</sup> regras NAT estão associadas a uma instância de máquina virtual específica atrás Balanceador de carga. O tráfego de rede que chega à porta, 21 é enviado para uma máquina virtual específica no Porta 22 associado esta regra NAT. Tem de especificar um protocolo (UDP ou TCP) para uma regra NAT. Não podem ser atribuídos ambos os protocolos para a mesma porta.

1. Crie as regras NAT.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh1 --protocol tcp --frontend-port 21 --backend-port 22
        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name nrplb --name ssh2 --protocol tcp --frontend-port 23 --backend-port 22

2. Crie uma regra de Balanceador de carga.

        azure network lb rule create nrprg nrplb lbrule -p tcp -f 80 -b 80 -t NRPfrontendpool -o NRPbackendpool

3. Crie uma sonda de estado de funcionamento.

        azure network lb probe create --resource-group nrprg --lb-name nrplb --name healthprobe --protocol "http" --port 80 --path healthprobe.aspx --interval 15 --count 4

4. Verificar as suas definições.

        azure network lb show nrprg nrplb

    Saída esperada:

        info:    Executing command network lb show
        + Looking up the load balancer "nrplb"
        + Looking up the public ip "NRPPublicIP"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb
        data:    Name                            : nrplb
        data:    Type                            : Microsoft.Network/loadBalancers
        data:    Location                        : eastus
        data:    Provisioning State              : Succeeded
        data:    Frontend IP configurations:
        data:      Name                          : NRPfrontendpool
        data:      Provisioning state            : Succeeded
        data:      Public IP address id          : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/publicIPAddresses/NRPPublicIP
        data:      Public IP allocation method   : Static
        data:      Public IP address             : 40.114.13.145
        data:
        data:    Backend address pools:
        data:      Name                          : NRPbackendpool
        data:      Provisioning state            : Succeeded
        data:
        data:    Load balancing rules:
        data:      Name                          : HTTP
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 80
        data:      Backend port                  : 80
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:      Backend address pool          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:
        data:    Inbound NAT rules:
        data:      Name                          : ssh1
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 21
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:      Name                          : ssh2
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Tcp
        data:      Frontend port                 : 23
        data:      Backend port                  : 22
        data:      Enable floating IP            : false
        data:      Idle timeout in minutes       : 4
        data:      Frontend IP configuration     : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/frontendIPConfigurations/NRPfrontendpool
        data:
        data:    Probes:
        data:      Name                          : healthprobe
        data:      Provisioning state            : Succeeded
        data:      Protocol                      : Http
        data:      Port                          : 80
        data:      Interval in seconds           : 15
        data:      Number of probes              : 4
        data:
        info:    network lb show command OK

## <a name="create-nics"></a>Criar NIC

Precisa de criar NIC (ou modificar os existentes) e associá-las para regras, as regras do Balanceador de carga e sondas NAT.

1. Criar uma NIC denominada *lb nic1 ser*e associá-lo a regra NAT *rdp1* e o conjunto de endereços de back-end *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" eastus

    Saída esperada:

        info:    Executing command network nic create
        + Looking up the network interface "lb-nic1-be"
        + Looking up the subnet "nrpvnetsubnet"
        + Creating network interface "lb-nic1-be"
        + Looking up the network interface "lb-nic1-be"
        data:    Id                              : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/networkInterfaces/lb-nic1-be
        data:    Name                            : lb-nic1-be
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : eastus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 10.0.0.4
        data:      Private IP Allocation Method  : Dynamic
        data:      Subnet                        : /subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet
        data:      Load balancer backend address pools
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool
        data:      Load balancer inbound NAT rules:
        data:        Id                          : /subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1
        data:
        info:    network nic create command OK

2. Criar uma NIC denominada *lb nic2 ser*e associá-lo a regra NAT *rdp2* e o conjunto de endereços de back-end *NRPbackendpool* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/NRPbackendpool" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" eastus

3. Criar uma máquina de virtual (VM) com o nome *web1*e associá-lo a NIC denominada *lb nic1 ser*. Uma conta de armazenamento denominada *web1nrp* foi criada antes de executar o comando abaixo.

        azure vm create --resource-group nrprg --name web1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] VMs num Balanceador de carga necessitam de ser o mesmo conjunto de disponibilidade. Utilizar `azure availset create` para criar a disponibilidade de uma conjunto.

    O resultado deverá ser semelhante ao seguinte:

        info:    Executing command vm create
        + Looking up the VM "web1"
        Enter username: azureuser
        Enter password for azureuser: *********
        Confirm password: *********
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account web1nrp
        + Looking up the availability set "nrp-avset"
        info:    Found an Availability set "nrp-avset"
        + Looking up the NIC "lb-nic1-be"
        info:    Found an existing NIC "lb-nic1-be"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/####################################/resourceGroups/NRPRG/providers/Microsoft.Network/virtualNetworks/NRPVnet/subnets/NRPVnetSubnet" in the NIC "lb-nic1-be"
        info:    This is a NIC without publicIP configured
        + Creating VM "web1"
        info:    vm create command OK

    >[AZURE.NOTE] A mensagem informativa que **Este é uma NIC sem publicIP configurado** é esperada desde o NIC criado para o Balanceador de carga ligar à Internet utilizando o endereço de IP público do carregamento balanceador.

    Uma vez que o *lb nic1 ser* NIC está associada a regra NAT *rdp1* , pode ligar a *web1* utilizando o RDP através de porta 3441 no balanceador de carga.

4. Criar uma máquina de virtual (VM) com o nome *web2*e associá-lo a NIC denominada *lb nic2 ser*. Uma conta de armazenamento denominada *web1nrp* foi criada antes de executar o comando abaixo.

        azure vm create --resource-group nrprg --name web2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="update-an-existing-load-balancer"></a>Atualizar um balanceador de carga existente

Pode adicionar regras referenciar uma Balanceador de carga existente. No exemplo seguinte, é adicionada uma nova regra de Balanceador de carga para um existente Balanceador de carga **NRPlb**

    azure network lb rule create --resource-group nrprg --lb-name nrplb --name lbrule2 --protocol tcp --frontend-port 8080 --backend-port 8051 --frontend-ip-name frontendnrppool --backend-address-pool-name NRPbackendpool

## <a name="delete-a-load-balancer"></a>Eliminar um balanceador de carga

Utilize o seguinte comando para remover um balanceador de carga:

    azure network lb delete --resource-group nrprg --name nrplb

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-cli.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
