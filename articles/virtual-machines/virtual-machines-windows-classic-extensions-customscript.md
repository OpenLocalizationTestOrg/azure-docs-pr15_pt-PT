<properties
   pageTitle="Extensão de Script personalizada numa VM Windows | Microsoft Azure"
   description="Automatizar tarefas de configuração do Azure VM utilizando a extensão de Script personalizado para executar scripts de PowerShell numa VM remoto do Windows"
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="08/06/2015"
   ms.author="kundanap"/>

# <a name="custom-script-extension-for-windows-virtual-machines"></a>Extensão de Script personalizada para máquinas de virtuais do Windows

Este artigo fornece uma descrição geral de como utilizar a extensão de Script personalizado no Windows VMs ao utilizar os cmdlets do Azure PowerShell com o Azure APIs de gestão de serviço.

Extensões de máquina virtual (VM) são criadas pela Microsoft e fidedignos fabricantes de terceiros para aumentar a funcionalidade da VM. Para obter uma descrição geral das extensões VM, consulte o artigo [Azure VM extensões e funcionalidades](virtual-machines-windows-extensions-features.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos, utilizando o modelo de Gestor de recursos](virtual-machines-windows-extensions-customscript.md).

## <a name="custom-script-extension-overview"></a>Descrição geral de extensão de Script personalizado

Com a extensão de Script personalizado para o Windows, pode executar scripts de PowerShell uma VM remota sem iniciar sessão para a mesma. Pode executar os scripts depois de aprovisionar a VM ou em qualquer altura durante o ciclo de vida da VM sem abrir qualquer portas adicionais. Incluam os casos de utilização mais comuns para executar a extensão de Script personalizado em execução, instalar e configurar o software adicional na VM após está aprovisionado.

### <a name="prerequisites-for-running-the-custom-script-extension"></a>Pré-requisitos para executar a extensão de Script personalizado

1. Instalar <a href="http://azure.microsoft.com/downloads" target="_blank">os cmdlets do Azure PowerShell</a> versão 0.8.0 ou posterior.
2. Se pretender que os scripts sejam executados numa VM existente, certifique-se de que agente de VM está ativado na VM. Se não estiver instalado, siga estes [passos](virtual-machines-windows-classic-agents-and-extensions.md). Se a VM é criada a partir do portal Azure, VM agente está instalado por predefinição.
3. Carregue os scripts que pretende executar na VM ao armazenamento do Azure. Os scripts podem ter um único contentor ou vários contentores de armazenamento.
4. Deverá ser criado o script para que o script de entrada, que é iniciado pela extensão, inicia outros scripts.

## <a name="custom-script-extension-scenarios"></a>Cenários de extensão de Script personalizados

### <a name="upload-files-to-the-default-container"></a>Carregar ficheiros para o contentor predefinido

O exemplo seguinte mostra como pode executar os scripts na VM se estiverem no contentor de armazenamento da conta predefinida da sua subscrição. Carregue os scripts para ContainerName. Pode verificar a conta de armazenamento predefinida utilizando o **Get-AzureSubscription – predefinido** comando.

O exemplo seguinte cria uma VM, mas também pode executar o mesmo cenário numa VM existente.

    # Create a new VM in Azure.
    $vm = New-AzureVMConfig -Name $name -InstanceSize Small -ImageName $imagename
    $vm = Add-AzureProvisioningConfig -VM $vm -Windows -AdminUsername $username -Password $password
    // Add Custom Script extension to the VM. The container name refers to the storage container that contains the file.
    $vm = Set-AzureVMCustomScriptExtension -VM $vm -ContainerName $container -FileName 'start.ps1'
    New-AzureVM -ServiceName $servicename -Location $location -VMs $vm
    #  After the VM is created, the extension downloads the script from the storage location and executes it on the VM.

    # Viewing the  script execution output.
    $vm = Get-AzureVM -ServiceName $servicename -Name $name
    # Use the position of the extension in the output as index.
    $vm.ResourceExtensionStatusList[i].ExtensionSettingStatus.SubStatusList

### <a name="upload-files-to-a-non-default-storage-container"></a>Carregar ficheiros para um contentor de armazenamento não predefinida

Este cenário mostra como utilizar um contentor de armazenamento não predefinida dentro da mesma subscrição ou de uma subscrição diferente para carregar scripts e ficheiros. Este exemplo mostra um VM existente, mas podem ser efectuadas as mesmas operações enquanto estiver a criar uma VM.

        Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileName 'file1.ps1','file2.ps1' -Run 'file.ps1' | Update-AzureVM

### <a name="upload-scripts-to-multiple-containers-across-different-storage-accounts"></a>Carregar scripts para vários contentores em contas de armazenamento diferente

  Se são armazenados os ficheiros de script através de vários membros em contentores, terá de fornecer o URL de assinaturas (SA) completo acesso partilhado para os ficheiros para executar os scripts.

      Get-AzureVM -Name $name -ServiceName $servicename | Set-AzureVMCustomScriptExtension -StorageAccountName $storageaccount -StorageAccountKey $storagekey -ContainerName $container -FileUri $fileUrl1, $fileUrl2 -Run 'file.ps1' | Update-AzureVM


### <a name="add-the-custom-script-extension-from-the-azure-portal"></a>Adicionar a extensão de Script personalizado a partir do portal do Azure

Vá para a VM no <a href="https://portal.azure.com/ " target="_blank">portal do Azure</a> e adicionar a extensão, especificando o ficheiro de script para executar.

  ![Especificar o ficheiro de script][5]


### <a name="uninstall-the-custom-script-extension"></a>Desinstalar a extensão de Script personalizado

Pode desinstalar a extensão de Script personalizado a partir da VM utilizando o comando seguinte.

      get-azureVM -ServiceName KPTRDemo -Name KPTRDemo | Set-AzureVMCustomScriptExtension -Uninstall | Update-AzureVM

### <a name="use-the-custom-script-extension-with-templates"></a>Utilize a extensão de Script personalizado com modelos

Para saber mais sobre como utilizar a extensão de Script personalizado com o Gestor de recursos do Azure modelos, consulte o artigo [utilizar a extensão de Script personalizado para o Windows VMs com modelos de Gestor de recursos do Azure](virtual-machines-windows-extensions-customscript.md).

<!--Image references-->
[5]: ./media/virtual-machines-windows-classic-extensions-customscript/addcse.png
