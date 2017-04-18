<properties
    pageTitle="Automatizado a aplicação de patches para SQL Server VMs (clássico) | Microsoft Azure"
    description="Explica a funcionalidade de correcção automática para SQL Server máquinas virtuais em execução no Azure utilizando o modo clássico de implementação."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/26/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-classic"></a>Automatizado correcção para SQL Server em máquinas virtuais do Azure (clássico)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-automated-patching.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-patching.md)

Patching automatizado estabelece uma janela de manutenção para um Azure Máquina Virtual com o SQL Server. Só podem ser instaladas as atualizações automáticas durante esta janela de manutenção. Para o SQL Server, assegura que actualizações do sistema e qualquer reinicia associada ocorre à melhor hora possíveis para a base de dados. Patching automatizado depende da [Extensão do SQL Server IaaS agente](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para ver a versão do Gestor de recursos neste artigo, consulte o artigo [Automatizado corrigir para SQL Server no Gestor de recursos de máquinas virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar correcção automática, considere os pré-requisitos seguintes:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão do SQL Server**:

- SQL Server 2012
- SQL Server 2014
- SQL Server 2016

**Azure PowerShell**:

- [Instalar os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md).

**Extensão do SQL Server IaaS**:

- [Instale a extensão de IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para a aplicação de patches automática. Para VMs clássicos, tem de utilizar o PowerShell para configurar estas definições.

|Definição|Valores possíveis|Descrição|
|---|---|---|
|**Automatizado a aplicação de patches**|Ativar/desativar (desactivado)|Ativa ou desativa a aplicação de patches automática para uma máquina virtual Azure.|
|**Agenda de manutenção**|Todos os dias, segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira, Sábado, Domingo|A agenda para transferir e instalar atualizações do Windows, SQL Server e Microsoft para a sua máquina virtual.|
|**Hora de início de manutenção**|24 de 0|A hora de início local para atualizar a máquina virtual.|
|**Duração da janela de manutenção**|30 180|O número de minutos permitidos para concluir a transferência e instalação das atualizações.|
|**Categoria de patches**|Importante|A categoria de atualizações para transferir e instalar.|

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

O exemplo seguinte, PowerShell é utilizado para configurar o correcção automática numa existente VM de servidor de SQL. O comando **Novo AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para as atualizações automáticas.

    $aps = New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoPatchingSettings $aps | Update-AzureVM

Com base neste exemplo, a tabela seguinte descreve o efeito prático no destino Azure VM:

|Parâmetro|Efeito|
|---|---|
|**DayOfWeek**|Patches instalados cada Quinta-feira.|
|**MaintenanceWindowStartingHour**|Atualizações de início em 11:00 am.|
|**MaintenanceWindowsDuration**|Tem de estar instalados patches dentro de 120 minutos. Com base na hora de início, tem de concluir por 1:00 pm.|
|**PatchCategory**|A definição só é possível para este parâmetro é "Importante".|

Pode demorar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para desativar a aplicação de patches automatizado, execute o mesmo sem o ativar parâmetro - para o novo-AzureVMSqlServerAutoPatchingConfig de script. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a aplicação de patches automática.

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre outras tarefas de automatização disponíveis, consulte o artigo [SQL Server IaaS agente extensão](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para mais informações sobre a execução do SQL Server no Azure VMs, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
