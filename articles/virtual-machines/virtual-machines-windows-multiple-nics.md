<properties
   pageTitle="Criar uma VM Windows com vários NIC | Microsoft Azure"
   description="Saiba como criar uma VM Windows com vários NIC anexada utilizar modelos do Azure PowerShell ou Gestor de recursos."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="iainfoulds"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure"
   ms.date="10/27/2016"
   ms.author="iainfou"/>

# <a name="creating-a-windows-vm-with-multiple-nics"></a>Criar uma VM Windows com vários NIC
Pode criar uma máquina de virtual (VM) no Azure que tenha várias interfaces de rede virtual (NIC) anexadas ao mesmo. Um cenário comum seria ter sub-redes diferentes para conectividade front-end e back-end ou uma rede dedicada a uma solução de cópia de segurança ou monitorização. Este artigo fornece comandos rápidos para criar uma VM com vários NIC anexada. Para obter informações detalhadas, incluindo como criar várias NIC dentro seus próprios scripts de PowerShell, saiba mais sobre como [Implementar multi-NIC VMs](../virtual-network/virtual-network-deploy-multinic-arm-ps.md). Diferentes [tamanhos VM](virtual-machines-windows-sizes.md) suportar um número variado de NIC, por isso, o tamanho do seu VM em conformidade.

>[AZURE.WARNING] Tem de anexar NIC vários quando cria uma VM - não é possível adicionar NIC para uma VM existente. Pode [criar uma VM com base em de discos virtual original](virtual-machines-windows-vhd-copy.md) e crie várias NIC como implementar a VM.

## <a name="create-core-resources"></a>Criar core recursos
Certifique-se de que tem a [mais recente Azure PowerShell instalado e configurado](../powershell-install-configure.md). Inicie sessão na sua conta Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos seguintes, substitua os nomes dos parâmetros de exemplo com os seus próprios valores. Exemplo os nomes dos parâmetros incluídos `myResourceGroup`, `mystorageaccount`, e `myVM`.

Primeiro, crie um grupo de recursos. O exemplo seguinte cria um grupo de recursos com o nome `myResourceGroup` na `WestUs` localização:

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "WestUS"
```

Crie uma conta de armazenamento para sua VMs colocar em espera. O exemplo seguinte cria uma conta de armazenamento denominada `mystorageaccount`:

```powershell
$storageAcc = New-AzureRmStorageAccount -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "mystorageaccount" `
    -Kind "Storage" -SkuName "Premium_LRS" 
```

## <a name="create-virtual-network-and-subnets"></a>Criar rede virtual e sub-redes
Definir duas sub-redes de rede virtual - um para o tráfego front-end e outra para o tráfego de back-end. O exemplo seguinte define duas sub-redes, com o nome `mySubnetFrontEnd` e `mySubnetBackEnd`:

```powershell
$mySubnetFrontEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetFrontEnd" `
    -AddressPrefix "192.168.1.0/24"
$mySubnetBackEnd = New-AzureRmVirtualNetworkSubnetConfig -Name "mySubnetBackEnd" `
    -AddressPrefix "192.168.2.0/24"
```

Crie a sua rede virtual e sub-redes. O exemplo seguinte cria uma rede virtual denominada `myVnet`:

```powershell
$myVnet = New-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myVnet" -AddressPrefix "192.168.0.0/16" `
    -Subnet $mySubnetFrontEnd,$mySubnetBackEnd
```


## <a name="create-multiple-nics"></a>Criar múltiplas NIC
Crie duas NIC, como anexar um NIC à sub-rede front-end e um NIC à sub-rede back-end. O exemplo seguinte cria duas NIC, com o nome `myNic1` e `myNic2`:

```powershell
$frontEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetFrontEnd'}
$myNic1 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic1" -SubnetId $frontEnd.Id

