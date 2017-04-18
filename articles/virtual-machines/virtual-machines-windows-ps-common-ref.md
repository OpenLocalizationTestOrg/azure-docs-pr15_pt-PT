<properties 
   pageTitle="Comandos do PowerShell comuns para VMs | Microsoft Azure"
   description="Comandos do PowerShell comuns para começar a criar e gerir o seu VMs no Azure no Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="davidmu1" 
   manager="timlt" 
   editor="tysonn" 
   tags="azure-resource-manager"/>
   
<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/27/2016"
   ms.author="davidmu" />

# <a name="common-powershell-commands-for-creating-and-managing-vms"></a>Comandos do PowerShell comuns para criar e gerir VMs

Este artigo aborda algumas dos comandos do Azure PowerShell que pode utilizar para criar e gerir máquinas virtuais na sua subscrição do Azure.  Para obter ajuda mais detalhada com específicos parâmetros da linha de comandos e opções, pode utilizar **Get-Help** *comando*.

Veja [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md) para obter informações sobre como instalar a versão mais recente do Azure PowerShell, selecionar a sua subscrição e iniciar sessão na sua conta.

## <a name="create-a-vm"></a>Criar uma VM

Tarefa | Comando
-------------- | -------------------------
Criar uma configuração VM | $vm = [Novo AzureRmVMConfig](https://msdn.microsoft.com/library/mt603727.aspx) - VMName "vm_name" - VMSize "vm_size"<BR></BR><BR></BR>A configuração de VM é utilizada para definir ou atualizar as definições para a VM. A configuração está inicializada com o nome do seu [tamanho](virtual-machines-windows-sizes.md)e a VM.
Adicionar as definições de configuração | $vm = [Conjunto AzureRmVMOperatingSystem](https://msdn.microsoft.com/library/mt603843.aspx) - VM $vm-Windows - nome do computador "nome_computador"-credenciais $cred - ProvisionVMAgent - EnableAutoUpdate<BR></BR><BR></BR>Definições do sistema operativo incluindo [credenciais](https://technet.microsoft.com/library/hh849815.aspx) são adicionadas ao objeto configuração que criou anteriormente AzureRmVMConfig novo a utilizar.
Adicionar uma interface de rede | $vm = [Adicionar AzureRmVMNetworkInterface](https://msdn.microsoft.com/library/mt619351.aspx) - VM $vm-Id $NIC ID<BR></BR><BR></BR>Uma VM tem de ter uma [interface de rede](virtual-machines-windows-ps-create.md) para comunicar numa rede virtual. Também pode utilizar [Get-AzureRmNetworkInterface](https://msdn.microsoft.com/library/mt619434.aspx) para obter um objeto de interface de rede existente.
Especificar uma imagem de plataforma | $vm = [Conjunto AzureRmVMSourceImage](https://msdn.microsoft.com/library/mt619344.aspx) - VM $vm - PublisherName "publisher_name"-oferecer "publisher_offer" - Skus "product_sku"-versão "mais recente"<BR></BR><BR></BR>[Informações sobre a imagem](virtual-machines-windows-cli-ps-findimage.md) é adicionada ao objeto configuração que criou anteriormente AzureRmVMConfig novo a utilizar. O objeto devolvido por este comando só é utilizado quando define o disco de SO para utilizar uma imagem de plataforma.
Definir OS do disco para utilizar uma imagem de plataforma | $vm = [Conjunto AzureRmVMOSDisk](https://msdn.microsoft.com/library/mt603746.aspx) - VM $vm-- VhdUri de "disk_name" nome "http://mystore1.blob.core.windows.net/vhds/disk_name.vhd" - CreateOption FromImage<BR></BR><BR></BR>O nome do disco do sistema operativo e a sua localização no [armazenamento](../storage/storage-powershell-guide-full.md) é adicionado ao objeto configuração que criou anteriormente.
Definir OS do disco para utilizar uma imagem GRG | $vm = conjunto AzureRmVMOSDisk - VM $vm-- SourceImageUri de "disk_name" nome "https://mystore1.blob.core.windows.net/system/Microsoft.Compute/Images/myimages/myprefix-osDisk. {guid} VHD"- VhdUri"https://mystore1.blob.core.windows.net/vhds/disk_name.vhd"- CreateOption FromImage-Windows<BR></BR><BR></BR>O nome do disco do sistema operativo, a localização da imagem original e a localização do disco no [armazenamento](../storage/storage-powershell-guide-full.md) é adicionado ao objeto configuração.
Definir OS do disco para utilizar uma imagem especializada | $vm = conjunto AzureRmVMOSDisk - VM $vm-- VhdUri de "name_of_disk" nome "http://mystore1.blob.core.windows.net/vhds/" - CreateOption anexar - Windows
Criar uma VM | [Novo AzureRmVM]() - ResourceGroupName "resource_group_name"-localização "location_name" - VM $vm<BR></BR><BR></BR>Todos os recursos são criados num [grupo de recursos](../powershell-azure-resource-manager.md). A VM tem de ser criada na mesma [localização](https://msdn.microsoft.com/library/azure/dn495177.aspx) como o grupo de recursos. Antes de executar este comando, execute novo AzureRmVMConfig, AzureRmVMOperatingSystem conjunto, AzureRmVMSourceImage conjunto, adicionar AzureRmVMNetworkInterface e AzureRmVMOSDisk conjunto.

## <a name="get-information-about-vms"></a>Obter informações sobre VMs

Tarefa | Comando
-------------- | -------------------------
Lista VMs numa subscrição| [Get-AzureRmVM](https://msdn.microsoft.com/library/mt603718.aspx)
Lista VMs num grupo de recursos | Get AzureRmVM - ResourceGroupName "resource_group_name"<BR></BR><BR></BR>Para obter uma lista de grupos de recursos na sua subscrição, utilize [Get-AzureRmResourceGroup](https://msdn.microsoft.com/library/mt679016.aspx).
Obter informações sobre uma VM | Get AzureRmVM - ResourceGroupName "resource_group_name"-vm_name"nome"

## <a name="manage-vms"></a>Gerir VMs

Tarefa | Comando
-------------- | -------------------------
Iniciar uma VM | [Iniciar AzureRmVM](https://msdn.microsoft.com/library/mt603453.aspx) - ResourceGroupName "resource_group_name"-vm_name"nome"
Parar uma VM | [Parar AzureRmVM](https://msdn.microsoft.com/library/mt603483.aspx) - ResourceGroupName "resource_group_name"-vm_name"nome"
Reiniciar uma VM em execução | [Reiniciar AzureRmVM](https://msdn.microsoft.com/library/mt603775.aspx) - ResourceGroupName "resource_group_name"-vm_name"nome"
Eliminar uma VM | [Remover AzureRmVM](https://msdn.microsoft.com/library/mt603641.aspx) - ResourceGroupName "resource_group_name"-vm_name"nome"
Generalizar uma VM | [Definir AzureRmVm](https://msdn.microsoft.com/library/mt603688.aspx) - ResourceGroupName YourResourceGroup-vm_name"nome"-Generalized<BR></BR><BR></BR>Execute este comando antes de executar AzureRmVMImage guardar.
Capturar uma VM | [AzureRmVMImage guardar](https://msdn.microsoft.com/library/mt619423.aspx) - ResourceGroupName "resource_group_name" - VMName "vm_name" - DestinationContainerName "image_container" - VHDNamePrefix "image_name_prefix"-caminho "C:\filepath\filename.json"<BR></BR><BR></BR>Uma máquina virtual tem de ser [Encerrar e generalized](virtual-machines-windows-generalize-vhd.md) para ser utilizado para criar uma imagem. Antes de executar este comando, execute AzureRmVm conjunto.
Actualizar uma VM | [Actualização AzureRmVM](https://msdn.microsoft.com/library/mt603662.aspx) - ResourceGroupName "resource_group_name" - VM $vm<BR></BR><BR></BR>Obter a configuração de VM atual utilizando Get-AzureRmVM, alterar as definições de configuração no objeto VM e, em seguida, execute este comando.
Adicionar um disco de dados para uma VM | [Adicionar AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603673.aspx) - VM $vm-- VhdUri de "disk_name" nome "https://mystore1.blob.core.windows.net/vhds/disk_name.vhd" - LUN #-colocação em cache ReadWrite - DiskSizeinGB # - CreateOption vazia<BR></BR><BR></BR>Utilize Get-AzureRmVM para obter o objeto VM. Especifique o número LUN e o tamanho do disco. Execute a atualização-AzureRmVM para aplicar as alterações à configuração da VM. O disco que adicionar não está inicializado. Para obter informações sobre a inicialização discos medida que são adicionadas, consulte o artigo [gerir máquinas virtuais do Azure utilizando o Gestor de recursos e PowerShell](virtual-machines-windows-ps-manage.md).
Remover um disco de dados a partir de uma VM | [Remover AzureRmVMDataDisk](https://msdn.microsoft.com/library/mt603614.aspx) - VM $vm-disk_name"nome"<BR></BR><BR></BR>Utilize Get-AzureRmVM para obter o objeto VM. Execute a atualização-AzureRmVM para aplicar as alterações à configuração da VM.
Adicionar uma extensão a uma VM | [Definir AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx) - ResourceGroupName "resource_group_name"-- VMName de "azure_location" localização "vm_name"-extension_name"nome"-Publisher "publisher_name"-tipo "extension_type" - TypeHandlerVersion "#. #"-definições $Settings - ProtectedSettings $ProtectedSettings<BR></BR><BR></BR>Execute este comando com as [informações de configuração](virtual-machines-windows-extensions-configuration-samples.md) adequadas para a extensão que pretende instalar.
Remover uma extensão de VM | [Remover AzureRmVMExtension](https://msdn.microsoft.com/library/mt603782.aspx) - ResourceGroupName "resource_group_name"-- VMName de "extension_name" nome "vm_name"

## <a name="next-steps"></a>Próximos passos

- Consulte os passos básicos para criar uma máquina virtual no [criar uma VM Windows utilizando o Gestor de recursos e PowerShell](virtual-machines-windows-ps-create.md).

