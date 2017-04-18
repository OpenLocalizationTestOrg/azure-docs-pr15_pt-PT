<properties
   pageTitle="Gerir estatísticas sobre tabelas no armazém de dados SQL | Microsoft Azure"
   description="Introdução ao estatísticas sobre tabelas no armazém de dados do SQL Azure."
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
   ms.date="06/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="managing-statistics-on-tables-in-sql-data-warehouse"></a>Gerir estatísticas sobre tabelas no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

Quanto mais armazém de dados SQL sabe sobre os seus dados, mais rápido-pode executar consultas de dados.  A forma que o utilizador informa o armazém de dados SQL sobre os seus dados, é por recolhem estatísticas sobre os seus dados.  Está com dificuldades estatísticas dos seus dados é um do mais importantes que pode fazer para optimizar as suas consultas.  Estatísticas de ajudam a criar o plano mais ideal das suas consultas armazém de dados de SQL.  Isto acontece porque o otimizador de consulta SQL armazém de dados é uma otimizador de custo com base.  Isto é, se compara o custo dos diversos planos de consulta e, em seguida, seleciona o plano com o custo mais baixo, que também deve ser o plano que executará o mais rápido.

Estatísticas podem ser criadas numa única coluna, várias colunas ou num índice remissivo de uma tabela.  Estatísticas são armazenadas num histograma que captura o intervalo e selectividade de valores.  Esta é de interesse específico quando necessita do otimizador de avaliar associações, GROUP BY, HAVING e cláusulas WHERE numa consulta.  Por exemplo, se o otimizador de estimativas do que a data em que está a filtrar na sua consulta irá devolver 1 linha, pode escolher a muito diferente planear que se as estimativas que data tem selecionado irá devolver 1 milhão de linhas.  Enquanto criar estatísticas é extremamente importante, também é igualmente importante refletir esse estatísticas *com exatidão* o estado atual da tabela.  Está com dificuldades estatísticas atualizadas assegura que um bom plano está seleccionado pelo otimizador de.  Os planos criados pelo otimizador só são tão boas como as estatísticas dos seus dados.

O processo de criação e atualizar as estatísticas atualmente é um processo manual, mas é muito simples fazer.  Este é ao contrário do SQL Server que cria e actualiza estatísticas sobre colunas simples e os índices automaticamente.  Ao utilizar as informações abaixo, pode automatizar significativamente a gestão das estatísticas dos seus dados. 

## <a name="getting-started-with-statistics"></a>Introdução ao estatísticas

 Criar estatísticas de amostra cada coluna é uma forma fácil de começar a trabalhar com as estatísticas.  Uma vez que também é igualmente importante manter as estatísticas atualizado, pode ser uma abordagem conservadora atualizar as estatísticas de diariamente ou depois de cada carga. São sempre compromissos entre o desempenho e o custo para criar e actualizar estatísticas.  Se encontrar está a demorar demasiado tempo a manter todas as estatísticas, poderá querer tentar ser selective mais sobre as colunas que tenham estatísticas ou as colunas que necessitem de actualizações frequentes.  Por exemplo, poderá pretender atualizar colunas de data diariamente, tal como podem ser adicionados novos valores em vez de após cada carga. Novamente, irá obter mais benefícios por ter estatísticas na colunas envolvidas na associações, GROUP BY, HAVING e cláusulas WHERE.  Se tiver uma tabela com muitas colunas que só são utilizados na cláusula SELECT, não poderão ajudá-estatísticas sobre estas colunas e despesas um pouco mais esforço para identificar apenas as colunas onde irão ajudá-estatísticas, pode reduzir o tempo para manter as estatísticas.

## <a name="multi-column-statistics"></a>Estatísticas de várias colunas

Para além de criar estatísticas nas colunas única, poderá constatar que as suas consultas irão beneficiar estatísticas de várias colunas.  Estatísticas de várias colunas são estatísticas criadas numa lista de colunas.  Incluem as estatísticas de coluna única na primeira coluna na lista, bem como algumas informações de coluna de publicação em correlação denominado densidade.  Por exemplo, se tiver uma tabela que junta à outra duas colunas, poderá constatar que armazém de dados SQL melhor pode optimizar o plano se compreende pode utilizar para a relação entre duas colunas.   Estatísticas de várias colunas podem melhorar o desempenho da consulta para algumas operações como compostas associações e agrupar por.

