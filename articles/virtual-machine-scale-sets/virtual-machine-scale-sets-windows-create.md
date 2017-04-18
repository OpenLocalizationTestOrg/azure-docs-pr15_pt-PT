<properties
    pageTitle="Criar um conjunto de escala de Máquina Virtual através do PowerShell | Microsoft Azure"
    description="Criar um conjunto de escala de Máquina Virtual através do PowerShell"
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="davidmu"/>

# <a name="create-a-windows-virtual-machine-scale-set-using-azure-powershell"></a>Criar uma escala de máquina virtual Windows definir através do PowerShell do Azure

Estes passos, siga uma abordagem de preencher-em-de-células em branco para criar um conjunto de escala Azure máquina virtual. Ver [Conjuntos de descrição geral do escala Máquina Virtual](virtual-machine-scale-sets-overview.md) . o para saber mais acerca de conjuntos de escala.

Deverá demorar cerca de 30 minutos a execute os passos neste artigo.

## <a name="step-1-install-azure-powershell"></a>Passo 1: Instalar o Azure PowerShell

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a sua subscrição e iniciar sessão na sua conta.

## <a name="step-2-create-resources"></a>Passo 2: Criar recursos

Crie os recursos que são necessários para o novo conjunto de escala.

### <a name="resource-group"></a>Grupo de recursos

Um conjunto de escala de máquina virtual tem de estar contido num grupo de recursos.

1. Obter uma lista das localizações disponíveis onde pode colocar recursos:

        Get-AzureLocation | Sort Name | Select Name

2. Selecione uma localização que funciona melhor para si, substitua o valor de **$locName** com esse nome de localização e, em seguida, criar a variável de:

        $locName = "location name from the list, such as Central US"

3. Substitua o valor de **$rgName** com o nome que pretende utilizar para o novo grupo de recursos e, em seguida, criar a variável de: 

        $rgName = "resource group name"
        
4. Crie o grupo de recursos:
    
        New-AzureRmResourceGroup -Name $rgName -Location $locName

    Deverá visualizar algo parecido com este exemplo:

        ResourceGroupName : myrg1
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        ResourceId        : /subscriptions/########-####-####-####-############/resourceGroups/myrg1

### <a name="storage-account"></a>Conta de armazenamento

Uma conta de armazenamento é utilizada por uma máquina virtual para armazenar o disco do sistema operativo e diagnóstico dados utilizados para dimensionamento. Sempre que possível, é melhor prática ter uma conta de armazenamento para cada máquina virtual criada num conjunto de escala. Se não for possível, plano para o mais do que 20 VMs por conta de armazenamento. O exemplo neste artigo mostra três contas de armazenamento a ser criadas para três máquinas virtuais.

1. Substitua o valor de **$saName** com um nome para a conta de armazenamento. Teste o nome para a exclusividade. 

        $saName = "storage account name"
        Get-AzureRmStorageAccountNameAvailability $saName

    Se a resposta for **Verdadeiro**, o seu nome de proposta é exclusivo.

3. Substitua o valor de **$saType** com o tipo de conta de armazenamento e, em seguida, crie a variável de:  

        $saType = "storage account type"
        
    Os valores possíveis são: Standard_LRS, Standard_GRS, Standard_RAGRS ou Premium_LRS.
        
4. Crie a conta:
    
        New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

    Deverá visualizar algo parecido com este exemplo:

        ResourceGroupName   : myrg1
        StorageAccountName  : myst1
        Id                  : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microsoft
                              .Storage/storageAccounts/myst1
        Location            : centralus
        AccountType         : StandardLRS
        CreationTime        : 3/15/2016 4:51:52 PM
        CustomDomain        :
        LastGeoFailoverTime :
        PrimaryEndpoints    : Microsoft.Azure.Management.Storage.Models.Endpoints
        PrimaryLocation     : centralus
        ProvisioningState   : Succeeded
        SecondaryEndpoints  :
        SecondaryLocation   :
        StatusOfPrimary     : Available
        StatusOfSecondary   :
        Tags                : {}
        Context             : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext

