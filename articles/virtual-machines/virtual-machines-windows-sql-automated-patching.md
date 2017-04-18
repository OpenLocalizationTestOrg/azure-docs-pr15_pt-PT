<properties
    pageTitle="Automatizado a aplicação de patches para SQL Server VMs (Gestor de recurso) | Microsoft Azure"
    description="Explica a funcionalidade Correcção automática para SQL Server máquinas virtuais em execução no Azure utilizando o Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="automated-patching-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Automatizado a aplicação de patches para SQL Server em máquinas virtuais do Azure (Gestor de recurso)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-automated-patching.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-patching.md)

Patching automatizado estabelece uma janela de manutenção para um Azure Máquina Virtual com o SQL Server. Só podem ser instaladas as atualizações automáticas durante esta janela de manutenção. Para o SQL Server, este rescriction assegura que actualizações do sistema e qualquer reinicia associada ocorre à melhor hora possíveis para a base de dados. Patching automatizado depende da [Extensão do SQL Server IaaS agente](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica. Para ver a versão clássica deste artigo, consulte o artigo [Automatizado corrigir para SQL Server em máquinas virtuais de Azure clássica](virtual-machines-windows-classic-sql-automated-patching.md).

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

- [Instalar os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md) se planeia configurar a aplicação de patches automatizado com o PowerShell.

>[AZURE.NOTE] Patching automatizado baseia-se na extensão do agente de SQL Server IaaS. Imagens da Galeria de máquina virtual SQL atuais adicionar esta extensão por predefinição. Para mais informações, consulte o artigo [SQL Server IaaS agente extensão](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para a aplicação de patches automática. Os passos de configuração real variam dependendo de se utiliza o portal de Azure ou os comandos do PowerShell de Windows Azure.

|Definição|Valores possíveis|Descrição|
|---|---|---|
|**Automatizado a aplicação de patches**|Ativar/desativar (desactivado)|Ativa ou desativa a aplicação de patches automática para uma máquina virtual Azure.|
|**Agenda de manutenção**|Todos os dias, segunda-feira, Terça-feira, quarta-feira, Quinta-feira, sexta-feira, Sábado, Domingo|A agenda para transferir e instalar atualizações do Windows, SQL Server e Microsoft para a sua máquina virtual.|
|**Hora de início de manutenção**|24 de 0|A hora de início local para atualizar a máquina virtual.|
|**Duração da janela de manutenção**|30 180|O número de minutos permitidos para concluir a transferência e instalação das atualizações.|
|**Categoria de patches**|Importante|A categoria de atualizações para transferir e instalar.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Pode utilizar o portal do Azure para configurar correcção automática durante o aprovisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novos VMs
Utilize o portal do Azure para configurar a aplicação de patches automatizado quando cria uma nova máquina de Virtual do SQL Server no modelo de implementação de Gestor de recursos.

Na pá **definições do SQL Server** , selecione a **aplicação de patches automática**. A seguinte Azure portal captura de ecrã mostra o pá **SQL automatizado corrigir** .

![No Azure portal automatizado aplicação de patches SQL](./media/virtual-machines-windows-sql-automated-patching/azure-sql-arm-patching.png)

Contexto, consulte o tópico concluído no [aprovisionamento uma máquina de virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione o seu máquina de virtual do SQL Server. Em seguida, selecione a secção pá as **definições de** **configuração do SQL Server** .

![SQL correcção automática para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-existing-vms.png)

No pá **configuração do SQL Server** , clique no botão **Editar** automatizadas correcção secção.

![Configurar o SQL automatizado corrigir para VMs existentes](./media/virtual-machines-windows-sql-automated-patching/azure-sql-rm-patching-configuration.png)

Quando tiver terminado, clique no botão **OK** na parte inferior da pá a **configuração do SQL Server** para guardar as alterações.

Se estiver a activar correcção automática pela primeira vez, o Azure configura o SQL Server IaaS Agent em segundo plano. Durante este período de tempo, o portal do Azure poderá não o mostrar que a aplicação de patches automática está configurada. Aguarde alguns minutos para que o agente de ser instalado, configurado. Depois de que o portal do Azure reflete as novas definições.

>[AZURE.NOTE] Também pode configurar automatizado aplicação de patches utilizando um modelo. Para mais informações, consulte o artigo [Guia de introdução Azure modelo de correcção automática](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autopatching-update).

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

Depois de aprovisionar o VM SQL, utilize o PowerShell para configurar a aplicação de patches automática.

O exemplo seguinte, PowerShell é utilizado para configurar o correcção automática numa existente VM de servidor de SQL. O comando **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig** configura uma nova janela de manutenção para as atualizações automáticas.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $aps = AzureRM.Compute\New-AzureVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"

    Set-AzureRmVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname

Com base neste exemplo, a tabela seguinte descreve o efeito prático no destino Azure VM:

|Parâmetro|Efeito|
|---|---|
|**DayOfWeek**|Patches instalados cada Quinta-feira.|
|**MaintenanceWindowStartingHour**|Atualizações de início em 11:00 am.|
|**MaintenanceWindowsDuration**|Tem de estar instalados patches dentro de 120 minutos. Com base na hora de início, tem de concluir por 1:00 pm.|
|**PatchCategory**|A definição só é possível para este parâmetro é **importante**.|

Pode demorar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para desativar a aplicação de patches automatizado, execute o script mesmo sem a **-Ativar** parâmetro para a **AzureRM.Compute\New AzureVMSqlServerAutoPatchingConfig**. Falta da **-Ativar** parâmetro assinala o comando para desativar a funcionalidade.

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre outras tarefas de automatização disponíveis, consulte o artigo [SQL Server IaaS agente extensão](virtual-machines-windows-sql-server-agent-extension.md).

Para mais informações sobre a execução do SQL Server no Azure VMs, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
