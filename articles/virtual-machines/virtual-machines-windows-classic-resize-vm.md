<properties
    pageTitle="Redimensionar uma VM clássica do Windows | Microsoft Azure"
    description="Redimensione uma máquina de virtual Windows criada no modelo clássico de implementação, através do Azure Powershell."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="Drewm3"
    manager="timlt"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="drewm"/>


# <a name="resize-a-windows-vm-created-in-the-classic-deployment-model"></a>Redimensionar uma VM Windows criados no modelo de implementação clássico

Este artigo mostra-lhe como redimensionar um VM do Windows, criados no modelo de implementação clássica através do Azure Powershell.

Quando considerar a capacidade para redimensionar uma VM, existem dois conceitos que controlam o intervalo de tamanhos de disponíveis para redimensionar a máquina virtual. O primeiro conceito é a região em que é implementada a VM. A lista de tamanhos de VM disponíveis na região está no separador Serviços da página web Azure regiões. O segundo conceito é o hardware físico atualmente que aloja a VM. Os servidores de físicos alojamento VMs são agrupados em clusters de hardware físico comuns. O método de alteração do tamanho da memória virtual difere dependendo se o tamanho da memória virtual novo pretendido for suportado pelo cluster hardware atualmente que aloja a VM.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Também pode [redimensionar uma VM criada no modelo de implementação de Gestor de recursos](virtual-machines-windows-resize-vm.md).


## <a name="add-your-account"></a>Adicionar a sua conta

Tem de configurar Azure o PowerShell para trabalhar com recursos Azure clássicos. Siga os passos abaixo para configurar o Azure o PowerShell para gerir os recursos clássicos.

1. Na linha de comandos do PowerShell, escreva `Add-AzureAccount` e clique em **Enter**. 
2. Escreva o endereço de e-mail associado à sua subscrição do Azure e clique em **continuar**. 
3. Escreva a palavra-passe para a sua conta. 
4. Clique em **Iniciar sessão**. 


## <a name="resize-in-the-same-hardware-cluster"></a>Redimensionar no mesmo cluster de hardware

Para redimensionar uma VM para um tamanho de disponível no cluster hardware que aloja a VM, execute os passos seguintes.

1. Execute o seguinte comando do PowerShell para os tamanhos de VM disponíveis no cluster hardware que aloja o serviço de nuvem que contém a VM da lista.

    ```powershell
    Get-AzureService | where {$_.ServiceName -eq "<cloudServiceName>"}
    ```

2. Execute os seguintes comandos para redimensionar a VM.

    ```powershell
    Get-AzureVM -ServiceName <cloudServiceName> -Name <vmName> | Set-AzureVMSize -InstanceSize <newVMSize> | Update-AzureVM
    ```

## <a name="resize-on-a-new-hardware-cluster"></a>Redimensionar num novo cluster de hardware

Para redimensionar uma VM para um tamanho de não está disponível no cluster hardware alojamento VM, na nuvem serviço e todos os VMs no serviço de nuvem tem de recriar. Cada serviço em nuvem está alojado num cluster de hardware único para que todos os VMs no serviço de nuvem tem de ser um tamanho de que é suportado num cluster de hardware. Os passos seguintes irão descrevem como redimensionar uma VM ao eliminar e, em seguida, recriar o serviço de nuvem.

1. Execute o seguinte comando do PowerShell para os tamanhos VM disponíveis na região da lista. 

    ```powershell
    Get-AzureLocation | where {$_.Name -eq "<locationName>"}
    ```

2. Tome nota do todas as definições de configuração para cada VM no serviço na nuvem que contém a VM para ser redimensionada. 
3. Elimine todos os VMs no serviço de nuvem, ao selecionar a opção para manter os discos para cada VM.
4. Recrie a VM para ser redimensionada utilizando o tamanho da memória virtual pretendido.
5. Recrie todos os outros VMs foram o serviço de nuvem utilizando um tamanho da memória virtual disponível no cluster hardware agora que aloja o serviço de nuvem.

Um exemplo de script para eliminar e recriar um serviço de nuvem utilizando um novo tamanho da memória virtual pode ser encontrado [aqui](https://github.com/Azure/azure-vm-scripts). 


## <a name="next-steps"></a>Próximos passos

- [Redimensionar uma VM criada no modelo de implementação de Gestor de recursos](virtual-machines-windows-resize-vm.md).