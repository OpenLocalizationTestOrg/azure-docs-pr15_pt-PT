<properties
    pageTitle="Restaurar uma base de dados do Azure SQL eliminado (Azure portal) | Microsoft Azure"
    description="Restaure uma base de dados do Azure SQL eliminado (Azure portal)."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/12/2016"
    ms.author="sstein"
    ms.workload="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="restore-a-deleted-azure-sql-database-using-the-azure-portal"></a>Restaurar uma base de dados do Azure SQL eliminada utilizando o Portal do Azure

> [AZURE.SELECTOR]
- [Descrição geral](sql-database-recovery-using-backups.md)
- [**Restaurar eliminados DB: Portal**](sql-database-restore-deleted-database-portal.md)
- [Restaurar eliminados DB: PowerShell](sql-database-restore-deleted-database-powershell.md)

## <a name="select-the-database-to-restore"></a>Selecione a base de dados para restaurar 

Para restaurar uma base de dados eliminada no portal do Azure:

1.  No [portal do Azure](https://portal.azure.com), clique em **mais serviços** > **servidores SQL Server**.
3.  Selecione o servidor que continha a base de dados que pretende restaurar.
4.  Desloque para baixo para a secção **operações** de pá seu servidor e selecione **eliminados bases de dados**: ![restaurar uma base de dados do Azure SQL](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Selecione a base de dados que pretende restaurar.
6.  Especifique um nome de base de dados e clique em **OK**:

    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)


## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md)
