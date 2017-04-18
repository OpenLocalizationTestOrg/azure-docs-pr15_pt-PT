<properties
    pageTitle="Cópia de segurança automática para máquinas de virtuais do SQL Server (Gestor de recurso) | Microsoft Azure"
    description="Explica a funcionalidade de cópia de segurança automatizado para SQL Server em máquinas virtuais do Azure utilizando o Gestor de recursos de executar. "
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
    ms.date="07/14/2016"
    ms.author="jroth" />

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-resource-manager"></a>Cópia de segurança automática para SQL Server em máquinas virtuais do Azure (Gestor de recurso)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-backup.md)

Cópia de segurança automatizada configura automaticamente [Gerido cópia de segurança do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bases de dados numa VM Azure a executar o SQL Server 2014 Standard ou Enterprise. Permite-lhe configurar cópias de segurança regulares da base de dados que utilizam o armazenamento de Blobs do Azure resistentes. Cópia de segurança automatizada depende da [Extensão do SQL Server IaaS agente](virtual-machines-windows-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]modelo de implementação clássica. Para ver a versão clássica deste artigo, consulte o artigo [Cópia de segurança automatizado para SQL Server em máquinas virtuais de Azure clássica](virtual-machines-windows-classic-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a cópia de segurança automatizado, considere os pré-requisitos seguintes:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão/edição do SQL Server**:

- SQL Server 2014 padrão
- SQL Server 2014 Enterprise

**Configuração da base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo

**Azure PowerShell**:

- [Instalar os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md) se planeia configurar cópia de segurança automatizado com PowerShell.

>[AZURE.NOTE] Cópia de segurança automatizada baseia-se na extensão do agente de SQL Server IaaS. Imagens da Galeria de máquina virtual SQL atuais adicionar esta extensão por predefinição. Para mais informações, consulte o artigo [SQL Server IaaS agente extensão](virtual-machines-windows-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizado. Os passos de configuração real variam dependendo de se utiliza o portal de Azure ou os comandos do PowerShell de Windows Azure.

|Definição|Intervalo (predefinição)|Descrição|
|---|---|---|
|**Cópia de segurança automatizada**|Ativar/desativar (desactivado)|Ativa ou desativa a cópia de segurança automatizado para uma VM Azure a executar o SQL Server 2014 Standard ou Enterprise.|
|**Período de retenção**|1-30 dias (30 dias)|O número de dias para guardar uma cópia de segurança.|
|**Conta de armazenamento**|Conta de armazenamento Azure (a conta de armazenamento criada para a VM especificada)|Uma conta de armazenamento Azure a utilizar para armazenar ficheiros de cópia de segurança automatizado no armazenamento blob do. Um contentor é criado neste localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e nome do computador.|
|**Encriptação**|Ativar/desativar (desactivado)|Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança encontram-se na conta de armazenamento especificada no mesmo contentor automaticbackup utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, é gerado um certificado novo com essa palavra-passe, mas o certificado antigo permanece restaurar prévias cópias de segurança.|
|**Palavra-passe**|Texto de palavra-passe (nenhum)|Uma palavra-passe para chaves de encriptação. Este procedimento só é necessário se encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que a cópia de segurança foi colocada.|

## <a name="configuration-in-the-portal"></a>Configuração no Portal
Pode utilizar o Portal do Azure para configurar a cópia de segurança automatizado durante o aprovisionamento ou para VMs existentes.

### <a name="new-vms"></a>Novos VMs
Utilize o Portal do Azure para configurar a cópia de segurança automatizado quando cria uma nova Virtual máquina do SQL Server 2014 no modelo de implementação de Gestor de recursos.

Na pá **definições do SQL Server** , selecione a **cópia de segurança automática**. A seguinte Azure portal captura de ecrã mostra o pá de **Cópia de segurança de automatizado SQL** .

![Configuração do SQL automatizado cópia de segurança no portal do Azure](./media/virtual-machines-windows-sql-automated-backup/azure-sql-arm-autobackup.png)

Contexto, consulte o tópico concluído no [aprovisionamento uma máquina de virtual do SQL Server no Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione o seu máquina de virtual do SQL Server. Em seguida, selecione a secção pá as **definições de** **configuração do SQL Server** .

![SQL automatizado cópia de segurança para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-existing-vms.png)

No pá **configuração do SQL Server** , clique no botão **Editar** na secção de cópia de segurança automática.

![Configurar a cópia de segurança de automatizado SQL para VMs existentes](./media/virtual-machines-windows-sql-automated-backup/azure-sql-rm-autobackup-configuration.png)

Quando tiver terminado, clique no botão **OK** na parte inferior da pá a **configuração do SQL Server** para guardar as alterações.

Se estiver a activar cópia de segurança automatizado pela primeira vez, o Azure configura o SQL Server IaaS Agent em segundo plano. Durante este período de tempo, o portal do Azure poderá não o mostrar que a cópia de segurança automatizado está configurada. Aguarde alguns minutos para que o agente de ser instalado, configurado. Depois de que o portal do Azure vai refletir as novas definições.

>[AZURE.NOTE] Também pode configurar a cópia de segurança automatizado utilizando um modelo. Para mais informações, consulte o artigo [Guia de introdução Azure modelo para cópia de segurança automatizado](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-autobackup-update).

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

Depois de aprovisionar o VM SQL, utilize o PowerShell para configurar a cópia de segurança automatizado.

No seguinte exemplo PowerShell, a cópia de segurança automatizado está configurada para uma 2014 VM existente do SQL Server. O comando **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** configura as definições de cópia de segurança automatizado para armazenar as cópias de segurança da conta de armazenamento Azure associada a máquina virtual. Estas cópias de segurança serão retidas para 10 dias. O comando **Definir AzureRmVMSqlServerExtension** atualiza a VM Azure especificado com estas definições.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriodInDays 10 -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Pode demorar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para ativar a encriptação, modifique o script para passar o parâmetro **EnableEncryption** juntamente com uma palavra-passe (cadeia segura) para o parâmetro **CertificatePassword** anterior. O seguinte script permite que as definições de cópia de segurança automatizado no exemplo anterior e adiciona a encriptação.

    $vmname = "vmname"
    $resourcegroupname = "resourcegroupname"
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = AzureRM.Compute\New-AzureVMSqlServerAutoBackupConfig -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword -ResourceGroupName $resourcegroupname

    Set-AzureRmVMSqlServerExtension -AutoBackupSettings $autobackupconfig -VMName $vmname -ResourceGroupName $resourcegroupname

Para desativar a cópia de segurança automática, execute o script mesmo sem a **-Ativar** parâmetro para o comando **AzureRM.Compute\New AzureVMSqlServerAutoBackupConfig** . Falta da **-Ativar** parâmetro assinala o comando para desativar a funcionalidade. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a cópia de segurança automatizado.

>[AZURE.NOTE] Remover o SQL Server IaaS Agent não remove as definições de cópia de segurança automatizado configuradas anteriormente. Deverá desactivar a cópia de segurança automatizado antes de desativar ou desinstalar o SQL Server IaaS Agent.

## <a name="next-steps"></a>Próximos passos

Cópia de segurança automatizada configura gerido cópia de segurança Azure VMs. Por isso, é importante para [Rever a documentação para cópia de segurança geridos](https://msdn.microsoft.com/library/dn449496.aspx) compreender o comportamento e implicações.

Pode localizar adicional cópia de segurança e restaurar orientações para SQL Server no Azure VMs no seguinte tópico: [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte o artigo [SQL Server IaaS agente extensão](virtual-machines-windows-sql-server-agent-extension.md).

Para mais informações sobre a execução do SQL Server no Azure VMs, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
