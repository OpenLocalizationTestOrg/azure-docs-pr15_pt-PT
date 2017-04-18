<properties
    pageTitle="Monitorizar e gerir um conjunto de base de dados flexível com c# | Microsoft Azure"
    description="Utilize técnicas de desenvolvimento c# da base de dados para gerir um agrupamento de base de dados flexível de base de dados do Azure SQL."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="csharp"
    ms.workload="data-management"
    ms.date="10/04/2016"
    ms.author="sstein"/>

# <a name="monitor-and-manage-an-elastic-database-pool-with-cx23"></a>Monitorizar e gerir um conjunto de base de dados flexível com C & #x 23; 

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Saiba como gerir um [conjunto de base de dados flexível](sql-database-elastic-pool.md) utilizando C & #x 23;. 

>[AZURE.NOTE] Muitas funcionalidades novas da base de dados SQL só são suportadas quando está a utilizar o [modelo de implementação do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md), pelo que deve utilizar sempre as mais recentes **biblioteca de gestão do Azure SQL da base de dados para o .NET ([documentos](https://msdn.microsoft.com/library/azure/mt349017.aspx) | [NuGet pacote](https://www.nuget.org/packages/Microsoft.Azure.Management.Sql))**. O mais antigo [bibliotecas com base no modelo de implementação clássica](https://www.nuget.org/packages/Microsoft.WindowsAzure.Management.Sql) são suportadas para retrocompatibilidade apenas, para que recomendamos que utilize as bibliotecas de Gestor de recursos com base mais recentes.

Para concluir os passos neste artigo, tem os seguintes itens:

- Um agrupamento de flexível (o conjunto de pretende gerir). Para criar um conjunto de dados, consulte o artigo [criar um conjunto de base de dados flexível com c#](sql-database-elastic-pool-create-csharp.md).
- Visual Studio. Para obter uma cópia gratuita do Visual Studio, consulte a página de [Transferências do Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs) .


## <a name="move-a-database-into-an-elastic-pool"></a>Mover uma base de dados para um conjunto de dados flexível

Pode mover uma base de dados autónoma ou reduzir um conjunto de dados.  

    // Retrieve current database properties.

    currentDatabase = sqlClient.Databases.Get("resourcegroup-name", "server-name", "Database1").Database;

    // Configure create or update parameters with existing property values, override those to be changed.
    DatabaseCreateOrUpdateParameters updatePooledDbParameters = new DatabaseCreateOrUpdateParameters()
    {
        Location = currentDatabase.Location,
        Properties = new DatabaseCreateOrUpdateProperties()
        {
            Edition = "Standard",
            RequestedServiceObjectiveName = "ElasticPool",
            ElasticPoolName = "ElasticPool1",
            MaxSizeBytes = currentDatabase.Properties.MaxSizeBytes,
            Collation = currentDatabase.Properties.Collation,
        }
    };

    // Update the database.
    var dbUpdateResponse = sqlClient.Databases.CreateOrUpdate("resourcegroup-name", "server-name", "Database1", updatePooledDbParameters);

## <a name="list-databases-in-an-elastic-pool"></a>Bases de dados de lista num conjunto de dados flexível

Para obter todas as bases de dados num conjunto de dados, contacte o método de [ListDatabases](https://msdn.microsoft.com/library/microsoft.azure.management.sql.elasticpooloperationsextensions.listdatabases) .

    //List databases in the elastic pool
    DatabaseListResponse dbListInPool = sqlClient.ElasticPools.ListDatabases("resourcegroup-name", "server-name", "ElasticPool1");
    Console.WriteLine("Databases in Elastic Pool {0}", "server-name.ElasticPool1");
    foreach (Database db in dbListInPool)
    {
        Console.WriteLine("  Database {0}", db.Name);
    }

## <a name="change-performance-settings-of-a-pool"></a>Alterar definições de desempenho de um conjunto de dados

Obtenha existentes as propriedades do conjunto de dados. Modifique os valores e executar o método de CreateOrUpdate.

    var currentPool = sqlClient.ElasticPools.Get("resourcegroup-name", "server-name", "ElasticPool1").ElasticPool;

    // Configure create or update parameters with existing property values, override those to be changed.
    ElasticPoolCreateOrUpdateParameters updatePoolParameters = new ElasticPoolCreateOrUpdateParameters()
    {
        Location = currentPool.Location,
        Properties = new ElasticPoolCreateOrUpdateProperties()
        {
            Edition = currentPool.Properties.Edition,
            DatabaseDtuMax = 50, /* Setting DatabaseDtuMax to 50 limits the eDTUs that any one database can consume */
            DatabaseDtuMin = 10, /* Setting DatabaseDtuMin above 0 limits the number of databases that can be stored in the pool */
            Dtu = (int)currentPool.Properties.Dtu,
            StorageMB = currentPool.Properties.StorageMB,  /* For a Standard pool there is 1 GB of storage per eDTU. */
        }
    };

    newPoolResponse = sqlClient.ElasticPools.CreateOrUpdate("resourcegroup-name", "server-name", "ElasticPool1", newPoolParameters);


## <a name="latency-of-elastic-pool-operations"></a>Latência de operações de agrupamento flexível

- Alterar o eDTUs mínimo por eDTUs de base de dados ou máximo por bases de dados normalmente conclui em cinco minutos ou menos.
- Tempo para alterar o tamanho de agrupamento (eDTUs) depende do tamanho combinado de todas as bases de dados no conjunto. Alterações média 90 minutos ou menos por 100 GB. Por exemplo, se o espaço total de todas as bases de dados no conjunto de é 200 GB, em seguida, a latência esperada para alterar o eDTU agrupamento por agrupamento é 3 horas ou menos.




## <a name="additional-resources"></a>Recursos adicionais

- [Códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados](sql-database-develop-error-messages.md).
- [Base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [APIs de gestão de recursos Azure](https://msdn.microsoft.com/library/azure/dn948464.aspx)
- [Criar um novo conjunto de base de dados flexível com c#](sql-database-elastic-pool-create-csharp.md)
- [Quando deve ser utilizado um agrupamento de base de dados flexível?](sql-database-elastic-pool-guidance.md)
- Consulte o artigo [dimensionamento saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md): utilizar ferramentas de base de dados flexível para fora de escala, mover os dados, de consulta ou crie transações.

