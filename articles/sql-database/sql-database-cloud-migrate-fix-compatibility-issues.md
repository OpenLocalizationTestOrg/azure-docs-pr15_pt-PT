<properties
   pageTitle="Corrigir problemas de compatibilidade de base de dados do SQL Server antes da migração para a base de dados SQL | Microsoft Azure"
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

# <a name="use-sql-azure-migration-wizard-to-fix-sql-server-database-compatibility-issues-before-migration-to-azure-sql-database"></a>Utilizar o Assistente de migração do SQL Azure para problemas de compatibilidade de base de dados SQL Server corrigir antes da migração para a base de dados do SQL Azure

> [AZURE.SELECTOR]
- Utilizar o [Assistente de migração Azure SQL](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Utilizar [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Utilizar [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

Neste artigo, saiba detetar e corrigir problemas de compatibilidade de base de dados do SQL Server utilizando o Assistente de migração do SQL Azure antes da migração para a base de dados do SQL Azure.

## <a name="using-sql-azure-migration-wizard"></a>Utilizar o Assistente de migração do SQL Azure

Utilize a ferramenta de CodePlex do [Assistente de migração do SQL Azure](http://sqlazuremw.codeplex.com/) gerar um script T-SQL a partir de uma base de dados de origem incompatíveis. Este script, em seguida, é transformado pelo Assistente para que seja compatível com a base de dados do SQL. Em seguida, ligue à base de dados do SQL Azure para executar o script. Esta ferramenta também analisa os ficheiros de rastreio para determinar problemas de compatibilidade. O script pode ser gerado com esquema apenas ou pode incluir dados no formato BCP. Documentação adicional, incluindo orientações passo a passo está disponível no CodePlex no [Assistente de migração do SQL Azure](http://sqlazuremw.codeplex.com/).  

 ![Diagrama de migração SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Esquema de incompatível não em todos os que são detetado pelo assistente pode ser corrigidos pelo seus incorporadas transformações. Script incompatível que não pode ser endereçado são reportados como erros, com comentários inseridos para o script gerado. Se número for detetado, utilize o Visual Studio ou SQL Server Management Studio para visualizar passo a passo e corrigir cada erro que não foi possível corrigir utilizando o Assistente de migração do SQL Server.

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migrar uma base de dados compatível com SQL Server para a base de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
