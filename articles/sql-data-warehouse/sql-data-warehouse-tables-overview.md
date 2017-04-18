<properties
   pageTitle="Descrição geral de tabelas no armazém de dados SQL | Microsoft Azure"
   description="Introdução ao Azure SQL dados armazém tabelas."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sonyam"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/04/2016"
   ms.author="sonyama;barbkess;jrj"/>

# <a name="overview-of-tables-in-sql-data-warehouse"></a>Descrição geral de tabelas no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

Introdução ao criar tabelas no armazém de dados SQL é simple.  A sintaxe de [Criar tabela][] básica segue a sintaxe comuns que é muito provavelmente já familiarizado com a partir de trabalhar com outras bases de dados.  Para criar uma tabela, pode simplesmente tem de atribuir um nome a sua tabela, atribua um nome das colunas e definir tipos de dados para cada coluna.  Se tiver criar tabelas nas outras bases de dados, este deverá ter um aspeto muito familiar para si.

```sql  
CREATE TABLE Customers (FirstName VARCHAR(25), LastName VARCHAR(25))
 ``` 

O exemplo acima cria uma tabela denominada clientes com duas colunas, NomePróprio e apelido.  Cada coluna é definida com um tipo de dados de VARCHAR(25), que limita os dados a 25 caracteres.  Estes fundamentais atributos de uma tabela, bem como outras pessoas, principalmente são os mesmos como outras bases de dados.  Tipos de dados são definidos para cada coluna e garantir a integridade dos dados.  Índices podem ser adicionados para melhorar o desempenho, reduzindo e/s.  Podem ser adicionados a partições para melhorar o desempenho quando precisar de modificar os dados.

[Mudar o nome] [ RENAME] uma tabela de armazém de dados SQL tem o seguinte aspeto:

```sql  
RENAME OBJECT Customer TO CustomerOrig; 
 ```

## <a name="distributed-tables"></a>Tabelas distribuídas

Um novo atributo fundamental introduzido pelo sistemas distribuídos como armazém de dados SQL é a **coluna de distribuição**.  A coluna de distribuição é muito o que parece.  É a coluna que determina como distribuir ou dividir os dados nos bastidores.  Quando cria uma tabela sem especificar a coluna de distribuição, a tabela é distribuída automaticamente utilizando **round robin**.  Enquanto round robin tabelas podem ser suficientes em alguns cenários, definir colunas de distribuição pode reduzir consideravelmente movimento de dados durante as consultas, assim otimizar o desempenho.  Consulte o artigo [distribuir a uma tabela][distribuir] para saber mais sobre como selecionar uma coluna de distribuição.

## <a name="indexing-and-partitioning-tables"></a>Indexação e a partições tabelas

À medida que tornam-se mais avançadas na utilização de armazém de dados SQL e pretende otimizar o desempenho, irá querer saber mais sobre a estrutura da tabela.  Para saber mais, consulte os artigos sobre [Tipos de dados de tabela][Tipos de dados], [distribuir a uma tabela][distribuir], [indexação de uma tabela]de[índice remissivo] e [uma tabela de criação de partições][partição].

## <a name="table-statistics"></a>Estatísticas da tabela

Estatísticas são uma extremamente importantes para obter o melhor desempenho fora do seu armazém de dados SQL.  Uma vez que armazém de dados SQL não ainda cria automaticamente e actualizar as estatísticas por si, como poderá ter sido fornecido esperar Azure SQL base de dados, ao ler nosso artigo sobre [Estatísticas][] poderão ser um dos artigos mais importantes que lê para se certificar de que obter o melhor desempenho a partir do seu consultas.

## <a name="temporary-tables"></a>Tabelas temporárias

As tabelas temporárias são tabelas que só existem para a duração do início de sessão e não podem ser visualizadas por outros utilizadores.  As tabelas temporárias podem ser uma boa forma para impedir que outras pessoas vejam os resultados temporários e também reduzir a necessidade de limpeza.  Uma vez que as tabelas temporárias também utilizam armazenamento local, pode oferecem um desempenho mais rápido para algumas operações.  Consulte os artigos[temporária] da [Tabela temporária]para obter mais detalhes acerca das tabelas temporárias.

