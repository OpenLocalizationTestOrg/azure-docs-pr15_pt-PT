<properties 
    pageTitle="Implementar máquinas virtuais de Windows | Microsoft Azure" 
    description="Descreve como implementar máquinas de virtuais do Windows para mitigar RDP problemas de ligação." 
    services="virtual-machines-windows" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-windows" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-windows"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>


# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Implementar máquina virtual para novo nó Azure

Se tiver sido opostas dificuldades resolução de problemas de ambiente de trabalho remoto (RDP) ligação ou aplicação access baseados no Windows Azure máquina virtual (VM), reimplementar a VM pode ajudar. Quando implementar uma VM, move o cursor a VM para um novo nó dentro de infraestrutura do Azure e, em seguida, oferece-lo novamente no, mantendo a todas as opções de configuração e recursos associados. Este artigo mostra-lhe como implementar uma VM utilizando o PowerShell do Azure ou o portal do Azure.

> [AZURE.NOTE] Depois de implementar uma VM, o disco temporário é perdido e endereços IP dinâmicos associados a interface de rede virtual são atualizados. 

## <a name="using-azure-powershell"></a>Utilizar o Azure PowerShell

Certifique-se de que tem o PowerShell mais recente do Azure 1. x instalado no seu computador. Para mais informações, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Utilize este comando do Azure PowerShell a implementar a máquina virtual:

    Set-AzureRmVM -Redeploy -ResourceGroupName $rgname -Name $vmname 


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Próximos passos
Se estiver a ter problemas em ligar à sua VM, pode encontrar ajuda específica no [ligações RDP de resolução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md) ou [RDP detalhada passos de resolução de problemas](virtual-machines-windows-detailed-troubleshoot-rdp.md). Se não consegue aceder a uma aplicação em execução no seu VM, também pode ler [resolução de problemas de aplicação](virtual-machines-windows-troubleshoot-app-connection.md).