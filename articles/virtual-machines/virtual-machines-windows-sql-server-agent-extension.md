<properties
    pageTitle="Extensão de agente do SQL Server para SQL Server VMs (Gestor de recurso) | Microsoft Azure"
    description="Este tópico descreve como gerir a extensão do agente do SQL Server, que automatiza tarefas de administração do SQL Server específicas. Estes incluem cópia de segurança automatizado, correcção automática e integração com o Azure chave cofre. Este tópico utiliza o modo de implementação do Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-resource-manager"></a>Extensão de agente do SQL Server para SQL Server VMs (Gestor de recurso)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)

É executado a extensão de agente IaaS (SQLIaaSExtension) do SQL Server em máquinas virtuais Azure para automatizar tarefas de administração. Este tópico fornece uma descrição geral dos serviços suportados pelo extensão, bem como as instruções de instalação, estado e remoção.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica. Para ver a versão clássica deste artigo, consulte o artigo [SQL Server agente extensão para SQL Server VMs clássica](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados

A extensão de agente do SQL Server IaaS suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
|---------------------|-------------------------------|
| **Cópia de segurança automática de SQL** | Automatiza o agendamento da cópias de segurança para todas as bases de dados para a instância predefinida do SQL Server na VM. Para mais informações, consulte o artigo [automatizadas cópia de segurança para SQL Server em máquinas virtuais do Azure (Gestor de recurso)](virtual-machines-windows-sql-automated-backup.md).|
| **SQL automatizado a aplicação de patches** | Configura uma janela de manutenção durante o qual as atualizações para o seu VM podem demorar local, para que pode evitar atualizações durante as horas para sua carga de trabalho. Para mais informações, consulte o artigo [correcção automática para SQL Server em máquinas virtuais do Azure (Gestor de recurso)](virtual-machines-windows-sql-automated-patching.md).|
| **Integração do cofre chave Azure** | Permite-lhe instalar e configurar o Azure chave cofre no seu VM de servidor SQL automaticamente. Para mais informações, consulte o artigo [Configurar Azure chave cofre integração para SQL Server no Azure VMs (Gestor de recurso)](virtual-machines-windows-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Pré-requisitos

Requisitos para utilizar o SQL Server IaaS agente no seu VM:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versões do SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Transferir e configurar os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md)

## <a name="installation"></a>Instalação

A extensão de agente do SQL Server IaaS é instalada automaticamente quando o aprovisionamento de uma das imagens da Galeria de máquina virtual do SQL Server.

Se criar uma máquina de virtual só de sistema operativo Windows Server, pode instalar a extensão manualmente, utilizando o cmdlet do PowerShell **AzureVMSqlServerExtension conjunto** . Por exemplo, o seguinte comando instala a extensão numa VM de servidor só de sistema operativo Windows e atribui-"SQLIaaSExtension".

    Set-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension" -Version "1.2"

Se atualizar para a versão mais recente da extensão do agente de IaaS SQL, tem de reiniciar o computador virtual depois de atualizar a extensão.

>[AZURE.NOTE] Se instalar manualmente a extensão de agente do SQL Server IaaS numa VM do Windows Server, tem de utilizar e gerir as suas funcionalidades utilizando os comandos do PowerShell. A interface do portal só está disponível para imagens da galeria do SQL Server.

## <a name="status"></a>Estado

É uma forma de verificar se a extensão está instalada ver o estado de agente no Portal do Azure. Selecione **todas as definições** na pá máquina virtual e, em seguida, clique em **extensões**. Deverá visualizar a extensão de **SQLIaaSExtension** listada.

![Extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-portal.png)

Também pode utilizar o cmdlet do Powershell de Azure **Get-AzureVMSqlServerExtension** .

    Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"

O comando anterior confirma o agente é instalado e fornece informações de estado geral. Também pode obter informações de estado específico sobre a cópia de segurança automatizado e Patching com os seguintes comandos.

    $sqlext = Get-AzureRmVMSqlServerExtension -VMName "vmname" -ResourceGroupName "resourcegroupname"
    $sqlext.AutoPatchingSettings
    $sqlext.AutoBackupSettings

## <a name="removal"></a>Remoção   

No Portal do Azure, pode desinstalar a extensão clicando nas reticências na pá **extensões** das propriedades de máquina virtual. Em seguida, clique em **Eliminar**.

![Desinstalar a extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-sql-server-agent-extension/azure-rm-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o cmdlet do Powershell **AzureRmVMSqlServerExtension remover** .

    Remove-AzureRmVMSqlServerExtension -ResourceGroupName "resourcegroupname" -VMName "vmname" -Name "SQLIaasExtension"

## <a name="next-steps"></a>Próximos passos

Começar a utilizar um dos serviços na suportados pela extensão. Para obter mais detalhes, consulte os tópicos referenciados na secção [suportadas serviços](#supported-services) deste artigo.

Para mais informações sobre a execução do SQL Server em máquinas virtuais do Azure, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
