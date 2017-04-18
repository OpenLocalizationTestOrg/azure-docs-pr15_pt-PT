<properties
   pageTitle="Determinar a compatibilidade de base de dados SQL utilizando SqlPackage.exe | Microsoft Azure"
   description="Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade de base de dados SQL, SqlPackage"
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

# <a name="determine-sql-database-compatibility-using-sqlpackageexe"></a>Determinar a compatibilidade de base de dados SQL utilizando SqlPackage.exe

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Classificação de actualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)

Neste artigo, saiba determinar se é compatível com a migrar a base de dados SQL utilizando o utilitário de linha de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) uma base de dados do SQL Server.

## <a name="using-sqlpackageexe"></a>Utilizar SqlPackage.exe

1. Abra uma linha de comandos e altere um diretório que contém a versão mais recente do sqlpackage.exe. Este utilitário é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [As ferramentas de dados do SQL Server para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou pode transferir a versão mais recente do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente a partir do Centro de transferências da Microsoft.
2. Execute o seguinte comando SqlPackage com os seguintes argumentos para o seu ambiente:

    ' sqlpackage.exe /Action:Export /ssn: < nome_servidor > /sdn: < nome_base_dados > /tf: /p:TableData < target_file > = < schema_name.table_name >>< ficheiro_de_saída > 2 > & 1'

  	| Argumento  | Descrição  |
  	|---|---|
  	| < nome_servidor >  | nome do servidor de origem  |
  	| < nome_base_dados >  | nome da base de dados de origem  |
  	| < target_file >  | nome do ficheiro e a localização de ficheiro BACPAC  |
  	| < schema_name.table_name >  | as tabelas para o qual os dados são saída para o ficheiro de destino  |
  	| < ficheiro_de_saída >  | o nome do ficheiro e a localização para o ficheiro de exportação com erros, se existirem  |

    O motivo para o argumento /p:TableName é que apenas pretendemos testar para compatibilidade de base de dados para exportar para o Azure SQL DB V12 em vez de exportar os dados de todas as tabelas. Infelizmente, o argumento de exportação para sqlpackage.exe não suporta extrair zero tabelas. Precisa de especificar pelo menos uma tabela, como uma única tabela pequenas. < Ficheiro_de_saída > contém o relatório de erros. O "> 2 > & 1" tubos de cadeia a saída padrão e o erro-padrão resultantes da execução de comando para o ficheiro de saída especificado.

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01.png)

3. Abra o ficheiro de exportação e reveja os erros de compatibilidade, se existirem. 

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage02.png)

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
[versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Corrigir problemas de compatibilidade de migração de base de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar uma base de dados compatível com SQL Server para a base de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
