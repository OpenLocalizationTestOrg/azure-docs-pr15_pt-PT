<properties
   pageTitle="Criar a partições tabelas no armazém de dados SQL | Microsoft Azure"
   description="Introdução ao tabela partições no armazém de dados do SQL Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="07/18/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="partitioning-tables-in-sql-data-warehouse"></a>Criar a partições tabelas no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

Criação de partições é suportada em todos os tipos de tabela armazém de dados SQL; incluindo columnstore agrupada, índice agrupado e pilha.  Criação de partições também é suportada em todos os tipos de distribuição, incluindo hash ou round robin distribuído.  Criar a partições permite que dividir os seus dados em grupos mais pequenos de dados e na maioria dos casos, partições é feito numa coluna de data.

## <a name="benefits-of-partitioning"></a>Vantagens de criação de partições

Criação de partições pode beneficiar desempenho de consulta e manutenção de dados.  Se prestações ambas ou apenas são dependente como os dados são carregados e se a mesma coluna pode ser utilizada para ambos os fins, uma vez que a partições só podem ser efetuado numa coluna.

### <a name="benefits-to-loads"></a>Benefícios aos carregamentos

O benefício principal da criação de partições no armazém de dados do SQL é melhorar a eficiência e desempenho de carregamento dados através da utilização de eliminação de partição, mudar e unir.  Na maioria dos casos dados divididos em partições uma coluna de data que estreitamente está associada à sequência de que os dados são carregados para a base de dados.  Uma das vantagens da utilização das partições para manter dados maiores-lo a evitar registo da transação.  Enquanto simplesmente inserir, atualizar ou eliminar dados pode ser abordagem mais eficaz, com um pequeno pensamento e esforço, utilizando a partições durante o processo de carga pode substancialmente melhorar o desempenho.

Mudar a partições pode ser utilizado para remover ou substituir uma secção de uma tabela rapidamente.  Por exemplo, uma tabela de factos de vendas pode conter dados apenas para os últimos 36 meses.  No final de cada mês, mês do mais antigo de dados de vendas é eliminado a partir da tabela.  Estes dados podem ser eliminados utilizando uma instrução de eliminar para eliminar os dados para o mês do mais antigo.  No entanto, eliminar uma grande quantidade de dados linha por-linha com uma instrução delete pode demorar muito tempo, bem como criar o risco de grandes operações que pode demorar muito tempo para anulação se algo de errado.  Uma abordagem mais ideal é simplesmente largue a mais antiga partição de dados.  Onde eliminar as linhas individuais poderá demorar horas, eliminar uma partição inteira pode demorar segundos.

### <a name="benefits-to-queries"></a>Benefícios em consultas

Criação de partições pode também ser utilizada para melhorar o desempenho da consulta.  Se uma consulta aplica um filtro numa coluna com partições, este pode limitar a pesquisa a apenas as partições Qualificável que pode ser um subconjunto dos dados, evitando uma pesquisa completa tabela muito mais pequeno.  Com a introdução dos índices columnstore agrupadas, as vantagens de desempenho de eliminação predicado são úteis inferior, mas em alguns casos podem existir uma vantagem às consultas.  Por exemplo, se a tabela de factos de vendas é dividida em 36 meses utilizando o campo Data de venda e, em seguida consultas esse filtro à data da venda podem ignorar a procurar na partições que não correspondem ao filtro.

## <a name="partition-sizing-guidance"></a>Orientação de redimensionamento partição

Enquanto a partições podem ser utilizados para melhorar o desempenho alguns cenários, criar uma tabela com a partições **demasiadas** pode afetar o desempenho em determinadas circunstâncias.  Estas preocupações se verifiquem especialmente para tabelas columnstore agrupadas.  Para criar a partições para ser útil, é importante compreender quando utilizar a partições e o número de partições a criar.  Não existe nenhuma regra fast disco rígida Control a partições quantos são demasiadas, depende os seus dados e a partições quantos estão a ser carregados para em simultâneo.  Mas como um geral regra útil, tenha em conta da ação de adicionar 10s para 100s das partições, não 1000s.

