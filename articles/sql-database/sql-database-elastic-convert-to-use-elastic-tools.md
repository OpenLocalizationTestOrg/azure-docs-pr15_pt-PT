<properties
   pageTitle="Migrar bases de dados existentes para escala-out | Microsoft Azure"
   description="Converter bases de dados sharded utilizar ferramentas de base de dados flexível criando um shard Gestor de mapa"
   services="sql-database"
   documentationCenter=""
   authors="ddove"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="10/24/2016"
   ms.author="ddove"/>

# <a name="migrate-existing-databases-to-scale-out"></a>Migrar bases de dados existentes para fora de escala

Gerir facilmente a sua dimensionada-out sharded bases de dados existentes com ferramentas de base de dados de base de dados do Azure SQL (tal como a [biblioteca de base de dados flexível do cliente](sql-database-elastic-database-client-library.md)). Primeiro tem de converter um conjunto de bases de dados para utilizar o [Gestor de mapear shard](sql-database-elastic-scale-shard-map-management.md)existente. 

## <a name="overview"></a>Descrição geral
Para migrar uma base de dados sharded existente: 

1. Prepare a [base de dados do shard mapa manager](sql-database-elastic-scale-shard-map-management.md).
2. Crie o mapa shard.
3. Prepare as shards individuais.  
2. Adicione mapeamentos ao mapa de shard.

