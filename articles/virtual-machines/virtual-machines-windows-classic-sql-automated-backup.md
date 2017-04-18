<properties
    pageTitle="Cópia de segurança automática para máquinas de virtuais do SQL Server (clássico) | Microsoft Azure"
    description="Explica a funcionalidade de cópia de segurança automatizado para SQL Server em máquinas virtuais do Azure utilizando o Gestor de recursos de executar. "
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

# <a name="automated-backup-for-sql-server-in-azure-virtual-machines-classic"></a>Cópia de segurança automatizada para SQL Server em máquinas virtuais do Azure (clássico)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-sql-automated-backup.md)
- [Clássico](virtual-machines-windows-classic-sql-automated-backup.md)

Cópia de segurança automatizada configura automaticamente [Gerido cópia de segurança do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx) para todos os novos e existentes bases de dados numa VM Azure a executar o SQL Server 2014 Standard ou Enterprise. Permite-lhe configurar cópias de segurança regulares da base de dados que utilizam o armazenamento de Blobs do Azure resistentes. Cópia de segurança automatizada depende da [Extensão do SQL Server IaaS agente](virtual-machines-windows-classic-sql-server-agent-extension.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Para ver a versão do Gestor de recursos neste artigo, consulte o artigo [Cópia de segurança automatizado para SQL Server no Gestor de recursos de máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar a cópia de segurança automatizado, considere os pré-requisitos seguintes:

**Sistema operativo**:

- Windows Server 2012
- Windows Server 2012 R2

**Versão/edição do SQL Server**:

- SQL Server 2014 padrão
- SQL Server 2014 Enterprise

>[AZURE.NOTE] SQL Server 2016 ainda não é suportada para cópia de segurança automatizado.

**Configuração da base de dados**:

- Bases de dados de destino tem de utilizar o modelo de recuperação completo.

**Azure PowerShell**:

- [Instalar os comandos do Azure PowerShell mais recentes](../powershell-install-configure.md).

**Extensão do SQL Server IaaS**:

- [Instale a extensão de IaaS do SQL Server](virtual-machines-windows-classic-sql-server-agent-extension.md).

## <a name="settings"></a>Definições

A tabela seguinte descreve as opções que podem ser configuradas para cópia de segurança automatizado. Para VMs clássicos, tem de utilizar o PowerShell para configurar estas definições.

|Definição|Intervalo (predefinição)|Descrição|
|---|---|---|
|**Cópia de segurança automatizada**|Ativar/desativar (desactivado)|Ativa ou desativa a cópia de segurança automatizado para uma VM Azure a executar o SQL Server 2014 Standard ou Enterprise.|
|**Período de retenção**|1-30 dias (30 dias)|O número de dias para guardar uma cópia de segurança.|
|**Conta de armazenamento**|Conta de armazenamento Azure (a conta de armazenamento criada para a VM especificada)|Uma conta de armazenamento Azure a utilizar para armazenar ficheiros de cópia de segurança automatizado no armazenamento blob do. Um contentor é criado neste localização para armazenar todos os ficheiros de cópia de segurança. A Convenção de nomenclatura de ficheiro de cópia de segurança inclui a data, hora e nome do computador.|
|**Encriptação**|Ativar/desativar (desactivado)|Ativa ou desativa a encriptação. Quando a encriptação está ativada, os certificados utilizados para restaurar a cópia de segurança encontram-se na conta de armazenamento especificada no mesmo contentor automaticbackup utilizando a mesma Convenção de nomenclatura. Se alterar a palavra-passe, é gerado um certificado novo com essa palavra-passe, mas o certificado antigo permanece restaurar prévias cópias de segurança.|
|**Palavra-passe**|Texto de palavra-passe (nenhum)|Uma palavra-passe para chaves de encriptação. Este procedimento só é necessário se encriptação está ativada. Para restaurar uma cópia de segurança encriptada, tem de ter a palavra-passe correta e o certificado relacionado que foi utilizado no momento que a cópia de segurança foi colocada.|

## <a name="configuration-with-powershell"></a>Configuração com PowerShell

No seguinte exemplo PowerShell, a cópia de segurança automatizado está configurada para uma 2014 VM existente do SQL Server. O comando **Novo AzureVMSqlServerAutoBackupConfig** configura as definições de cópia de segurança automatizado para armazenar as cópias de segurança na conta de armazenamento Azure especificada pela variável de $storageaccount. Estas cópias de segurança serão retidas para 10 dias. O comando **Definir AzureVMSqlServerExtension** atualiza a VM Azure especificado com estas definições.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Pode demorar alguns minutos para instalar e configurar o SQL Server IaaS Agent.

Para ativar encriptação, modifique o script para passar o parâmetro EnableEncryption juntamente com uma palavra-passe (cadeia segura) para o parâmetro CertificatePassword anterior. O seguinte script permite que as definições de cópia de segurança automatizado no exemplo anterior e adiciona a encriptação.

    $storageaccount = "<storageaccountname>"
    $storageaccountkey = (Get-AzureStorageKey -StorageAccountName $storageaccount).Primary
    $storagecontext = New-AzureStorageContext -StorageAccountName $storageaccount -StorageAccountKey $storageaccountkey
    $password = "P@ssw0rd"
    $encryptionpassword = $password | ConvertTo-SecureString -AsPlainText -Force  
    $autobackupconfig = New-AzureVMSqlServerAutoBackupConfig -StorageContext $storagecontext -Enable -RetentionPeriod 10 -EnableEncryption -CertificatePassword $encryptionpassword

    Get-AzureVM -ServiceName <vmservicename> -Name <vmname> | Set-AzureVMSqlServerExtension -AutoBackupSettings $autobackupconfig | Update-AzureVM

Para desativar a cópia de segurança automática, execute o script mesmo sem a **-Ativar** parâmetro para o **Novo AzureVMSqlServerAutoBackupConfig**. Tal como acontece com a instalação, pode demorar alguns minutos para desativar a cópia de segurança automatizado.

>[AZURE.NOTE] Desativar e desinstalar o SQL Server IaaS Agent não remove as definições de cópia de segurança gerido configuradas anteriormente. Deverá desactivar a cópia de segurança automatizado antes de desativar ou desinstalar o SQL Server IaaS Agent.

## <a name="next-steps"></a>Próximos passos

Cópia de segurança automatizada configura gerido cópia de segurança Azure VMs. Por isso, é importante para [Rever a documentação para cópia de segurança geridos](https://msdn.microsoft.com/library/dn449496.aspx) compreender o comportamento e implicações.

Pode localizar adicional cópia de segurança e restaurar orientações para SQL Server no Azure VMs no seguinte tópico: [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-backup-recovery.md).

Para obter informações sobre outras tarefas de automatização disponíveis, consulte o artigo [SQL Server IaaS agente extensão](virtual-machines-windows-classic-sql-server-agent-extension.md).

Para mais informações sobre a execução do SQL Server no Azure VMs, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
