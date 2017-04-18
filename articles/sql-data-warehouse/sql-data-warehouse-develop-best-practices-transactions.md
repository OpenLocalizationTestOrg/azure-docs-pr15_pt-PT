<properties
   pageTitle="Otimizar as transações para armazém de dados SQL | Microsoft Azure"
   description="Melhores práticas orientações sobre atualizações da transação eficiente de escrita no armazém de dados do SQL Azure"
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
   ms.date="07/31/2016"
   ms.author="jrj;barbkess"/>

# <a name="optimizing-transactions-for-sql-data-warehouse"></a>Otimizar as transações para armazém de dados SQL

Este artigo explica como otimizar o desempenho do seu código transaccional enquanto minimizar o risco para reversões longos.

## <a name="transactions-and-logging"></a>Registo de transações e

Transações são um componente importante de um motor de base de dados relacional. Armazém de dados SQL utiliza transações durante a modificação de dados. Estas transações podem ser implícitos ou explícitos. Única `INSERT`, `UPDATE` e `DELETE` as instruções são todos os exemplos de transações implícitos. Transações explícitas são explicitamente escritas por um programador utilizando `BEGIN TRAN`, `COMMIT TRAN` ou `ROLLBACK TRAN` e são normalmente utilizadas quando precisa de ser associados em conjunto numa única unidade atómica várias declarações de modificação. 

Armazém de dados SQL Azure consolida alterações à base de dados através da transação registos. Cada distribuição tem os suas próprias registo de transações. Escritas de registo da transação são automáticas. Não existe nenhuma configuração obrigatória. No entanto, embora este processo garante a escrita apresentar uma sobrecarga no sistema. Pode minimizar o impacto escrevendo código forma transaccional eficiente. Código de forma transaccional eficiente enquadra ficarem duas categorias.

- Tirar partido construções de registo mínimas sempre que possível
- Dados de processo com secções âmbito para evitar no singular transações longa
- Tomar uma partição de mudança de padrão para modificações grandes para uma determinada partição

## <a name="minimal-vs-full-logging"></a>Mínimas vs. registo completo

Ao contrário de operações totalmente registadas, que utilizam o registo de transações para controlar cada alteração de linha, operações mínimas registadas controle do alocações de medida e apenas alterações de metadados. Por conseguinte, registo mínimo envolve apenas as informações que são necessárias para anular a transação em caso de uma falha ou um pedido de explícita de tipos de registo (`ROLLBACK TRAN`). Como muito menos informações são registadas no registo de transações, uma operação de mínimas com sessão iniciada executa melhor do que uma operação totalmente registada tamanho semelhantes. Além disso, uma vez que escreve menos o registo de transações, é gerado um montante muito mais pequeno dos dados de registo e por isso, é e/s mais eficiente.

Os limites de segurança da transação apenas serem aplicadas a operações totalmente registadas.

>[AZURE.NOTE] Operações mínimas registadas podem participar em transações explícitas. Tal como todas as alterações nas estruturas de alocação são monitorizadas, é possível recuperar operações mínimas registadas. É importante compreender que a alteração é registada "mínimas" não é barra com sessão iniciada.

## <a name="minimally-logged-operations"></a>Operações mínimas registadas

As seguintes operações forem capazes de mínimas sejam registados:

- Criar tabela como SELECIONAR ([CTAS][])
- INSERIR... SELECIONE
- CRIAR ÍNDICE
- ALTER RECONSTRUIR DE ÍNDICE REMISSIVO
- ÍNDICE DE LISTA PENDENTE
- TRUNCAR TABELA
- TABELA DE LISTA PENDENTE
- ALTER TABELA MUDAR PARTIÇÃO

<!--
- MERGE
- UPDATE on LOB Types .WRITE
- SELECT..INTO
-->

>[AZURE.NOTE] Operações de movimento de dados internas (tais como `BROADCAST` e `SHUFFLE`) não são afetados pelo limite de segurança da transação.

## <a name="minimal-logging-with-bulk-load"></a>Registo mínimo com carga em volume

