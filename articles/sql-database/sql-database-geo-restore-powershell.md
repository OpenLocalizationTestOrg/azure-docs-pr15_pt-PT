<properties
    pageTitle="Restaurar uma base de dados do SQL Azure a partir de uma cópia de segurança geo redundantes (PowerShell) | Microsoft Azure"
    description="Restaurar uma base de dados do SQL Azure para um novo servidor a partir de uma cópia de segurança geo redundantes"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="powershell"
    ms.workload="NA"
    ms.date="07/17/2016"
    ms.author="sstein"/>

# <a name="restore-an-azure-sql-database-from-a-geo-redundant-backup-by-using-powershell"></a>Restaurar uma base de dados do SQL Azure a partir de uma cópia de segurança geo redundantes utilizando o PowerShell


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-recovery-using-backups.md)
- [Restauro Geo: Portal Azure](sql-database-geo-restore-portal.md)

Este artigo mostra-lhe como restaurar a base de dados para um novo servidor utilizando geo restaurar. Isto pode ser feito através do PowerShell.

[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="geo-restore-your-database-into-a-standalone-database"></a>Geo-restaurar a base de dados para uma base de dados autónomo

1. Obter a geo redundantes cópia de segurança da base de dados que pretende restaurar utilizando o [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Iniciar o restauro da cópia de segurança geo redundantes utilizando o [restaurar-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID -Edition "Standard" -RequestedServiceObjectiveName "S2"


## <a name="geo-restore-your-database-into-an-elastic-database-pool"></a>Geo-restaurar a base de dados para um agrupamento de base de dados flexível

1. Obter a geo redundantes cópia de segurança da base de dados que pretende restaurar utilizando o [Get-AzureRmSqlDatabaseGeoBackup] (https://msdn.microsoft.com/library/azure/mt693388(v=azure.300\).aspx) cmdlet.

        $GeoBackup = Get-AzureRmSqlDatabaseGeoBackup -ResourceGroupName "resourcegroup01" -ServerName "server01" -DatabaseName "database01"

2. Iniciar o restauro da cópia de segurança geo redundantes utilizando o [restaurar-AzureRmSqlDatabase] (https://msdn.microsoft.com/library/azure/mt693390(v=azure.300\).aspx) cmdlet. Especifique o nome do conjunto de dados que pretende restaurar a base de dados para.

        Restore-AzureRmSqlDatabase –FromGeoBackup -ResourceGroupName "TargetResourceGroup" -ServerName "TargetServer" -TargetDatabaseName "RestoredDatabase" –ResourceId $GeoBackup.ResourceID –ElasticPoolName "elasticpool01"  


## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md).
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte a [base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md).
- Para saber como utilizar as cópias de segurança automatizadas para a recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md).
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md).  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md).
