<properties
    pageTitle="Reportar por toda a bases de dados de saída dimensionada nuvem | Microsoft Azure"
    description="como configurar consultas flexível sobre a partições horizontais"    
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/27/2016"
    ms.author="torsteng" />

# <a name="reporting-across-scaled-out-cloud-databases-preview"></a>Reportar por toda a bases de dados de saída dimensionada nuvem (pré-visualização)

![Consulta em shards][1]

Bases de dados sharded Distribuir linhas ao longo de dados com escala saída camada. Esquema de é idêntico em todos os participantes bases de dados, também conhecidos como a partições horizontal. Utilizar uma consulta flexível, pode criar relatórios que aparecem em todas as bases de dados numa base de dados sharded.

Para um guia de introdução, consulte o artigo [relatórios através de bases de dados de saída dimensionada nuvem](sql-database-elastic-query-getting-started.md).

Para que não sejam sharded bases de dados, consulte o artigo [consulta ao longo de bases de dados na nuvem com diferentes esquemas](sql-database-elastic-query-vertical-partitioning.md). 

 
## <a name="prerequisites"></a>Pré-requisitos

* Crie um mapa de shard utilizando a biblioteca de cliente flexível da base de dados. consulte o artigo [Shard mapear gestão](sql-database-elastic-scale-shard-map-management.md). Ou utilizar a aplicação de exemplo na [Introdução às ferramentas de base de dados flexível](sql-database-elastic-scale-get-started.md).
* Em alternativa, consulte o artigo [Migrar bases de dados existentes fora dimensionada bases de dados](sql-database-elastic-convert-to-use-elastic-tools.md).
* O utilizador tem de possuir permissão de alterar qualquer origem de dados externa. Esta permissão está incluído com a permissão de alterar a base de dados.
* Permissões de alterar qualquer origem de dados EXTERNOS são necessários para se referir à origem de dados subjacentes.

## <a name="overview"></a>Descrição geral

Estas instruções criar a representação de metadados da sua camada sharded dados da base de dados de consulta flexível. 


