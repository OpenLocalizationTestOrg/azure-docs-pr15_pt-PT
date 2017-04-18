<properties
    pageTitle="Desligar um disco de dados a partir de uma VM Windows | Microsoft Azure"
    description="Saiba como desanexar um disco de dados a partir de uma máquina virtual no Azure utilizando o modelo de implementação do Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="cynthn"/>



# <a name="how-to-detach-a-data-disk-from-a-windows-virtual-machine"></a>Como desanexar um disco de dados a partir de uma máquina de virtual do Windows


Quando já não precisar de um disco de dados que está anexado a uma máquina virtual, pode facilmente desanexá-lo. Remove o disco de máquina virtual, mas não removê-lo a partir do armazenamento. 

> [AZURE.WARNING] Se desanexar um disco que não é eliminado automaticamente. Se subscreveu o armazenamento de Premium, continuará implicam taxas de armazenamento para o disco. Para obter mais informações consulte [preços e a faturação quando utilizar o armazenamento de Premium](../storage/storage-premium-storage.md#pricing-and-billing). 

Se pretender voltar a utilizar os dados existentes no disco, pode anexá-lo a mesma máquina virtual ou outro.  


## <a name="detach-a-data-disk-using-the-portal"></a>Desligar um disco de dados através do portal

1. No centro do portal, selecione **máquinas virtuais**.

2. Selecione a máquina virtual que tem o disco de dados que pretende desanexar e, em seguida, clique em **todas as definições**.

3. Na pá **Definições** , selecione **discos**.

4. Na pá **discos** , selecione o disco de dados que gostaria de desanexar.

5. No pá para o disco de dados, clique em **Desanexar**.


    ![Captura de ecrã que mostra o botão desanexar.](./media/virtual-machines-windows-detach-disk/detach-disk.png)

O disco permanece no armazenamento mas já não está ligado a uma máquina virtual.


## <a name="detach-a-data-disk-using-powershell"></a>Desligar um disco de dados através do PowerShell

Neste exemplo, o primeiro comando obtém a máquina virtual denominada **MyVM07** no grupo de recursos **RG11** utilizando o cmdlet Get-AzureRmVM. O comando armazena a máquina virtual na variável **$VirtualMachine** . 

O segundo comando remove o disco de dados denominado DataDisk3 de máquina virtual. 

O comando final atualiza o estado da máquina virtual para concluir o processo de remoção do disco de dados.

    $VirtualMachine = Get-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" 
    Remove-AzureRmVMDataDisk -VM $VirtualMachine -Name "DataDisk3"
    Update-AzureRmVM -ResourceGroupName "RG11" -Name "MyVM07" -VM $VirtualMachine


Para obter mais informações, consulte o artigo [Remover AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx)

## <a name="next-steps"></a>Próximos passos

Se pretender reutilizar o disco de dados, pode simplesmente [anexá-lo para outro VM](virtual-machines-windows-attach-disk-portal.md)