`CTAS`e `INSERT...SELECT` são ambos em volume carregar operações. No entanto, ambos estão influenciados pela definição da tabela de destino e dependem do cenário de carregamento. Segue-se uma tabela que explica se a operação em volume serão totalmente ou mínimas registada:  

| Índice primária               | Cenário de carregamento                                            | Modo de registo |
| --------------------------- | -------------------------------------------------------- | ------------ |
| Área de dados dinâmicos                        | Qualquer                                                      | **Mínimas**  |
| Índice agrupado             | Tabela de destino vazio                                       | **Mínimas**  |
| Índice agrupado             | Linhas carregadas não se sobrepõem com páginas existentes no destino | **Mínimas**  |
| Índice agrupado             | Linhas carregadas sobreponha-a com páginas existentes no destino        | Completo         |
| Índice de Columnstore agrupado | Tamanho de lotes > = 102,400 por partição alinhada à distribuição | **Mínimas**  |
| Índice de Columnstore agrupado | Tamanho < 102,400 por partição alinhada à distribuição de batch  | Completo         |

É de notar que qualquer escritas para atualizar índices secundários ou que não sejam agrupadas será sempre operações totalmente registadas.

> [AZURE.IMPORTANT] Armazém de dados SQL tem 60 distribuições. Por conseguinte, partindo do princípio que todas as linhas são distribuídas uniformemente e fiquem numa única partição, o lote será necessário contém 6,144,000 linhas ou maior mínimas ter sessão iniciada ao escrever a um índice de Columnstore agrupadas. Se a tabela que se tiver a partições e limites de partição do intervalo de linhas a ser inseridas, em seguida, terá de 6,144,000 linhas por limite partição assumindo que o mesmo distribuição dos dados. Cada partição em cada distribuição independentemente deve exceder o limite de 102.400 linha para o inserir mínimas serão registados para a distribuição.

Carregar os dados numa tabela não vazios com um índice agrupado com frequência pode conter uma mistura de linhas totalmente com sessão iniciadas e mínimas com sessão iniciadas. Um índice agrupado é uma árvore desequilibrada (árvore de b) das páginas. Se a página a ser escritos já contém as linhas de outra transação, em seguida, estas escritas serão completamente registadas. No entanto, se a página estiver em branco, em seguida, escrever para essa página será mínimas registado.

## <a name="optimizing-deletes"></a>Otimizar os elimina

`DELETE`é uma operação totalmente com sessão iniciada.  Se precisar de eliminar uma grande quantidade de dados numa tabela ou uma partição, geralmente faz sentido mais `SELECT` os dados que pretende manter, podem ser executados como uma operação de mínimas com sessão iniciada.  Para realizar esta tarefa, crie uma nova tabela com [CTAS][].  Depois de criada, utilize a [Mudar o nome de][] trocar a sua tabela antiga com a tabela recentemente criada.

```sql
-- Delete all sales transactions for Promotions except PromotionKey 2.

--Step 01. Create a new table select only the records we want to kep (PromotionKey 2)
CREATE TABLE [dbo].[FactInternetSales_d]
WITH
(   CLUSTERED COLUMNSTORE INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
WHERE   [PromotionKey] = 2
OPTION (LABEL = 'CTAS : Delete')
;

--Step 02. Rename the Tables to replace the 
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_d] TO [FactInternetSales];
```

## <a name="optimizing-updates"></a>Otimizar as atualizações

`UPDATE`é uma operação totalmente com sessão iniciada.  Se precisar de atualizar um grande número de linhas numa tabela ou uma partição com frequência pode ser muito mais eficiente para utilizar uma operação mínimas com sessão iniciada como [CTAS][] para fazê-lo.

No exemplo abaixo de uma tabela completa foi convertida atualizar um `CTAS` para que seja possível mínimo registo.

Neste caso podemos está retrospectivamente a adicionar um montante de desconto para as vendas na tabela:

