<properties
    pageTitle="Copiar uma base de dados do Azure SQL através do portal Azure | Microsoft Azure"
    description="Criar uma cópia da base de dados Azure SQL"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/19/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database-using-the-azure-portal"></a>Copiar uma base de dados do SQL Azure através do portal Azure

> [AZURE.SELECTOR]
- [Descrição geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Os passos seguintes mostram-lhe como copiar uma base de dados SQL com o [Azure portal](https://portal.azure.com) para o mesmo servidor ou um servidor diferente.

Para copiar uma base de dados do SQL, tem os seguintes itens:

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure simplesmente clique **Versão de avaliação gratuita** na parte superior desta página e, em seguida, regresse para concluir deste artigo.
- Base de dados SQL para copiar. Se não tiver uma base de dados do SQL, crie uma seguir os passos neste artigo: [criar a primeira base de dados do Azure SQL](sql-database-get-started.md).


## <a name="copy-your-sql-database"></a>Copiar a base de dados SQL

Abra a página de base de dados do SQL para a base de dados que pretende copiar:

1.  Aceda ao [Azure portal](https://portal.azure.com).
2.  Clique em **mais serviços** > **bases de dados SQL**e, em seguida, clique em da base de dados pretendido.
3.  Na página de base de dados SQL, clique em **Copiar**:

    ![Base de dados SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  Na página **Copiar** , é fornecido um nome de base de dados predefinido. Escreva um nome diferente, se pretender (todas as bases de dados num servidor tem de ter nomes únicos).
2.  Selecione um **servidor de destino**. O servidor de destino é onde a cópia da base de dados é criada. Pode copiar a base de dados para o mesmo servidor ou um servidor diferente. Pode criar um servidor ou selecione um servidor existente a partir da lista. 
3.  Depois de selecionar as opções de **servidor de destino**, o **agrupamento de base de dados flexível**e **preços camada** irão estar ativada. Se o servidor tiver um conjunto de dados, pode copiar a base de dados para o mesmo.
3.  Clique em **OK** para iniciar o processo de copiar.

    ![Base de dados SQL](./media/sql-database-copy-portal/copy-page.png)


## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorizar o progresso da operação de cópia

- Depois de iniciar a cópia, clique na notificação portal para obter detalhes.

    ![notificação][3]
 
    ![Monitor][4]


## <a name="verify-the-database-is-live-on-the-server"></a>Verifique se que a base de dados é direto no servidor

- Clique em **mais serviços** > **bases de dados SQL** e verifique se a nova base de dados está **Online**.


## <a name="resolve-logins"></a>Resolver os inícios de sessão

Para resolver os inícios de sessão depois de concluída a operação de cópia, consulte o artigo [resolver os inícios de sessão](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [copiar uma base de dados do Azure SQL](sql-database-copy.md) para uma descrição geral de copiar uma base de dados do SQL Azure.
- Consulte o artigo [copiar uma base de dados do Azure SQL através do PowerShell](sql-database-copy-powershell.md) para copiar uma base de dados através do PowerShell.
- Consulte o artigo [copiar uma base de dados do Azure SQL utilizando o T-SQL](sql-database-copy-transact-sql.md) para copiar uma base de dados utilizando o Transact-SQL.
- Veja [como gerir a segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md) para saber mais sobre a gestão de utilizadores e inícios de sessão ao copiar uma base de dados para um servidor lógico diferente.



## <a name="additional-resources"></a>Recursos adicionais

- [Gerir os inícios de sessão](sql-database-manage-logins.md)
- [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md)
- [Exportar a base de dados para um BACPAC](sql-database-export.md)
- [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- [Documentação de base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

