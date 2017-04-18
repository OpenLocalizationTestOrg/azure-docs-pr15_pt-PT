<properties
   pageTitle="Restaurar um armazém de dados do Azure SQL (PowerShell) | Microsoft Azure"
   description="Tarefas do PowerShell para restaurar um armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="Lakshmi1812"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/21/2016"
   ms.author="lakshmir;barbkess;sonyama"/>

# <a name="restore-an-azure-sql-data-warehouse-powershell"></a>Restaurar um armazém de dados do Azure SQL (PowerShell)

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Portal][]
- [PowerShell][]
- [RESTO][]

Este artigo vai aprender restaurar um armazém de dados SQL Azure através do PowerShell.

## <a name="before-you-begin"></a>Antes de começar

**Verifique se a sua capacidade DTU.** Cada armazém de dados SQL está alojado ao SQL server (por exemplo, myserver.database.windows.net) que tem uma quota DTU predefinida.  Antes de poder restaurar um armazém de dados SQL, certifique-se de que o seu servidor SQL tem suficiente quota DTU restante para a base de dados a ser restaurada. Para saber como calcular DTU necessário ou para pedir DTU mais, consulte o artigo [solicitar uma alteração de quota DTU][].

### <a name="install-powershell"></a>Instalar o PowerShell

Para poder utilizar o Azure PowerShell com armazém de dados do SQL, terá de instalar o Azure PowerShell versão 1.0 ou superior.  Pode verificar a sua versão executando **módulo Get - ListAvailable-nome AzureRM**.  Pode ser instalada a versão mais recente do [Microsoft Web plataforma Installer][].  Para mais informações sobre como instalar a versão mais recente, consulte o artigo [como instalar e configurar o Azure PowerShell][].

## <a name="restore-an-active-or-paused-database"></a>Restaurar uma base de dados ativo ou em pausa

Para restaurar uma base de dados a partir de um instantâneo utilize o cmdlet do PowerShell [AzureRmSqlDatabase restaurar][] .

1. Abra o Windows PowerShell.
2. Ligar à sua conta Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados a ser restaurada.
4. Liste os pontos de restauro da base de dados.
5. Selecione o ponto de restauro desejadas utilizando o RestorePointCreationDate.
6. Restaure a base de dados para o ponto de restauro pretendida.
7. Verifique se a base de dados restaurado está online.

```Powershell

$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# List the last 10 database restore points
((Get-AzureRMSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName ($DatabaseName).RestorePointCreationDate)[-10 .. -1]

# Or list all restore points
Get-AzureRmSqlDatabaseRestorePoints -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Get the specific database to restore
$Database = Get-AzureRmSqlDatabase -ResourceGroupName $ResourceGroupName -ServerName $ServerName -DatabaseName $DatabaseName

# Pick desired restore point using RestorePointCreationDate
$PointInTime="<RestorePointCreationDate>"  

# Restore database from a restore point
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromPointInTimeBackup –PointInTime $PointInTime -ResourceGroupName $Database.ResourceGroupName -ServerName $Database.$ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $Database.ResourceID

# Verify the status of restored database
$RestoredDatabase.status

```

>[AZURE.NOTE] Quando tiver concluído a restaurar, pode configurar a sua base de dados recuperado pelos seguintes [Configurar a sua base de dados após a recuperação][].


## <a name="restore-a-deleted-database"></a>Restaurar uma base de dados eliminada

Para restaurar uma base de dados eliminado, utilize o cmdlet [AzureRmSqlDatabase restaurar][] .

1. Abra o Windows PowerShell.
2. Ligar à sua conta Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados eliminado para ser restaurada.
4. Obter a base de dados eliminado específico.
5. Restaure a base de dados eliminado.
6. Verifique se a base de dados restaurado está online.

```Powershell
$SubscriptionName="<YourSubscriptionName>"
$ResourceGroupName="<YourResourceGroupName>"
$ServerName="<YourServerNameWithoutURLSuffixSeeNote>"  # Without database.windows.net
$DatabaseName="<YourDatabaseName>"
$NewDatabaseName="<YourDatabaseName>"

Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Get the deleted database to restore
$DeletedDatabase = Get-AzureRmSqlDeletedDatabaseBackup -ResourceGroupName $ResourceGroupNam -ServerName $ServerName -DatabaseName $DatabaseName

# Restore deleted database
$RestoredDatabase = Restore-AzureRmSqlDatabase –FromDeletedDatabaseBackup –DeletionDate $DeletedDatabase.DeletionDate -ResourceGroupName $DeletedDatabase.ResourceGroupName -ServerName $DeletedDatabase.ServerName -TargetDatabaseName $NewDatabaseName –ResourceId $DeletedDatabase.ResourceID

# Verify the status of restored database
$RestoredDatabase.status
```

>[AZURE.NOTE] Quando tiver concluído a restaurar, pode configurar a sua base de dados recuperado pelos seguintes [Configurar a sua base de dados após a recuperação][].


## <a name="restore-from-an-azure-geographical-region"></a>Restaurar a partir de uma região geográfica Azure

Para recuperar uma base de dados, utilize o cmdlet [AzureRmSqlDatabase restaurar][] .

1. Abra o Windows PowerShell.
2. Ligar à sua conta Azure e listar todas as subscrições associadas à sua conta.
3. Selecione a subscrição que contém a base de dados a ser restaurada.
4. Obter a base de dados que pretende recuperar.
5. Crie o pedido de recuperação para a base de dados.
6. Verificar o estado da base de dados restaurado geo.

```Powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Select-AzureRmSubscription -SubscriptionName "<Subscription_name>"

# Get the database you want to recover
$GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourServerName>" -DatabaseName "<YourDatabaseName>"

# Recover database
$GeoRestoredDatabase = Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "<YourResourceGroupName>" -ServerName "<YourTargetServer>" -TargetDatabaseName "<NewDatabaseName>" –ResourceId $GeoBackup.ResourceID

# Verify that the geo-restored database is online
$GeoRestoredDatabase.status
```

>[AZURE.NOTE] Para configurar a sua base de dados depois de concluído o restauro, consulte o artigo [Configurar a sua base de dados após a recuperação][]. 


A base de dados recuperado será com capacidade de TDE se a base de dados de origem for ativado TDE.


## <a name="next-steps"></a>Próximos passos
Para saber mais sobre as funcionalidades de continuidade do negócio de edições de base de dados do Azure SQL, leia a [Descrição geral de continuidade do negócio base de dados do SQL Azure][].

<!--Image references-->

<!--Article references-->
[Azure descrição geral do continuidade de empresas base de dados SQL]: sql-database-business-continuity.md
[Solicitar uma alteração de quota DTU]: ./sql-data-warehouse-get-started-create-support-ticket.md#request-quota-change
[Configurar a sua base de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery
[Como instalar e configurar PowerShell do Azure]: powershell-install-configure.md
[Descrição geral]: ./sql-data-warehouse-restore-database-overview.md
[Portal]: ./sql-data-warehouse-restore-database-portal.md
[PowerShell]: ./sql-data-warehouse-restore-database-powershell.md
[RESTO]: ./sql-data-warehouse-restore-database-rest-api.md
[Configurar a sua base de dados após a recuperação]: ./sql-database-disaster-recovery.md#configure-your-database-after-recovery

<!--MSDN references-->
[Restaurar AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt693390.aspx

<!--Other Web references-->
[Azure Portal]: https://portal.azure.com/
[Microsoft Web plataforma Installer]: https://aka.ms/webpi-azps
