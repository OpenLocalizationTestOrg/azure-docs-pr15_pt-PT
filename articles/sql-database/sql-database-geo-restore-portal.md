<properties
    pageTitle="Restaurar uma base de dados do Azure SQL a partir de uma cópia de segurança automática (Azure portal) | Microsoft Azure"
    description="Restaure uma base de dados do Azure SQL a partir de uma cópia de segurança automática (Azure portal)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/18/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-an-azure-sql-database-from-an-automatic-backup-using-the-azure-portal"></a>Restaurar uma base de dados do Azure SQL a partir de uma cópia de segurança automática através do portal Azure


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-recovery-using-backups.md#geo-restore)
- [Restaurar Geo: PowerShell](sql-database-geo-restore-powershell.md)

Este artigo mostra-lhe como restaurar a base de dados de uma [cópia de segurança automática](sql-database-automated-backups.md) para um novo servidor com a [Opção Restaurar-Geo](sql-database-recovery-using-backups/.md#geo-restore) através do portal Azure.

## <a name="select-a-database-to-restore"></a>Selecione para restaurar uma base de dados

Para restaurar uma base de dados no portal do Azure, execute os seguintes passos:

1.  Aceda ao [Azure portal](https://portal.azure.com).
2.  No lado esquerdo do ecrã, selecione **+ Novo** > **bases de dados** > **Base de dados SQL**:

    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-geo-restore-portal/new-sql-database.png)

3.  Selecione a **cópia de segurança** como a origem e, em seguida, selecione a cópia de segurança que pretende restaurar. Especifique um nome de base de dados, um servidor que pretende restaurar a base de dados, e, em seguida, clique em **Criar**:
  
    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-geo-restore-portal/geo-restore.png)

Monitorize o estado da operação de restaurar, clicando no ícone de notificação no canto superior direito da página. 


## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md)
