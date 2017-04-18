<properties
    pageTitle="Capturar uma imagem VM do GRG Azure VM | Microsoft Azure"
    description="Saiba como capturar uma imagem VM a partir de uma VM Azure GRG criados no modelo de implementação de Gestor de recursos"
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
    ms.date="10/20/2016"
    ms.author="cynthn"/>

# <a name="how-to-capture-a-vm-image-from-a-generalized-azure-vm"></a>Como capturar uma imagem VM a partir de uma VM GRG do Azure


Este artigo mostra-lhe como utilizar o Azure PowerShell para criar uma imagem de uma VM Azure GRG. Em seguida, pode utilizar a imagem para criar outra VM. A imagem inclui o disco de sistema operativo e os discos de dados que estão anexados à máquina virtual. A imagem não inclui os recursos de rede virtual, pelo que necessita configurar os recursos ao criar a nova VM. 


## <a name="prerequisites"></a>Pré-requisitos

- Tem de ter já [generalized a VM](virtual-machines-windows-generalize-vhd.md). Generalizar uma VM remove todas as informações da conta pessoal, entre outras coisas e prepara o computador para ser utilizado como uma imagem.

- Tem de ter Azure PowerShell versão 1.0.x ou mais recente instalada. Se ainda não o tiver já instalou o PowerShell, leia [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter os passos de instalação.


## <a name="log-in-to-azure-powershell"></a>Iniciar sessão no Azure PowerShell

1. Abra o PowerShell do Azure e inicie sessão na sua conta Azure.

    ```powershell
    Login-AzureRmAccount
    ```

    É aberta uma janela de pop-up para introduzir as credenciais da conta Azure.

2. Obter a subscrição IDs para as suas subscrições disponíveis.

    ```powershell
    Get-AzureRmSubscription
    ```

3. Definir a subscrição correta utilizando o ID da subscrição.

    ```powershell
    Select-AzureRmSubscription -SubscriptionId "<subscriptionID>"
    ```

## <a name="deallocate-the-vm-and-set-the-state-to-generalized"></a>Retirar a VM e definir o estado de generalized       

1. Retirar os recursos VM.

    ```powershell
    Stop-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName>
    ```

    O *Estado* para a VM no portal do Azure altera de **parado** para **parado (desatribuído)**.

2. Defina o estado da máquina virtual para **Generalized**. 

    ```powershell
    Set-AzureRmVm -ResourceGroupName <resourceGroup> -Name <vmName> -Generalized
    ```

3. Verifique o estado da VM. A secção **OSState/generalized** para a VM deve ter **DisplayStatus** definido para **VM generalized**.  

    ```powershell
    $vm = Get-AzureRmVM -ResourceGroupName <resourceGroup> -Name <vmName> -Status
    $vm.Statuses
    ```

## <a name="create-the-image"></a>Criar a imagem 

1. Copie a imagem de máquina virtual para o contentor de armazenamento de destino ao utilizar este comando. A imagem é criada na mesma conta de armazenamento como a máquina virtual original. O `-Path` parâmetro guarda uma cópia do modelo JSON localmente. O `-DestinationContainerName` parâmetro é o nome do contentor que pretende sem soltar as imagens. Se o contentor não existir, é criado por si.

    ```powershell
    Save-AzureRmVMImage -ResourceGroupName <resourceGroupName> -Name <vmName> `
        -DestinationContainerName <destinationContainerName> -VHDNamePrefix <templateNamePrefix> `
        -Path <C:\local\Filepath\Filename.json>
    ```

    Pode obter o URL da sua imagem a partir do modelo de ficheiro JSON. Vá para os **recursos** > **storageProfile** > **osDisk** > **imagem** > **uri** secção para o caminho completo da sua imagem. O URL da imagem tem a seguinte apresentação: `https://<storageAccountName>.blob.core.windows.net/system/Microsoft.Compute/Images/<imagesContainer>/<templatePrefix-osDisk>.xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx.vhd`.
    
    Também pode verificar o URI no portal. A imagem é copiada a um contentor chamado **sistema** na sua conta de armazenamento. 


## <a name="next-steps"></a>Próximos passos

- Agora pode [criar uma VM da imagem](virtual-machines-windows-create-vm-generalized.md).

