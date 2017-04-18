<properties
    pageTitle="Copiar uma base de dados do Azure SQL | Microsoft Azure"
    description="Criar uma cópia da base de dados Azure SQL"
    services="sql-database"
    documentationCenter=""
    authors="anosov1960"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="10/24/2016"
    ms.author="sstein; sashan"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>



# <a name="copy-an-azure-sql-database"></a>Copiar uma base de dados do Azure SQL

> [AZURE.SELECTOR]
- [Descrição geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Pode utilizar o Azure [base de dados SQL automatizado cópias de segurança](sql-database-automated-backups.md) para criar uma cópia da base de dados SQL. A cópia da base de dados utiliza a mesma tecnologia como a funcionalidade de replicação geo. Mas, ao contrário de replicação geo-termina a ligação de replicação como depois da fase de propagação está concluída. Por conseguinte, a base de dados de cópia é um instantâneo da base de dados de origem partir a hora do pedido de cópia.  
Pode criar a cópia da base de dados no mesmo servidor ou um servidor diferente. Nível de service camada e de desempenho (camada comparar) a cópia da base de dados são os mesmos como a base de dados de origem por predefinição. Ao utilizar a API, pode selecionar um nível de desempenho diferentes dentro da mesma camada de serviço (edition). Depois da cópia estiver concluída, a cópia torna-se uma base de dados totalmente funcional independente. Neste momento, pode atualizar ou alterá-lo para qualquer edição. Os inícios de sessão, os utilizadores e permissões podem ser geridas de forma independente.  

Quando copia uma base de dados no mesmo servidor lógico, os mesma os inícios de sessão podem ser utilizados em ambas as bases de dados. O que utilizar para copiar a base de dados principal de segurança torna-se o proprietário da base de dados (DBO) numa nova base de dados. Todos os utilizadores de base de dados, as respetivas permissões e seus identificadores de segurança (SID) são copiadas para a cópia da base de dados.  

Quando copia uma base de dados para um servidor lógico diferente, o principal no novo servidor de segurança torna-se o proprietário da base de dados na nova base de dados. Se utiliza o [contidas utilizadores de base de dados](sql-database-manage-logins.md) para o acesso a dados assegura principais e secundários bases de dados tem sempre as mesmas credenciais de utilizador para que depois de concluída a cópia que possa imediatamente acedê-la com as mesmas credenciais. Se utiliza o [Azure Active Directory](../active-directory/active-directory-whatis.md), pode eliminar completamente a necessidade de gerir credenciais na cópia. No entanto, quando copia a base de dados para um novo servidor, o acesso de início de sessão com base geralmente não funcionam porque os inícios de sessão não existe no servidor novo. Veja [como gerir a segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md) para saber mais sobre a gestão de inícios de sessão ao copiar uma base de dados para um servidor lógico diferente. 

Para copiar uma base de dados do SQL, precisa do seguinte:

- Uma subscrição do Azure. Se precisar de uma subscrição do Azure simplesmente clique **Versão de avaliação gratuita** na parte superior desta página e, em seguida, regresse para concluir deste artigo.
- Base de dados SQL para copiar. Se não tiver uma base de dados do SQL, crie uma seguir os passos neste artigo: [criar a primeira base de dados do Azure SQL](sql-database-get-started.md).

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [copiar uma base de dados do Azure SQL através do portal Azure](sql-database-copy-portal.md) para copiar uma base de dados através do portal Azure.
- Consulte o artigo [copiar uma base de dados do Azure SQL através do PowerShell](sql-database-copy-powershell.md) para copiar uma base de dados através do PowerShell.
- Consulte o artigo [copiar uma base de dados do Azure SQL utilizando o T-SQL](sql-database-copy-transact-sql.md) para copiar uma base de dados utilizando o Transact-SQL.
- Veja [como gerir a segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md) para saber mais sobre a gestão de utilizadores e inícios de sessão ao copiar uma base de dados para um servidor lógico diferente.



## <a name="additional-resources"></a>Recursos adicionais

- [Gerir os inícios de sessão](sql-database-manage-logins.md)
- [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md)
- [Exportar a base de dados para um BACPAC](sql-database-export.md)
- [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- [Documentação de base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
