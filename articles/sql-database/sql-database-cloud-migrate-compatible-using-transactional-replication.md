<properties
   pageTitle="Migrar a base de dados SQL utilizando a replicação transaccional | Microsoft Azure"
   description="Microsoft Azure base de dados SQL, migração de base de dados, importar base de dados, replicação transaccional"
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
   ms.date="08/23/2016"
   ms.author="carlrab"/>

# <a name="migrate-sql-server-database-to-azure-sql-database-using-transactional-replication"></a>Migrar a base de dados do SQL Server para base de dados do SQL Azure utilizando a replicação transaccional

> [AZURE.SELECTOR]
- [Assistente de migração de SSMS](sql-database-cloud-migrate-compatible-using-ssms-migration-wizard.md)
- [Exportar para ficheiro BACPAC](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [Importar do ficheiro BACPAC](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Replicação transaccional](sql-database-cloud-migrate-compatible-using-transactional-replication.md)

Neste artigo, saiba migrar uma base de dados do SQL Server compatível para base de dados do SQL Azure com indisponibilidade mínima utilizando a replicação transaccional do SQL Server.

## <a name="understanding-the-transactional-replication-architecture"></a>Noções sobre a arquitetura de replicação transaccional

Quando que não pode perder de forma alguma remover a base de dados do SQL Server de produção enquanto a migração está a ocorrer, pode utilizar replicação transaccional do SQL Server como a sua solução de migração. Para utilizar esta solução, configurar a base de dados do SQL Azure como um subscritor a instância do SQL Server no local que pretende migrar. No local distribuidor replicação transaccional sincroniza os dados da base de dados no local a ser sincronizada (o fabricante) enquanto novas transações continuar a ocorrer. 

Também pode utilizar a replicação transaccional para migrar um subconjunto da base de dados no local. A publicação que pode criar uma réplica da base de dados do SQL Azure pode estar limitada a um subconjunto de tabelas da base de dados a ser replicadas. Para cada tabela que está a ser replicada, pode limitar os dados para um subconjunto das linhas e/ou um subconjunto das colunas.

Com a replicação transaccional, todas as alterações aos dados ou esquema aparecem na sua base de dados do SQL Azure. Quando a sincronização está concluída e estiver pronto para migrar, altere a cadeia de ligação das suas aplicações para reencaminhe-os para a base de dados do SQL Azure. Assim que a replicação transaccional esgotando quaisquer alterações para a esquerda na sua base de dados no local e todas as suas aplicações aponte para Azure DB, pode desinstalar a replicação transaccional. A base de dados do SQL Azure é agora o seu sistema de produção.

 ![Diagrama de SeedCloudTR](./media/sql-database-cloud-migrate/SeedCloudTR.png)

## <a name="transactional-replication-requirements"></a>Requisitos de replicação transaccionais

Replicação transaccional é uma tecnologia incorporada e integrada com o SQL Server desde o SQL Server 6.5. É uma tecnologia maduro e com provas dadas que a maioria dos DBAs sabe com os quais têm experiência. Com o [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server), agora é possível configurar a sua base de dados do SQL Azure como um [subscritor replicação transaccional](https://msdn.microsoft.com/library/mt589530.aspx) à sua publicação no local. A experiência do que se começar a configurar a partir do Management Studio é a mesma como se configurar um subscritor replicação transaccional num servidor no local. Suporte para este cenário é suportado quando o publisher e o distribuidor estão pelo menos um dos seguintes versões do SQL Server:

 - SQL Server 2016 e superior 
 - SQL Server 2014 SP1 CU3 e superior
 - SQL Server 2014 RTM CU10 e superior
 - SQL Server 2012 SP2 CU8 e superior
 - SQL Server 2012 SP3 e superior


> [AZURE.IMPORTANT] Utilize a versão mais recente do SQL Server Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. Versões mais antigas do SQL Server Management Studio não consegue configurar base de dados SQL como um subscritor. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="next-steps"></a>Próximos passos

- [Versão mais recente do SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Versão mais recente do SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [SQL Server 2016](https://www.microsoft.com/en-us/cloud-platform/sql-server)

## <a name="additional-resources"></a>Recursos adicionais

- [Replicação transaccional](https://msdn.microsoft.com/library/mt589530.aspx)
- [V12 de base de dados SQL](sql-database-v12-whats-new.md)
- [O Transact-SQL parcialmente ou não suportada funções](sql-database-transact-sql-information.md)
- [Migrar bases de dados que não sejam SQL Server utilizando o Assistente de migração de servidor do SQL](http://blogs.msdn.com/b/ssma/)
