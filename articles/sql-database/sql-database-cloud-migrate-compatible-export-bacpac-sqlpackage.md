<properties
   pageTitle="Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SqlPackage | Microsoft Azure"
   description="Microsoft Azure base de dados SQL, migração de base de dados, exportar base de dados, Exportar ficheiro BACPAC, sqlpackage"
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

# <a name="export-a-sql-server-database-to-a-bacpac-file-using-sqlpackage"></a>Exportar uma base de dados do SQL Server para um ficheiro BACPAC com SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

Este artigo mostra como exportar uma base de dados do SQL Server para um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizando o utilitário da linha de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [As ferramentas de dados do SQL Server para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou pode transferir a versão mais recente do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente a partir do Centro de transferências da Microsoft.

1. Abra uma linha de comandos e altere um diretório que contém o utilitário da linha de comandos sqlpackage.exe - este utilitário é fornecido com Visual Studio e do SQL Server. Utilize a pesquisa no seu computador para encontrar o caminho no seu ambiente.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:

    ' sqlpackage.exe /Action:Export /ssn: < nome_servidor > /sdn: < nome_base_dados > /tf: < target_file >

  	| Argumento  | Descrição  |
  	|---|---|
  	| < nome_servidor >  | nome do servidor de origem  |
  	| < nome_base_dados >  | nome da base de dados de origem  |
  	| < target_file >  | nome do ficheiro e a localização de ficheiro BACPAC  |

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importar um BACPAC a base de dados do SQL Azure com SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importar um BACPAC para SqlPackage de base de dados do Azure SQL](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importar um BACPAC portal Azure de base de dados do SQL Azure](sql-database-import.md)
- [Importar um BACPAC PowerShell de base de dados do Azure SQL](sql-database-import-powershell.md)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
