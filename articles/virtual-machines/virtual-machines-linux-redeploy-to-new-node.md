<properties 
    pageTitle="Implementar máquinas virtuais de Linux | Microsoft Azure" 
    description="Descreve como implementar máquinas virtuais de Linux para mitigar SSH problemas de ligação." 
    services="virtual-machines-linux" 
    documentationCenter="virtual-machines" 
    authors="iainfoulds" 
    manager="timlt"
    tags="azure-resource-manager,top-support-issue" 
/>
    

<tags 
    ms.service="virtual-machines-linux" 
    ms.devlang="na" 
    ms.topic="support-article" 
    ms.tgt_pltfrm="vm-linux"
    ms.workload="infrastructure" 
    ms.date="09/19/2016" 
    ms.author="iainfou" 
/>

# <a name="redeploy-virtual-machine-to-new-azure-node"></a>Implementar máquina virtual para novo nó Azure

Se tiver sido opostas dificuldades SSH resolução de problemas ou aplicação acesso a uma máquina de virtual (VM), Azure Reimplementar a VM poderão ajudar. Quando implementar uma VM, move o cursor a VM para um novo nó dentro de infraestrutura do Azure e, em seguida, oferece-lo novamente no, mantendo a todas as opções de configuração e recursos associados. Este artigo mostra-lhe como implementar uma VM utilizando Azure clip ou o portal do Azure.

> [AZURE.NOTE] Depois de implementar uma VM, o disco temporário é perdido e endereços IP dinâmicos associados a interface de rede virtual são atualizados. 


## <a name="using-azure-cli"></a>Utilizar o clip Azure

Certifique-se de que tem o [Mais recente Azure clip instalado](../xplat-cli-install.md) no seu computador e o utilizador está no modo de Gestor de recursos (`azure config mode arm`).

Utilize o seguinte comando Azure clip a implementar a máquina virtual:

```bash
azure vm redeploy --resourcegroup <resourcegroup> --vm-name <vmname> 
```

Pode ver o estado da alteração VM à medida que vai durante o processo novo lançamento. O `PowerState` da VM vai 'Aconteça' para 'Actualizar', em seguida, 'Iniciar' e finalmente 'Executar' à medida que vai durante o processo de Reimplementar para um novo anfitrião. Verificar o estado de VMs dentro de um grupo de recursos com:

```bash
azure vm list -g <resourcegroup>
```


[AZURE.INCLUDE [virtual-machines-common-redeploy-to-new-node](../../includes/virtual-machines-common-redeploy-to-new-node.md)]


## <a name="next-steps"></a>Próximos passos
Se estiver a ter problemas em ligar à sua VM, pode encontrar ajuda específica no [SSH ligações de resolução de problemas](virtual-machines-linux-troubleshoot-ssh-connection.md) ou [SSH detalhada passos de resolução de problemas](virtual-machines-linux-detailed-troubleshoot-ssh-connection.md). Se não consegue aceder a uma aplicação em execução no seu VM, também pode ler [resolução de problemas de aplicação](virtual-machines-linux-troubleshoot-app-connection.md).