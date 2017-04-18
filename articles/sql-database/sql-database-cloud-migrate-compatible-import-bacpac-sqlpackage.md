<properties
   pageTitle="Importar para a base de dados SQL a partir de um ficheiro BACPAC com SqlPackage"
   description="Microsoft Azure base de dados SQL, migração de base de dados, importar base de dados, importar ficheiro BACPAC, sqlpackage"
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

# <a name="import-to-sql-database-from-a-bacpac-file-using-sqlpackage"></a>Importar para a base de dados SQL a partir de um ficheiro BACPAC com SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar para a base de dados SQL de um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) utilizando o utilitário da linha de comandos [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx) . Este utilitário é fornecido com as versões mais recentes do [SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx) e [As ferramentas de dados do SQL Server para Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx), ou pode transferir a versão mais recente do [SqlPackage](https://www.microsoft.com/en-us/download/details.aspx?id=53876) diretamente a partir do Centro de transferências da Microsoft.


> [AZURE.NOTE] Os seguintes passos partem do pressuposto de que já tem aprovisionado um servidor de base de dados SQL, ter as informações de ligação à mão e verificou que a sua base de dados de origem é compatível.

## <a name="import-from-a-bacpac-file-into-azure-sql-database-using-sqlpackage"></a>Importar a partir de um ficheiro BACPAC para base de dados do SQL Azure utilizando SqlPackage

Utilize os passos seguintes para utilizar o utilitário de linha de comandos [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) para importar uma base de dados do SQL Server compatível (ou base de dados Azure SQL) a partir de um ficheiro BACPAC.

> [AZURE.NOTE] Os seguintes passos partem do pressuposto de que já tenham aprovisionado um servidor de base de dados do Azure SQL e ter as informações de ligação à mão.

1. Abra uma linha de comandos e altere um diretório que contém o utilitário da linha de comandos sqlpackage.exe - este utilitário é fornecido com Visual Studio e do SQL Server.
2. Execute o seguinte comando sqlpackage.exe com os seguintes argumentos para o seu ambiente:

    `sqlpackage.exe /Action:Import /tsn:< server_name > /tdn:< database_name > /tu:< user_name > /tp:< password > /sf:< source_file >`

  	| Argumento  | Descrição  |
  	|---|---|
  	| < nome_servidor >  | nome do servidor de destino  |
  	| < nome_base_dados >  | nome da base de dados de destino  |
  	| < nomedoutilizador >  | o nome de utilizador no servidor de destino |
  	| < palavra-passe >  | palavra-passe de utilizador  |
  	| < source_file >  | o nome do ficheiro e a localização para o ficheiro BACPAC ser importado  |

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
