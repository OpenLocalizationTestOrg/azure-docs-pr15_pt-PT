<properties
    pageTitle="Criar uma cópia da sua VM Windows | Microsoft Azure"
    description="Saiba como criar uma cópia da sua VM Azure especializadas a executar o Windows, no modelo de implementação de Gestor de recursos."
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
    ms.date="09/21/2016"
    ms.author="cynthn"/>

# <a name="create-a-vm-from-a-specialized-vhd"></a>Criar uma VM a partir de um VHD especializado

Crie uma nova VM ao como anexar um VHD especializado como o disco de SO através do Powershell. Um VHD especializado mantém as contas de utilizador, aplicações e outros dados de estado a partir do seu VM original. 

Se pretender criar uma VM a partir de um VHD GRG, consulte o artigo [criar uma VM a partir de uma imagem VHD GRG](virtual-machines-windows-create-vm-generalized.md).

## <a name="create-the-subnet-and-vnet"></a>Criar sub-rede e vNet

Crie a vNet e sub-rede da [rede virtual](../virtual-network/virtual-networks-overview.md).

1. Crie a sub-rede. Este exemplo cria uma sub-rede **mySubNet**, no de grupo recurso **myResourceGroup**, com o nome e define o prefixo do endereço sub-rede **10.0.0.0/24**.

    ```powershell
    $rgName = "myResourceGroup"
    $subnetName = "mySubNet"
    $singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24
    ```

2. Crie o vNet. Este exemplo define o nome de rede virtual para ser **myVnetName**, a localização para **US oeste**e o prefixo do endereço para a rede para **10.0.0.0/16**virtual. 

    ```powershell
    $location = "West US"
    $vnetName = "myVnetName"
    $vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
    ```    
            
## <a name="create-a-public-ip-address-and-nic"></a>Criar um endereço IP público e NIC

Para permitir a comunicação com máquina virtual na rede virtual, necessita de um [endereço IP público](../virtual-network/virtual-network-ip-addresses-overview-arm.md) e uma interface de rede.

1. Crie o endereço IP público. Neste exemplo, o nome do endereço IP público está definido para **myIP**.

    ```powershell
    $ipName = "myIP"
    $pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location -AllocationMethod Dynamic
    ```       

2. Criar NIC. Neste exemplo, o nome NIC está definido para **myNicName**.

    ```powershell
    $nicName = "myNicName"
    $nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $location -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
    ```

## <a name="create-the-network-security-group-and-an-rdp-rule"></a>Criar o grupo de segurança de rede e uma regra RDP

Para poder iniciar sessão no seu VM utilizando o RDP, tem de ter uma regra de segurança que permita acesso RDP na porta 3389. Porque o VHD para a nova VM foi criado a partir de uma existente VM especializada, depois da VM é criada pode utilizar uma conta existente a partir do computador virtual origem que tinham permissão para iniciar sessão utilizando o RDP.

Este exemplo define o nome NSG **myNsg** e o nome da regra RDP para **myRdpRule**.

```powershell
$nsgName = "myNsg"

$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389

$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule
```

Para obter mais informações sobre regras NSG e os pontos finais, consulte [portas de abertura para uma VM no Azure através do PowerShell](virtual-machines-windows-nsg-quickstart-powershell.md).

## <a name="create-the-vm-configuration"></a>Criar a configuração de VM

Configure a configuração de VM para ligar o VHD copiado como o sistema operativo VHD.


```powershell
    # Set the URI for the VHD that you want to use. In this example, the VHD file named "myOsDisk.vhd" is kept in a storage account named "myStorageAccount" in a container named "myContainer".
    $osDiskUri = "https://myStorageAccount.blob.core.windows.net/myContainer/myOsDisk.vhd"
    
    #Set the VM name and size. This example sets the VM name to "myVM" and the VM size to "Standard_A2".
    $vmName = "myVM"
    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize "Standard_A2"

    #Add the NIC
    $vm = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic.Id

    #Add the OS disk by using the URL of the copied OS VHD. In this example, when the OS disk is created, the term "osDisk" is appened to the VM name to create the OS disk name. This example also specifies that this Windows-based VHD should be attached to the VM as the OS disk.
    $osDiskName = $vmName + "osDisk"
    $vm = Set-AzureRmVMOSDisk -VM $vm -Name $osDiskName -VhdUri $osDiskUri -CreateOption attach -Windows
```


Se tiver discos de dados que precisam de ser anexado a VM, também deve adicionar o seguinte procedimento: 

```powershell
    # Optional: Add data disks by using the URLs of the copied data VHDs at the appropriate Logical Unit Number (Lun).
    $dataDiskName = $vmName + "dataDisk"
    $vm = Add-AzureRmVMDataDisk -VM $vm -Name $dataDiskName -VhdUri $dataDiskUri -Lun 0 -CreateOption attach
```

Os dados e o sistema operativo disco URLs o seguinte aspeto: `https://StorageAccountName.blob.core.windows.net/BlobContainerName/DiskName.vhd`. Pode encontrar esta no portal de navegar para o contentor de armazenamento de destino, clicando no sistema operativo ou dados VHD que foi copiado e, em seguida, copiar o conteúdo do URL.


## <a name="create-the-vm"></a>Criar a VM

Crie a VM utilizando as configurações que criámos apenas.

```powershell
#Create the new VM
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $vm
```

Se este comando foi efetuada com êxito, verá o resultado da seguinte forma:

```
RequestId IsSuccessStatusCode StatusCode ReasonPhrase
--------- ------------------- ---------- ------------
                         True         OK OK   
 
```
 
## <a name="verify-that-the-vm-was-created"></a>Certifique-se de que a VM foi criada 
 
Deverá visualizar a VM recentemente criada no [portal do Azure](https://portal.azure.com), em **Procurar** > **máquinas virtuais**, ou ao utilizar os seguintes comandos do PowerShell:

```powershell
    $vmList = Get-AzureRmVM -ResourceGroupName $rgName
    $vmList.Name
```

## <a name="next-steps"></a>Próximos passos

Para iniciar sessão sua nova máquina virtual, navegue para a VM no [portal](https://portal.azure.com), clique em **Ligar**e abra o ficheiro RDP de ambiente de trabalho remoto. Utilize as credenciais de conta da sua máquina virtual original para iniciar sessão na sua nova máquina virtual. Para mais informações, consulte o artigo [como ligar e iniciar sessão para uma máquina virtual Azure a executar o Windows](virtual-machines-windows-connect-logon.md).







