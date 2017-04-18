<properties 
    pageTitle="Consultar várias shard | Microsoft Azure" 
    description="Execute consultas em várias shards utilizando a biblioteca de cliente flexível da base de dados." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="torsteng" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/12/2016" 
    ms.author="torsteng"/>

# <a name="multi-shard-querying"></a>Consultar várias shard

## <a name="overview"></a>Descrição geral

Com as [Ferramentas de base de dados flexível](sql-database-elastic-scale-introduction.md), pode criar soluções sharded da base de dados. **Consultar várias shard** é utilizada para tarefas, como relatórios/recolha de dados que requerem a execução de uma consulta que aumenta através de vários shards. (Em contraste com as [Encaminhamento dependentes de dados](sql-database-elastic-scale-data-dependent-routing.md), que efetua todo o trabalho com base numa única shard.) 

## <a name="overview"></a>Descrição geral

1. Receber uma [**RangeShardMap**](https://msdn.microsoft.com/library/azure/dn807318.aspx) ou [**ListShardMap**](https://msdn.microsoft.com/library/azure/dn807370.aspx) utilizando o [**TryGetRangeShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetrangeshardmap.aspx), o [**TryGetListShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.trygetlistshardmap.aspx)ou o método de [**GetShardMap**](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmapmanager.getshardmap.aspx) . Consulte [**construir uma ShardMapManager**](sql-database-elastic-scale-shard-map-management.md#constructing-a-shardmapmanager) e [**obter um RangeShardMap ou ListShardMap**](sql-database-elastic-scale-shard-map-management.md#get-a-rangeshardmap-or-listshardmap).
2. Crie um objecto **[MultiShardConnection](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardconnection.aspx)** .
2. Crie um **[MultiShardCommand](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.aspx)**. 
3. Defina a **[propriedade CommandText](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.commandtext.aspx#P:Microsoft.Azure.SqlDatabase.ElasticScale.Query.MultiShardCommand.CommandText)** a um comando T-SQL.
3. Execute o comando ao contactar o **[método de ExecuteReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multishardcommand.executereader.aspx)**.
4. Ver os resultados utilizando a **[classe de MultiShardDataReader](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.multisharddatareader.aspx)**. 

## <a name="example"></a>Exemplo

O código seguinte ilustra a utilização de várias shard consultar utilizando um determinado **ShardMap** denominada *myShardMap*. 

    using (MultiShardConnection conn = new MultiShardConnection( 
                                        myShardMap.GetShards(), 
                                        myShardConnectionString) 
          ) 
    { 
    using (MultiShardCommand cmd = conn.CreateCommand())
           { 
            cmd.CommandText = "SELECT c1, c2, c3 FROM ShardedTable"; 
            cmd.CommandType = CommandType.Text; 
            cmd.ExecutionOptions = MultiShardExecutionOptions.IncludeShardNameColumn; 
            cmd.ExecutionPolicy = MultiShardExecutionPolicy.PartialResults; 

            using (MultiShardDataReader sdr = cmd.ExecuteReader()) 
                { 
                    while (sdr.Read())
                        { 
                            var c1Field = sdr.GetString(0); 
                            var c2Field = sdr.GetFieldValue<int>(1); 
                            var c3Field = sdr.GetFieldValue<Int64>(2);
                        } 
                } 
           } 
    } 

 
Uma diferença de chave é construção de shard com várias ligações. Sempre que funciona **SqlConnection** numa única base de dados, o **MultiShardConnection** leva-o até uma ***coleção de shards*** como a respectiva entrada. Preencha o conjunto de shards a partir de um mapa de shard. A consulta é executada, em seguida, sobre a recolha de shards utilizando a semântica de **UNION ALL** reunir um único resultado geral. Opcionalmente, o nome do shard a linha do qual origina a partir do pode ser adicionado ao resultado ao utilizar a propriedade **ExecutionOptions** no comando. 

Tenha em atenção a chamada para **myShardMap.GetShards()**. Este método obtém todos os shards a partir do mapa shard e fornece uma forma fácil de executar uma consulta em todas as bases de dados relevantes. A coleção de shards para uma consulta com várias shard pode ser refinada ainda mais efetuando uma consulta LINQ sobre a coleção de devolvido da chamada para **myShardMap.GetShards()**. Em combinação com a política de resultados parciais, a funcionalidade atual no shard com várias consultar foi concebida para funcionar bem para dezenas até centenas de shards.

Atualmente, uma limitação com shard com várias consultar está a falta de validação para shards e shardlets são avisados. Enquanto o encaminhamento de dependentes de dados verifica que um determinado shard faz parte do mapa shard no momento da consultar, shard com várias consultas não siga esta verificação. Isto pode levar a shard com várias consultas em execução no bases de dados que foram removidas do mapa de shard.

## <a name="multi-shard-queries-and-split-merge-operations"></a>Shard com várias consultas e operações de intercalação de divisão

Shard com várias consultas não verificam se shardlets da base de dados consultado sua participação numa operações em série dividido em curso. (Consulte [dimensionamento utilizando a ferramenta de intercalação de divisão de base de dados flexível](sql-database-elastic-scale-overview-split-and-merge.md).) Isto pode originar inconsistências onde as linhas da mesma shardlet mostram para várias bases de dados na mesma consulta com várias shard. Tenha em atenção estes limitações e ter em consideração drenagem operações de intercalação dividido em curso e as alterações ao mapa de shard enquanto executa shard com várias consultas.

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

## <a name="see-also"></a>Consulte também
**[System.Data.SqlClient](http://msdn.microsoft.com/library/System.Data.SqlClient.aspx)** aulas e os métodos.


Gerir shards utilizando a [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md). Inclui um espaço de nomes denominado [Microsoft.Azure.SqlDatabase.ElasticScale.Query](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.query.aspx) que fornece a capacidade de vários shards através de uma única consulta e o resultado da consulta. Fornece uma produção ao consultar através de uma coleção de shards. Também fornece as políticas de execução alternativo, resultados parciais em particular, para lidar com falhas quando consultar sobre shards muitos.  

 