## <a name="external-tables"></a>Tabelas externas

As tabelas externas, também conhecido como Polybase tabelas, são tabelas que podem ser consultadas a partir do armazém de dados SQL, mas o ponto de dados externos a partir do armazém de dados SQL.  Por exemplo, pode criar uma tabela externa que aponta para ficheiros de armazenamento de Blobs do Azure.  Para obter mais detalhes sobre como criar e uma tabela externa da consulta, consulte [carregar dados com Polybase][].  

## <a name="unsupported-table-features"></a>Funcionalidades de tabela não suportadas

Enquanto armazém de dados SQL contém muitos as mesmas funcionalidades de tabela disponibilizadas pelos outras bases de dados, existem algumas funcionalidades que ainda não são suportadas.  Abaixo encontra uma lista de algumas das funcionalidades de tabela que ainda não são suportadas.

| Funcionalidades não suportadas |
| --- |
|[Propriedade de identidade][] (consulte o artigo [Atribuir solução de chave de substituição][])|
|Chave primária, chaves externas, exclusivos e de verificação [Restrições de tabela][]|
|[Índices exclusivos][]|
|[Colunas calculadas][]|
|[Colunas dispersas][]|
|[Tipos de definidos pelo utilizador][]|
|[Sequência][]|
|[Accionadores][]|
|[Vistas indexadas][]|
|[Sinónimos][]|

## <a name="table-size-queries"></a>Consultas de tamanho de tabela

Uma forma simple para identificar espaço e linhas média consumidas por uma tabela em cada uma das 60 distribuições, é utilizar [DBCC PDW_SHOWSPACEUSED][].

