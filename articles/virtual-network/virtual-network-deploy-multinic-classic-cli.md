<properties
   pageTitle="Implementar multi NIC VMs utilizando o clip do Azure no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como implementar multi NIC VMs utilizando o clip do Azure no modelo de implementação clássico"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/02/2016"
   ms.author="jdial" />

#<a name="deploy-multi-nic-vms-classic-using-the-azure-cli"></a>Implementar multi NIC VMs (clássico) utilizando o clip do Azure

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NIC) a cada um dos seus VMs. NIC vários Activar separação de tipos de tráfego ao longo NIC. Por exemplo, um NIC pode comunicar com a Internet, enquanto outro comunica apenas com recursos internos não ligados à Internet. A capacidade para separar o tráfego de rede através de vários NIC é necessária para muitos eletrodomésticos de virtual rede, tal como a entrega da aplicação e soluções de otimização WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Atualmente, não pode ter VMs com uma única NIC e VMs com vários NIC no serviço de nuvem do mesmo. Por isso, terá de lhe implementar os servidores de back-end num serviço em nuvem diferente que e todos os outros componentes no cenário. Os passos abaixo utilizam um serviço na nuvem denominado *IaaSStory* relativamente às principais e recursos *IaaSStory-back-end* para os servidores de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder implementar os servidores de back-end, é necessário implementar o serviço de nuvem principal com todos os recursos necessários para este cenário. No mínimo, tem de criar uma rede virtual com uma sub-rede para o back-end. Aceda a [criar uma rede virtual utilizando o clip do Azure](virtual-networks-create-vnet-classic-cli.md) para saber como implementar uma rede virtual.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementar o VMs back-end

Os VMs back-end variam consoante a criação de recursos listados abaixo.

- **Conta de armazenamento para discos de dados**. Para um melhor desempenho, discos dados nos servidores de base de dados irão utilizar tecnologia de unidade (SSD) estado sólido que requer uma conta de armazenamento premium. Certifique-se a localização do Azure implementar para suportar o armazenamento de premium.
- **NIC**. Cada VM terão duas NIC, uma para acesso de base de dados e outro para gestão.
- **Definir disponibilidade**. Serão adicionados todos os servidores de base de dados para uma única disponibilidade conjunto, para garantir que pelo menos um das VMs está para cima e a executar durante a manutenção.

### <a name="step-1---start-your-script"></a>Passo 1 - iniciar o script

Pode transferir o script de festa completo utilizado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-cli.sh). Siga os passos abaixo para alterar o script para trabalhar no seu ambiente.

1. Altere os valores das variáveis abaixo com base no seu grupo de recursos existente implementado acima na [Pré-requisitos](#Prerequisites).

        location="useast2"
        vnetName="WTestVNet"
        backendSubnetName="BackEnd"

2. Altere os valores das variáveis abaixo com base nos valores que pretende utilizar para a sua implementação de back-end.

        backendCSName="IaaSStory-Backend"
        prmStorageAccountName="iaasstoryprmstorage"
        image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
        avSetName="ASDB"
        vmSize="Standard_DS3"
        diskSize=127
        vmNamePrefix="DB"
        osDiskName="osdiskdb"
        dataDiskPrefix="db"
        dataDiskName="datadisk"
        ipAddressPrefix="192.168.2."
        username='adminuser'
        password='adminP@ssw0rd'
        numberOfVMs=2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passo 2 - criar recursos necessários para o seu VMs

1. Crie um novo serviço de nuvem para todos os VMs de back-end. Repare que a utilização do `$backendCSName` variável para o nome do grupo de recursos, e `$location` Azure à região.

        azure service create --serviceName $backendCSName \
            --location $location

2. Crie uma conta de armazenamento de premium do sistema operativo e dados discos a ser utilizadas pelo seu VMs.

        azure storage account create $prmStorageAccountName \
            --location $location \
            --type PLRS

### <a name="step-3---create-vms-with-multiple-nics"></a>Passo 3 - criar VMs com vários NIC

1. Iniciar um ciclo para criar múltiplas VMs, com base na `numberOfVMs` variáveis.

        for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
        do

2. Para cada VM, especifique o nome e endereço IP de cada uma das duas NIC.

            nic1Name=$vmNamePrefix$suffixNumber-DA
            x=$((suffixNumber+3))
            ipAddress1=$ipAddressPrefix$x

            nic2Name=$vmNamePrefix$suffixNumber-RA
            x=$((suffixNumber+53))
            ipAddress2=$ipAddressPrefix$x

4. Crie a VM. Repare que a utilização do `--nic-config` parâmetro, que contém uma lista de todos os NIC com nome, sub-rede e endereço IP.

            azure vm create $backendCSName $image $username $password \
                --connect $backendCSName \
                --vm-name $vmNamePrefix$suffixNumber \
                --vm-size $vmSize \
                --availability-set $avSetName \
                --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
                --virtual-network-name $vnetName \
                --subnet-names $backendSubnetName \
                --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Para cada VM, crie dois discos de dados.

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd

            azure vm disk attach-new $vmNamePrefix$suffixNumber \
                $diskSize \
                vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
        done

### <a name="step-4---run-the-script"></a>Passo 4 - executar o script

Agora que transferiu e alteradas o script com base nas suas necessidades, execute o script para criar o back-end VMs de base de dados com vários NIC.

1. Guarde o script e executá-la a partir do seu terminal **festa** . Irá ver o resultado inicial, conforme apresentado abaixo.

        info:    Executing command service create
        info:    Creating cloud service
        data:    Cloud service name IaaSStory-Backend
        info:    service create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM

2. Depois de alguns minutos, irá terminar a execução e verá o resto da saída, conforme apresentado abaixo.

        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm create
        info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
        info:    Looking up virtual network
        info:    Looking up cloud service
        info:    Getting cloud service properties
        info:    Looking up deployment
        info:    Creating VM
        info:    OK
        info:    vm create command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
        info:    Executing command vm disk attach-new
        info:    Getting virtual machines
        info:    Adding Data-Disk
        info:    vm disk attach-new command OK