5. Repita os passos 1 a 4 para criar três contas de armazenamento, por exemplo myst1, myst2 e myst3.

### <a name="virtual-network"></a>Rede virtual

Uma rede virtual é necessária para as máquinas virtuais do conjunto de escala.

1. Substitua o valor de **$subnetName** com o nome que pretende utilizar para a sub-rede na rede virtual e, em seguida, criar a variável de: 

        $subnetName = "subnet name"
        
2. Crie a configuração de sub-rede:
    
        $subnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
        
    O prefixo de endereço pode ser diferente na sua rede virtual.

3. Substitua o valor de **$netName** com o nome que pretende utilizar para a rede virtual e, em seguida, criar a variável de: 

        $netName = "virtual network name"
        
4. Crie a rede virtual:
    
        $vnet = New-AzureRmVirtualNetwork -Name $netName -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $subnet

### <a name="configuration-of-the-scale-set"></a>Configuração do conjunto de escala

Tem de todos os recursos que precisa para a escala de conjunto de configuração, por isso, vamos criá-lo.  

1. Substitua o valor de **$ipName** com o nome que pretende utilizar para a configuração de IP e, em seguida, criar a variável de: 

        $ipName = "IP configuration name"
        
2. Crie a configuração de IP:

        $ipConfig = New-AzureRmVmssIpConfig -Name $ipName -LoadBalancerBackendAddressPoolsId $null -SubnetId $vnet.Subnets[0].Id

2. Substitua o valor de **$vmssConfig** com o nome que pretende utilizar para a configuração do conjunto de escala e, em seguida, criar a variável de:   

        $vmssConfig = "Scale set configuration name"
        
3. Crie a configuração para o conjunto de escala:

        $vmss = New-AzureRmVmssConfig -Location $locName -SkuCapacity 3 -SkuName "Standard_A0" -UpgradePolicyMode "manual"
        
    Este exemplo mostra que uma escala de configurar a ser criado com três máquinas virtuais. Consulte o artigo [Descrição geral de conjuntos de escala de Máquina Virtual](virtual-machine-scale-sets-overview.md) para obter mais informações acerca da capacidade de conjuntos de escala. Este passo também inclui a definir o tamanho (designado por SkuName) das máquinas virtuais do conjunto. Para localizar um tamanho adequado às suas necessidades, veja [tamanhos para máquinas virtuais](../virtual-machines/virtual-machines-windows-sizes.md).
    
4. Adicione a configuração da interface de rede à configuração do conjunto de escala:
        
        Add-AzureRmVmssNetworkInterfaceConfiguration -VirtualMachineScaleSet $vmss -Name $vmssConfig -Primary $true -IPConfiguration $ipConfig
        
    Deverá visualizar algo parecido com este exemplo:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     :
        OverProvision         :
        Id                    :
        Name                  :
        Type                  :
        Location              : Central US
        Tags                  :

#### <a name="operating-system-profile"></a>Perfil do sistema operativo

1. Substitua o valor de **$computerName** o prefixo do nome de computador que pretende utilizar e, em seguida, criar a variável de: 

        $computerName = "computer name prefix"
        
2. Substitua o valor de **$adminName** o nome da conta de administrador nas máquinas virtuais e, em seguida, crie a variável de:

        $adminName = "administrator account name"
        
3. Substitua o valor de **$adminPassword** com a palavra-passe da conta e, em seguida, crie a variável de:

        $adminPassword = "password for administrator accounts"
        
4. Crie o perfil do sistema operativo:

        Set-AzureRmVmssOsProfile -VirtualMachineScaleSet $vmss -ComputerNamePrefix $computerName -AdminUsername $adminName -AdminPassword $adminPassword

#### <a name="storage-profile"></a>Perfil de armazenamento