Estas técnicas podem ser implementadas através da [biblioteca de cliente do .NET Framework](http://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Client/)ou scripts PowerShell encontrados no [Azure SQL DB - scripts de ferramentas da base de dados flexível](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db). Os exemplos aqui utilizam os scripts de PowerShell.

Para mais informações sobre o ShardMapManager, consulte o artigo [Shard mapear gestão](sql-database-elastic-scale-shard-map-management.md). Para obter uma descrição geral das ferramentas da base de dados flexível, consulte o artigo [Descrição geral das funcionalidades de base de dados flexível](sql-database-elastic-scale-introduction.md).

## <a name="prepare-the-shard-map-manager-database"></a>Preparar a base de dados do Gestor de mapa shard

O Gestor de mapa shard é uma base de dados especial que contém os dados para gerir bases de dados de saída dimensionada. Pode utilizar uma base de dados existente ou criar uma nova base de dados. Tenha em atenção que serve como Gestor de mapa shard uma base de dados não deve ser a mesma base de dados como uma shard. Tenha em atenção que o script do PowerShell não crie a base de dados por si. 

## <a name="step-1-create-a-shard-map-manager"></a>Passo 1: criar um shard Gestor de mapa

    # Create a shard map manager. 
    New-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 
    #<server_name> and <smm_db_name> are the server name and database name 
    # for the new or existing database that should be used for storing 
    # tenant-database mapping information.

### <a name="to-retrieve-the-shard-map-manager"></a>Para obter o Gestor de mapa shard

Após a criação, pode obter o Gestor de mapa shard com este cmdlet. Este passo é necessária, sempre que tem de utilizar o objeto ShardMapManager.

    # Try to get a reference to the Shard Map Manager  
    $ShardMapManager = Get-ShardMapManager -UserName '<user_name>' 
    -Password '<password>' 
    -SqlServerName '<server_name>' 
    -SqlDatabaseName '<smm_db_name>' 

  
## <a name="step-2-create-the-shard-map"></a>Passo 2: criar o mapa de shard

Tem de selecionar o tipo de mapa de shard para criar. A escolha depende a arquitetura de base de dados: 

1. Inquilino única por bases de dados (para termos, consulte o [Glossário](sql-database-elastic-scale-glossary.md)). 
2. Múltiplos inquilinos por bases de dados (dois tipos):
    3. Mapeamento de lista
    4. Mapeamento de intervalo
 

Para um modelo de inquilino único, crie um mapa de shard **mapeamento de lista** . O modelo de inquilino único atribui uma base de dados por inquilino. Este é um modelo de eficaz para programadores SaaS como simplifica a gestão de.

![Mapeamento de lista][1]

O modelo de inquilino com várias atribui inquilinos várias a uma base de dados única (e pode distribuir grupos de inquilinos em várias bases de dados). Utilize este modelo quando esperado cada inquilino para têm necessidades em termos dados pequenas. Neste modelo, podemos atribuir um intervalo de inquilinos a uma base de dados utilizando o **mapeamento de intervalo**. 
 

![Mapeamento de intervalo][2]

Ou que possa implementar um modelo de base de dados do inquilino com várias utilizando um *mapeamento de lista* para atribuir múltiplos inquilinos a uma base de dados simples. Por exemplo, DS1 é utilizada para armazenar informações sobre o id de inquilino 1 e 5 e DB2 armazena dados para o inquilino 10 e inquilino 7. 

![Diversas inquilinos no DB único][3] 

**Com base na sua escolha, escolha uma das seguintes opções:**

### <a name="option-1-create-a-shard-map-for-a-list-mapping"></a>Opção 1: criar um mapa de shard para um mapeamento de lista
Crie um mapa de shard utilizando o objeto ShardMapManager. 

    # $ShardMapManager is the shard map manager object. 
    $ShardMap = New-ListShardMap -KeyType $([int]) 
    -ListShardMapName 'ListShardMap' 
    -ShardMapManager $ShardMapManager 
 
 
### <a name="option-2-create-a-shard-map-for-a-range-mapping"></a>Opção 2: criar um mapa de shard para um mapeamento de intervalo

Note que para utilizar este padrão de mapeamento, o id de inquilino valores tem de estar intervalos contínuos e é aceitável ter intervalo: controla nos intervalos ignorando simplesmente o intervalo ao criar as bases de dados.

    # $ShardMapManager is the shard map manager object 
    # 'RangeShardMap' is the unique identifier for the range shard map.  
    $ShardMap = New-RangeShardMap 
    -KeyType $([int]) 
    -RangeShardMapName 'RangeShardMap' 
    -ShardMapManager $ShardMapManager 

### <a name="option-3-list-mappings-on-a-single-database"></a>Opção 3: Listar os mapeamentos nos única base de dados
Também a configurar este padrão requer a criação de um mapa de lista, conforme mostrado no passo 2, a opção 1.

## <a name="step-3-prepare-individual-shards"></a>Passo 3: Preparar shards individuais

Adicione cada shard (base de dados) para o Gestor de mapa shard. Isto prepara as bases de dados individuais para armazenar informações sobre o mapeamento. Execute este método no cada shard.
     
    Add-Shard 
    -ShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>'
    # The $ShardMap is the shard map created in step 2.
 

## <a name="step-4-add-mappings"></a>Passo 4: Adicionar mapeamentos

A adição de mapeamentos depende do tipo de mapa de shard que criou. Se tiver criado um mapa de lista, adicionar mapeamentos de lista. Se tiver criado um mapa de intervalo, adicionar mapeamentos de intervalo.

### <a name="option-1-map-the-data-for-a-list-mapping"></a>Opção 1: mapear os dados para um mapeamento de lista

Mapear os dados ao adicionar um mapeamento de lista para cada inquilino.  

    # Create the mappings and associate it with the new shards 
    Add-ListMapping 
    -KeyType $([int]) 
    -ListPoint '<tenant_id>' 
    -ListShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 

### <a name="option-2-map-the-data-for-a-range-mapping"></a>Opção 2: mapear os dados para um mapeamento de intervalo

Adicione os mapeamentos de intervalo para todo o inquilino id intervalo – associações de base de dados:

    # Create the mappings and associate it with the new shards 
    Add-RangeMapping 
    -KeyType $([int]) 
    -RangeHigh '5' 
    -RangeLow '1' 
    -RangeShardMap $ShardMap 
    -SqlServerName '<shard_server_name>' 
    -SqlDatabaseName '<shard_database_name>' 


### <a name="step-4-option-3-map-the-data-for-multiple-tenants-on-a-single-database"></a>Passo 4 opção 3: mapear os dados para múltiplos inquilinos numa única base de dados

Para cada inquilino, execute o ListMapping adicionar (opção 1, acima). 


## <a name="checking-the-mappings"></a>Verificar os mapeamentos

Informações sobre os shards existentes e os mapeamentos eles associados podem ser consultadas utilizando a seguinte comandos:  

    # List the shards and mappings 
    Get-Shards -ShardMap $ShardMap 
    Get-Mappings -ShardMap $ShardMap 

## <a name="summary"></a>Resumo

Depois de concluir a configuração, pode começar a utilizar a biblioteca do cliente flexível da base de dados. Também pode utilizar o [Encaminhamento dependente de dados](sql-database-elastic-scale-data-dependent-routing.md) e [consulta com várias shard](sql-database-elastic-scale-multishard-querying.md).

## <a name="next-steps"></a>Próximos passos


Obtenha os scripts de PowerShell do [sripts de ferramentas da base de dados do Azure SQL elásticos DB](https://gallery.technet.microsoft.com/scriptcenter/Azure-SQL-DB-Elastic-731883db).

As ferramentas também são no GitHub: [Azure/elásticos db ferramentas](https://github.com/Azure/elastic-db-tools).

Utilize a ferramenta de intercalação de divisão para mover os dados para ou de um modelo de inquilino com várias a um modelo de inquilino único. Consulte o artigo [ferramenta de impressão em série de divisão](sql-database-elastic-scale-get-started.md).

## <a name="additional-resources"></a>Recursos adicionais

Para obter informações sobre comuns padrões de arquitectura de dados das aplicações de base de dados do inquilinos com várias software-como-a-service (SaaS), consulte o artigo [Padrões de Design para inquilino com várias SaaS aplicações com base de dados do SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md).

## <a name="questions-and-feature-requests"></a>Perguntas e pedidos de funcionalidade

Para as questões, consulte comunique-no [Fórum de base de dados SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) e para pedidos de funcionalidades, consulte adicioná-los para o [Fórum de comentários de base de dados SQL](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/sql-database-elastic-convert-to-use-elastic-tools/listmapping.png
[2]: ./media/sql-database-elastic-convert-to-use-elastic-tools/rangemapping.png
[3]: ./media/sql-database-elastic-convert-to-use-elastic-tools/multipleonsingledb.png
 