## <a name="updating-statistics"></a>Atualizar as estatísticas

Atualizar as estatísticas é uma parte importante da sua rotina de gestão de base de dados.  Quando altera a distribuição dos dados da base de dados, estatísticas tem de ser atualizados.  Estatísticas Desatualizadas, serão direcionado para o desempenho da consulta sub ideal.

É uma melhor prática atualizar as estatísticas sobre colunas de data cada dia como novas datas são adicionadas.  Cada novas linhas de tempo são carregados para o armazenamento de dados, novas datas de carga ou datas da transação são adicionadas. Estes alterar a distribuição de dados e faça as estatísticas de desatualizado. Por outro lado, estatísticas sobre uma coluna de país/região numa tabela de cliente nunca poderão ter de ser atualizados, tal como é geralmente não altera a distribuição dos valores. Partindo do princípio de que a distribuição é constante entre os clientes, adicionar novas linhas para a variação de tabela não é aceder ao alterar a distribuição de dados. No entanto, se seu armazém de dados contém apenas um país e trazer dados de um país novo, que resulta em dados a partir de vários países a ser armazenados, em seguida, altamente tem de atualizar estatísticas sobre a coluna país/região.

Uma das perguntas primeiros para pedir ao for uma consulta de resolução de problemas, "São as estatísticas de atualizado?"

Esta pergunta não é um que pode ser respondida por idade dos dados. Um objeto de estatísticas atualizados pode ser muito antigo se não tiver havido não altera o material os dados subjacentes. Quando o número de linhas foi alterado substancialmente ou existe uma alteração material na distribuição dos valores de uma coluna determinado *, em seguida,* está na altura de atualizar as estatísticas.  

Para sua referência, **SQL Server** (não armazém de dados SQL) atualiza automaticamente as estatísticas de seguintes situações:

- Se tiver zero linhas na tabela, ao adicionar linhas, irá obter uma atualização automática das estatísticas
- Quando adiciona mais de 500 linhas a uma tabela, começando com menos de 500 linhas (por exemplo, no início tiver 499 e, em seguida, adicionar 500 linhas para um total de 999 linhas), irá obter uma atualização automática 
- Assim que tiver mais de 500 linhas que tem de adicionar 500 linhas adicionais + 20% do tamanho da tabela antes do que verá uma atualização automática do Estado

Uma vez que não existe nenhuma das DMV para determinar se dados dentro da tabela foram alterados desde as últimas estatísticas de tempo foram atualizadas, o sabendo a idade de estatísticas do utilizador, pode fornecer-lhe com a peça da imagem.  Pode utilizar a seguinte consulta para determinar a última vez que as estatísticas de onde atualizados sobre cada tabela.  

> [AZURE.NOTE] Lembre-se de que se existir uma alteração material na distribuição dos valores de uma coluna determinado, deverá actualizar estatísticas, independentemente da última vez que foram atualizadas.  

```sql
SELECT
    sm.[name] AS [schema_name],
    tb.[name] AS [table_name],
    co.[name] AS [stats_column_name],
    st.[name] AS [stats_name],
    STATS_DATE(st.[object_id],st.[stats_id]) AS [stats_last_updated_date]
FROM
    sys.objects ob
    JOIN sys.stats st
        ON  ob.[object_id] = st.[object_id]
    JOIN sys.stats_columns sc    
        ON  st.[stats_id] = sc.[stats_id]
        AND st.[object_id] = sc.[object_id]
    JOIN sys.columns co    
        ON  sc.[column_id] = co.[column_id]
        AND sc.[object_id] = co.[object_id]
    JOIN sys.types  ty    
        ON  co.[user_type_id] = ty.[user_type_id]
    JOIN sys.tables tb    
        ON  co.[object_id] = tb.[object_id]
    JOIN sys.schemas sm    
        ON  tb.[schema_id] = sm.[schema_id]
WHERE
    st.[user_created] = 1;
```