1. Substitua o valor de **$storageProfile** com o nome que pretende utilizar para o perfil de armazenamento e, em seguida, criar a variável de:  

        $storageProfile = "storage profile name"
        
2. Crie as variáveis que definem a imagem para utilizar:  
      
        $imagePublisher = "MicrosoftWindowsServer"
        $imageOffer = "WindowsServer"
        $imageSku = "2012-R2-Datacenter"
        
    Para localizar as informações sobre outras imagens a utilizar, veja [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e o clip do Azure](../virtual-machines/virtual-machines-windows-cli-ps-findimage.md).
        
3. Substitua o valor de **$vhdContainers** com uma lista que contém os caminhos de onde são armazenados os discos rígido virtuais, tal como "https://mystorage.blob.core.windows.net/vhds" e, em seguida, crie a variável de:
       
        $vhdContainers = @("https://myst1.blob.core.windows.net/vhds","https://myst2.blob.core.windows.net/vhds","https://myst3.blob.core.windows.net/vhds")
        
4. Crie o perfil de armazenamento:

        Set-AzureRmVmssStorageProfile -VirtualMachineScaleSet $vmss -ImageReferencePublisher $imagePublisher -ImageReferenceOffer $imageOffer -ImageReferenceSku $imageSku -ImageReferenceVersion "latest" -Name $storageProfile -VhdContainer $vhdContainers -OsDiskCreateOption "FromImage" -OsDiskCaching "None"  

### <a name="virtual-machine-scale-set"></a>Conjunto de escala de máquina virtual

Por fim, pode criar o conjunto de escala.

1. Substitua o valor de **$vmssName** com o nome do conjunto de escala de máquina virtual e, em seguida, crie a variável de:

        $vmssName = "scale set name"
        
2. Crie o conjunto de escala:

        New-AzureRmVmss -ResourceGroupName $rgName -Name $vmssName -VirtualMachineScaleSet $vmss

    Deverá visualizar algo parecido com este exemplo que mostra uma implementação com êxito:

        Sku                   : Microsoft.Azure.Management.Compute.Models.Sku
        UpgradePolicy         : Microsoft.Azure.Management.Compute.Models.UpgradePolicy
        VirtualMachineProfile : Microsoft.Azure.Management.Compute.Models.VirtualMachineScaleSetVMProfile
        ProvisioningState     : Updating
        OverProvision         :
        Id                    : /subscriptions/########-####-####-####-############/resourceGroups/myrg1/providers/Microso
                                ft.Compute/virtualMachineScaleSets/myvmss1
        Name                  : myvmss1
        Type                  : Microsoft.Compute/virtualMachineScaleSets
        Location              : centralus
        Tags                  :

## <a name="step-3-explore-resources"></a>Passo 3: Explorar recursos

Utilize estes recursos para explorar o conjunto de escala de máquina virtual que criou:

- Portal do Azure - uma quantidade limitada de informações estiver disponível através do portal.
- [Azure recurso Explorer](https://resources.azure.com/) - esta ferramenta é adequado para a explorar o estado atual do seu conjunto de escala.
- Azure PowerShell - Utilize este comando para obter informações:

        Get-AzureRmVmss -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        
        Or 
        
        Get-AzureRmVmssVM -ResourceGroupName "resource group name" -VMScaleSetName "scale set name"
        

## <a name="next-steps"></a>Próximos passos

- Gerir o conjunto de escala que que acabou de criar utilizando as informações em [gerir máquinas virtuais num conjunto de escala de Máquina Virtual](virtual-machine-scale-sets-windows-manage.md)
- Considere a configuração escala automática da sua escala definir utilizando informações nos [conjuntos de escala de dimensionamento e máquina virtual automáticas](virtual-machine-scale-sets-autoscale-overview.md)
- Saiba mais sobre o dimensionamento vertical ao rever [autoscale Vertical com conjuntos de escala de Máquina Virtual](virtual-machine-scale-sets-vertical-scale-reprovision.md)
