<properties
   pageTitle="Migrar a base de dados do SQL Server para base de dados SQL utilizando implementar base de dados ao Assistente de base de dados do Microsoft Azure | Microsoft Azure"
   description="Microsoft Azure base de dados SQL, migração de base de dados, o Assistente de base de dados do Microsoft Azure"
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

# <a name="migrate-sql-server-database-to-sql-database-using-deploy-database-to-microsoft-azure-database-wizard"></a>Migrar a base de dados do SQL Server para base de dados SQL utilizando implementar base de dados ao Assistente de base de dados do Microsoft Azure


> [AZURE.SELECTOR]
- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar do ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

A base de dados implementar ao Assistente de base de dados do Microsoft Azure no SQL Server Management Studio migra uma [base de dados do SQL Server compatível](sql-database-cloud-migrate.md) diretamente no seu servidor de base de dados do Azure SQL.

## <a name="use-the-deploy-database-to-microsoft-azure-database-wizard"></a>Utilizar a base de dados implementar ao Assistente de base de dados do Microsoft Azure

> [AZURE.NOTE] Os seguintes passos partem do pressuposto de que tem um [aprovisionado base de dados SQL server](https://azure.microsoft.com/documentation/learning-paths/sql-database-training-learn-sql-database/).

1. Certifique-se de que tem a versão mais recente do SQL Server Management Studio. Novas versões do Management Studio são atualizadas mensal para permanecer sincronizado com atualizações ao portal do Azure.

    > [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).

2. Abra o Management Studio e ligar a sua base de dados do SQL Server para ser migrados no Explorador do objeto.
3. Botão direito do rato a base de dados no Explorador de objeto, aponte para **tarefas**e clique em **Implementar base de dados do Microsoft Azure SQL base de dados...**

    ![Implementar Azure a partir do menu de tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard01.png)

4.  No Assistente de implementação, clique em **seguinte**e, em seguida, clique em **Ligar** para configurar a ligação ao seu servidor de base de dados SQL.

    ![Implementar Azure a partir do menu de tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard002.png)

5. Em ligar à caixa de diálogo do servidor, introduza as suas informações de ligação para ligar ao seu servidor de base de dados SQL.

    ![Implementar Azure a partir do menu de tarefas](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard00.png)

5.  Forneça a seguinte para o ficheiro [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) este assistente cria durante o processo de migração:

 - O **novo nome da base de dados** 
 - A **Edição do Microsoft Azure SQL da base de dados** ([camada de serviços](sql-database-service-tiers.md))
 - O **tamanho máximo de base de dados**
 - **Objectivo de serviço** (nível de desempenho)
 - O **nome de ficheiro temporário**  

    ![Exportar definições de](./media/sql-database-cloud-migrate/MigrateUsingDeploymentWizard02.png)

6.  Conclua o assistente. Consoante o tamanho e complexidade da base de dados, implementação poderá demorar alguns minutos a quantidade de horas. Se este assistente detetar problemas de compatibilidade, os erros são apresentados no ecrã e não continuar a migração. Para obter orientações sobre como corrigir problemas de compatibilidade de base de dados, vá para [corrigir problemas de compatibilidade de base de dados](sql-database-cloud-migrate-fix-compatibility-issues.md).

7.  Através do Explorador do objeto, ligar a sua base de dados migrado no seu servidor de base de dados do Azure SQL.
8.  Utilizando o portal Azure, ver a sua base de dados e as respetivas propriedades.

## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## <a name="additional-resources"></a>Recursos adicionais

- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