```sql
--Step 01. Create a new table containing the "Update". 
CREATE TABLE [dbo].[FactInternetSales_u]
WITH
(   CLUSTERED INDEX
,   DISTRIBUTION = HASH([ProductKey])
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20000101, 20010101, 20020101, 20030101, 20040101, 20050101
                                                ,   20060101, 20070101, 20080101, 20090101, 20100101, 20110101
                                                ,   20120101, 20130101, 20140101, 20150101, 20160101, 20170101
                                                ,   20180101, 20190101, 20200101, 20210101, 20220101, 20230101
                                                ,   20240101, 20250101, 20260101, 20270101, 20280101, 20290101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
OPTION (LABEL = 'CTAS : Update')
;

--Step 02. Rename the tables
RENAME OBJECT [dbo].[FactInternetSales]   TO [FactInternetSales_old];
RENAME OBJECT [dbo].[FactInternetSales_u] TO [FactInternetSales];

--Step 03. Drop the old table
DROP TABLE [dbo].[FactInternetSales_old]
```

> [AZURE.NOTE] Voltar a criar tabelas grandes beneficiar utilizar funcionalidades de gestão de carga de trabalho do armazém de dados SQL. Para obter mais detalhes, consulte a secção de gestão de carga de trabalho no artigo [simultaneidade][] .

## <a name="optimizing-with-partition-switching"></a>Otimizar com a mudança de partição

Quando são confrontadas com modificações grande escala dentro de uma [partição de tabela][], em seguida, uma partição mudar padrão faz com que muitas sentido. Se a modificação de dados é significativa e abrange múltiplas partições, em seguida, basta iteração as partições atinge o mesmo resultado.

Os passos para executar um parâmetro partição são da seguinte forma:
1. Criar um esvaziar saída partição
2. Executar a atualização como um CTAS
3. Mudar a saída dos dados existentes para a tabela out
4. Mudar dos novos dados
5. Limpar os dados

No entanto, para ajudar a identificar as partições para alternar pela primeira vez será precisamos de criar um procedimento helper, como a abaixo. 

```sql
CREATE PROCEDURE dbo.partition_data_get
    @schema_name           NVARCHAR(128)
,   @table_name            NVARCHAR(128)
,   @boundary_value        INT
AS
IF OBJECT_ID('tempdb..#ptn_data') IS NOT NULL
BEGIN
    DROP TABLE #ptn_data
END
CREATE TABLE #ptn_data
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
WITH CTE
AS
(
SELECT  s.name                          AS [schema_name]
,       t.name                          AS [table_name]
,       p.partition_number              AS [ptn_nmbr]
,       p.[rows]                        AS [ptn_rows]
,       CAST(r.[value] AS INT)          AS [boundary_value]
FROM        sys.schemas                 AS s
JOIN        sys.tables                  AS t    ON  s.[schema_id]       = t.[schema_id]
JOIN        sys.indexes                 AS i    ON  t.[object_id]       = i.[object_id]
JOIN        sys.partitions              AS p    ON  i.[object_id]       = p.[object_id] 
                                                AND i.[index_id]        = p.[index_id] 
JOIN        sys.partition_schemes       AS h    ON  i.[data_space_id]   = h.[data_space_id]
JOIN        sys.partition_functions     AS f    ON  h.[function_id]     = f.[function_id]
LEFT JOIN   sys.partition_range_values  AS r    ON  f.[function_id]     = r.[function_id] 
                                                AND r.[boundary_id]     = p.[partition_number]
WHERE i.[index_id] <= 1
)
SELECT  *
FROM    CTE
WHERE   [schema_name]       = @schema_name
AND     [table_name]        = @table_name
AND     [boundary_value]    = @boundary_value
OPTION (LABEL = 'dbo.partition_data_get : CTAS : #ptn_data')
;
GO
```

Este procedimento maximiza reutilização de código e mantém a partição de mudança de exemplo mais compacto.

O código abaixo demonstra os cinco passos mencionados acima para alcançar uma partição completa mudar rotina.