Ao criar a partições em tabelas **columnstore agrupada** , é importante ter em consideração serão direcionado quantas linhas em cada partição.  Para compressão ideal e desempenho das tabelas columnstore agrupadas, é necessário um mínimo de 1 milhão de linhas por distribuição e partição.  Antes das partições são criadas, armazém de dados SQL divide já cada tabela em 60 bases de dados distribuídos.  Partições adicionado a uma tabela são além as distribuições criadas nos bastidores.  Utilizar neste exemplo, se a tabela de factos de vendas contidas 36 partições mensais e dado que armazém de dados SQL tem 60 distribuições, em seguida, na tabela de factos vendas deve conter 60 milhões de linhas por mês ou mil milhões de 2.1 quando são propagados todos os meses.  Se uma tabela contiver significativamente menor linhas que o número mínimo recomendada de linhas por partição, considere utilizar a partições menos encaminhá-aumentar o número de linhas por partição.  Consulte também o artigo de[índice] de [indexação]que inclui consultas que podem ser executadas em armazém de dados SQL para avaliar a qualidade dos índices de columnstore cluster.

## <a name="syntax-difference-from-sql-server"></a>Diferença de sintaxe do SQL Server

Armazém de dados SQL apresenta uma definição simplificada das partições que é ligeiramente diferente do SQL Server.  Criação de partições funções e esquemas não são utilizadas no armazém de dados do SQL como se encontram no SQL Server.  Em vez disso, tudo o que precisa de fazer é identificar com partições coluna e os pontos de limite.  Enquanto a sintaxe de criação de partições poderá ser ligeiramente diferente do SQL Server, os conceitos básicos são os mesmos.  Uma coluna de partição por tabela, que pode ser variavam partição de suporte do SQL Server e armazém de dados SQL.  Para saber mais sobre como criar partições, consulte o artigo [divididos tabelas e índices][].

O exemplo abaixo de uma instrução SQL Data Warehouse divididos [Criar tabela][] , partições a tabela FactInternetSales na coluna OrderDateKey:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

## <a name="migrating-partitioning-from-sql-server"></a>Migrar a partições do SQL Server

Para migrar as definições de partição do SQL Server para armazém de dados SQL simplesmente:

- Elimine o [esquema de partição][]de SQL Server.
- Adicione a definição de [função partição][] ao criar tabela.

Se estiver a migrar uma tabela com partições a partir de uma instância do SQL Server a SQL abaixo pode ajudá-lo para investigar o número de linhas que estão em cada partição.  Tenha em atenção que, se for utilizada a mesma granularidade partições no armazém de dados SQL, o número de linhas por partição irá diminuir por um fator de 60.  

```sql
-- Partition information for a SQL Server Database
SELECT      s.[name]                        AS      [schema_name]
,           t.[name]                        AS      [table_name]
,           i.[name]                        AS      [index_name]
,           p.[partition_number]            AS      [partition_number]
,           SUM(a.[used_pages]*8.0)         AS      [partition_size_kb]
,           SUM(a.[used_pages]*8.0)/1024    AS      [partition_size_mb]
,           SUM(a.[used_pages]*8.0)/1048576 AS      [partition_size_gb]
,           p.[rows]                        AS      [partition_row_count]
,           rv.[value]                      AS      [partition_boundary_value]
,           p.[data_compression_desc]       AS      [partition_compression_desc]
FROM        sys.schemas s
JOIN        sys.tables t                    ON      t.[schema_id]         = s.[schema_id]
JOIN        sys.partitions p                ON      p.[object_id]         = t.[object_id]
JOIN        sys.allocation_units a          ON      a.[container_id]      = p.[partition_id]
JOIN        sys.indexes i                   ON      i.[object_id]         = p.[object_id]
                                            AND     i.[index_id]          = p.[index_id]
JOIN        sys.data_spaces ds              ON      ds.[data_space_id]    = i.[data_space_id]
LEFT JOIN   sys.partition_schemes ps        ON      ps.[data_space_id]    = ds.[data_space_id]
LEFT JOIN   sys.partition_functions pf      ON      pf.[function_id]      = ps.[function_id]
LEFT JOIN   sys.partition_range_values rv   ON      rv.[function_id]      = pf.[function_id]
                                            AND     rv.[boundary_id]      = p.[partition_number]
WHERE       p.[index_id] <=1
GROUP BY    s.[name]
,           t.[name]
,           i.[name]
,           p.[partition_number]
,           p.[rows]
,           rv.[value]
,           p.[data_compression_desc]
;
```

