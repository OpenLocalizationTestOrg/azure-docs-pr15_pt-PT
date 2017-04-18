<properties 
    pageTitle="Adicionar um shard utilizando ferramentas de base de dados flexível | Microsoft Azure" 
    description="Como utilizar flexível escala APIs para adicionar novos shards a um shard definir." 
    services="sql-database" 
    documentationCenter="" 
    manager="jhubbard" 
    authors="ddove" 
    editor=""/>

<tags 
    ms.service="sql-database" 
    ms.workload="sql-database" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="05/27/2016" 
    ms.author="ddove"/>

# <a name="adding-a-shard-using-elastic-database-tools"></a>Adicionar um shard utilizando ferramentas de base de dados flexível

## <a name="to-add-a-shard-for-a-new-range-or-key"></a>Para adicionar um shard para um novo intervalo ou tecla  

Aplicações tem muitas vezes de simplesmente adicionar novos shards para processar dados são esperados a partir de novas chaves ou intervalos chaves, para um mapa de shard que já existe. Por exemplo, uma aplicação sharded por inquilino ID poderá ter de aprovisionar um novo shard para um novo inquilino ou mensal sharded dados poderá ter um novo shard aprovisionado antes do início de cada novo mês. 

Se o novo intervalo de valores chave não ainda parte de um mapeamento existente, é muito simples adicionar o novo shard e associar a nova chave ou o intervalo para esse shard. 

### <a name="example--adding-a-shard-and-its-range-to-an-existing-shard-map"></a>Exemplo: adicionar um shard e respetivo intervalo para um mapa de shard existente
Este exemplo utiliza a [TryGetShard](https://msdn.microsoft.com/library/azure/dn823929.aspx) [CreateShard](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardmap.createshard.aspx), [CreateRangeMapping](https://msdn.microsoft.com/library/azure/dn807221.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeShardMap`1.CreateRangeMapping(Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.RangeMappingCreationInfo{`0})) métodos e cria uma instância da turma [ShardLocation](https://msdn.microsoft.com/library/azure/microsoft.azure.sqldatabase.elasticscale.shardmanagement.shardlocation.shardlocation.aspx#M:Microsoft.Azure.SqlDatabase.ElasticScale.ShardManagement.ShardLocation.) . No exemplo abaixo, uma base de dados com o nome **sample_shard_2** e todos os objetos necessárias no esquema dentro-foram criadas para colocar em espera intervalo [300, 400).  

    // sm is a RangeShardMap object.
    // Add a new shard to hold the range being added. 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Create the mapping and associate it with the new shard 
    sm.CreateRangeMapping(new RangeMappingCreationInfo<long> 
                            (new Range<long>(300, 400), shard2, MappingStatus.Online)); 


Como alternativa, pode utilizar o Powershell para criar um novo mapa Gestor da Shard. Um exemplo está disponível [aqui](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).
## <a name="to-add-a-shard-for-an-empty-part-of-an-existing-range"></a>Para adicionar um shard uma vazia parte de um intervalo existente  

Em alguns casos, poderá ter já mapeado um intervalo para um shard e preenchida parcialmente com dados, mas pretende agora dados futuros ser direcionado para uma shard diferente. Por exemplo, shard por intervalo de dia e tem já atribuídos 50 dias de um shard, mas num dia 24, pretende colar dados futuros terra num shard diferente. A base de dados flexível [ferramenta dividido em série](sql-database-elastic-scale-overview-split-and-merge.md) , pode executar esta operação, mas se movimento de dados não é necessário (por exemplo, dados para o intervalo de dias, [25, 50), ou seja, dia 25 inclusive a 50 exclusivas, ainda não existir) pode executar este totalmente utilizando as APIs de gestão de mapa Shard diretamente.

### <a name="example-splitting-a-range-and-assigning-the-empty-portion-to-a-newly-added-shard"></a>Exemplo: dividir um intervalo e atribuir a parte em branco para um shard recentemente adicionado

Uma base de dados com o nome "sample_shard_2" e todos os objetos necessárias no esquema dentro-ter sido criada.  

 
    // sm is a RangeShardMap object.
    // Add a new shard to hold the range we will move 
    Shard shard2 = null; 

    if (!sm.TryGetShard(new ShardLocation(shardServer, "sample_shard_2"),out shard2)) 
    { 
    
        shard2 = sm.CreateShard(new ShardLocation(shardServer, "sample_shard_2"));  
    } 

    // Split the Range holding Key 25 

    sm.SplitMapping(sm.GetMappingForKey(25), 25); 

    // Map new range holding [25-50) to different shard: 
    // first take existing mapping offline 
    sm.MarkMappingOffline(sm.GetMappingForKey(25)); 
    // now map while offline to a different shard and take online 
    RangeMappingUpdate upd = new RangeMappingUpdate(); 
    upd.Shard = shard2; 
    sm.MarkMappingOnline(sm.UpdateMapping(sm.GetMappingForKey(25), upd)); 

**Importante**: utilizar esta técnica apenas se tem a certeza de que o intervalo para o mapeamento actualizado está vazio.  Métodos acima não verificar dados para o intervalo a ser movido, por isso, é melhor incluir verificações no seu código.  Se existirem linhas no intervalo a ser movido, a distribuição de dados reais não irá corresponder o mapa shard atualizada. Utilize a [ferramenta de intercalação de divisão](sql-database-elastic-scale-overview-split-and-merge.md) para executar a operação em vez disso, nestes casos.  


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]
 