```sql
--Create a partitioned aligned empty table to switch out the data 
IF OBJECT_ID('[dbo].[FactInternetSales_out]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_out]
END

CREATE TABLE [dbo].[FactInternetSales_out]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS
SELECT *
FROM    [dbo].[FactInternetSales]
WHERE 1=2
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Create a partitioned aligned table and update the data in the select portion of the CTAS
IF OBJECT_ID('[dbo].[FactInternetSales_in]') IS NOT NULL
BEGIN
    DROP TABLE [dbo].[FactInternetSales_in]
END

CREATE TABLE [dbo].[FactInternetSales_in]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED COLUMNSTORE INDEX
,   PARTITION   (   [OrderDateKey] RANGE RIGHT 
                                    FOR VALUES  (   20020101, 20030101
                                                )
                )
)
AS 
SELECT
    [ProductKey]  
,   [OrderDateKey] 
,   [DueDateKey]  
,   [ShipDateKey] 
,   [CustomerKey] 
,   [PromotionKey] 
,   [CurrencyKey] 
,   [SalesTerritoryKey]
,   [SalesOrderNumber]
,   [SalesOrderLineNumber]
,   [RevisionNumber]
,   [OrderQuantity]
,   [UnitPrice]
,   [ExtendedAmount]
,   [UnitPriceDiscountPct]
,   ISNULL(CAST(5 as float),0) AS [DiscountAmount]
,   [ProductStandardCost]
,   [TotalProductCost]
,   ISNULL(CAST(CASE WHEN [SalesAmount] <=5 THEN 0
         ELSE [SalesAmount] - 5
         END AS MONEY),0) AS [SalesAmount]
,   [TaxAmt]
,   [Freight]
,   [CarrierTrackingNumber] 
,   [CustomerPONumber]
FROM    [dbo].[FactInternetSales]
WHERE   OrderDateKey BETWEEN 20020101 AND 20021231
OPTION (LABEL = 'CTAS : Partition Switch IN : UPDATE')
;

--Use the helper procedure to identify the partitions
--The source table
EXEC dbo.partition_data_get 'dbo','FactInternetSales',20030101
DECLARE @ptn_nmbr_src INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_src

--The "in" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_in',20030101
DECLARE @ptn_nmbr_in INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_in

--The "out" table
EXEC dbo.partition_data_get 'dbo','FactInternetSales_out',20030101
DECLARE @ptn_nmbr_out INT = (SELECT ptn_nmbr FROM #ptn_data)
SELECT @ptn_nmbr_out

--Switch the partitions over
DECLARE @SQL NVARCHAR(4000) = '
ALTER TABLE [dbo].[FactInternetSales]   SWITCH PARTITION '+CAST(@ptn_nmbr_src AS VARCHAR(20))   +' TO [dbo].[FactInternetSales_out] PARTITION ' +CAST(@ptn_nmbr_out AS VARCHAR(20))+';
ALTER TABLE [dbo].[FactInternetSales_in] SWITCH PARTITION '+CAST(@ptn_nmbr_in AS VARCHAR(20))   +' TO [dbo].[FactInternetSales] PARTITION '     +CAST(@ptn_nmbr_src AS VARCHAR(20))+';'
EXEC sp_executesql @SQL

--Perform the clean-up
TRUNCATE TABLE dbo.FactInternetSales_out;
TRUNCATE TABLE dbo.FactInternetSales_in;

DROP TABLE dbo.FactInternetSales_out
DROP TABLE dbo.FactInternetSales_in
DROP TABLE #ptn_data
```

## <a name="minimize-logging-with-small-batches"></a>Registo com pequenos lotes de minimizar

Para operações de modificação de dados grandes, poderá fazer sentido para dividir a operação em porções ou secções para organizar a unidade de trabalho.

Um exemplo é fornecido abaixo. O tamanho do lote foi definido para um número comum para realçar a técnica. Na realidade, o tamanho do lote seria significativamente maior. 