## <a name="workload-management"></a>Gestão de carga de trabalho

Uma peça final considerações para factor a decisão de partição tabela são a [Gestão de carga de trabalho][].  Gestão de carga de trabalho no armazém de dados SQL é principalmente a gestão de memória e simultaneidade.  No armazém de dados SQL a memória máxima atribuída a cada distribuição durante a execução de consulta é recurso regulado aulas.  Idealmente as partições serão ser dimensionadas tendo em conta outros fatores como as necessidades de memória construir columnstore agrupadas índices.  Agrupado columnstore índices benefício substancialmente quando são-lhe atribuídas mais memória.  Por conseguinte, irá querer para se certificar de que uma recompilação do índice remissivo partição não é fique completamente privada de memória. Aumentar a quantidade de memória disponível para a sua consulta pode ser realizado pelos mudar da função predefinida, smallrc, para uma das outras funções como largerc.

Obter informações sobre a alocação de memória por distribuição estão disponíveis ao consultar as vistas de gestão de dinâmica Governador recurso. Na realidade conceder sua memória vai ser menor do que os valores abaixo. No entanto, isto fornece um nível de orientação que pode utilizar quando as partições para operações de gestão de dados de redimensionamento.  Experimente evitar as partições para além de conceder a memória fornecida pela classe Recurso extra grandes de redimensionamento. Se a partições ultrapasse nesta figura corre o risco de pressão de memória que por sua vez leva a menos compressão ideal.

```sql
SELECT  rp.[name]                               AS [pool_name]
,       rp.[max_memory_kb]                      AS [max_memory_kb]
,       rp.[max_memory_kb]/1024                 AS [max_memory_mb]
,       rp.[max_memory_kb]/1048576              AS [mex_memory_gb]
,       rp.[max_memory_percent]                 AS [max_memory_percent]
,       wg.[name]                               AS [group_name]
,       wg.[importance]                         AS [group_importance]
,       wg.[request_max_memory_grant_percent]   AS [request_max_memory_grant_percent]
FROM    sys.dm_pdw_nodes_resource_governor_workload_groups  wg
JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools   rp ON wg.[pool_id] = rp.[pool_id]
WHERE   wg.[name] like 'SloDWGroup%'
AND     rp.[name]    = 'SloDWPool'
;
```

## <a name="partition-switching"></a>Mudar de partição

Armazém de dados SQL suporta a partições dividir, intercalar e mudar. Cada um destas funções é excuted com a instrução [ALTER TABLE][] .

Para mudar a partições entre duas tabelas tem de garantir que a partições Alinhar no respetivos respetivos limites e que correspondem as definições de tabela. À medida que não estão disponíveis para o intervalo de valores numa tabela de impor restrições de verificação a tabela de origem tem de conter os limites de partição mesmo como tabela de destino. Se não for o caso, em seguida, o parâmetro partição falhará como os metadados partição não serão sincronizados.

### <a name="how-to-split-a-partition-that-contains-data"></a>Como dividir uma partição que contém dados

O método mais eficaz para dividir uma partição de que já contém dados está a utilizar um `CTAS` declaração. Se a tabela com partições estiver um columnstore agrupada, em seguida, a tabela partição deve ser vazia antes de poder ser dividido.