Colunas de data no armazém de dados, por exemplo, normalmente necessitem frequentes estatísticas atualizações. Cada novas linhas de tempo são carregados para o armazenamento de dados, novas datas de carga ou datas da transação são adicionadas. Estes alterar a distribuição de dados e faça as estatísticas de desatualizado.  Por outro lado, estatísticas sobre uma coluna de género numa tabela de cliente nunca poderão ter de ser atualizados. Partindo do princípio de que a distribuição é constante entre os clientes, adicionar novas linhas para a variação de tabela não é aceder ao alterar a distribuição de dados. No entanto, se o seu armazém de dados contém apenas um género e um novo requisito resultados no sexos várias, em seguida, altamente tem de atualizar as estatísticas sobre a coluna género.

Para obter uma explicação ainda mais, consulte o artigo [Estatísticas][] no MSDN.

## <a name="implementing-statistics-management"></a>Implementar a gestão de estatísticas

Muitas vezes é uma boa ideia para expandir os seus dados processo para se certificar de que as estatísticas são atualizadas no final da carga de carregamento. O carregamento de dados é quando tabelas com mais frequência alterar o seu tamanho e/ou a sua distribuição dos valores. Por conseguinte, este é um local para implementar o algumas processos de gestão de lógico.

Algumas orientações são fornecidas abaixo para atualizar as estatísticas de durante o processo de carregamento:

- Certifique-se de que cada tabela carregada contém pelo menos um objeto de estatísticas atualizado. Isto atualiza as informações de tamanho (contagem de linhas e contagem de páginas) de tabelas como parte da atualização de estatística.
- Concentre-se na sua participação numa associação, agrupar por, ordenar por e DISTINCT cláusulas de colunas
- Considere atualizar "ascendente chave" colunas como da transação datas com mais frequência como estes valores não serão incluídas em histograma estatísticas.
- Considere a atualizar as colunas de distribuição estático menos frequentemente.
- Lembre-se de que cada objeto de estatística é atualizado em série. Implementar a simplesmente `UPDATE STATISTICS <TABLE_NAME>` podem não ser ideal - especialmente para tabelas grande com muitas objetos estatísticas.

> [AZURE.NOTE] Para obter mais detalhes sobre [ascendente chave] consulte a documentação de técnica de modelo de estimativa de cardinalidade do SQL Server 2014.

Para obter uma explicação ainda mais, consulte o artigo [Cardinalidade estimativa][] no MSDN.

## <a name="examples-create-statistics"></a>Exemplos: Criar estatísticas

Estes exemplos mostram como utilizar várias opções para a criação de estatísticas. As opções que utilizar para cada coluna variam consoante as características dos seus dados e como a coluna será utilizada em consultas.

### <a name="a-create-single-column-statistics-with-default-options"></a>RESPOSTAS. Criar uma coluna estatísticas com as opções predefinidas

Para criar estatísticas numa coluna, basta fornece um nome para o objeto estatísticas e o nome da coluna.

Esta sintaxe utiliza todas as opções predefinidas. Por predefinição, o armazém de dados SQL amostras 20 percentagem da tabela quando cria estatísticas.

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]);
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1);
```

### <a name="b-create-single-column-statistics-by-examining-every-row"></a>B. Criar uma coluna estatísticas ao examinar todas as linhas

A taxa de amostragem predefinida de 20 por cento é suficiente para a maior parte das situações. No entanto, pode ajustar a taxa de amostragem.

Para a tabela completa de exemplo, utilize esta sintaxe:

```sql
CREATE STATISTICS [statistics_name] ON [schema_name].[table_name]([column_name]) WITH FULLSCAN;
```

Por exemplo:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH FULLSCAN;
```

### <a name="c-create-single-column-statistics-by-specifying-the-sample-size"></a>C. Criar uma coluna estatísticas, especificando o tamanho da amostra

Em alternativa, pode especificar o tamanho da amostra como uma percentagem:

```sql
CREATE STATISTICS col1_stats ON dbo.table1 (col1) WITH SAMPLE = 50 PERCENT;
```

