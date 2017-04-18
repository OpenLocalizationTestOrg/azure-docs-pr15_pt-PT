<properties
   pageTitle="Gerir o seu máquinas virtuais utilizando o Azure PowerShell | Microsoft Azure"
   description="Saiba mais comandos que pode utilizar para automatizar tarefas na gestão máquinas virtuais."
   services="virtual-machines-windows"
   documentationCenter="windows"
   authors="singhkays"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

   <tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="10/12/2016"
   ms.author="kasing"/>

# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Gerir o seu máquinas virtuais utilizando o PowerShell do Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Muitas das tarefas que fazer cada dia para gerir o seu VMs podem ser automatizados utilizando os cmdlets do Azure PowerShell. Este artigo fornece-lhe comandos de exemplo para tarefas mais simples e ligações para artigos que mostram os comandos para tarefas mais complexas.

>[AZURE.NOTE] Se ainda não o tiver instalado e configurado o Azure PowerShell, pode obter instruções no artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="how-to-use-the-example-commands"></a>Como utilizar os comandos de exemplo
Terá de algum do texto na comandos substitua o texto que altura é adequado para o seu ambiente. O < e > símbolos indicam texto necessário para substituir. Quando substitui o texto, remova os símbolos de mas deixar as aspas no local.

## <a name="get-a-vm"></a>Obter uma VM
Esta é uma tarefa básica que irá utilizar com frequência. Utilizá-lo para obter informações sobre uma VM, executarem tarefas numa VM ou obter de saída para armazenar uma variável.

Para obter informações sobre a VM, executa este comando, substituir tudo aspas, incluindo o < e > carateres:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Para armazenar a saída numa variável $vm, execute:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Inicie sessão numa VM baseados no Windows

Execute estes comandos:

>[AZURE.NOTE] Pode obter o nome do serviço máquina virtual e na nuvem da apresentação do comando **Get-AzureVM** .
>
    $svcName = "<cloud service name>"
    $vmName = "<virtual machine name>"
    $localPath = "<drive and folder location to store the downloaded RDP file, example: c:\temp >"
    $localFile = $localPath + "\" + $vmname + ".rdp"
    Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

## <a name="stop-a-vm"></a>Parar uma VM

Execute este comando:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Utilize este parâmetro para manter o IP virtual (VIP) do serviço de nuvem, caso seja a última VM desse serviço na nuvem. <br><br> Se utilizar o parâmetro StayProvisioned, irá ainda ser faturada para a VM.

## <a name="start-a-vm"></a>Iniciar uma VM

Execute este comando:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>Anexar um disco de dados
Esta tarefa requer alguns passos. Em primeiro lugar, utilize o * * * adicionar-AzureDataDisk * * * cmdlet para adicionar o disco ao objeto $vm. Em seguida, utilizar o cmdlet **AzureVM atualizar** para atualizar a configuração da VM.

Também terá de decidir se pretende anexar um disco novo ou que contém dados. Para um novo disco, o comando cria o ficheiro. vhd e anexa-lo.

Para anexar um novo disco, execute este comando:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

Para anexar um disco de dados existente, execute este comando:

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

Para anexar discos de dados a partir de um ficheiro. vhd existente no armazenamento blob do, execute este comando:

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Criar uma VM baseados no Windows

Para criar uma nova máquina de virtual baseados no Windows Azure, utilize as instruções em [Utilizar o PowerShell para criar e pré-configurar baseados no Windows máquinas virtuais Azure](virtual-machines-windows-classic-create-powershell.md). Passos neste tópico que através da criação de um Azure PowerShell comando definir que cria uma VM baseados no Windows que pode ser pré-configuradas:

- Com membros de domínio do Active Directory.
- Com discos adicionais.
- Como um membro de um existente balanceamento de carga definir.
- Com um endereço IP estático.
