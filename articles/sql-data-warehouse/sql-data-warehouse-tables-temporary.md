<properties
   pageTitle="As tabelas temporárias no armazém de dados do SQL | Microsoft Azure"
   description="Introdução ao temporárias tabelas no armazém de dados do SQL Azure."
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
   ms.date="06/29/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="temporary-tables-in-sql-data-warehouse"></a>Tabelas temporárias no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

As tabelas temporárias são muito útil quando está a processar dados - especialmente durante a transformação onde os resultados intermédios estão breves. No armazém de dados do SQL tabelas temporárias existem ao nível de sessão.  Só estão visíveis para a sessão, na qual foram criadas e automaticamente desaparecem quando termina a sessão essa sessão.  As tabelas temporárias oferecem uma vantagem de desempenho porque os respetivos resultados são escritos no local em vez de armazenamento remoto.  Tabelas temporárias são um pouco diferentes no armazém de dados do SQL Azure base de dados do SQL Azure como poderá ser acedido a partir de qualquer local dentro da sessão, incluindo dentro e fora de um procedimento armazenado.

Este artigo contém sugestões essenciais para utilizar tabelas temporárias e realça os princípios das tabelas temporário ao nível da sessão. Utilizando as informações neste artigo pode ajudar a aplicar o seu código, melhorar a reutilização dos e facilidade de manutenção do código.

## <a name="create-a-temporary-table"></a>Criar uma tabela temporária

As tabelas temporárias são criadas por basta definir um prefixo para o nome da tabela com um `#`.  Por exemplo:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]       NVARCHAR(128) NOT NULL
,   [table_name]            NVARCHAR(128) NOT NULL
,   [stats_name]            NVARCHAR(128) NOT NULL
,   [stats_is_filtered]     BIT           NOT NULL
,   [seq_nmbr]              BIGINT        NOT NULL
,   [two_part_name]         NVARCHAR(260) NOT NULL
,   [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
```

As tabelas temporárias também podem ser criadas com um `CTAS` utilizando exatamente a mesmo abordagem:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,   HEAP
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
``` 

>[AZURE.NOTE] `CTAS`é um comando muito poderoso e tem a vantagem adicional de que está a ser muito eficiente na sua utilização de espaço de registo da transação. 


## <a name="dropping-temporary-tables"></a>Largando tabelas temporárias

Quando é criada uma nova sessão, sem tabelas temporárias devem existir.  No entanto, se estiver a chamar o mesmo procedimento armazenado, que cria um temporário com o mesmo nome, para se certificar de que seu `CREATE TABLE` as instruções são efetuada com êxito uma verificação de pré-lançamento existência simple com um `DROP` podem ser utilizados como no exemplo abaixo:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Relativamente à codificação consistência, é aconselhável para utilizar este padrão para tabelas e tabelas temporárias.  Também é aconselhável utilizar `DROP TABLE` para remover tabelas temporárias quando tiver concluído com os mesmos no seu código.  No procedimento armazenado desenvolvimento é bastante comum para ver os comandos de lista pendente agrupados no final de um procedimento para garantir que estes objetos são desorganizado.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Código modularizing

Uma vez que as tabelas temporárias podem ser vistas em qualquer lugar numa sessão de utilizador, este pode tirar partido para o ajudar a aplicar o código da aplicação.  Por exemplo, o procedimento armazenado abaixo reúne as práticas recomendadas dos acima para gerar DDL que irão atualizar todas as estatísticas de base de dados por nome de estatística.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                               AS [schema_name]
,       tb.[name]                                                               AS [table_name]
,       st.[name]                                                               AS [stats_name]
,       st.[has_filter]                                                         AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects         AS ob
JOIN    sys.stats           AS st   ON  ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc   ON  st.[stats_id]       = sc.[stats_id]
                                    AND st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co   ON  sc.[column_id]      = co.[column_id]
                                    AND sc.[object_id]      = co.[object_id]
JOIN    sys.tables          AS tb   ON  co.[object_id]      = tb.[object_id]
JOIN    sys.schemas         AS sm   ON  tb.[schema_id]      = sm.[schema_id]
WHERE   1=1
AND     st.[user_created]   = 1
GROUP BY
        sm.[name]
,       tb.[name]
,       st.[name]
,       st.[filter_definition]
,       st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

Nesta fase a ação apenas que ocorreu é a criação de um procedimento armazenado que irá simplesmente gerado uma tabela temporária, #stats_ddl, com instruções DDL.  Este procedimento armazenado será largue #stats_ddl se já existe para assegurar não falhar se executar mais do que uma vez dentro de uma sessão.  No entanto, uma vez que não existe nenhuma `DROP TABLE` no final do procedimento armazenado, quando o procedimento armazenado estiver concluída,-deixará a tabela criada para que podem ser lidos fora do procedimento armazenado.  No armazém de dados do SQL, ao contrário de outras bases de dados do SQL Server, é possível utilizar a tabela temporária fora o procedimento que criou.  As tabelas temporárias armazém de dados SQL podem ser utilizadas **em qualquer local** dentro da sessão. Isto pode levar a mais modular e fácil de gerir código como no exemplo abaixo:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

## <a name="temporary-table-limitations"></a>Limitações da tabela temporária

Armazém de dados SQL impor algumas limitações ao implementar tabelas temporárias.  Atualmente, apenas a sessão âmbito temporárias as tabelas são suportadas.  As tabelas temporárias global não são suportadas.  Além disso, não não possível criar vistas tabelas temporárias.

## <a name="next-steps"></a>Próximos passos

Para saber mais, consulte os artigos [Descrição geral de tabela da][Descrição geral], [Tipos de dados de tabela][Tipos de dados], [distribuir a uma tabela][distribuir], [indexação de uma tabela]de[índice remissivo], [uma tabela de criação de partições][partição de] [Manutenção de estatísticas da tabela]e[Estatísticas].  Para mais informações sobre procedimentos recomendados, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

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

<!--MSDN references-->

<!--Other Web references-->