1. [CRIAR A CHAVE DE MODELO GLOBAL](https://msdn.microsoft.com/library/ms174382.aspx)
2. [CRIAR A BASE DE DADOS CONFINADO CREDENCIAIS](https://msdn.microsoft.com/library/mt270260.aspx)
3. [CRIAR A ORIGEM DE DADOS EXTERNA](https://msdn.microsoft.com/library/dn935022.aspx)
4. [CRIAR TABELA EXTERNA](https://msdn.microsoft.com/library/dn935021.aspx) 

## <a name="11-create-database-scoped-master-key-and-credentials"></a>Criar base de dados 1.1 confinados chave principal e as credenciais 

A credencial é utilizada pela consulta flexível para ligar à sua bases de dados remotos.  

    CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'password';
    CREATE DATABASE SCOPED CREDENTIAL <credential_name>  WITH IDENTITY = '<username>',  
    SECRET = '<password>'
    [;]
 
>[AZURE.NOTE] Certifique-se de que o *"\<nome de utilizador\>"* não inclui qualquer *"@servername"* sufixo. 

## <a name="12-create-external-data-sources"></a>1.2 Criar origens de dados externas

Sintaxe:

    <External_Data_Source> ::=    
    CREATE EXTERNAL DATA SOURCE <data_source_name> WITH                                            
            (TYPE = SHARD_MAP_MANAGER,
                    LOCATION = '<fully_qualified_server_name>',
            DATABASE_NAME = ‘<shardmap_database_name>',
            CREDENTIAL = <credential_name>, 
            SHARD_MAP_NAME = ‘<shardmapname>’ 
                   ) [;] 

### <a name="example"></a>Exemplo 

    CREATE EXTERNAL DATA SOURCE MyExtSrc 
    WITH 
    ( 
        TYPE=SHARD_MAP_MANAGER,
        LOCATION='myserver.database.windows.net', 
        DATABASE_NAME='ShardMapDatabase', 
        CREDENTIAL= SMMUser, 
        SHARD_MAP_NAME='ShardMap' 
    );
 
Obter a lista de origens de dados externas atual: 

    select * from sys.external_data_sources; 

Origem de dados externa referencia seu mapa shard. Uma consulta flexível, em seguida, utiliza a origem de dados externa e o mapa de shard subjacente para enumerar as bases de dados que participam na camada de dados. As mesmas credenciais são utilizadas para ler o mapa shard e aceder aos dados nas shards durante o processamento de uma consulta flexível. 

## <a name="13-create-external-tables"></a>1.3 criar tabelas externas a 
 
Sintaxe:  

    CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name  
        ( { <column_definition> } [ ,...n ])     
        { WITH ( <sharded_external_table_options> ) }
    ) [;]  
    
    <sharded_external_table_options> ::= 
      DATA_SOURCE = <External_Data_Source>,       
      [ SCHEMA_NAME = N'nonescaped_schema_name',] 
      [ OBJECT_NAME = N'nonescaped_object_name',] 
      DISTRIBUTION = SHARDED(<sharding_column_name>) | REPLICATED |ROUND_ROBIN

**Exemplo**

    CREATE EXTERNAL TABLE [dbo].[order_line]( 
         [ol_o_id] int NOT NULL, 
         [ol_d_id] tinyint NOT NULL,
         [ol_w_id] int NOT NULL, 
         [ol_number] tinyint NOT NULL, 
         [ol_i_id] int NOT NULL, 
         [ol_delivery_d] datetime NOT NULL, 
         [ol_amount] smallmoney NOT NULL, 
         [ol_supply_w_id] int NOT NULL, 
         [ol_quantity] smallint NOT NULL, 
         [ol_dist_info] char(24) NOT NULL 
    ) 
    
    WITH 
    ( 
        DATA_SOURCE = MyExtSrc, 
        SCHEMA_NAME = 'orders', 
        OBJECT_NAME = 'order_details', 
        DISTRIBUTION=SHARDED(ol_w_id)
    ); 

Obter a lista de tabelas externas a partir da base de dados atual: 

    SELECT * from sys.external_tables; 

Ao largar tabelas externas:

    DROP EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name. ] table_name[;]

### <a name="remarks"></a>Observações

Os dados\_cláusula origem define a origem de dados externa (um mapa de shard) que é utilizada para a tabela externa.  

ESQUEMA de\_nome e OBJETO\_cláusulas nome mapeiam a definição de tabela externa para uma tabela num esquema diferente. Se for omitido, o esquema do objeto remoto é considerado "dbo" e o nome do mesmo é considerado idêntico ao nome de tabela externa a ser definido. Isto é útil se o nome da sua tabela remoto já é disponibilizado na base de dados onde pretende criar a tabela externa. Por exemplo, que pretende definir uma tabela externa para obter uma vista de agregação de vistas de catálogo ou camada DMVs nos seus dados com escala saída. Uma vez que as vistas de catálogo e DMVs já existir localmente, não é possível utilizar os seus nomes para a definição de tabela externa. Em alternativa, utilize um nome diferente e utilizar a vista de catálogo ou as de DMV nome no esquema de\_nome e/ou OBJETO\_cláusulas de nome. (Veja o exemplo abaixo). 

A cláusula distribuição Especifica a distribuição de dados utilizada para esta tabela. Processador a consulta utiliza as informações fornecidas na cláusula distribuição para criar os planos de consulta mais eficientes.  

1. **SHARDED** significa dados estiver dividido em partições horizontalmente ao longo das bases de dados. A partições tecla para a distribuição de dados é o parâmetro **< sharding_column_name >** .
2. **REPLICADA** significa que idênticos cópias da tabela estão presentes no cada base de dados. É da sua responsabilidade para se certificar de que as réplicas são idênticas entre as bases de dados.
3. **Arred\_ROBIN** significa que a tabela horizontalmente tiver partições utilizando um método de distribuição dependente da aplicação. 

**Dados camada referência**: A tabela externa DDL que se refere a uma origem de dados externos. Origem de dados externa Especifica um mapa de shard que fornece a tabela externa com as informações necessárias para localizar todas as bases de dados no seu camada de dados. 


### <a name="security-considerations"></a>Considerações de segurança 

Utilizadores com acesso para a tabela externa automaticamente obter acesso a tabelas subjacentes remotos em credenciais indicadas na definição de origem de dados externos. Evite indesejada elevação de privilégios através de credenciais da origem de dados externos. Utilize conceder ou REVOGAR para uma tabela externa como se fosse uma tabela normal.  

Assim que tiver definido a sua origem de dados externos e as suas tabelas externas, agora pode usar completo T-SQL sobre suas tabelas externas.

## <a name="example-querying-horizontal-partitioned-databases"></a>Exemplo: consultar bases de dados com partições horizontais 

A seguinte consulta executa uma associação de três-se entre armazém, encomendas e linhas da encomenda e utiliza agregados de várias e um filtro selective. -Assume a partições (1) horizontal (sharding) e (2) que estão sharded por coluna id do armazém armazém, encomendas e linhas da encomenda e que a consulta flexível pode cocriação localize associações nas shards e a parte da consulta no shards paralelamente dispendiosa do processo. 

    select  
         w_id as warehouse,
         o_c_id as customer,
         count(*) as cnt_orderline,
         max(ol_quantity) as max_quantity,
         avg(ol_amount) as avg_amount, 
         min(ol_delivery_d) as min_deliv_date
    from warehouse 
    join orders 
    on w_id = o_w_id
    join order_line 
    on o_id = ol_o_id and o_w_id = ol_w_id 
    where w_id > 100 and w_id < 200 
    group by w_id, o_c_id 
 
## <a name="stored-procedure-for-remote-t-sql-execution-spexecuteremote"></a>Procedimento para remota T-SQL armazenado: sp\_execute_remote

Consulta flexível também apresenta um procedimento armazenado que fornece acesso direto às shards. O procedimento armazenado chama [sp\_executar \_remoto](https://msdn.microsoft.com/library/mt703714) e pode ser utilizado para executar remotos procedimentos armazenados ou o código T SQL nas bases de dados remotos. Bastam seguintes parâmetros: 

* Nome da origem de dados (nvarchar): O nome da origem de dados externa do tipo RDBMS. 
* Consulta (nvarchar): consulta a T-SQL para ser executado em cada shard. 
* Declaração de parâmetro (nvarchar) - opcional: cadeia com definições de tipo de dados para os parâmetros utilizados para o parâmetro de consulta (como sp_executesql). 
* Lista de valores de parâmetro - opcional: separados por vírgulas lista de valores de parâmetros (como sp_executesql).

O sp\_executar\_remoto utiliza a origem de dados externos fornecida nos parâmetros de invocação para executar a instrução de T SQL determinada nas bases de dados remotos. Utiliza a credencial da origem de dados externas para ligar à base de dados de manager shardmap e as bases de dados remotos.  

Exemplo: 

    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse' 

## <a name="connectivity-for-tools"></a>Conectividade de ferramentas  

Utilizar cadeias de ligação normais do SQL Server para ligar a sua aplicação, as ferramentas de integração de BI e dados para a base de dados com as suas definições de tabela externa. Certifique-se de que é suportado do SQL Server como uma origem de dados de uma ferramenta. Em seguida, referência a base de dados de consulta flexível como quaisquer outras bases de dados do SQL Server ligado para a ferramenta e utilize tabelas externos a partir do ferramenta ou de aplicações como se fossem tabelas locais. 

## <a name="best-practices"></a>Melhores práticas 

* Certifique-se de que a base de dados de ponto final de consulta flexível tenha sido dado acesso para a base de dados shardmap e todos os shards através das firewalls DB do SQL.  

* Validar ou impor a distribuição de dados definida pela tabela externa. Se a distribuição de dados reais é a diferença entre a distribuição especificada na sua definição de tabela, as consultas podem lucro resultados inesperados. 

* Consulta flexível atualmente não efetua eliminação shard quando predicados sobre a chave de sharding permitirá-lo com segurança excluir determinadas shards de processamento.

* Consulta flexível funciona melhor para consultas onde a maior parte do cálculo pode ser efetuado nas shards. Obter normalmente o melhor desempenho de consulta com predicados de filtro selective que possa ser avaliado no shards ou associações sobre as teclas de criação de partições que podem ser executadas de uma forma alinhado à partição no shards todos os. Outras padrões de consulta poderão ter de carregar grandes quantidades de dados a partir de shards para o nó cabeça e podem executar mal

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-query-horizontal-partitioning/horizontalpartitioning.png
<!--anchors-->
