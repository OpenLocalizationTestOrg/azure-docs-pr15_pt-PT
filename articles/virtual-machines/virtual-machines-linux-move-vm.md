<properties
    pageTitle="Mover uma VM Linux | Microsoft Azure"
    description="Mova uma VM Linux para outra Azure subscrição ou grupo de recursos no modelo de implementação de Gestor de recursos."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/08/2016"
    ms.author="cynthn"/>

    


# <a name="move-a-linux-vm-to-another-subscription-or-resource-group"></a>Deslocar-se uma VM Linux para outra subscrição ou grupo de recursos

Este artigo explica como mover uma VM Linux entre grupos de recursos ou subscrições. Mover uma VM entre subscrições pode ser útil se tiver criado uma VM numa subscrição pessoal e agora pretende movê-lo para a subscrição da sua empresa.

> [AZURE.NOTE] Novo recurso IDs são criados como parte do mover. Assim que a VM tenha sido movida, tem de atualizar as ferramentas e a utilizar o novo recurso IDs de scripts. 


## <a name="use-the-azure-cli-to-move-a-vm"></a>Utilize o clip do Azure para mover uma VM 

Para mover com êxito uma VM, tem de mover a VM e todos os seus recursos de suporte. Utilize o comando **Mostrar grupo azure** para listar todos os recursos num grupo de recursos e respectivos IDs. É útil encaminhamento a saída deste comando para um ficheiro para que possa copiar e colar os IDs de comandos posteriores.

    azure group show <resourceGroupName>

Para mover uma VM e os seus recursos para outro grupo de recursos, utilize o comando de clip **recurso azure mover** . O exemplo seguinte mostra como mover uma VM e os recursos mais comuns, é necessário. Vamos utilizar o parâmetro **-i** e passam numa lista separados por vírgulas (sem espaços) de IDs para os recursos para mover.

    
    vm=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Compute/virtualMachines/<vmName>
    nic=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkInterfaces/<nicName>
    nsg=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/networkSecurityGroups/<nsgName>
    pip=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/publicIPAddresses/<publicIPName>
    vnet=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Network/virtualNetworks/<vnetName>
    diag=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<diagnosticStorageAccountName>
    storage=/subscriptions/<sourceSubscriptionID>/resourceGroups/<sourceResourceGroup>/providers/Microsoft.Storage/storageAccounts/<storageAcountName>      
    
    azure resource move --ids $vm,$nic,$nsg,$pip,$vnet,$storage,$diag -d "<destinationResourceGroup>"
    
Se pretender mover a VM e os seus recursos para uma subscrição diferente, adicione o **– destino subscriptionId #60; destinationSubscriptionID & #62;** parâmetro para especificar a subscrição de destino.

Se estiver a trabalhar a partir da linha de comandos num computador Windows, terá de adicionar um **$** à frente os nomes das variáveis quando declara-los. Isto não é necessário no Linux.

São-lhe pedido para confirmar que pretende mover o recurso especificado. Escreva **Y** para confirmar que pretende mover os recursos.
    

[AZURE.INCLUDE [virtual-machines-common-move-vm](../../includes/virtual-machines-common-move-vm.md)]

## <a name="next-steps"></a>Próximos passos

Pode mover vários tipos de recursos diferentes entre os grupos de recursos e subscrições. Para mais informações, consulte o artigo [Mover recursos para novo grupo de recursos ou subscrição](../resource-group-move-resources.md).    