```sql
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, utilizando os comandos DBCC pode ser bastante limitar.  Vistas de gestão de dinâmica (DMVs) permite-lhe ver mais detalhes, bem como dar-lhe muito maior controlo sobre os resultados da consulta.  Comece por criar esta vista, que será referida por muitos dos nossos exemplos este e outros artigos.

```sql
CREATE VIEW dbo.vTableSizes
AS
WITH base
AS
(
SELECT 
 GETDATE()                                                             AS  [execution_time]
, DB_NAME()                                                            AS  [database_name]
, s.name                                                               AS  [schema_name]
, t.name                                                               AS  [table_name]
, QUOTENAME(s.name)+'.'+QUOTENAME(t.name)                              AS  [two_part_name]
, nt.[name]                                                            AS  [node_table_name]
, ROW_NUMBER() OVER(PARTITION BY nt.[name] ORDER BY (SELECT NULL))     AS  [node_table_name_seq]
, tp.[distribution_policy_desc]                                        AS  [distribution_policy_name]
, c.[name]                                                             AS  [distribution_column]
, nt.[distribution_id]                                                 AS  [distribution_id]
, i.[type]                                                             AS  [index_type]
, i.[type_desc]                                                        AS  [index_type_desc]
, nt.[pdw_node_id]                                                     AS  [pdw_node_id]
, pn.[type]                                                            AS  [pdw_node_type]
, pn.[name]                                                            AS  [pdw_node_name]
, di.name                                                              AS  [dist_name]
, di.position                                                          AS  [dist_position]
, nps.[partition_number]                                               AS  [partition_nmbr]
, nps.[reserved_page_count]                                            AS  [reserved_space_page_count]
, nps.[reserved_page_count] - nps.[used_page_count]                    AS  [unused_space_page_count]
, nps.[in_row_data_page_count] 
    + nps.[row_overflow_used_page_count] 
    + nps.[lob_used_page_count]                                        AS  [data_space_page_count]
, nps.[reserved_page_count] 
 - (nps.[reserved_page_count] - nps.[used_page_count]) 
 - ([in_row_data_page_count] 
         + [row_overflow_used_page_count]+[lob_used_page_count])       AS  [index_space_page_count]
, nps.[row_count]                                                      AS  [row_count]
from 
    sys.schemas s
INNER JOIN sys.tables t
    ON s.[schema_id] = t.[schema_id]
INNER JOIN sys.indexes i
    ON  t.[object_id] = i.[object_id]
    AND i.[index_id] <= 1
INNER JOIN sys.pdw_table_distribution_properties tp
    ON t.[object_id] = tp.[object_id]
INNER JOIN sys.pdw_table_mappings tm
    ON t.[object_id] = tm.[object_id]
INNER JOIN sys.pdw_nodes_tables nt
    ON tm.[physical_name] = nt.[name]
INNER JOIN sys.dm_pdw_nodes pn
    ON  nt.[pdw_node_id] = pn.[pdw_node_id]
INNER JOIN sys.pdw_distributions di
    ON  nt.[distribution_id] = di.[distribution_id]
INNER JOIN sys.dm_pdw_nodes_db_partition_stats nps
    ON nt.[object_id] = nps.[object_id]
    AND nt.[pdw_node_id] = nps.[pdw_node_id]
    AND nt.[distribution_id] = nps.[distribution_id]
LEFT OUTER JOIN (select * from sys.pdw_column_distribution_properties where distribution_ordinal = 1) cdp
    ON t.[object_id] = cdp.[object_id]
LEFT OUTER JOIN sys.columns c
    ON cdp.[object_id] = c.[object_id]
    AND cdp.[column_id] = c.[column_id]
)
, size
AS
(
SELECT
   [execution_time]
,  [database_name]
,  [schema_name]
,  [table_name]
,  [two_part_name]
,  [node_table_name]
,  [node_table_name_seq]
,  [distribution_policy_name]
,  [distribution_column]
,  [distribution_id]
,  [index_type]
,  [index_type_desc]
,  [pdw_node_id]
,  [pdw_node_type]
,  [pdw_node_name]
,  [dist_name]
,  [dist_position]
,  [partition_nmbr]
,  [reserved_space_page_count]
,  [unused_space_page_count]
,  [data_space_page_count]
,  [index_space_page_count]
,  [row_count]
,  ([reserved_space_page_count] * 8.0)                                 AS [reserved_space_KB]
,  ([reserved_space_page_count] * 8.0)/1000                            AS [reserved_space_MB]
,  ([reserved_space_page_count] * 8.0)/1000000                         AS [reserved_space_GB]
,  ([reserved_space_page_count] * 8.0)/1000000000                      AS [reserved_space_TB]
,  ([unused_space_page_count]   * 8.0)                                 AS [unused_space_KB]
,  ([unused_space_page_count]   * 8.0)/1000                            AS [unused_space_MB]
,  ([unused_space_page_count]   * 8.0)/1000000                         AS [unused_space_GB]
,  ([unused_space_page_count]   * 8.0)/1000000000                      AS [unused_space_TB]
,  ([data_space_page_count]     * 8.0)                                 AS [data_space_KB]
,  ([data_space_page_count]     * 8.0)/1000                            AS [data_space_MB]
,  ([data_space_page_count]     * 8.0)/1000000                         AS [data_space_GB]
,  ([data_space_page_count]     * 8.0)/1000000000                      AS [data_space_TB]
,  ([index_space_page_count]  * 8.0)                                   AS [index_space_KB]
,  ([index_space_page_count]  * 8.0)/1000                              AS [index_space_MB]
,  ([index_space_page_count]  * 8.0)/1000000                           AS [index_space_GB]
,  ([index_space_page_count]  * 8.0)/1000000000                        AS [index_space_TB]
FROM base
)
SELECT * 
FROM size
;
```

### <a name="table-space-summary"></a>Resumo do espaço de tabela

Esta consulta devolve linhas e espaço por tabela.  É uma excelente consulta para ver quais as tabelas são suas tabelas maiores e se são round robin ou hash distribuído.  Para tabelas hash distribuído também mostra a coluna de distribuição.  Na maioria dos casos suas tabelas maior devem ser hash distribuído com um índice columnstore agrupadas.

```sql
SELECT 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
,    COUNT(distinct partition_nmbr) as nbr_partitions
,    SUM(row_count)                 as table_row_count
,    SUM(reserved_space_GB)         as table_reserved_space_GB
,    SUM(data_space_GB)             as table_data_space_GB
,    SUM(index_space_GB)            as table_index_space_GB
,    SUM(unused_space_GB)           as table_unused_space_GB
FROM 
    dbo.vTableSizes
