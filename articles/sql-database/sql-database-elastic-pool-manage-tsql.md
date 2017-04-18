<properties 
    pageTitle="Criar ou mover uma base de dados do Azure SQL para um agrupamento de flexível utilizando T-SQL | Microsoft Azure" 
    description="Utilize T-SQL para criar uma base de dados do Azure SQL num conjunto de dados flexível. Ou utilize T-SQL para mover a datbase e terminar conjuntos de dados." 
    services="sql-database" 
    documentationCenter="" 
    authors="srinia" 
    manager="jhubbard" 
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-management" 
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-transact-sql"></a>Monitorizar e gerir um conjunto de base de dados flexível com Transact-SQL  

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)

Utilize os comandos [Criar base de dados (base de dados do SQL Azure)](https://msdn.microsoft.com/library/dn268335.aspx) e [Alter Database(Azure SQL Database)](https://msdn.microsoft.com/library/mt574871.aspx) para criar e mover bases de dados para e terminar flexível conjuntos de dados. O conjunto de flexível tem de existir antes de poder utilizar estes comandos. Estes comandos afetam apenas bases de dados. Criação de novos agrupamentos de dados e a definição das propriedades do conjunto de dados (por exemplo, mínimo e máximo eDTUs) não podem ser alterados com comandos T-SQL.

## <a name="create-a-new-database-in-an-elastic-pool"></a>Criar uma nova base de dados num conjunto de dados flexível
Utilize o comando criar a base de dados com a opção SERVICE_OBJECTIVE.   

    CREATE DATABASE db1 ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3M100] ));
    -- Create a database named db1 in a pool named S3M100.

Todas as bases de dados num conjunto de dados flexível herdam a camada de serviço do conjunto de flexível (Basic, padrão, Premium). 


## <a name="move-a-database-between-elastic-pools"></a>Mover uma base de dados entre flexível conjuntos de dados
Utilize o comando de alterar a base de dados com a modificar e configurar o serviço\_opção OBJETIVO como ELÁSTICOS\_agrupamento; Defina o nome para o nome do conjunto de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [PM125] ));
    -- Move the database named db1 to a pool named P1M125  

## <a name="move-a-database-into-an-elastic-pool"></a>Mover uma base de dados para um conjunto de dados flexível 
Utilize o comando de alterar a base de dados com a modificar e configurar o serviço\_opção OBJETIVO como ELASTIC_POOL; Defina o nome para o nome do conjunto de destino.

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = ELASTIC_POOL (name = [S3100] ));
    -- Move the database named db1 to a pool named S3100.

## <a name="move-a-database-out-of-an-elastic-pool"></a>Mover uma base de dados fora de um conjunto de dados flexível
Utilize o comando ALTER DATABASE e configure o SERVICE_OBJECTIVE para um dos níveis de desempenho (S0, S1, etc).

    ALTER DATABASE db1 MODIFY ( SERVICE_OBJECTIVE = 'S1');
    -- Changes the database into a stand-alone database with the service objective S1.

## <a name="list-databases-in-an-elastic-pool"></a>Bases de dados de lista num conjunto de dados flexível
Utilizar o [sys.database\_serviço \_objetivos vista](https://msdn.microsoft.com/library/mt712619) para listar todas as bases de dados num conjunto de dados flexível. Inicie sessão no modelo global de base de dados para a vista de consulta.

    SELECT d.name, slo.*  
    FROM sys.databases d 
    JOIN sys.database_service_objectives slo  
    ON d.database_id = slo.database_id
    WHERE elastic_pool_name = 'MyElasticPool'; 

## <a name="get-resource-usage-data-for-a-pool"></a>Obter dados de utilização de recursos para um conjunto de dados

Utilizar o [sys.elastic\_agrupamento \_recurso \_estatística vista](https://msdn.microsoft.com/library/mt280062.aspx) examinar as estatísticas de utilização de recursos de um conjunto de flexível num servidor lógico. Inicie sessão no modelo global de base de dados para a vista de consulta.

    SELECT * FROM sys.elastic_pool_resource_stats 
    WHERE elastic_pool_name = 'MyElasticPool'
    ORDER BY end_time DESC;

## <a name="get-resource-usage-for-an-elastic-database"></a>Obter a utilização de recursos para uma base de dados flexível

Utilizar o [sys.dm\_ db\_ recurso\_estatística vista](https://msdn.microsoft.com/library/dn800981.aspx) ou [sys.resource \_estatística vista](https://msdn.microsoft.com/library/dn269979.aspx) examinar as estatísticas de utilização de recursos de uma base de dados num conjunto de dados flexível. Este processo é semelhante à utilização de recursos para qualquer única base de dados de consultar.

## <a name="next-steps"></a>Próximos passos

Depois de criar um agrupamento de base de dados flexível, pode gerir flexível bases de dados no conjunto de criando tarefas flexível. Tarefas flexível facilitam a execução de T-SQL de scripts contra qualquer número de bases de dados no conjunto. Para mais informações, consulte o artigo [Descrição geral de tarefas da base de dados flexível](sql-database-elastic-jobs-overview.md). 

Consulte o artigo [dimensionamento saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md): utilizar ferramentas de base de dados flexível para fora de escala, mover os dados, de consulta ou crie transações.
