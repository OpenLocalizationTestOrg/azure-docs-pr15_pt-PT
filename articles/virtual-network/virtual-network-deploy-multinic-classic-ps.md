<properties
   pageTitle="Implementar multi NIC VMs através do PowerShell no modelo de implementação clássica | Microsoft Azure"
   description="Saiba como implementar multi NIC VMs através do PowerShell no modelo de implementação clássico"
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

#<a name="deploy-multi-nic-vms-classic-using-powershell"></a>Implementar multi NIC VMs (clássico) através do PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

Pode criar máquinas virtuais (VMs) no Azure e anexar várias interfaces de rede (NIC) a cada um dos seus VMs. NIC vários Activar separação de tipos de tráfego ao longo NIC. Por exemplo, um NIC pode comunicar com a Internet, enquanto outro comunica apenas com recursos internos não ligados à Internet. A capacidade para separar o tráfego de rede através de vários NIC é necessária para muitos eletrodomésticos de virtual rede, tal como a entrega da aplicação e soluções de otimização WAN.

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Atualmente, não pode ter VMs com uma única NIC e VMs com vários NIC no serviço de nuvem do mesmo. Por isso, terá de lhe implementar os servidores de back-end num serviço em nuvem diferente que e todos os outros componentes no cenário. Os passos abaixo utilizam um serviço na nuvem denominado *IaaSStory* relativamente às principais e recursos *IaaSStory-back-end* para os servidores de back-end.

## <a name="prerequisites"></a>Pré-requisitos

Antes de poder implementar os servidores de back-end, é necessário implementar o serviço de nuvem principal com todos os recursos necessários para este cenário. No mínimo, tem de criar uma rede virtual com uma sub-rede para o back-end. Aceda a [criar uma rede virtual utilizando o PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) para saber como implementar uma rede virtual.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="deploy-the-back-end-vms"></a>Implementar o VMs back-end

Os VMs back-end variam consoante a criação de recursos listados abaixo.

- **Sub-rede back-end**. Os servidores de base de dados farão parte do sub-rede separada, segregar tráfego. O script abaixo espera nesta sub-rede existir num vnet denominada *WTestVnet*.
- **Conta de armazenamento para discos de dados**. Para um melhor desempenho, discos dados nos servidores de base de dados irão utilizar tecnologia de unidade (SSD) estado sólido que requer uma conta de armazenamento premium. Certifique-se a localização do Azure implementar para suportar o armazenamento de premium.
- **Definir disponibilidade**. Serão adicionados todos os servidores de base de dados para uma única disponibilidade conjunto, para garantir que pelo menos um das VMs está para cima e a executar durante a manutenção.

### <a name="step-1---start-your-script"></a>Passo 1 - iniciar o script

Pode transferir o script PowerShell completo utilizado [aqui](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/virtual-network-deploy-multinic-classic-ps.ps1). Siga os passos abaixo para alterar o script para trabalhar no seu ambiente.

1. Altere os valores das variáveis abaixo com base no seu grupo de recursos existente implementado acima na [Pré-requisitos](#Prerequisites).

        $location              = "West US"
        $vnetName              = "WTestVNet"
        $backendSubnetName     = "BackEnd"

2. Altere os valores das variáveis abaixo com base nos valores que pretende utilizar para a sua implementação de back-end.

        $backendCSName         = "IaaSStory-Backend"
        $prmStorageAccountName = "iaasstoryprmstorage"
        $avSetName             = "ASDB"
        $vmSize                = "Standard_DS3"
        $diskSize              = 127
        $vmNamePrefix          = "DB"
        $dataDiskSuffix        = "datadisk"
        $ipAddressPrefix       = "192.168.2."
        $numberOfVMs           = 2

### <a name="step-2---create-necessary-resources-for-your-vms"></a>Passo 2 - criar recursos necessários para o seu VMs

Tem de criar um novo serviço em nuvem e uma conta de armazenamento para os discos de dados para todas as VMs. Também precisa de especificar uma imagem e uma conta de administrador local para os VMs. Para criar estes recursos, execute os passos seguintes.

1. Crie um novo serviço na nuvem.

        New-AzureService -ServiceName $backendCSName -Location $location

2. Crie uma nova conta de armazenamento de premium.

        New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
            -Location $location `
            -Type Premium_LRS

3. Defina a conta de armazenamento criada anteriormente, como a conta de armazenamento atual para a sua subscrição.

        $subscription = Get-AzureSubscription `
            | where {$_.IsCurrent -eq $true}  
        Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
            -CurrentStorageAccountName $prmStorageAccountName

4. Selecione uma imagem para a VM.

        $image = Get-AzureVMImage `
            | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
            | sort PublishedDate -Descending `
            | select -ExpandProperty ImageName -First 1

5. Defina o administrador local credenciais de conta.

        $cred = Get-Credential -Message "Enter username and password for local admin account"

### <a name="step-3---create-vms"></a>Passo 3 - criar VMs

Tem de utilizar um ciclo para tantas VMs à medida que pretende e criar a NIC e VMs necessárias dentro do ciclo de criar. Para criar a NIC e VMs, execute os passos seguintes.

1. Iniciar uma `for` Repetir continuamente a repetir os comandos para criar uma VM e duas NIC as vezes conforme necessário, com base em valores da `$numberOfVMs` variável.

        for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Criar um `VMConfig` objeto especificando a imagem, tamanho e disponibilidade definido para a VM.

            $vmName = $vmNamePrefix + $suffixNumber
            $vmConfig = New-AzureVMConfig -Name $vmName `
                            -ImageName $image `
                            -InstanceSize $vmSize `
                            -AvailabilitySetName $avSetName  

3. Aprovisione a VM como um VM do Windows.

            Add-AzureProvisioningConfig -VM $vmConfig -Windows `
                -AdminUsername $cred.UserName `
                -Password $cred.Password

4. Predefinir o NIC e atribuí-la um endereço IP estático.

            Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
            Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Adicione uma segunda NIC para cada VM.

            Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
                -SubnetName $backendSubnetName `
                -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
                -VM $vmConfig

6. Crie a discos de dados para cada VM.

            $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk1Name `
                -LUN 0       

            $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
            Add-AzureDataDisk -CreateNew -VM $vmConfig `
                -DiskSizeInGB $diskSize `
                -DiskLabel $dataDisk2Name `
                -LUN 1

7. Crie cada VM e o ciclo de fim.

            New-AzureVM -VM $vmConfig `
                -ServiceName $backendCSName `
                -Location $location `
                -VNetName $vnetName
        }

### <a name="step-4---run-the-script"></a>Passo 4 - executar o script

Agora que transferiu e alteradas o script com base nas suas necessidades, execução ele script para criar o back-end VMs de base de dados com vários NIC.

1. Guarde o script e executá-la a partir da linha de comandos do **PowerShell** ou **O ISE do PowerShell**. Irá ver o resultado inicial, conforme apresentado abaixo.

        OperationDescription    OperationId                          OperationStatus
        --------------------    -----------                          ---------------
        New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
        New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      

        WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Preencha as informações necessárias na linha de comandos do credenciais e clique em **OK**. Será apresentado o resultado abaixo.

        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
        New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