### <a name="d-create-single-column-statistics-on-only-some-of-the-rows"></a>D. Criar uma coluna estatísticas apenas em algumas das linhas

Outra opção, pode criar estatísticas de uma parte das linhas na tabela. Esta opção é denominada uma estatística filtrada.

Por exemplo, pode utilizar estatísticas filtradas quando planeia uma partição específica de uma tabela com partições grande de consulta. Através da criação estatísticas nos apenas valores partição, a precisão das estatísticas irá melhorar a e, por conseguinte, a melhorar o desempenho da consulta.

Este exemplo cria estatísticas num intervalo de valores. Os valores facilmente podem ser definidos para fazer corresponder o intervalo de valores numa partição.

```sql
CREATE STATISTICS stats_col1 ON table1(col1) WHERE col1 > '2000101' AND col1 < '20001231';
```

> [AZURE.NOTE] Para o otimizador de consulta para considere utilizar estatísticas filtradas quando que escolhe o plano de consulta distribuído, a consulta tem ajustado na definição do objeto estatísticas. Ao utilizar o exemplo anterior, o local onde a cláusula tem de especificar valores col1 entre 2000101 e 20001231 da consulta.

### <a name="e-create-single-column-statistics-with-all-the-options"></a>E. Criar estatísticas de uma coluna com todas as opções

Obviamente, pode, combinar as opções em conjunto. O exemplo abaixo cria um objeto de estatísticas filtrados com um tamanho da amostra personalizado:

```sql
CREATE STATISTICS stats_col1 ON table1 (col1) WHERE col1 > '2000101' AND col1 < '20001231' WITH SAMPLE = 50 PERCENT;
```

Para a referência completa, consulte o artigo [Criar estatísticas][] MSDN.

### <a name="f-create-multi-column-statistics"></a>F. Criar estatísticas de várias colunas

Para criar uma estatísticas de várias colunas, simplesmente utilizar os exemplos anteriores, mas especificar mais colunas.

> [AZURE.NOTE] O histograma, que é utilizado para calcular o número de linhas no resultado da consulta, só está disponível para a primeira coluna listada na definição de objeto do estatísticas.

Neste exemplo, o histograma está no *produto\_categoria*. Estatísticas de publicação em várias colunas são calculadas no *produto\_categoria* e *produto\_sub_c\ategory*:

```sql
CREATE STATISTICS stats_2cols ON table1 (product_category, product_sub_category) WHERE product_category > '2000101' AND product_category < '20001231' WITH SAMPLE = 50 PERCENT;
```

Uma vez que existe uma correlação entre *produto\_categoria* e *produto\_sub\_categoria*, um stat de várias coluna pode ser útil se estas colunas são acedidas ao mesmo tempo.

### <a name="g-create-statistics-on-all-the-columns-in-a-table"></a>G. Criar estatísticas em todas as colunas numa tabela

Uma forma de criar estatísticas é para problemas de comandos criar estatísticas depois de criar a tabela.

```sql
CREATE TABLE dbo.table1
(
   col1 int
,  col2 int
,  col3 int
)
WITH
  (
    CLUSTERED COLUMNSTORE INDEX
  )
;

CREATE STATISTICS stats_col1 on dbo.table1 (col1);
CREATE STATISTICS stats_col2 on dbo.table2 (col2);
CREATE STATISTICS stats_col3 on dbo.table3 (col3);
```

### <a name="h-use-a-stored-procedure-to-create-statistics-on-all-columns-in-a-database"></a>H. Utilizar um procedimento armazenado para criar estatísticas de todas as colunas numa base de dados

Armazém de dados SQL não tem um procedimento armazenado do sistema equivalente a [sp_create_stats] [no SQL Server. Este procedimento armazenado cria um objeto de estatísticas de única coluna, cada coluna da base de dados que ainda não tiver estatísticas.

Isto irá ajudar a começar a trabalhar com a estrutura da base de dados. Esteja à vontade para adaptá-lo para as suas necessidades.

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_create_stats]
(   @create_type    tinyint -- 1 default 2 Fullscan 3 Sample
,   @sample_pct     tinyint
)
AS