$backEnd = $myVnet.Subnets|?{$_.Name -eq 'mySubnetBackEnd'}
$myNic2 = New-AzureRmNetworkInterface -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNic2" -SubnetId $backEnd.Id
```

Normalmente, também pode criar um [grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) ou [Balanceador de carga](../load-balancer/load-balancer-overview.md) para ajudar a gerir e distribuir o tráfego pelos seus VMs. O artigo [mais detalhada multi-NIC VM](../virtual-network/virtual-network-deploy-multinic-arm-ps.md) orienta-o através de criar um grupo de segurança de rede e atribuir NIC.


## <a name="create-the-virtual-machine"></a>Criar a máquina virtual
Agora começar a construir a sua configuração VM. Cada tamanho da memória virtual tem um limite para o número total de NIC que pode adicionar um VM. Leia mais sobre os [tamanhos VM do Windows](virtual-machines-windows-sizes.md). 

Em primeiro lugar, defina as suas credenciais VM o `$cred` variável da seguinte forma:

```powershell
$cred = Get-Credential
```

O exemplo seguinte define uma VM denominada `myVM` e utiliza um tamanho da memória virtual que suporta até duas NIC (`Standard_DS2_v2`):

```powershell
$vmConfig = New-AzureRmVMConfig -VMName "myVM" -VMSize "Standard_DS2_v2"
```

Crie o resto da sua configuração VM. O exemplo seguinte cria uma Windows Server 2012 R2 VM:

```powershell
$vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName Te"MyVM" `
    -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
$vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName "MicrosoftWindowsServer" `
    -Offer "WindowsServer" -Skus "2012-R2-Datacenter" -Version "latest"
```

Anexe duas NIC que criou anteriormente:

```powershell
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic1.Id -Primary
$vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $myNic2.Id
```

Configure o armazenamento e disco virtual para a nova VM:

```powershell
$blobPath = "vhds/WindowsVMosDisk.vhd"
$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + $blobPath
$diskName = "windowsvmosdisk"
$vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $diskName -VhdUri $osDiskUri `
    -CreateOption "fromImage"
```

Por fim, crie uma VM:

```powershell
New-AzureRmVM -VM $vmConfig -ResourceGroupName "myResourceGroup" -Location "WestUS"
```

## <a name="creating-multiple-nics-using-resource-manager-templates"></a>Criar múltiplas NIC utilizar modelos de Gestor de recursos
Modelos de Gestor de recursos Azure utilizam declarativos JSON ficheiros para definir o seu ambiente. Pode obter uma [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md). Modelos de Gestor de recursos fornecem uma maneira de criar várias instâncias de um recurso durante a implementação, tal como criar várias NIC. Utilize a *funcionalidade copiar* para especificar o número de instâncias para criar:

```bash
"copy": {
    "name": "multiplenics"
    "count": "[parameters('count')]"
}
```

Leia mais sobre como [criar várias instâncias utilizando a *função Copiar*](../resource-group-create-multiple.md). 

Também pode utilizar uma `copyIndex()` acrescentar, em seguida, um número para um nome de recurso, que permite-lhe criar `myNic1`, `MyNic2`, etc. A imagem seguinte mostra um exemplo de acrescentar o valor de índice:

```bash
"name": "[concat('myNic', copyIndex())]", 
```

Pode obter um exemplo completo da [criação de vários NIC utilizar modelos de Gestor de recursos](../virtual-network/virtual-network-deploy-multinic-arm-template.md).

## <a name="next-steps"></a>Próximos passos
Certifique-se rever [Windows VM tamanhos](virtual-machines-windows-sizes.md) ao tentar criar uma VM com vários NIC. Paga a atenção para o número máximo de NIC cada tamanho da memória virtual suporta. 

Lembre-se de que não é possível adicionar NIC adicional para uma VM existente, terá de criar todos os a NIC quando implementar a VM. Tomar cuidado quando planear as implementações para se certificar de que tem todos os a conectividade de rede necessários desde o início.