Segue-se uma tabela de columnstore com partições de exemplo que contém uma linha em cada partição:

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
        [ProductKey]            int          NOT NULL
    ,   [OrderDateKey]          int          NOT NULL
    ,   [CustomerKey]           int          NOT NULL
    ,   [PromotionKey]          int          NOT NULL
    ,   [SalesOrderNumber]      nvarchar(20) NOT NULL
    ,   [OrderQuantity]         smallint     NOT NULL
    ,   [UnitPrice]             money        NOT NULL
    ,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    (20000101
                    )
                )
)
;

INSERT INTO dbo.FactInternetSales
VALUES (1,19990101,1,1,1,1,1,1);
INSERT INTO dbo.FactInternetSales
VALUES (1,20000101,1,1,1,1,1,1);


CREATE STATISTICS Stat_dbo_FactInternetSales_OrderDateKey ON dbo.FactInternetSales(OrderDateKey);
```

> [AZURE.NOTE] Ao criar o objeto de estatística, podemos Certifique-se que metadados da tabela são mais preciso. Se omitir podemos criar estatísticas, armazém de dados SQL irá utilizar valores predefinidos. Para obter detalhes sobre as estatísticas reveja [Estatísticas][].

Vamos, em seguida, pode consultar a contagem de linha utilizando o `sys.partitions` catálogo vista:

```sql
SELECT  QUOTENAME(s.[name])+'.'+QUOTENAME(t.[name]) as Table_name
,       i.[name] as Index_name
,       p.partition_number as Partition_nmbr
,       p.[rows] as Row_count
,       p.[data_compression_desc] as Data_Compression_desc
FROM    sys.partitions p
JOIN    sys.tables     t    ON    p.[object_id]   = t.[object_id]
JOIN    sys.schemas    s    ON    t.[schema_id]   = s.[schema_id]
JOIN    sys.indexes    i    ON    p.[object_id]   = i.[object_Id]
                            AND   p.[index_Id]    = i.[index_Id]
WHERE t.[name] = 'FactInternetSales'
;
```

Se estamos a tentar dividir nesta tabela, iremos irá obter um erro:

```sql
ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Ocorreu uma falha msg 35346 nível 15, estado 1, linha 44 DIVIDIR cláusula da instrução ALTER PARTIÇÃO porque a partição não está vazia.  Apenas a partições vazias podem ser divididas em quando existe um índice de columnstore na tabela. Considere desativar o índice columnstore antes da instrução ALTER PARTIÇÃO de emissão, em seguida, a reformulação repetida o índice columnstore após alterar PARTIÇÃO estar concluída.

No entanto, pode utilizamos `CTAS` para criar uma nova tabela para armazenarem os nossos dados.

```sql
CREATE TABLE dbo.FactInternetSales_20000101
    WITH    (   DISTRIBUTION = HASH(ProductKey)
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101
                                )
                            )
            )
AS
SELECT *
FROM    FactInternetSales
WHERE   1=2
;
```

Como os limites de partição são alinhados a um parâmetro é permitido. Isto irá deixar a tabela de origem com uma partição vazia que recomendamos subsequentemente pode dividir.

```sql
ALTER TABLE FactInternetSales SWITCH PARTITION 2 TO  FactInternetSales_20000101 PARTITION 2;

ALTER TABLE FactInternetSales SPLIT RANGE (20010101);
```

Tudo o que falta para fazer consiste em Alinhar os nossos dados para os limites de partição novo utilizando `CTAS` e mudar os nossos dados novamente à tabela principal

```sql
CREATE TABLE [dbo].[FactInternetSales_20000101_20010101]
    WITH    (   DISTRIBUTION = HASH([ProductKey])
            ,   CLUSTERED COLUMNSTORE INDEX
            ,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                                (20000101,20010101
                                )
                            )
            )
AS
SELECT  *
FROM    [dbo].[FactInternetSales_20000101]
WHERE   [OrderDateKey] >= 20000101
AND     [OrderDateKey] <  20010101
;

ALTER TABLE dbo.FactInternetSales_20000101_20010101 SWITCH PARTITION 2 TO dbo.FactInternetSales PARTITION 2;
```