IF @create_type NOT IN (1,2,3)
BEGIN
    THROW 151000,'Invalid value for @stats_type parameter. Valid range 1 (default), 2 (fullscan) or 3 (sample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN;
    DROP TABLE #stats_ddl;
END;

CREATE TABLE #stats_ddl
WITH    (   DISTRIBUTION    = HASH([seq_nmbr])
        ,   LOCATION        = USER_DB
        )
AS
WITH T
AS
(
SELECT      t.[name]                        AS [table_name]
,           s.[name]                        AS [table_schema_name]
,           c.[name]                        AS [column_name]
,           c.[column_id]                   AS [column_id]
,           t.[object_id]                   AS [object_id]
,           ROW_NUMBER()
            OVER(ORDER BY (SELECT NULL))    AS [seq_nmbr]
FROM        sys.[tables] t
JOIN        sys.[schemas] s         ON  t.[schema_id]       = s.[schema_id]
JOIN        sys.[columns] c         ON  t.[object_id]       = c.[object_id]
LEFT JOIN   sys.[stats_columns] l   ON  l.[object_id]       = c.[object_id]
                                    AND l.[column_id]       = c.[column_id]
                                    AND l.[stats_column_id] = 1
LEFT JOIN   sys.[external_tables] e ON  e.[object_id]       = t.[object_id]
WHERE       l.[object_id] IS NULL
AND         e.[object_id] IS NULL -- not an external table
)
SELECT  [table_schema_name]
,       [table_name]
,       [column_name]
,       [column_id]
,       [object_id]
,       [seq_nmbr]
,       CASE @create_type
        WHEN 1
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+')' AS VARCHAR(8000))
        WHEN 2
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH FULLSCAN' AS VARCHAR(8000))
        WHEN 3
        THEN    CAST('CREATE STATISTICS '+QUOTENAME('stat_'+table_schema_name+ '_' + table_name + '_'+column_name)+' ON '+QUOTENAME(table_schema_name)+'.'+QUOTENAME(table_name)+'('+QUOTENAME(column_name)+') WITH SAMPLE '+@sample_pct+'PERCENT' AS VARCHAR(8000))
        END AS create_stat_ddl
FROM T
;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''
;