```sql
SET NO_COUNT ON;
IF OBJECT_ID('tempdb..#t') IS NOT NULL
BEGIN
    DROP TABLE #t;
    PRINT '#t dropped';
END

CREATE TABLE #t
WITH    (   DISTRIBUTION = ROUND_ROBIN
        ,   HEAP
        )
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS seq_nmbr
,       SalesOrderNumber
,       SalesOrderLineNumber
FROM    dbo.FactInternetSales
WHERE   [OrderDateKey] BETWEEN 20010101 and 20011231
;

DECLARE @seq_start      INT = 1
,       @batch_iterator INT = 1
,       @batch_size     INT = 50
,       @max_seq_nmbr   INT = (SELECT MAX(seq_nmbr) FROM dbo.#t)
;

DECLARE @batch_count    INT = (SELECT CEILING((@max_seq_nmbr*1.0)/@batch_size))
,       @seq_end        INT = @batch_size
;

SELECT COUNT(*)
FROM    dbo.FactInternetSales f

PRINT 'MAX_seq_nmbr '+CAST(@max_seq_nmbr AS VARCHAR(20))
PRINT 'MAX_Batch_count '+CAST(@batch_count AS VARCHAR(20))

WHILE   @batch_iterator <= @batch_count
BEGIN
    DELETE
    FROM    dbo.FactInternetSales
    WHERE EXISTS
    (
            SELECT  1
            FROM    #t t
            WHERE   seq_nmbr BETWEEN  @seq_start AND @seq_end
            AND     FactInternetSales.SalesOrderNumber      = t.SalesOrderNumber
            AND     FactInternetSales.SalesOrderLineNumber  = t.SalesOrderLineNumber
    )
    ;

    SET @seq_start = @seq_end
    SET @seq_end = (@seq_start+@batch_size);
    SET @batch_iterator +=1;
END
```

## <a name="pause-and-scaling-guidance"></a>Colocar em pausa e orientações de dimensionamento

Armazém de dados SQL Azure permite-lhe colocar em pausa, retomar e dimensionar o seu armazém de dados a pedido. Quando colocar em pausa ou dimensionar o seu armazém de dados SQL é importante compreender que quaisquer transações em voo são terminadas imediatamente; a causar qualquer transações abertas ser revertido. Se sua carga de trabalho tivesse emitido uma tempo em execução e incompletas modificações nos dados antes da operação de colocar em pausa ou escala, este trabalho terão de ser anuladas. Isto pode afetar o tempo que demora a colocar em pausa ou dimensionar a base de dados do armazém de dados do SQL Azure. 

> [AZURE.IMPORTANT] Ambos `UPDATE` e `DELETE` são operações totalmente registadas e para que estes Anular/refazer operações podem significativamente demorar mais tempo do que equivalente tem sessão iniciada mínimas operações. 

É o melhor cenário permitir que em transações de modificação de dados de voo concluídas antes de interromper ou dimensionamento armazém de dados SQL. No entanto, esta poderá não sempre ser prática. Para mitigar o risco de uma longa reversão, considere uma das seguintes opções:

- Voltar a escrever longa operações utilizando [CTAS][]
- Divide a operação em blocos; a funcionar com um subconjunto das linhas

## <a name="next-steps"></a>Próximos passos

Consulte o artigo [transações no armazém de dados do SQL][] para saber mais sobre níveis de isolamento e transaccionais limites.  Para obter uma descrição geral das outras melhores práticas, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Transações no armazém de dados SQL]: ./sql-data-warehouse-develop-transactions.md
[partição de tabela]: ./sql-data-warehouse-tables-partition.md
[Simultaneidade]: ./sql-data-warehouse-develop-concurrency.md
[CTAS]: ./sql-data-warehouse-develop-ctas.md
[Práticas recomendadas do armazém de dados SQL]: ./sql-data-warehouse-best-practices.md

<!--MSDN references-->
[alter index]:https://msdn.microsoft.com/library/ms188388.aspx
[MUDAR O NOME]: https://msdn.microsoft.com/library/mt631611.aspx

<!-- Other web references -->