GROUP BY 
     database_name
,    schema_name
,    table_name
,    distribution_policy_name
,     distribution_column
,    index_type_desc
ORDER BY
    table_reserved_space_GB desc
;
```

### <a name="table-space-by-distribution-type"></a>Espaço de tabela por tipo de distribuição

```sql
SELECT 
     distribution_policy_name
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY distribution_policy_name
;
```

### <a name="table-space-by-index-type"></a>Espaço de tabela por tipo de índice remissivo

```sql
SELECT 
     index_type_desc
,    SUM(row_count)                as table_type_row_count
,    SUM(reserved_space_GB)        as table_type_reserved_space_GB
,    SUM(data_space_GB)            as table_type_data_space_GB
,    SUM(index_space_GB)           as table_type_index_space_GB
,    SUM(unused_space_GB)          as table_type_unused_space_GB
FROM dbo.vTableSizes
GROUP BY index_type_desc
;
```

### <a name="distribution-space-summary"></a>Espaço de distribuição resumo

```sql
SELECT 
    distribution_id
,    SUM(row_count)                as total_node_distribution_row_count
,    SUM(reserved_space_MB)        as total_node_distribution_reserved_space_MB
,    SUM(data_space_MB)            as total_node_distribution_data_space_MB
,    SUM(index_space_MB)           as total_node_distribution_index_space_MB
,    SUM(unused_space_MB)          as total_node_distribution_unused_space_MB
FROM dbo.vTableSizes
GROUP BY     distribution_id
ORDER BY    distribution_id
;
```

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os artigos no [Tipos de dados de tabela]de[Tipos de dados], [distribuir a uma tabela][distribuir], [indexação de uma tabela]de[índice remissivo], [uma tabela de criação de partições][partição], [Mantendo estatísticas da tabela][Estatísticas] e [Tabelas temporárias][temporário].  Para mais informações sobre procedimentos recomendados, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Descrição geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice remissivo]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporária]: ./sql-data-warehouse-tables-temporary.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md
[Carregar os dados com Polybase]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md

<!--MSDN references-->
[CRIAR TABELA]: https://msdn.microsoft.com/library/mt203953.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx
[DBCC PDW_SHOWSPACEUSED]: https://msdn.microsoft.com/library/mt204028.aspx
[Propriedade de identidade]: https://msdn.microsoft.com/library/ms186775.aspx
[Atribuir a solução de chave de substituição]: https://blogs.msdn.microsoft.com/sqlcat/2016/02/18/assigning-surrogate-key-to-dimension-tables-in-sql-dw-and-aps/
[Restrições de tabela]: https://msdn.microsoft.com/library/ms188066.aspx
[Colunas calculadas]: https://msdn.microsoft.com/library/ms186241.aspx
[Colunas dispersas]: https://msdn.microsoft.com/library/cc280604.aspx
[Tipos de definidos pelo utilizador]: https://msdn.microsoft.com/library/ms131694.aspx
[Sequência]: https://msdn.microsoft.com/library/ff878091.aspx
[Accionadores]: https://msdn.microsoft.com/library/ms189799.aspx
[Vistas indexadas]: https://msdn.microsoft.com/library/ms191432.aspx
[Sinónimos]: https://msdn.microsoft.com/library/ms177544.aspx
[Índices exclusivos]: https://msdn.microsoft.com/library/ms188783.aspx

<!--Other Web references-->
