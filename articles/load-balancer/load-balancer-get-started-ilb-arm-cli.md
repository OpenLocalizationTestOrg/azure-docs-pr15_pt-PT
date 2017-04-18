<properties
   pageTitle="Criar um balanceador de carga interno utilizando o clip do Azure no Gestor de recursos | Microsoft Azure"
   description="Saiba como criar um balanceador de carga interno utilizando o clip do Azure no Gestor de recursos"
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

# <a name="create-an-internal-load-balancer-by-using-the-azure-cli"></a>Criar um balanceador de carga interno utilizando o clip do Azure

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)][modelo de implementação clássica](load-balancer-get-started-ilb-classic-cli.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## <a name="deploy-the-solution-by-using-the-azure-cli"></a>Implementar a solução utilizando o clip do Azure

Os seguintes passos mostram como criar um balanceador de carga de acesso à Internet, utilizando o Gestor de recursos do Azure com clip. Com o Gestor de recursos do Azure, cada recurso é criado e configurado individualmente e, em seguida, colocar em conjunto para criar um recurso.

Terá de criar e configurar os objetos para implementar um balanceador de carga seguintes:

- **Configuração do IP de front-end**: contém endereços IP públicos para o tráfego de rede recebidas
- **Conjunto de endereços de back-end**: contém interfaces de rede (NIC) que permitem as máquinas virtuais para receber o tráfego de rede de Balanceador de carga
- **Regras de balanceamento de carga**: contém regras mapeiam uma porta pública no balanceador de carga à porta no conjunto de endereços de back-end
- **Regras de entrada NAT**: contém regras mapeiam uma porta pública no balanceador de carga para uma porta para uma máquina virtual específica no conjunto de endereços de back-end
- **Sondas**: contém sondas de estado de funcionamento que são utilizadas para verificar a disponibilidade de instâncias de máquinas virtuais no conjunto de endereços de back-end

Para mais informações, consulte o artigo [De suporte para Balanceador de carga de Gestor de recursos do Azure](load-balancer-arm.md).

## <a name="set-up-cli-to-use-resource-manager"></a>Configurar o clip para utilizar o Gestor de recursos

1. Se nunca tiver utilizado o Azure clip, consulte o artigo [instalar e configurar o clip do Azure](../../articles/xplat-cli-install.md). Siga as instruções no ponto onde selecionar a sua conta Azure e subscrição.

2. Execute o comando **azure config modo** para mudar para modo de Gestor de recursos, da seguinte forma:

        azure config mode arm

    Saída esperada:

        info:    New mode is arm

## <a name="create-an-internal-load-balancer-step-by-step"></a>Criar um balanceador de carga interno passo a passo

1. Inicie sessão no Azure.

        azure login

    Quando lhe for pedido, introduza as suas credenciais Azure.

2. Altere as ferramentas de comando para o modo de Gestor de recursos do Azure.

        azure config mode arm

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Todos os recursos no Gestor de recursos do Azure estão associados um grupo de recursos. Se ainda não o feito ainda, crie um grupo de recursos.

    azure group create <resource group name> <location>

## <a name="create-an-internal-load-balancer-set"></a>Criar um conjunto de Balanceador de carga interno

1. Criar um balanceador de carga interno

    No seguinte cenário, é criado um grupo de recursos com o nome nrprg na região do Leste dos EUA.

        azure network lb create --name nrprg --location eastus

    >[AZURE.NOTE] Todos os recursos para uma balanceadores de carga interno, tais como redes virtuais e sub-redes de rede virtual, tem de ser no mesmo grupo de recursos e na mesma região.

2. Crie um endereço IP Front-end para o Balanceador de carga interno.

    Tem de ser o endereço IP que utilizar no intervalo sub-rede da sua rede virtual.

        azure network lb frontend-ip create --resource-group nrprg --lb-name ilbset --name feilb --private-ip-address 10.0.0.7 --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet

3. Crie o conjunto de endereços de back-end.

        azure network lb address-pool create --resource-group nrprg --lb-name ilbset --name beilb

    Depois de definir um endereço IP Front-end e um conjunto de endereços de back-end, pode criar regras de Balanceador de carga, regras de entrada NAT, e estado de funcionamento personalizado sondas.

4. Crie uma regra de Balanceador de carga para o Balanceador de carga interno.

    Ao seguir os passos anteriores, o comando cria uma regra de Balanceador de carga para escutar porta 1433 no conjunto de front-end e também a enviar tráfego de balanceamento de carga de rede para o conjunto de endereços de back-end, utilizando a porta 1433.

        azure network lb rule create --resource-group nrprg --lb-name ilbset --name ilbrule --protocol tcp --frontend-port 1433 --backend-port 1433 --frontend-ip-name feilb --backend-address-pool-name beilb

5. Crie regras de entrada NAT.

    As regras de entrada NAT são utilizadas para criar os pontos finais num Balanceador de carga Ir para uma instância de máquina virtual específica. Os passos anteriores criar duas regras NAT para ambiente de trabalho remoto.

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule1 --protocol TCP --frontend-port 5432 --backend-port 3389

        azure network lb inbound-nat-rule create --resource-group nrprg --lb-name ilbset --name NATrule2 --protocol TCP --frontend-port 5433 --backend-port 3389

6. Crie sondas de estado de funcionamento do Balanceador de carga.

    Uma sonda de estado de funcionamento verifica todas as instâncias de máquina virtual para se certificar de que podem enviar tráfego de rede. A instância de máquina virtual com controlos de sonda falha é removida o Balanceador de carga até vai novamente online e uma marca de verificação sonda determina que é Saudável.

        azure network lb probe create --resource-group nrprg --lb-name ilbset --name ilbprobe --protocol tcp --interval 300 --count 4

    >[AZURE.NOTE]A plataforma do Microsoft Azure utiliza um endereço de IPv4 estático, publicamente encaminhável para uma variedade de cenários administrativos. O endereço IP é 168.63.129.16. Este endereço IP não deve ser bloqueado por qualquer firewalls, uma vez que este pode causar comportamentos inesperados.
    >Relativamente às Azure interna balanceamento de carga, este endereço IP é utilizado por sondas a partir do Balanceador de carga de monitorização para determinar o estado de funcionamento para máquinas virtuais num conjunto de balanceamento de carga. Se um grupo de segurança de rede é utilizado para restringir o tráfego para o Azure máquinas virtuais num conjunto de internamente balanceamento de carga ou é aplicado a uma rede virtual sub-rede, certifique-se de que é adicionada uma regra de segurança de rede para permitir o tráfego de 168.63.129.16.

## <a name="create-nics"></a>Criar NIC

Precisa de criar NIC (ou modificar os existentes) e associá-las para regras, as regras do Balanceador de carga e sondas NAT.

1. Criar uma imagem com o nome *lb nic1 ser*e, em seguida, associá-lo a regra NAT *rdp1* e o conjunto de endereços de back-end *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic1-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp1" --location eastus

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

2. Criar uma imagem com o nome *lb nic2 ser*e, em seguida, associá-lo a regra NAT *rdp2* e o conjunto de endereços de back-end *beilb* .

        azure network nic create --resource-group nrprg --name lb-nic2-be --subnet-name nrpvnetsubnet --subnet-vnet-name nrpvnet --lb-address-pool-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/backendAddressPools/beilb" --lb-inbound-nat-rule-ids "/subscriptions/####################################/resourceGroups/nrprg/providers/Microsoft.Network/loadBalancers/nrplb/inboundNatRules/rdp2" --location eastus

3. Criar uma máquina de virtual *DS1*com o nome e, em seguida, associá-la NIC com o nome *lb nic1 ser*. É criada uma conta de armazenamento denominada *web1nrp* antes de ser executado o seguinte comando:

        azure vm create --resource--resource-grouproup nrprg --name DB1 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic1-be --availset-name nrp-avset --storage-account-name web1nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

    >[AZURE.IMPORTANT] VMs num Balanceador de carga necessitam de ser o mesmo conjunto de disponibilidade. Utilizar `azure availset create` para criar a disponibilidade de uma conjunto.

4. Criar uma máquina de virtual (VM) com o nome *DB2*e, em seguida, associá-la NIC denominada *lb nic2 ser*. Uma conta de armazenamento denominada *web1nrp* foi criada antes de executar o seguinte comando.

        azure vm create --resource--resource-grouproup nrprg --name DB2 --location eastus --vnet-name nrpvnet --vnet-subnet-name nrpvnetsubnet --nic-name lb-nic2-be --availset-name nrp-avset --storage-account-name web2nrp --os-type Windows --image-urn MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:4.0.20150825

## <a name="delete-a-load-balancer"></a>Eliminar um balanceador de carga

Para remover um balanceador de carga, utilize o seguinte comando:

    azure network lb delete --resource-group nrprg --name ilbset

## <a name="next-steps"></a>Próximos passos

[Configurar um modo de distribuição do Balanceador de carga utilizando afinidade IP de origem](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)
