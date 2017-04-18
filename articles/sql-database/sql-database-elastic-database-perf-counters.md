<properties
    pageTitle="Contadores de desempenho para o Gestor de mapa shard"
    description="ShardMapManager escolares e dados dependentes encaminhamento contadores de desempenho"
    services="sql-database"
    documentationCenter=""
    manager="jhubbard"
    authors="SilviaDoomra"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="SilviaDoomra"/>

# <a name="performance-counters-for-shard-map-manager"></a>Contadores de desempenho para o Gestor de mapa shard

Pode capturar o desempenho do [Gestor de mapa shard](sql-database-elastic-scale-shard-map-management.md), especialmente quando utilizar o [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md). Contadores são criados com a classe de Microsoft.Azure.SqlDatabase.ElasticScale.Client métodos.  

Contadores são utilizadas para controlar o desempenho do operações [dependentes encaminhamento de dados](sql-database-elastic-scale-data-dependent-routing.md) . Estes contadores estão acessíveis no Monitor de desempenho, na categoria "Flexível da base de dados: Shard gestão".

**Para a versão mais recente:** Aceda a [Microsoft.Azure.SqlDatabase.ElasticScale.Client](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/). Consulte também o artigo [atualizar uma aplicação para utilizar a biblioteca de cliente flexível da base de dados mais recente](sql-database-elastic-scale-upgrade-client-library.md).

## <a name="prerequisites"></a>Pré-requisitos

* Para criar a categoria de desempenho e contadores, o utilizador tem de ser uma parte do grupo de **administradores** local no computador que aloja a aplicação.  

* Para criar uma instância de contador de desempenho e atualizar os contadores, o utilizador tem de ser membro do grupo de **administradores** ou **Utilizadores do Monitor de desempenho** . 

## <a name="create-performance-category-and-counters"></a>Criar a categoria de desempenho e contadores 

Para criar os contadores, chame o método de CreatePeformanceCategoryAndCounters a [classe de ShardMapManagmentFactory](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.aspx)de. Apenas um administrador pode executar o método: 

    ShardMapManagerFactory.CreatePerformanceCategoryAndCounters()  

Também pode utilizar [Este](https://gallery.technet.microsoft.com/scriptcenter/Elastic-DB-Tools-for-Azure-17e3d283) script do PowerShell para executar o método. O método cria contadores de desempenho seguintes:  

* **Em cache mapeamentos**: número de mapeamentos em cache para o mapa shard.
*  **Operações de DDR/seg**: taxa dependentes encaminhamento de operações de dados para o mapa shard. Este contador é atualizado quando uma chamada para [OpenConnectionForKey()](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.openconnectionforkey.aspx) resultados uma ligação com êxito para o shard de destino. 
*  **Mapeamento de cache de pesquisa acertos/seg**: taxa de operações de pesquisa efetuada com êxito cache para mapeamentos no mapa do shard. 
*  **Mapeamento de cache de pesquisa falhas/seg**: taxa de operações de pesquisa de falha na cache para mapeamentos no mapa do shard.
*  **Mapeamentos adicionado ou atualizado na cache de seg**: velocidade mapeamentos de que estão a ser adicionados ou atualizado na cache para o mapa shard. 
*  **Os mapeamentos removidos de cache/seg**: taxa no qual os mapeamentos estão a ser removidos da cache para o mapa shard. 

Contadores de desempenho são criados para cada mapa shard em cache por processo.  


## <a name="notes"></a>Notas
Os seguintes eventos acionam a criação dos contadores de desempenho:  

* Inicialização da [ShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.aspx) com [ansioso por carregamento](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerloadpolicy.aspx), se o ShardMapManager contiver qualquer mapas shard. Estes incluem o [GetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.getsqlshardmapmanager.aspx?f=255&MSPPError=-2147217396#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerFactory.GetSqlShardMapManager%28System.String,Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardMapManagerLoadPolicy%29) e os métodos de [TryGetSqlShardMapManager](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanagerfactory.trygetsqlshardmapmanager.aspx) .
* Pesquisa efetuada com êxito de um mapa de shard (utilizando [GetShardMap()](https://msdn.microsoft.com/library/azure/dn824215.aspx), [GetListShardMap()](https://msdn.microsoft.com/library/azure/dn824212.aspx) ou [GetRangeShardMap()](https://msdn.microsoft.com/library/azure/dn824173.aspx)). 

* Criação bem sucedida de mapa de shard utilizando CreateShardMap().

Os contadores de desempenho serão atualizados por todas as operações de cache executadas nos mapeamentos e mapa de shard. Remoção bem sucedida do mapa shard utilizar DeleteShardMap () reults no eliminação da instância contadores desempenho.  

## <a name="best-practices"></a>Melhores práticas

* Criação da categoria de desempenho e contadores deve ser executada apenas uma vez antes da criação de objeto ShardMapManager. Cada execução do comando CreatePerformanceCategoryAndCounters() limpa os contadores anterior (perder os dados comunicados por todas as instâncias) e cria novos ficheiros.  

* Instâncias de contador de desempenho são criadas por processo. Qualquer falha de aplicação ou a remoção de um mapa de shard da cache de irá resultar em eliminação de instâncias de contadores o desempenho.  

### <a name="see-also"></a>Consulte também

[Descrição geral das funcionalidades de base de dados flexível](sql-database-elastic-scale-introduction.md)  

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->

