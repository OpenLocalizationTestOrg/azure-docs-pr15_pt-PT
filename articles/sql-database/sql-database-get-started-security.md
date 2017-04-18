<properties
    pageTitle="Tutorial de base de dados SQL: introdução ao segurança"
    description="Saiba como criar contas de utilizador para aceder e gerir uma base de dados."
    keywords=""
    services="sql-database"
    documentationCenter=""
    authors="CarlRabeler"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="08/17/2016"
    ms.author="carlrab"/>

# <a name="sql-database-tutorial-create-sql-database-user-accounts-to-access-and-manage-a-database"></a>Tutorial de base de dados SQL: criar contas de utilizador para aceder e gerir uma base de dados de base de dados SQL


> [AZURE.SELECTOR]
- [Obter tutorial de introdução](sql-database-get-started-security.md)
- [Conceder acesso](sql-database-manage-logins.md)

Neste tutorial, saiba como utilizar o SQL Server Management Studio (SSMS) para:

- Inicie sessão na base de dados do SQL utilizando um início de sessão principal ao nível do servidor.
- Crie uma conta de utilizador de base de dados SQL.
- Conceda um utilizador de base de dados SQL [permissões de proprietário](https://msdn.microsoft.com/library/ms189121.aspx#Anchor_0).
- Ligar a uma base de dados SQL com uma conta de utilizador que não seja um principal ao nível do servidor.

[AZURE.INCLUDE [Login](../../includes/azure-getting-started-portal-login.md)]


[AZURE.INCLUDE [Create SQL Database logical server](../../includes/sql-database-sql-server-management-studio-connect-server-principal.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-database-user.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-grant-database-user-dbo-permissions.md)]


[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-sql-server-management-studio-connect-user.md)]


## <a name="next-steps"></a>Próximos passos
Agora que já foi concluída este tutorial de base de dados SQL e criado uma conta de utilizador e atribuídas ao utilizador permissões de dbo de conta, está pronto para obter mais informações sobre [segurança da base de dados SQL](sql-database-manage-logins.md).


