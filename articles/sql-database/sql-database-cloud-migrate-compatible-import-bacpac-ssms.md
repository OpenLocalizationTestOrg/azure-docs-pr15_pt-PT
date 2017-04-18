<properties
   pageTitle="Migrar uma base de dados do SQL Server para a base de dados do SQL Azure | Microsoft Azure"
   description="Base do Microsoft Azure SQL, base de dados implementar, migração de base de dados, base de dados de importar, exportar base de dados, o Assistente de migração"
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

# <a name="import-from-bacpac-to-sql-database-using-ssms"></a>Importar a partir de BACPAC a base de dados SQL utilizando SSMS

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal do Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

Este artigo mostra como importar a partir de um ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) para base de dados SQL utilizando o Assistente Exportar dados camada de aplicação no SQL Server Management Studio.

> [AZURE.NOTE] Os seguintes passos partem do pressuposto de que já tenham aprovisionado a instância lógica Azure SQL e ter as informações de ligação à mão.

1. Certifique-se de que tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal para permanecer sincronizado com atualizações ao portal do Azure.

     > [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Ligar ao seu servidor de base de dados do Azure SQL, com o botão direito na pasta de **bases de dados** e clique em **pedido de camada de dados de importação...**

    ![Item de menu da aplicação de camada de dados de importação](./media/sql-database-cloud-migrate/MigrateUsingBACPAC03.png)

3.  Para criar a base de dados numa base de dados do SQL Azure, importar um ficheiro BACPAC do seu disco local ou selecione a conta de armazenamento Azure e contentor para o qual carregou o ficheiro BACPAC.

    ![Definições de importação](./media/sql-database-cloud-migrate/MigrateUsingBACPAC04.png)

     > [AZURE.IMPORTANT] Quando importar um BACPAC de armazenamento de Blobs do Azure, utilize o armazenamento padrão. Importar um BACPAC a partir do armazenamento premium não é suportada.

4.  Forneça o **novo nome da base de dados** para a base de dados no Azure SQL DB, defina a **Edição do Microsoft Azure SQL da base de dados** (camada de serviços), **tamanho máximo de base de dados**e **Objectivo de serviço** (nível de desempenho).

    ![Definições de base de dados](./media/sql-database-cloud-migrate/MigrateUsingBACPAC05.png)

5.  Clique em **seguinte** e, em seguida, clique em **Concluir** para importar o ficheiro BACPAC para uma nova base de dados no servidor de base de dados do Azure SQL.

6. Através do Explorador do objeto, ligar a sua base de dados migrado no seu servidor de base de dados do Azure SQL.

6.  Utilizando o portal Azure, ver a sua base de dados e as respetivas propriedades.

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