Assim que tiver concluído o movimento dos dados é uma boa ideia para atualizar as estatísticas sobre a tabela de destino para se certificar de que refletem com exatidão a distribuição dos dados na suas partições respetivos nova:

```sql
UPDATE STATISTICS [dbo].[FactInternetSales];
```

### <a name="table-partitioning-source-control"></a>Partição controlo da origem de tabela

Para evitar a sua definição de tabela a partir do **ferrugem** no seu sistema de controlo da origem poderá querer ter em consideração a seguinte abordagem:

1. Criar a tabela como uma tabela com partições mas sem valores de partição

```sql
CREATE TABLE [dbo].[FactInternetSales]
(
    [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT FOR VALUES
                    ()
                )
)
;
```

2. `SPLIT`a tabela como parte do processo de implementação:

```sql
-- Create a table containing the partition boundaries

CREATE TABLE #partitions
WITH
(
    LOCATION = USER_DB
,   DISTRIBUTION = HASH(ptn_no)
)
AS
SELECT  ptn_no
,       ROW_NUMBER() OVER (ORDER BY (ptn_no)) as seq_no
FROM    (
        SELECT CAST(20000101 AS INT) ptn_no
        UNION ALL
        SELECT CAST(20010101 AS INT)
        UNION ALL
        SELECT CAST(20020101 AS INT)
        UNION ALL
        SELECT CAST(20030101 AS INT)
        UNION ALL
        SELECT CAST(20040101 AS INT)
        ) a
;

-- Iterate over the partition boundaries and split the table

DECLARE @c INT = (SELECT COUNT(*) FROM #partitions)
,       @i INT = 1                                 --iterator for while loop
,       @q NVARCHAR(4000)                          --query
,       @p NVARCHAR(20)     = N''                  --partition_number
,       @s NVARCHAR(128)    = N'dbo'               --schema
,       @t NVARCHAR(128)    = N'FactInternetSales' --table
;

WHILE @i <= @c
BEGIN
    SET @p = (SELECT ptn_no FROM #partitions WHERE seq_no = @i);
    SET @q = (SELECT N'ALTER TABLE '+@s+N'.'+@t+N' SPLIT RANGE ('+@p+N');');

    -- PRINT @q;
    EXECUTE sp_executesql @q;

    SET @i+=1;
END

-- Code clean-up

DROP TABLE #partitions;
```

Com esta abordagem permanece estático o código no controlo de origem e os partições valores de limite são permitidos para ser dinâmicos; com o armazém a evoluir ao longo do tempo.

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os artigos no [Descrição geral de tabela da][Descrição geral], [Tipos de dados de tabela]de[Tipos de dados], [distribuir a uma tabela][distribuir], [indexação de uma tabela]de[índice remissivo], [Mantendo estatísticas da tabela][Estatísticas] e [Tabelas temporárias][temporário].  Para mais informações sobre procedimentos recomendados, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Descrição geral]: ./sql-data-warehouse-tables-overview.md
[Tipos de dados]: ./sql-data-warehouse-tables-data-types.md
[Distribuir]: ./sql-data-warehouse-tables-distribute.md
[Índice remissivo]: ./sql-data-warehouse-tables-index.md
[Partição]: ./sql-data-warehouse-tables-partition.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md
[Temporária]: ./sql-data-warehouse-tables-temporary.md
[gestão de carga de trabalho]: ./sql-data-warehouse-develop-concurrency.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md

<!-- MSDN Articles -->
[Tabelas com partições e os índices]: https://msdn.microsoft.com/library/ms190787.aspx
[ALTER TABLE]: https://msdn.microsoft.com/en-us/library/ms190273.aspx
[CRIAR TABELA]: https://msdn.microsoft.com/library/mt203953.aspx
[função partição]: https://msdn.microsoft.com/library/ms187802.aspx
[esquema de partição]: https://msdn.microsoft.com/library/ms179854.aspx


<!-- Other web references -->