WHILE @i <= @t
BEGIN
    SET @s=(SELECT create_stat_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

Para criar estatísticas em todas as colunas na tabela com este procedimento, basta chame o procedimento.

```sql
prc_sqldw_create_stats;
```

## <a name="examples-update-statistics"></a>Exemplos: estatísticas de atualização

Para atualizar as estatísticas, pode:

1. Atualize um objeto de estatísticas. Especifique o nome do objeto estatísticas que pretende atualizar.
2. Atualize todos os objetos de estatísticas numa tabela. Especifique o nome da tabela em vez de um objeto de estatísticas específicas.


### <a name="a-update-one-specific-statistics-object"></a>RESPOSTAS. Atualizar um objeto de estatísticas específicas ###
Utilize a seguinte sintaxe para actualizar um objecto estatísticas específicas:

```sql
UPDATE STATISTICS [schema_name].[table_name]([stat_name]);
```

Por exemplo:

```sql
UPDATE STATISTICS [dbo].[table1] ([stats_col1]);
```

Ao atualizar objetos estatísticas específicas, pode minimizar o tempo e recursos necessários para gerir as estatísticas. Isto requer alguns pensamento, no entanto, para escolher os objetos estatísticas melhor atualizar.


### <a name="b-update-all-statistics-on-a-table"></a>B. Atualizar todas as estatísticas numa tabela ###
Isto mostra um método simples para atualizar todos os objetos de estatísticas numa tabela.

```sql
UPDATE STATISTICS [schema_name].[table_name];
```

Por exemplo:

```sql
UPDATE STATISTICS dbo.table1;
```

É fácil de utilizar esta declaração de. Lembre-se apenas este actualiza todas as estatísticas sobre a tabela e, consequentemente, poderá efetuar mais trabalho que é necessário. Se o desempenho não for um problema, esta é altamente a forma mais fácil e mais completa para garantir a estatísticas estão atualizadas.

> [AZURE.NOTE] Quando atualizar todas as estatísticas numa tabela, armazém de dados SQL faz uma pesquisa para a tabela para cada as estatísticas de exemplo. Se a tabela for grande, tem o número de colunas e estatísticas muitos, poderá ser mais eficiente para actualizar individuais estatísticas com base nas necessidades.

Para obter um implementação de uma `UPDATE STATISTICS` procedimento, consulte o artigo[temporário] de [Tabelas temporárias]. O método de implementação é ligeiramente diferente para o `CREATE STATISTICS` procedimento acima indicado, mas o resultado final é a mesma.

Para a sintaxe completa, consulte [As estatísticas de atualização][] no MSDN.

## <a name="statistics-metadata"></a>Estatísticas de metadados
Existem várias funções que pode utilizar para encontrar informações sobre as estatísticas e vista de sistema. Por exemplo, pode ver se um objecto estatísticas pode estar desatualizado utilizando a função de estatística data para ver quando as estatísticas foram última criadas ou atualizadas.

### <a name="catalog-views-for-statistics"></a>Vistas para as estatísticas de catálogo
Estas vistas do sistema fornecem informações sobre as estatísticas:

| Vista de catálogo | Descrição |
| :----------- | :---------- |
| [sys.Columns][]  | Uma linha para cada coluna. |
| [sys.Objects][]  | Uma linha para cada objeto da base de dados. |  |
| [sys.schemas][]  | Uma linha para cada esquema da base de dados. |  |
| [sys.Stats][] | Uma linha para cada objeto estatísticas. |
| [sys.stats_columns][] | Uma linha para cada coluna no objeto estatísticas. Ligações de volta para sys.columns. |
| [sys.Tables][] | Uma linha para cada tabela (inclui tabelas externas). |
| [sys.table_types][] | Uma linha para cada tipo de dados. |


### <a name="system-functions-for-statistics"></a>Funções estatísticas do sistema
Estas funções de sistema são úteis para trabalhar com as estatísticas:

| Função de sistema | Descrição |
| :-------------- | :---------- |
| [STATS_DATE][]    | Data que o objeto estatísticas foram atualizados pela última vez. |
| [DBCC SHOW_STATISTICS][] | Fornece informações de resumo ao nível e detalhadas sobre a distribuição dos valores como compreendidas pelo objeto estatísticas. |

### <a name="combine-statistics-columns-and-functions-into-one-view"></a>Combinar colunas estatísticas e funções numa vista

Esta vista mostra as colunas que se relacionam com as estatísticas e a função de [] [STATS_DATE()] em conjunto de resultados.

```sql
CREATE VIEW dbo.vstats_columns
AS
SELECT
        sm.[name]                           AS [schema_name]
,       tb.[name]                           AS [table_name]
,       st.[name]                           AS [stats_name]
,       st.[filter_definition]              AS [stats_filter_defiinition]
,       st.[has_filter]                     AS [stats_is_filtered]
,       STATS_DATE(st.[object_id],st.[stats_id])
                                            AS [stats_last_updated_date]
,       co.[name]                           AS [stats_column_name]
,       ty.[name]                           AS [column_type]
,       co.[max_length]                     AS [column_max_length]
,       co.[precision]                      AS [column_precision]
,       co.[scale]                          AS [column_scale]
,       co.[is_nullable]                    AS [column_is_nullable]
,       co.[collation_name]                 AS [column_collation_name]
,       QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS two_part_name
,       QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])
                                            AS three_part_name
FROM    sys.objects                         AS ob
JOIN    sys.stats           AS st ON    ob.[object_id]      = st.[object_id]
JOIN    sys.stats_columns   AS sc ON    st.[stats_id]       = sc.[stats_id]
                            AND         st.[object_id]      = sc.[object_id]
JOIN    sys.columns         AS co ON    sc.[column_id]      = co.[column_id]
                            AND         sc.[object_id]      = co.[object_id]
JOIN    sys.types           AS ty ON    co.[user_type_id]   = ty.[user_type_id]
JOIN    sys.tables          AS tb ON  co.[object_id]        = tb.[object_id]
JOIN    sys.schemas         AS sm ON  tb.[schema_id]        = sm.[schema_id]
WHERE   1=1
AND     st.[user_created] = 1
;
```

## <a name="dbcc-showstatistics-examples"></a>Exemplos de DBCC SHOW_STATISTICS()

DBCC SHOW_STATISTICS() mostra os dados contidos dentro de um objecto estatísticas. Estes dados é recebida três partes.

1. Cabeçalho
2. Densidade Vetor
3. Histograma

Os metadados de cabeçalho sobre as estatísticas. O histograma mostra a distribuição dos valores na primeira coluna de chave do objeto estatísticas. Correlação da publicação em várias colunas de densidade vector medidas. SQLDW calcula cardinalidade estimativas com qualquer um dos dados no objeto estatísticas.

### <a name="show-header-density-and-histogram"></a>Mostrar cabeçalho, densidade e histograma

Este exemplo simple mostra todas as três partes de um objeto de estatísticas.

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>)
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1);
```

### <a name="show-one-or-more-parts-of-dbcc-showstatistics"></a>Mostrar uma ou mais partes do DBCC SHOW_STATISTICS();

Se apenas estiver interessado em Ver partes específicas, utilize o `WITH` cláusula e especificar quais as partes que pretende ver:

```sql
DBCC SHOW_STATISTICS([<schema_name>.<table_name>],<stats_name>) WITH stat_header, histogram, density_vector
```

Por exemplo:

```sql
DBCC SHOW_STATISTICS (dbo.table1, stats_col1) WITH histogram, density_vector
```

## <a name="dbcc-showstatistics-differences"></a>Diferenças de DBCC SHOW_STATISTICS()
DBCC SHOW_STATISTICS() mais estritamente é implementada no armazém de dados SQL comparadas com o SQL Server.

1. Funcionalidades não documentadas não são suportadas
- Não é possível utilizar Stats_stream
- Não poderá participar resultados para subconjuntos específicos de dados de estatísticas, por exemplo, (STAT_HEADER associação DENSITY_VECTOR)
2. Não é possível definir NO_INFOMSGS para supressão de mensagem
3. Não pode ser utilizados entre parênteses Retos à volta de nomes de estatísticas
4. Não é possível utilizar os nomes das colunas para identificar objetos estatísticas
5. Erro personalizadas 2767 não é suportado

## <a name="next-steps"></a>Próximos passos

Para obter mais detalhes, consulte o artigo [DBCC SHOW_STATISTICS][] MSDN.  Para saber mais, consulte os artigos no [Descrição geral de tabela da][Descrição geral], [Tipos de dados de tabela][Tipos de dados], [distribuir a uma tabela][distribuir], [indexação de uma tabela]de[índice remissivo], [uma tabela de criação de partições][partição] e [Tabelas temporárias][temporário].  Para mais informações sobre procedimentos recomendados, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].  

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
[Estimativa cardinalidade]: https://msdn.microsoft.com/library/dn600374.aspx
[CRIAR ESTATÍSTICAS]: https://msdn.microsoft.com/library/ms188038.aspx
[DBCC SHOW_STATISTICS]:https://msdn.microsoft.com/library/ms174384.aspx
[Estatísticas]: https://msdn.microsoft.com/library/ms190397.aspx
[STATS_DATE]: https://msdn.microsoft.com/library/ms190330.aspx
[sys.Columns]: https://msdn.microsoft.com/library/ms176106.aspx
[sys.Objects]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.schemas]: https://msdn.microsoft.com/library/ms190324.aspx
[sys.Stats]: https://msdn.microsoft.com/library/ms177623.aspx
[sys.stats_columns]: https://msdn.microsoft.com/library/ms187340.aspx
[sys.Tables]: https://msdn.microsoft.com/library/ms187406.aspx
[sys.table_types]: https://msdn.microsoft.com/library/bb510623.aspx
[ESTATÍSTICAS DE ACTUALIZAÇÃO]: https://msdn.microsoft.com/library/ms187348.aspx

<!--Other Web references-->  
