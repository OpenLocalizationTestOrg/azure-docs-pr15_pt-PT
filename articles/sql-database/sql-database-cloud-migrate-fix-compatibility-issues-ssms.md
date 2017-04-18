<properties
   pageTitle="Corrigir problemas de compatibilidade de base de dados do SQL Server utilizando o SQL Server Management Studio antes da migração para a base de dados SQL | Microsoft Azure"
   description="Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade, o Assistente de migração do SQL Azure"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-migrate"
   ms.date="08/24/2016"
   ms.author="carlrab"/>

# <a name="fix-sql-server-database-compatibility-issues-using-sql-server-management-studio-before-migration-to-sql-database"></a>Corrigir problemas de compatibilidade de base de dados do SQL Server utilizando o SQL Server Management Studio antes da migração para a base de dados SQL

> [AZURE.SELECTOR]
- Utilizar o [Assistente de migração Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utilizar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Utilizar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Os utilizadores avançados podem corrigir problemas de compatibilidade de base de dados do SQL Server utilizando o SQL Server Management Studio antes da migração para a base de dados do SQL Azure.


> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="using-sql-server-management-studio"></a>Utilizar o SQL Server Management Studio

Utilize o SQL Server Management Studio para corrigir problemas de compatibilidade com vários comandos Transact-SQL, tal como **Alterar a base de dados**. Este método é principalmente para utilizadores experientes que estão à vontade com a trabalhar Transact-SQL numa base de dados dinâmicos. Caso contrário, é recomendado que utilize SSDT. 



## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar uma base de dados compatível com SQL Server para a base de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
