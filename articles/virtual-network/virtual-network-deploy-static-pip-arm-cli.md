<properties 
   pageTitle="Implementar uma VM com um endereço IP público estático utilizando o clip do Azure no Gestor de recursos | Microsoft Azure"
   description="Saiba como implementar VMs com um endereço IP público estático utilizando o clip do Azure no Gestor de recursos"
   services="virtual-network"
   documentationCenter="na"
   authors="jimdial"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/15/2016"
   ms.author="jdial" />

# <a name="deploy-a-vm-with-a-static-public-ip-using-the-azure-cli"></a>Implementar uma VM com um endereço IP público estático utilizando o clip do Azure

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="step-1---start-your-script"></a>Passo 1 - iniciar o script

Pode transferir o script de festa completo utilizado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/virtual-network-deploy-static-pip-arm-cli.sh). Siga os passos abaixo para alterar o script para trabalhar no seu ambiente.

1. Altere os valores das variáveis abaixo com base nos valores que pretende utilizar para a sua implementação. Os valores abaixo mapa para o cenário utilizado neste documento.

        # Set variables for the new resource group
        rgName="IaaSStory"
        location="westus"
        
        # Set variables for VNet
        vnetName="TestVNet"
        vnetPrefix="192.168.0.0/16"
        subnetName="FrontEnd"
        subnetPrefix="192.168.1.0/24"
        
        # Set variables for storage
        stdStorageAccountName="iaasstorystorage"
        
        # Set variables for VM
        vmSize="Standard_A1"
        diskSize=127
        publisher="Canonical"
        offer="UbuntuServer"
        sku="14.04.2-LTS"
        version="latest"
        vmName="WEB1"
        osDiskName="osdisk"
        nicName="NICWEB1"
        privateIPAddress="192.168.1.101"
        username='adminuser'
        password='adminP@ssw0rd'
        pipName="PIPWEB1"
        dnsName="iaasstoryws1"

## <a name="step-2---create-the-necessary-resources-for-your-vm"></a>Passo 2 - criar os recursos necessários para o seu VM

Antes de criar uma VM, precisa de um grupo de recursos, VNet, endereço IP público e NIC para ser utilizado pela VM.

1. Crie um novo grupo de recursos.

        azure group create $rgName $location
        
2. Crie a VNet e sub-rede.
        
        azure network vnet create --resource-group $rgName \
            --name $vnetName \
            --address-prefixes $vnetPrefix \
            --location $location
        azure network vnet subnet create --resource-group $rgName \
            --vnet-name $vnetName \
            --name $subnetName \
            --address-prefix $subnetPrefix

3. Crie o recurso IP público. 

        azure network public-ip create --resource-group $rgName \
            --name $pipName \
            --location $location \
            --allocation-method Static \
            --domain-name-label $dnsName 

4. Crie a interface de rede (NIC) para a VM na sub-rede criada acima, com o endereço IP público. Aviso do primeiro conjunto de comandos são utilizadas para obter o **Id** da sub-rede criado anteriormente.

        subnetId="$(azure network vnet subnet show --resource-group $rgName \
                        --vnet-name $vnetName \
                        --name $subnetName|grep Id)"

        subnetId=${subnetId#*/}
        
        azure network nic create --name $nicName \
            --resource-group $rgName \
            --location $location \
            --private-ip-address $privateIPAddress \
            --subnet-id $subnetId \
            --public-ip-name $pipName

    >[AZURE.TIP] O primeiro comando acima utiliza [grep](http://tldp.org/LDP/Bash-Beginners-Guide/html/sect_04_02.html) e [manipulação de cadeia](http://tldp.org/LDP/abs/html/string-manipulation.html) (mais especificamente, remoção subcadeia). 

5. Crie uma conta de armazenamento para o anfitrião da unidade VM SO.

        azure storage account create $stdStorageAccountName \
            --resource-group $rgName \
            --location $location --type LRS 

## <a name="step-3---create-the-vm"></a>Passo 3 - criar a VM 

Agora que todos os recursos necessários estão no local, pode criar uma nova VM.

1. Crie a VM.

        azure vm create --resource-group $rgName \
            --name $vmName \
            --location $location \
            --vm-size $vmSize \
            --subnet-id $subnetId \
            --nic-names $nicName \
            --os-type linux \
            --image-urn $publisher:$offer:$sku:$version \
            --storage-account-name $stdStorageAccountName \
            --storage-account-container-name vhds \
            --os-disk-vhd $osDiskName.vhd \
            --admin-username $username \
            --admin-password $password

2. Guarde o ficheiro de script.

## <a name="step-4---run-the-script"></a>Passo 4 - executar o script

Após efetuar as alterações necessárias e, compreensão o script Mostrar acima, execute o script. 

1. A partir da consola de festa, execute o script acima.

        sh myscript.sh

2. O resultado abaixo deve ser apresentado depois de alguns minutos.

        info:    Executing command group create
        info:    Getting resource group IaaSStory
        info:    Creating resource group IaaSStory
        info:    Created resource group IaaSStory
        data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory
        data:    Name:                IaaSStory
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
        info:    Executing command network vnet create
        info:    Looking up virtual network "TestVNet"
        info:    Creating virtual network "TestVNet"
        info:    Loading virtual network state
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : westus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK
        info:    Executing command network vnet subnet create
        info:    Looking up the subnet "FrontEnd"
        info:    Creating subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK
        info:    Executing command network public-ip create
        info:    Looking up the public ip "PIPWEB1"
        info:    Creating public ip address "PIPWEB1"
        info:    Looking up the public ip "PIPWEB1"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
        data:    Name                            : PIPWEB1
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Static
        data:    Idle timeout                    : 4
        data:    IP Address                      : 40.78.63.253
        data:    Domain name label               : iaasstoryws1
        data:    FQDN                            : iaasstoryws1.westus.cloudapp.azure.com
        info:    network public-ip create command OK
        info:    Executing command network nic create
        info:    Looking up the network interface "NICWEB1"
        info:    Looking up the public ip "PIPWEB1"
        info:    Creating network interface "NICWEB1"
        info:    Looking up the network interface "NICWEB1"
        data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/networkInterfaces/NICWEB1
        data:    Name                            : NICWEB1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory/providers/Microsoft.Network/publicIPAddresses/PIPWEB1
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/IaaSStory2/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK
        info:    Executing command storage account create
        info:    Creating storage account
        info:    storage account create command OK
        info:    Executing command vm create
        info:    Looking up the VM "WEB1"
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        info:    Looking up the storage account iaasstorystorage
        info:    Looking up the NIC "NICWEB1"
        info:    Creating VM "WEB1"
        info:    vm create command OK
