<properties
    pageTitle="Mover um Windows VM | Microsoft Azure"
    description="Mova uma VM do Windows para outro Azure subscrição ou grupo de recursos no modelo de implementação de Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>Deslocar-se uma VM do Windows para outro Azure subscrição ou grupo de recursos 

Este artigo explica como mover uma VM Windows entre grupos de recursos ou subscrições. Mover entre subscrições pode ser útil se tiver criado originalmente uma VM numa subscrição pessoal e agora pretende movê-lo para a subscrição da sua empresa para continuar a trabalhar.

> [AZURE.NOTE] Novo recurso IDs serão criados como parte do mover. Assim que a VM tenha sido movida, terá de atualizar as ferramentas e a utilizar o novo recurso IDs de scripts. 


[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]


## <a name="use-powershell-to-move-a-vm"></a>Utilizar o Powershell para mover uma VM

Para mover uma máquina virtual para outro grupo de recursos, é necessário para se certificar de que também mover todos os recursos dependentes. Para utilizar o cmdlet mover AzureRMResource, é necessário o nome do recurso e o tipo de recurso. Pode aceder a partir do cmdlet AzureRMResource localizar.

    Find-AzureRMResource -ResourceGroupNameContains "<sourceResourceGroupName>"
    

Para mover uma VM precisamos de mover vários recursos. Que possamos criar apenas variáveis separadas para cada recurso e, em seguida, enumere-as. Este exemplo inclui a maior parte dos recursos básicos para uma VM, mas pode adicionar mais conforme necessário.

    $sourceRG = "<sourceResourceGroupName>"
    $destinationRG = "<destinationResourceGroupName>"
    
    $vm = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Compute/virtualMachines" -ResourceName "<vmName>"
    $storageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<storageAccountName>"
    $diagStorageAccount = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Storage/storageAccounts" -ResourceName "<diagnosticStorageAccountName>"
    $vNet = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/virtualNetworks" -ResourceName "<vNetName>"
    $nic = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkInterfaces" -ResourceName "<nicName>"
    $ip = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/publicIPAddresses" -ResourceName "<ipName>"
    $nsg = Get-AzureRmResource -ResourceGroupName $sourceRG -ResourceType "Microsoft.Network/networkSecurityGroups" -ResourceName "<nsgName>"
    
    Move-AzureRmResource -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId

Para mover os recursos para outra subscrição, inclua o parâmetro **- DestinationSubscriptionId** . 

    Move-AzureRmResource -DestinationSubscriptionId "<destinationSubscriptionID>" -DestinationResourceGroupName $destinationRG -ResourceId $vm.ResourceId, $storageAccount.ResourceId, $diagStorageAccount.ResourceId, $vNet.ResourceId, $nic.ResourceId, $ip.ResourceId, $nsg.ResourceId



Será pedido para confirmar que pretende mover os recursos especificados. Escreva **Y** para confirmar que pretende mover os recursos.

  
## <a name="next-steps"></a>Próximos passos

Pode mover vários tipos de recursos diferentes entre os grupos de recursos e subscrições. Para mais informações, consulte o artigo [Mover recursos para novo grupo de recursos ou subscrição](../resource-group-move-resources.md).    