<properties
    pageTitle="Extensão de agente do SQL Server para SQL Server VMs (clássico) | Microsoft Azure"
    description="Este tópico descreve como gerir a extensão do agente do SQL Server, que automatiza tarefas de administração do SQL Server específicas. Estes incluem cópia de segurança automatizado, correcção automática e integração com o Azure chave cofre. Este tópico utiliza o modo clássico de implementação."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/27/2016"
    ms.author="jroth"/>

# <a name="sql-server-agent-extension-for-sql-server-vms-classic"></a>Extensão de agente do SQL Server para SQL Server VMs (clássico)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-server-agent-extension.md)
- [Clássico](virtual-machines-windows-classic-sql-server-agent-extension.md)

É executado a extensão de agente IaaS (SQLIaaSAgent) do SQL Server em máquinas virtuais Azure para automatizar tarefas de administração. Este tópico fornece uma descrição geral dos serviços suportados pelo extensão, bem como as instruções de instalação, estado e remoção.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para ver a versão do Gestor de recursos neste artigo, consulte o artigo [Extensão de agente do SQL Server para SQL Server VMs Gestor de recursos](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="supported-services"></a>Serviços suportados

A extensão de agente do SQL Server IaaS suporta as seguintes tarefas de administração:

| Funcionalidade de administração | Descrição |
|---------------------|-------------------------------|
| **Cópia de segurança automática de SQL** | Automatiza o agendamento da cópias de segurança para todas as bases de dados para a instância predefinida do SQL Server na VM. Para mais informações, consulte o artigo [cópia de segurança para SQL Server em máquinas virtuais do Azure (clássico) automática](virtual-machines-windows-classic-sql-automated-backup.md).|
| **SQL automatizado a aplicação de patches** | Configura uma janela de manutenção durante o qual as atualizações para o seu VM podem demorar local, para que pode evitar atualizações durante as horas para sua carga de trabalho. Para mais informações, consulte o artigo [automatizado a aplicação de patches para SQL Server em máquinas virtuais do Azure (clássico)](virtual-machines-windows-classic-sql-automated-patching.md).|
| **Integração do cofre chave Azure** | Permite-lhe instalar e configurar o Azure chave cofre no seu VM de servidor SQL automaticamente. Para mais informações, consulte o artigo [Configurar Azure chave cofre integração para SQL Server no Azure VMs (clássico)](virtual-machines-windows-classic-ps-sql-keyvault.md).|

## <a name="prerequisites"></a>Pré-requisitos

Requisitos para utilizar o SQL Server IaaS agente no seu VM:

### <a name="operating-system"></a>Sistema operativo:

- Windows Server 2012
- Windows Server 2012 R2

### <a name="sql-server-versions"></a>Versões do SQL Server:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

### <a name="azure-powershell"></a>Azure PowerShell:

[Transferir e configurar os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md).

Inicie o Windows PowerShell e ligá-lo à sua subscrição do Azure com o comando **Adicionar AzureAccount** .

    Add-AzureAccount

Se tiver múltiplas subscrições, utilize **Selecione AzureSubscription** para selecionar a subscrição que contém o destino VM clássica.

    Select-AzureSubscription -SubscriptionName <subscriptionname>

Neste momento, pode obter uma lista das máquinas virtuais clássicas e os respetivos nomes de serviço associado com o comando **Get-AzureVM** .

    Get-AzureVM

## <a name="installation"></a>Instalação

Para VMs clássicos, tem de utilizar o PowerShell para a extensão de agente do SQL Server IaaS de instalar e configurar os serviços associados. Utilize o cmdlet do PowerShell **Set-AzureVMSqlServerExtension** para instalar a extensão. Por exemplo, o seguinte comando instala a extensão numa VM de servidor do Windows (clássico) e atribui-"SQLIaaSExtension".

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -ReferenceName "SQLIaasExtension" -Version "1.2" | Update-AzureVM

Se atualizar para a versão mais recente da extensão do agente de IaaS SQL, tem de reiniciar o computador virtual depois de atualizar a extensão.

>[AZURE.NOTE] Máquinas virtuais clássicas não tem uma opção para instalar e configurar a extensão do agente de IaaS SQL através do portal.

## <a name="status"></a>Estado

É uma forma de verificar se a extensão está instalada ver o estado de agente no Portal do Azure. Selecione **todas as definições** na pá máquina virtual e, em seguida, clique em **extensões**. Deverá visualizar a extensão de **SQLIaaSAgent** listada.

![Extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-portal.png)

Também pode utilizar o cmdlet do Powershell de Azure **Get-AzureVMSqlServerExtension** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Get-AzureVMSqlServerExtension

## <a name="removal"></a>Remoção   

No Portal do Azure, pode desinstalar a extensão clicando nas reticências na pá **extensões** das propriedades de máquina virtual. Em seguida, clique em **Eliminar**.

![Desinstalar a extensão de IaaS agente do SQL Server no Portal do Azure](./media/virtual-machines-windows-classic-sql-server-agent-extension/azure-sql-server-iaas-agent-uninstall.png)

Também pode utilizar o cmdlet do Powershell **AzureVMSqlServerExtension remover** .

    Get-AzureVM –ServiceName "service" –Name "vmname" | Remove-AzureVMSqlServerExtension | Update-AzureVM

## <a name="next-steps"></a>Próximos passos

Começar a utilizar um dos serviços na suportados pela extensão. Para obter mais detalhes, consulte os tópicos referenciados na secção [suportadas serviços](#supported-services) deste artigo.

Para mais informações sobre a execução do SQL Server em máquinas virtuais do Azure, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
