<properties
    pageTitle="Restaurar uma base de dados do Azure SQL para um ponto anterior no tempo (Azure portal) | Microsoft Azure"
    description="Restaure uma base de dados do Azure SQL para um ponto anterior no tempo."
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


# <a name="restore-an-azure-sql-database-to-a-previous-point-in-time-with-the-azure-portal"></a>Restaurar uma base de dados do Azure SQL para um ponto anterior no tempo com o portal do Azure


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-recovery-using-backups.md)
- [Restaurar em qualquer altura: PowerShell](sql-database-point-in-time-restore-powershell.md)

Este artigo mostra-lhe como restaurar a base de dados para um ponto anterior em tempo de [que base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md) através do portal Azure.

## <a name="restore-a-sql-database-to-a-previous-point-in-time"></a>Restaurar uma base de dados do SQL para um ponto anterior no tempo

Selecione uma base de dados para restaurar no portal do Azure:

1.  Abra o [Azure portal](https://portal.azure.com).
2.  No lado esquerdo do ecrã, selecione **mais serviços** > **bases de dados SQL**.
3.  Clique em da base de dados que pretende restaurar.
4.  Na parte superior da página da sua base de dados, selecione **Restaurar**:

    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Na página **Restaurar** , selecione a data e hora (em vez de UTC) para restaurar a base de dados e, em seguida, clique em **OK**:

    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-point-in-time-restore-portal/restore-details.png)

## <a name="monitor-the-restore-operation"></a>Monitorizar a operação de restauro

1. Depois de clicar em **OK** no passo anterior, clique no ícone de notificação na parte superior direita da página e clique na notificação de **base de dados SQL restaurar** para obter detalhes.

    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-point-in-time-restore-portal/notification-icon.png)

2. A página de base de dados do SQL restaurar abre com informações sobre o estado do restauro. Pode clicar no item de linha para obter mais detalhes:

    ![Restaurar uma base de dados do Azure SQL](./media/sql-database-point-in-time-restore-portal/inprogress.png)

 

## <a name="next-steps"></a>Próximos passos

- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- Para saber mais sobre cópias de segurança da base de dados do SQL Azure automatizado, consulte o artigo [da base de dados do SQL automatizado cópias de segurança](sql-database-automated-backups.md)
- Para saber mais sobre utilizar cópias de segurança automatizadas para recuperação, consulte o artigo [Restaurar uma base de dados a partir das cópias de segurança iniciado pelo serviço](sql-database-recovery-using-backups.md)
- Para saber mais sobre as opções de recuperação mais rápidas, consulte o artigo [Replicação de Geo ativo](sql-database-geo-replication-overview.md)  
- Para saber como utilizar as cópias de segurança automatizadas para o arquivo, consulte o artigo [Copiar de base de dados](sql-database-copy.md)
