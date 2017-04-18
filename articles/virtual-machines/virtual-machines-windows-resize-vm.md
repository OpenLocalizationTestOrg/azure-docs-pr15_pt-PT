<properties
    pageTitle="Redimensionar um Windows VM | Microsoft Azure"
    description="Redimensione uma máquina de virtual Windows criada no modelo de implementação do Gestor de recursos, através do Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>

    
# <a name="resize-a-windows-vm"></a>Redimensionar um VM do Windows

Este artigo mostra-lhe como redimensionar um VM do Windows, criados no modelo de implementação de Gestor de recursos através do Azure Powershell.

Depois de criar uma máquina de virtual (VM), é possível dimensionar a VM para cima ou para baixo ao alterar o tamanho da memória virtual. Em alguns casos, tem de retirar a VM pela primeira vez. Isto pode acontecer se o novo tamanho não está disponível no cluster de hardware que está atualmente a alojar a VM.

## <a name="resize-a-windows-vm-not-in-an-availability-set"></a>Redimensionar uma VM Windows não num conjunto de disponibilidade

1. Liste os tamanhos VM que estão disponíveis no cluster de hardware onde está alojada a VM. 

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName> 
    ```

2. Se o tamanho pretendido estiver listado, execute os seguintes comandos para redimensionar a VM. Se o tamanho pretendido não estiver listado, avance para o passo 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVMsize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Se o tamanho pretendido não estiver listado, execute os seguintes comandos para retirar a VM, a redimensioná-la e reinicie a VM.

    ```powershell
    $rgname = "<resourceGroupName>"
    $vmname = "<vmName>"
    Stop-AzureRmVM -ResourceGroupName $rgname -VMName $vmname -Force
    $vm = Get-AzureRmVM -ResourceGroupName $rgname -VMName $vmname
    $vm.HardwareProfile.VmSize = "<newVMSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName $rgname
    Start-AzureRmVM -ResourceGroupName $rgname -Name $vmname
    ```

> [AZURE.WARNING] Retirar a VM disponibilização de quaisquer endereços IP dinâmicos atribuídos a VM. Os discos SO e os dados não são afetados. 

## <a name="resize-a-windows-vm-in-an-availability-set"></a>Redimensionar uma VM Windows num conjunto de disponibilidade

Se o novo tamanho para um VM num conjunto de disponibilidade não está disponível no cluster de hardware atualmente que aloja a VM, em seguida, todos os VMs no conjunto de disponibilidade terá de remover para redimensionar a VM. Também poderá ter de atualizar o tamanho dos outros VMs na disponibilidade definida após ter sido redimensionado uma VM. Para redimensionar uma VM num conjunto de disponibilidade, execute os passos seguintes.

1. Liste os tamanhos VM que estão disponíveis no cluster de hardware onde está alojada a VM.

    ```powershell
    Get-AzureRmVMSize -ResourceGroupName <resourceGroupName> -VMName <vmName>
    ```

2. Se o tamanho pretendido estiver listado, execute os seguintes comandos para redimensionar a VM. Se não estiver listado, avance para o passo 3.

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroupName> -VMName <vmName>
    $vm.HardwareProfile.VmSize = "<newVmSize>"
    Update-AzureRmVM -VM $vm -ResourceGroupName <resourceGroupName>
    ```

3. Se o tamanho pretendido não estiver listado, continue com os seguintes passos para retirar todos os VMs no conjunto de disponibilidade, redimensionar VMs e reinicie-los.

4.  Pare de todos os VMs no conjunto de disponibilidade.

    ```powershell
    $rg = "<resourceGroupName>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        Stop-AzureRmVM -ResourceGroupName $rg -Name $vmName -Force
    } 
    ```
              
5.  Redimensionar e reinicie os VMs no conjunto de disponibilidade.

    ```powershell
    $rg = "<resourceGroupName>"
    $newSize = "<newVmSize>"
    $as = Get-AzureRmAvailabilitySet -ResourceGroupName $rg
    $vmIds = $as.VirtualMachinesReferences
    foreach ($vmId in $vmIDs){
        $string = $vmID.Id.Split("/")
        $vmName = $string[8]
        $vm = Get-AzureRmVM -ResourceGroupName $rg -Name $vmName
        $vm.HardwareProfile.VmSize = $newSize
        Update-AzureRmVM -ResourceGroupName $rg -VM $vm
        Start-AzureRmVM -ResourceGroupName $rg -Name $vmName
    }
    ```

## <a name="next-steps"></a>Próximos passos

- Para escalabilidade adicional, executar várias instâncias VM e dimensionar saída. Para mais informações, consulte o artigo [Dimensionar automaticamente máquinas com o Windows num conjunto de escala de Máquina Virtual](../virtual-machine-scale-sets/virtual-machine-scale-sets-windows-autoscale.md).



