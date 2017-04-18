<properties
    pageTitle="Criar VM a partir de um VHD GRG | Microsoft Azure"
    description="Saiba como criar uma máquina de virtual do Windows a partir de uma imagem VHD GRG através do PowerShell do Azure, no modelo de implementação de Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-generalized-vhd-image"></a>Criar uma VM a partir de uma imagem VHD GRG

Uma imagem VHD GRG teve todas as suas informações de conta pessoal removidas utilizando [Sysprep](virtual-machines-windows-generalize-vhd.md). Pode criar um VHD GRG executando o Sysprep numa VM no local, em seguida, [carregar o VHD para Azure](virtual-machines-windows-upload-image.md), ou executando o Sysprep num VM existente do Azure e, em seguida, [copiar o VHD](virtual-machines-windows-vhd-copy.md).

Se pretender criar uma VM a partir de um VHD especializado, consulte o artigo [criar uma VM a partir de um VHD especializado](virtual-machines-windows-create-vm-specialized.md).

A forma mais rápida para criar uma VM a partir de um VHD GRG é utilizar [guia de introdução do modelo] (https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-from-user-image). 


## <a name="prerequisites"></a>Pré-requisitos

Se passar para utilizar um VHD carregado a partir de um VM no local, como um criada utilizando o Hyper-V, deve certificar-se de que seguiu as instruções no [artigo preparar um VHD do Windows para carregar para o Azure](virtual-machines-windows-prepare-for-upload-vhd-image.md). 

VHDs carregados e existente Azure VM VHDs tem de ser generalized antes de poder criar uma VM através deste método. Para obter mais informações, consulte o artigo [utilizar o Sysprep Generalize uma máquina de virtual do Windows](virtual-machines-windows-generalize-vhd.md). 


## <a name="set-the-uri-of-the-vhd"></a>Definir o URI do VHD

Se encontra o URI para o VHD utilizar no formato: https://**mystorageaccount**.blob.core.windows.net/**mycontainer**/.**MyVhdName**VHD. Neste exemplo VHD denominado **myVHD** está de conta de armazenamento **mystorageaccount** no contentor **mycontainer**.

```powershell
$imageURI = "https://mystorageaccount.blob.core.windows.net/mycontainer/myVhd.vhd"
```


## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Crie a vNet e sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).


1. Crie a sub-rede. O exemplo seguinte cria uma sub-rede denominada **mySubnet** no de grupo de recursos **myResourceGroup** com o prefixo de **10.0.0.0/24**endereço.  

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubnet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```
      
2. Crie a rede virtual. O exemplo seguinte cria uma rede virtual denominada **myVnet** na localização **Dos e.u.a. Ocidental** com o prefixo do endereço de **10.0.0.0/16**.  

    ```powershell
    $location = "West US"
    $vnetName = "myVnet"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-network-interface"></a>Criar uma interface de endereço e de rede IP pública

Para permitir a comunicação com máquina virtual na rede virtual, necessita de um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie um endereço IP público. Este exemplo cria um endereço IP público com o nome **myPip**. 

    ```powershell
    $ipName = "myPip"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Criar NIC. Este exemplo cria uma NIC denominada **myNic**. 

    ```powershell
    $nicName = "myNic"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para poder iniciar sessão no seu VM utilizando o RDP, tem de ter uma regra de segurança que permita acesso RDP na porta 3389. 

Este exemplo cria um NSG denominada **myNsg** que contém uma regra denominada **myRdpRule** que lhe permite tráfego RDP através da porta 3389. Para mais informações sobre NSGs, consulte [portas de abertura para uma VM no Azure através do PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```


## <a name="create-a-variable-for-the-virtual-network"></a>Criar uma variável para a rede virtual

Crie uma variável para a rede virtual concluída. 

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName $rgName -Name $vnetName
```

## <a name="create-the-vm"></a>Criar a VM

O seguinte script do PowerShell mostra como configurar as configurações de máquina virtual e utilizar a imagem VM carregada como a origem da instalação nova.

</br>


```powershell
    # Enter a new user name and password to use as the local administrator account 
    # for remotely accessing the VM.
    $cred = Get-Credential
    
    # Name of the storage account where the VHD is located. This example sets the 
    # storage account name as "myStorageAccount"
    $storageAccName = "myStorageAccount"
    
    # Name of the virtual machine. This example sets the VM name as "myVM".
    $vmName = "myVM"
    
    # Size of the virtual machine. This example creates "Standard_D2_v2" sized VM. 
    # See the VM sizes documentation for more information: 
    # https://azure.microsoft.com/documentation/articles/virtual-machines-windows-sizes/
    $vmSize = "Standard_D2_v2"
    
    # Computer name for the VM. This examples sets the computer name as "myComputer".
    $computerName = "myComputer"
    
    # Name of the disk that holds the OS. This example sets the 
    # OS disk name as "myOsDisk"
    $osDiskName = "myOsDisk"
    
    # Assign a SKU name. This example sets the SKU name as "Standard_LRS"
    # Valid values for -SkuName are: Standard_LRS - locally redundant storage, Standard_ZRS - zone redundant storage, Standard_GRS - geo redundant storage, Standard_RAGRS - read access geo redundant storage, Premium_LRS - premium locally redundant storage. 
    $skuName = "Standard_LRS"
    
    # Get the storage account where the uploaded image is stored
    $storageAcc = Get-AzureRmStorageAccount -ResourceGroupName $rgName -AccountName $storageAccName
    
    # Set the VM name and size
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize
    
    #Set the Windows operating system configuration and add the NIC
    $vm = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $computerName `
        -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm = Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id
    
    # Create the OS disk URI
    $osDiskUri = '{0}vhds/{1}-{2}.vhd' `
        -f $storageAcc.PrimaryEndpoints.Blob.ToString(), $vmName.ToLower(), $osDiskName
    
    # Configure the OS disk to be created from the existing VHD image (-CreateOption fromImage).
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri `
        -CreateOption fromImage -SourceImageUri $imageURI -Windows
    
    # Create the new VM
    New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

## <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada 

Quando estiver concluído, verá a VM recentemente criada no [portal do Azure](https://portal.azure.com) em **Procurar** > **máquinas virtuais**, ou ao utilizar os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximos passos

Para gerir a sua nova máquina virtual com o Azure PowerShell, consulte o artigo [Gerir virtual máquinas utilizando o Gestor de recursos do Azure e PowerShell](virtual-machines-windows-ps-manage.md).


