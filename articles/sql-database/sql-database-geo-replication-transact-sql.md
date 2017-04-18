<properties
    pageTitle="Configurar a replicação de Geo para base de dados do Azure SQL com o Transact-SQL | Microsoft Azure"
    description="Configurar a replicação de Geo para base de dados do SQL Azure utilizando o Transact-SQL"
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
    ms.workload="NA"
    ms.date="10/13/2016"
    ms.author="carlrab"/>

# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurar a replicação de Geo para base de dados do Azure SQL com o Transact-SQL

> [AZURE.SELECTOR]
- [Descrição geral](sql-database-geo-replication-overview.md)
- [Portal do Azure](sql-database-geo-replication-portal.md)
- [PowerShell](sql-database-geo-replication-powershell.md)
- [T-SQL](sql-database-geo-replication-transact-sql.md)

Este artigo mostra-lhe como configurar a replicação do Geo Active para uma base de dados do SQL Azure com Transact-SQL.

Para iniciar activação pós-falha utilizando o Transact-SQL, consulte o artigo [Iniciar planeado ou não planeado activação pós-de uma falha, para a base de dados do SQL Azure com Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

>[AZURE.NOTE] Geo-a replicação do Active (secundários legíveis) está agora disponível para todas as bases de dados em todas as camadas de serviço. De Abril de 2017 vai ser foi removido o tipo de secundário que não sejam legíveis e bases de dados que não sejam legíveis existentes serão automaticamente atualizadas para secundários legíveis.

Para configurar a replicação de Geo ativo utilizar Transact-SQL, precisa do seguinte:

- Uma subscrição do Azure.
- Um servidor de base de dados do Azure SQL lógico <MyLocalServer> e uma base de dados do SQL <MyDB> -a base de dados principal ao qual pretende criar uma réplica.
- Um ou mais lógicos base de dados do Azure SQL servidores < MySecondaryServer(n) > - os lógicos servidores que serão os servidores de parceiro irá criar bases de dados secundárias.
- Um início de sessão que está a página principal, Gestor de BD tem db_ownership da base de dados local que irá geo-replicação e ser Gestor de BD no parceiro servidor (es) para o qual irá configurar Geo replicação.
- SQL Server Management Studio (SSMS)

> [AZURE.IMPORTANT] É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizados com atualizações para o Microsoft Azure e base de dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).


## <a name="add-secondary-database"></a>Adicionar base de dados secundária

Pode utilizar a instrução **ALTER DATABASE** para criar uma replicadas geo secundário base de dados num servidor parceiro. Executar esta declaração da base de dados principal do servidor que contém a base de dados ser replicadas. Replicadas geo base de dados (a "primária base de dados") vai ter o mesmo nome como a base de dados a ser replicada e, por predefinição, terá o mesmo nível de serviço que a base de dados principal. A base de dados secundária pode ser legível ou que não sejam legíveis e pode ser uma base de dados única ou um databbase flexível. Para mais informações, consulte o artigo [Alterar da base de dados (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de serviços](sql-database-service-tiers.md).
Depois da base de dados secundária é criado e propagada, dados começarão a replicação de forma assíncrona da base de dados principal. Os passos abaixo descrevem como configurar a replicação Geo utilizando Management Studio. Passos para criar secundários de não lido e legíveis, quer seja com uma base de dados única ou uma base de dados flexível, são fornecidos.

> [AZURE.NOTE] Se existir uma base de dados no servidor especificado parceiro com o mesmo nome como a base de dados principal, que o comando irá falhar.


### <a name="add-non-readable-secondary-single-database"></a>Adicionar secundário que não sejam legíveis (base de dados único)

Utilize os passos seguintes para criar um secundário que não sejam legíveis como uma única base de dados.

1. Com a versão 13.0.600.65 ou posterior do SQL Server Management Studio.

     > [AZURE.IMPORTANT] Transferir a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendado utilizar sempre a versão mais recente do Management Studio para permanecer sincronizado com atualizações ao portal do Azure.


2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para transformar uma base de dados local uma replicação Geo primária com uma base de dados secundária não legível no MySecondaryServer1 onde MySecondaryServer1 é o seu nome de servidor amigável.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);

4. Clique em **Executar** para executar a consulta.


### <a name="add-readable-secondary-single-database"></a>Adicionar secundário legível (base de dados único)
Utilize os passos seguintes para criar um secundário legível como uma única base de dados.

1. No Management Studio, ligar ao seu servidor de lógica de base de dados do Azure SQL.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para transformar uma base de dados local uma replicação Geo primária com uma base de dados secundária legível num servidor secundário.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);

4. Clique em **Executar** para executar a consulta.



### <a name="add-non-readable-secondary-elastic-database"></a>Adicionar secundário que não sejam legíveis (base de dados flexível)

Utilize os passos seguintes para criar um secundário que não sejam legíveis como uma base de dados flexível.

1. No Management Studio, ligar ao seu servidor de lógica de base de dados do Azure SQL.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para transformar uma base de dados local uma replicação Geo primária com uma base de dados secundária não legível num servidor secundário num conjunto de dados flexível.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));

4. Clique em **Executar** para executar a consulta.



