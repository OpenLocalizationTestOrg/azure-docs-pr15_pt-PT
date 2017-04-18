<properties
   pageTitle="Utilizar o SQL Server Management Studio para compatibilidade determinar a base de dados do SQL antes da migração para a base de dados do SQL Azure | Microsoft Azure"
   description="Microsoft Azure SQL base de dados, migração de base de dados, compatibilidade de base de dados SQL, exportar dados camada de aplicação Assistente"
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
   ms.date="08/29/2016"
   ms.author="carlrab"/>

# <a name="use-sql-server-management-studio-to-determine-sql-database-compatibility-before-migration-to-azure-sql-database"></a>Utilizar o SQL Server Management Studio para compatibilidade determinar a base de dados do SQL antes da migração para a base de dados do SQL Azure

> [AZURE.SELECTOR]
- [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- [SqlPackage](sql-database-cloud-migrate-determine-compatibility-sqlpackage.md)
- [SSMS](sql-database-cloud-migrate-determine-compatibility-ssms.md)
- [Classificação de actualização](http://www.microsoft.com/download/details.aspx?id=48119)
- [SAMW](sql-database-cloud-migrate-fix-compatibility-issues.md)
 
Neste artigo que saiba para determinar se uma base de dados do SQL Server é compatível para migrar a base de dados SQL utilizando o Assistente Exportar dados camada de aplicação no SQL Server Management Studio.

## <a name="using-sql-server-management-studio"></a>Utilizar o SQL Server Management Studio

1. Certifique-se de que tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal para permanecer sincronizado com atualizações ao portal do Azure.

     > [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e ligue a sua base de dados de origem no Explorador do objeto.
3. Botão direito do rato a base de dados de origem no Explorador de objeto, aponte para **tarefas**e clique em **Exportar dados de aplicação de camadas...**

    ![Exportar uma aplicação de camada de dados a partir do menu de tarefas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS01.png)

4. No Assistente de exportação, clique em **seguinte**e, em seguida, no separador **Definições** , configure a exportação para guardar o ficheiro BACPAC para uma localização de disco local ou para um BLOBs do Azure. Um ficheiro BACPAC é guardado, se tiver sem problemas de compatibilidade de base de dados. Se existirem problemas de compatibilidade, estes serão apresentados na consola.

    ![Exportar definições de](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS02.png)

5. Para ignorar a exportação de dados, clique no **separador Avançadas** e desmarque a caixa de verificação **Selecionar tudo** . Neste momento o nosso objetivo é apenas testar a compatibilidade.

    ![Exportar definições de](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS03.png)

6. Clique em **seguinte** e, em seguida, clique em **Concluir**. Problemas de compatibilidade de base de dados, se existirem, são apresentados após o Assistente de valida o esquema.

    ![Exportar definições de](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS04.png)

7. Se forem apresentados sem erros, é compatível com a base de dados e estiver pronto para migrar. Se tiver de erros, é necessário para os corrigir. Para ver os erros, clique em **erro** para **Validating esquema**. 
    ![Exportar definições de](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSSMS05.png)

8.  Se o *. Ficheiro BACPAC é gerado com êxito, em seguida, a base de dados é compatível com a base de dados SQL e estiver pronto para migrar.

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Corrigir problemas de compatibilidade de migração de base de dados](sql-database-cloud-migrate.md#fix-database-migration-compatibility-issues)
- [Migrar uma base de dados compatível com SQL Server para a base de dados SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
