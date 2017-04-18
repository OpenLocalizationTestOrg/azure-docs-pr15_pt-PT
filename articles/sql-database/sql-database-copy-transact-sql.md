<properties 
    pageTitle="Copiar uma base de dados do Azure SQL utilizando o Transact-SQL | Microsoft Azure" 
    description="Criar cópia de uma base de dados do Azure SQL utilizando o Transact-SQL" 
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


# <a name="copy-an-azure-sql-database-using-transact-sql"></a>Copiar uma base de dados do Azure SQL utilizando o Transact-SQL


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)


Os passos seguintes mostram-lhe como copiar uma base de dados SQL com Transact-SQL para o mesmo servidor ou um servidor diferente. A operação de cópia da base de dados utiliza a instrução de [Criar a base de dados](https://msdn.microsoft.com/library/ms176061.aspx) .

Para concluir os passos neste artigo precisa do seguinte:

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure simplesmente clique **Versão de avaliação gratuita** na parte superior desta página e, em seguida, regresse para concluir deste artigo.
- Uma base de dados do Azure SQL. Se não tiver uma base de dados do SQL, crie uma seguir os passos neste artigo: [criar a primeira base de dados do Azure SQL](sql-database-get-started.md).
- [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/ms174173.aspx). Se não tiver SSMS ou se funcionalidades descritas neste artigo não estão disponíveis, [Transferir a versão mais recente](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="copy-your-sql-database"></a>Copiar a base de dados SQL

Inicie sessão na base de dados principal utilizando o início de sessão principal ao nível do servidor ou o início de sessão que criou a base de dados que pretende copiar. Inícios de sessão que não são o capital de nível do servidor tem de ser membros da função Gestor de BD para copiar as bases de dados. Para mais informações sobre os inícios de sessão e ligar ao servidor, consulte o artigo [Gerir inícios de sessão](sql-database-manage-logins.md).

Inicie a base de dados de origem com a instrução de [Criar a base de dados](https://msdn.microsoft.com/library/ms176061.aspx) de cópia. Executar esta declaração de inicia o processo de copiar a base de dados. Uma vez que copiar uma base de dados é um processo assíncrono, a instrução CREATE DATABASE devolve antes da base de dados conclui copiar.


### <a name="copy-a-sql-database-to-the-same-server"></a>Copiar uma base de dados do SQL no mesmo servidor

Inicie sessão na base de dados principal utilizando o início de sessão principal ao nível do servidor ou o início de sessão que criou a base de dados que pretende copiar. Inícios de sessão que não são o capital de nível do servidor tem de ser membros da função Gestor de BD para copiar as bases de dados.

Cópias este comando BaseDeDados1 para uma nova base de dados com o nome Database2 no mesmo servidor. Dependendo do tamanho da base de dados a operação de cópia poderá demorar algum tempo para concluir.

    -- Execute on the master database.
    -- Start copying.
    CREATE DATABASE Database1_copy AS COPY OF Database1;

### <a name="copy-a-sql-database-to-a-different-server"></a>Copiar uma base de dados do SQL para um servidor diferente

Inicie sessão na base de dados principal do servidor de destino, o servidor de base de dados do Azure SQL que a nova base de dados tem de ser criado. Utilize um início de sessão tem o mesmo nome e palavra-passe como proprietário da base de dados (DBO) da base de dados de origem no servidor de base de dados do Azure SQL de origem. O início de sessão no servidor de destino tem também ser um membro da função de Gestor de BD ou login principal ao nível do servidor.

Este comando copia BaseDeDados1 on servidor1 - para uma nova base de dados com o nome Database2 servidor2. Dependendo do tamanho da base de dados a operação de cópia poderá demorar algum tempo para concluir.


    -- Execute on the master database of the target server (server2)
    -- Start copying from Server1 to Server2
    CREATE DATABASE Database1_copy AS COPY OF server1.Database1;
    

## <a name="monitor-the-progress-of-the-copy-operation"></a>Monitorizar o progresso da operação de cópia

Monitorize o processo de cópia consultando as vistas sys.databases e sys.dm_database_copies. Enquanto a copiar está em curso, a coluna state_desc da vista de sys.databases para a nova base de dados está definida para copiar.


- Se o copiar falhar, a coluna de state_desc da vista de sys.databases para a nova base de dados é definida como suspeita. Neste caso, executar a instrução DROP na nova base de dados e tente novamente mais tarde.
- Se o copiar é concluída com êxito, a coluna state_desc da vista de sys.databases para a nova base de dados está definida para ONLINE. Neste caso, a cópia estiver concluído e a nova base de dados é uma base de dados normal, podem ser alteradas independentes da base de dados de origem.

> [AZURE.NOTE] -Se optar por cancelar a cópia enquanto este estiver em curso, execute a instrução [DROP DATABASE](https://msdn.microsoft.com/library/ms178613.aspx) numa nova base de dados. Em alternativa, executando a instrução de remover base de dados da base de dados de origem também cancela o processo de cópia.


## <a name="resolve-logins-after-the-copy-operation-completes"></a>Resolver os inícios de sessão depois de concluída a operação de cópia

Depois da nova base de dados estiver online no servidor de destino, utilize a instrução [ALTER USER](https://msdn.microsoft.com/library/ms176060.aspx) para mapear os utilizadores da nova base de dados para os inícios de sessão no servidor de destino. Para resolver órfãos utilizadores, consulte o artigo [Resolver problemas de utilizadores órfão](https://msdn.microsoft.com/library/ms175475.aspx). Consulte também o artigo [como gerir a segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md).

Todos os utilizadores a nova base de dados mantêm as permissões que tinham da base de dados de origem. O utilizador que iniciou a cópia da base de dados torna-se o proprietário da base de dados da base de dados nova e é atribuído um novo identificador de segurança (SID). Depois da copiar é concluída com êxito e antes de outros utilizadores são novamente mapeados, apenas o início de sessão que iniciou a copiar, o proprietário da base de dados (DBO), pode iniciar sessão para a nova base de dados.


## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [copiar uma base de dados do Azure SQL](sql-database-copy.md) para uma descrição geral de copiar uma base de dados do SQL Azure.
- Consulte o artigo [copiar uma base de dados do Azure SQL através do portal Azure](sql-database-copy-portal.md) para copiar uma base de dados através do portal Azure.
- Consulte o artigo [copiar uma base de dados do Azure SQL através do PowerShell](sql-database-copy-powershell.md) para copiar uma base de dados através do PowerShell.
- Veja [como gerir a segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md) para saber mais sobre a gestão de utilizadores e inícios de sessão ao copiar uma base de dados para um servidor lógico diferente.



## <a name="additional-resources"></a>Recursos adicionais

- [Gerir os inícios de sessão](sql-database-manage-logins.md)
- [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md)
- [Exportar a base de dados para um BACPAC](sql-database-export.md)
- [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- [Documentação de base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)