### <a name="add-readable-secondary-elastic-database"></a>Adicionar secundário legível (base de dados flexível)
Utilize os passos seguintes para criar um secundário legível como uma base de dados flexível.

1. No Management Studio, ligar ao seu servidor de lógica de base de dados do Azure SQL.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para transformar uma base de dados local uma replicação Geo primária com uma base de dados secundária legível num servidor secundário num conjunto de dados flexível.

        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));

4. Clique em **Executar** para executar a consulta.



## <a name="remove-secondary-database"></a>Remover a base de dados secundária

Pode utilizar a instrução **ALTER DATABASE** permanentemente denunciar a parceria de replicação entre uma base de dados secundária e do respectivo principal. Esta declaração é executada na base de dados principal no qual se encontra a base de dados principal. Após o termo relação, a base de dados secundária torna-se uma base de dados de leitura / escrita normal. Se a ligação à base de dados secundária é interrompida o comando é concluída com êxito mas secundária irão tornar-se leitura / escrita depois de conectividade é restaurada. Para mais informações, consulte o artigo [Alterar da base de dados (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de serviços](sql-database-service-tiers.md).

Utilize os passos seguintes para remover secundário de replicadas geo a partir de uma parceria de replicação Geo.

1. No Management Studio, ligar ao seu servidor de lógica de base de dados do Azure SQL.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução **ALTER DATABASE** para remover um secundário de replicadas geo.

        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;

4. Clique em **Executar** para executar a consulta.

## <a name="monitor-geo-replication-configuration-and-health"></a>Monitorizar o estado de funcionamento e configuração de replicação Geo

Tarefas de monitorização incluem a monitorização da configuração do Geo replicação e estado de funcionamento de replicação de dados.  Pode utilizar a vista de gestão de dinâmica **sys.dm_geo_replication_links** na base de dados principal para devolver informações sobre todas as ligações de replicação Designing para cada base de dados no servidor lógico de base de dados do Azure SQL. Esta vista contém uma linha para cada um da ligação de replicação entre as bases de dados principais e secundários. Pode utilizar a vista de gestão de dinâmica **sys.dm_replication_link_status** para devolver uma linha para cada base de dados do SQL Azure que está atualmente interessada numa ligação replicação replicação. Isto inclui principais e secundários bases de dados. Se existir mais do que uma ligação de replicação contínua para uma determinada base de dados principal, esta tabela contém uma linha para cada um das relações. A vista é criada no todas as bases de dados, incluindo o modelo global de lógico. No entanto, consultar esta vista no modelo global de lógica devolve um conjunto vazio. Pode utilizar a vista de gestão de dinâmica **sys.dm_operation_status** para mostrar o estado para todas as operações de base de dados, incluindo o estado das ligações de replicação. Para mais informações, consulte o artigo [sys.geo_replication_links (base de dados do SQL Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (base de dados do SQL Azure)](https://msdn.microsoft.com/library/mt575504.aspx)e [sys.dm_operation_status (base de dados do SQL Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Utilize os passos seguintes para monitorizar a parceria de replicação Geo.

1. No Management Studio, ligar ao seu servidor de lógica de base de dados do Azure SQL.

2. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **modelo global**e, em seguida, clique em **Nova consulta**.

3. Utilize a seguinte instrução para mostrar todas as bases de dados com ligações de replicação Geo.

        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;

4. Clique em **Executar** para executar a consulta.
5. Abra a pasta de bases de dados, expanda a pasta de **Bases de dados do sistema** , com o botão direito no **MyDB**e, em seguida, clique em **Nova consulta**.
6. Utilizar a seguinte instrução para mostrar os intervalos de replicação e a replicação de última hora do meu bases de dados secundárias de MyDB.

        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status

7. Clique em **Executar** para executar a consulta.
8. Utilize a seguinte instrução para mostrar as mais recentes operações de replicação geo associadas MyDB da base de dados.

        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC

9. Clique em **Executar** para executar a consulta.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Atualizar um secundário que não sejam legíveis para legível

De Abril de 2017 vai ser foi removido o tipo de secundário que não sejam legíveis e bases de dados que não sejam legíveis existentes serão automaticamente atualizadas para secundários legíveis. Se estiver a utilizar o secundários não legível hoje e que Atualize-los para ser lido, pode utilizar os seguintes passos simples para cada secundário.

> [AZURE.IMPORTANT] Não existe nenhum método personalizado da atualização no local de um secundário que não sejam legíveis para legível. Se largar seu apenas secundário, em seguida, a base de dados principal permanecerá desprotegida até nova secundária está completamente sincronizada. Se a SLA sua aplicação requer que a página principal está sempre protegida, deverá tomar em consideração de criar um secundário paralelo no servidor diferente antes de aplicar os atualização passos acima. Tenha em atenção que cada principal pode ter até 4 bases de dados secundárias.


1. Em primeiro lugar, ligar ao servidor *secundário* e largue a base de dados que não sejam legíveis secundário:  
        
        DROP DATABASE <MyNonReadableSecondaryDB>;

2. Agora, ligar para o servidor *principal* e adicionar um novo secundário legível

        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);




## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre Geo-replicação do Active, consulte o artigo - [Geo-a replicação do Active](sql-database-geo-replication-overview.md)
- Para obter uma descrição de continuidade do negócio e cenários, consulte o artigo [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
