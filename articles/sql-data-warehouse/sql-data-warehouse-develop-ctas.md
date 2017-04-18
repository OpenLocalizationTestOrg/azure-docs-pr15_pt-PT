<properties
   pageTitle="Criar tabela, selecione (CTAS) no armazém de dados SQL | Microsoft Azure"
   description="Sugestões para codificação com a tabela de criar como instrução select de (CTAS) no armazém de dados do SQL Azure para desenvolver soluções."
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
   ms.date="06/14/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="create-table-as-select-ctas-in-sql-data-warehouse"></a>Criar tabela como seleccione (CTAS) no armazém de dados SQL
Criar tabela como seleccione ou `CTAS` é uma das funcionalidades mais importantes T-SQL disponíveis. É uma operação totalmente parallelized que cria uma nova tabela com base no resultado de uma instrução SELECT. `CTAS`é a forma mais rápida e mais simples para criar uma cópia de uma tabela. Pode considerar que seja uma versão sobrecarregada do `SELECT..INTO` se quiser. Este artigo fornece exemplos e práticas recomendadas para `CTAS`.

## <a name="using-ctas-to-copy-a-table"></a>Utilizar CTAS para copiar uma tabela

Talvez utiliza um dos mais comuns de `CTAS` está a criar uma cópia de uma tabela para que possam alterar a DDL. Se por exemplo criado originalmente tabela como `ROUND_ROBIN` e agora pretende alterá-lo a uma tabela distribuída numa coluna, `CTAS` é como quiser alterar a coluna de distribuição. `CTAS`Também pode ser utilizado para alterar os tipos de criação de partições, indexação ou coluna.

Digamos que o criou nesta tabela utilizando o tipo de distribuição predefinido `ROUND_ROBIN` distribuído uma vez que nenhuma coluna de distribuição que foi especificada na `CREATE TABLE`.

```sql
CREATE TABLE FactInternetSales
(
    ProductKey int NOT NULL,
    OrderDateKey int NOT NULL,
    DueDateKey int NOT NULL,
    ShipDateKey int NOT NULL,
    CustomerKey int NOT NULL,
    PromotionKey int NOT NULL,
    CurrencyKey int NOT NULL,
    SalesTerritoryKey int NOT NULL,
    SalesOrderNumber nvarchar(20) NOT NULL,
    SalesOrderLineNumber tinyint NOT NULL,
    RevisionNumber tinyint NOT NULL,
    OrderQuantity smallint NOT NULL,
    UnitPrice money NOT NULL,
    ExtendedAmount money NOT NULL,
    UnitPriceDiscountPct float NOT NULL,
    DiscountAmount float NOT NULL,
    ProductStandardCost money NOT NULL,
    TotalProductCost money NOT NULL,
    SalesAmount money NOT NULL,
    TaxAmt money NOT NULL,
    Freight money NOT NULL,
    CarrierTrackingNumber nvarchar(25),
    CustomerPONumber nvarchar(25)
);
```

Agora que pretende criar uma nova cópia desta tabela com um índice de Columnstore agrupado para que pode tirar partido do desempenho dos agrupadas Columnstore tabelas. Também pretende distribuir esta tabela no ProductKey uma vez que estão a previsão das associações esta coluna e pretende evitar movimento de dados durante associações no ProductKey. Por fim também pretende adicionar a partições no OrderDateKey para que pode eliminar rapidamente dados antigos largando a partições antigas. Eis a instrução de CTAS que iria copie a tabela antiga para uma nova tabela.

```sql
CREATE TABLE FactInternetSales_new
WITH
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = HASH(ProductKey),
    PARTITION
    (
        OrderDateKey RANGE RIGHT FOR VALUES
        (
        20000101,20010101,20020101,20030101,20040101,20050101,20060101,20070101,20080101,20090101,
        20100101,20110101,20120101,20130101,20140101,20150101,20160101,20170101,20180101,20190101,
        20200101,20210101,20220101,20230101,20240101,20250101,20260101,20270101,20280101,20290101
        )
    )
)
AS SELECT * FROM FactInternetSales;
```

Por fim pode mudar o nome a suas tabelas para trocar na sua nova tabela e, em seguida, largue tabela antiga.

```sql
RENAME OBJECT FactInternetSales TO FactInternetSales_old;
RENAME OBJECT FactInternetSales_new TO FactInternetSales;

DROP TABLE FactInternetSales_old;
```

> [AZURE.NOTE] Armazém de dados SQL Azure é que ainda não suporte automática criar ou actualizar as estatísticas de automaticamente.  Para obter o melhor desempenho de consultas, é importante que estatísticas de ser criada em todas as colunas de todas as tabelas após a primeira carregar ou quaisquer alterações substanciais ocorrerem dos dados.  Para obter uma explicação detalhada das estatísticas, consulte o tópico de [Estatísticas][] no grupo desenvolver de tópicos.

## <a name="using-ctas-to-work-around-unsupported-features"></a>Utilizar CTAS para trabalhar em redor de funcionalidades não suportadas

`CTAS`Pode também ser utilizados para resolver um número de funcionalidades não suportadas listados abaixo. Isto pode frequentemente provar ao ser uma situação win/win como não só será o seu código estar em conformidade mas -lo com frequência executará mais rápido no armazém de dados SQL. Este é estatístico como resultado da sua estrutura totalmente parallelized. Cenários que podem ser trabalhou à volta com CTAS incluem:

- SELECIONE... PARA
- ASSOCIAÇÕES ANSI atualizações
- Associações ANSI no elimina
- Declaração de impressão em série

> [AZURE.NOTE] Experimente a ter em conta "CTAS primeira". Se achar que pode resolver um problema utilizando `CTAS` , que é normalmente, a melhor forma de abordagem-- mesmo se estiver a escrever mais dados como resultado.
>

## <a name="selectinto"></a>SELECIONE... PARA
Poderá encontrar `SELECT..INTO` é apresentado um número de casas na sua solução.

Abaixo está um exemplo de um `SELECT..INTO` declaração:

```sql
SELECT *
INTO    #tmp_fct
FROM    [dbo].[FactInternetSales]
```

Para converter acima para `CTAS` é bastante simples:

```sql
CREATE TABLE #tmp_fct
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
;
```

> [AZURE.NOTE] CTAS atualmente requer que uma coluna de distribuição seja especificado.  Se não estiver intencionalmente a tentar alterar a coluna de distribuição, seu `CTAS` irá efetuar mais rápido se selecionar uma coluna de distribuição que é igual a tabela subjacente como esta estratégia evita movimento de dados.  Se estiver a criar uma pequena tabela onde desempenho não é um fator, em seguida, pode especificar `ROUND_ROBIN` para evitar ter de decidir numa coluna de distribuição.

## <a name="ansi-join-replacement-for-update-statements"></a>Substituição de associação ANSI para declarações de actualização

Poderá constatar que tem uma atualização de complexa associa mais de duas tabelas em conjunto com ANSI participar sintaxe para executar a ATUALIZAÇÃO ou eliminar.

Imagine que tinha que atualizar a tabela seguinte:

```sql
CREATE TABLE [dbo].[AnnualCategorySales]
(   [EnglishProductCategoryName]    NVARCHAR(50)    NOT NULL
,   [CalendarYear]                  SMALLINT        NOT NULL
,   [TotalSalesAmount]              MONEY           NOT NULL
)
WITH
(
    DISTRIBUTION = ROUND_ROBIN
)
;
```

A consulta original poderá observou algo parecido com:

```sql
UPDATE  acs
SET     [TotalSalesAmount] = [fis].[TotalSalesAmount]
FROM    [dbo].[AnnualCategorySales]     AS acs
JOIN    (
        SELECT  [EnglishProductCategoryName]
        ,       [CalendarYear]
        ,       SUM([SalesAmount])              AS [TotalSalesAmount]
        FROM    [dbo].[FactInternetSales]       AS s
        JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
        JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
        JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
        JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
        WHERE   [CalendarYear] = 2004
        GROUP BY
                [EnglishProductCategoryName]
        ,       [CalendarYear]
        ) AS fis
ON  [acs].[EnglishProductCategoryName]  = [fis].[EnglishProductCategoryName]
AND [acs].[CalendarYear]                = [fis].[CalendarYear]
;
```

Uma vez que SQL Data Warehouse não suporta ANSI associações na `FROM` cláusula de um `UPDATE` instrução, não é possível copiar este código sobre sem alterar-ligeiramente.

Pode utilizar uma combinação de um `CTAS` e uma associação implícita para substituir este código:

```sql
-- Create an interim table
CREATE TABLE CTAS_acs
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT  ISNULL(CAST([EnglishProductCategoryName] AS NVARCHAR(50)),0)    AS [EnglishProductCategoryName]
,       ISNULL(CAST([CalendarYear] AS SMALLINT),0)                      AS [CalendarYear]
,       ISNULL(CAST(SUM([SalesAmount]) AS MONEY),0)                     AS [TotalSalesAmount]
FROM    [dbo].[FactInternetSales]       AS s
JOIN    [dbo].[DimDate]                 AS d    ON s.[OrderDateKey]             = d.[DateKey]
JOIN    [dbo].[DimProduct]              AS p    ON s.[ProductKey]               = p.[ProductKey]
JOIN    [dbo].[DimProductSubCategory]   AS u    ON p.[ProductSubcategoryKey]    = u.[ProductSubcategoryKey]
JOIN    [dbo].[DimProductCategory]      AS c    ON u.[ProductCategoryKey]       = c.[ProductCategoryKey]
WHERE   [CalendarYear] = 2004
GROUP BY
        [EnglishProductCategoryName]
,       [CalendarYear]
;

-- Use an implicit join to perform the update
UPDATE  AnnualCategorySales
SET     AnnualCategorySales.TotalSalesAmount = CTAS_ACS.TotalSalesAmount
FROM    CTAS_acs
WHERE   CTAS_acs.[EnglishProductCategoryName] = AnnualCategorySales.[EnglishProductCategoryName]
AND     CTAS_acs.[CalendarYear]               = AnnualCategorySales.[CalendarYear]
;

--Drop the interim table
DROP TABLE CTAS_acs
;
```

## <a name="ansi-join-replacement-for-delete-statements"></a>Substituição de associação ANSI para eliminar declarações
Por vezes, a melhor abordagem para eliminar dados é utilizar `CTAS`. Em vez de eliminar os dados simplesmente selecione os dados que pretende manter. Este especialmente verdadeiro para `DELETE` instruções que utilizam ansi unir sintaxe de uma vez que SQL Data Warehouse não suporta ANSI associações na `FROM` cláusula de um `DELETE` declaração.

Um exemplo de uma instrução DELETE convertido está disponível abaixo:

```sql
CREATE TABLE dbo.DimProduct_upsert
WITH
(   Distribution=HASH(ProductKey)
,   CLUSTERED INDEX (ProductKey)
)
AS -- Select Data you wish to keep
SELECT     p.ProductKey
,          p.EnglishProductName
,          p.Color
FROM       dbo.DimProduct p
RIGHT JOIN dbo.stg_DimProduct s
ON         p.ProductKey = s.ProductKey
;

RENAME OBJECT dbo.DimProduct        TO DimProduct_old;
RENAME OBJECT dbo.DimProduct_upsert TO DimProduct;
```

## <a name="replace-merge-statements"></a>Substituir declarações de impressão em série
Declarações de impressão em série podem ser substituídas, pelo menos na peça, utilizando `CTAS`. Também pode consolidar os `INSERT` e o `UPDATE` para uma única instrução. Qualquer registo eliminado seria necessário ser fechado desativar numa instrução segunda.

Um exemplo de um `UPSERT` está disponível abaixo:

```sql
CREATE TABLE dbo.[DimProduct_upsert]
WITH
(   DISTRIBUTION = HASH([ProductKey])
,   CLUSTERED INDEX ([ProductKey])
)
AS
-- New rows and new versions of rows
SELECT      s.[ProductKey]
,           s.[EnglishProductName]
,           s.[Color]
FROM      dbo.[stg_DimProduct] AS s
UNION ALL  
-- Keep rows that are not being touched
SELECT      p.[ProductKey]
,           p.[EnglishProductName]
,           p.[Color]
FROM      dbo.[DimProduct] AS p
WHERE NOT EXISTS
(   SELECT  *
    FROM    [dbo].[stg_DimProduct] s
    WHERE   s.[ProductKey] = p.[ProductKey]
)
;

RENAME OBJECT dbo.[DimProduct]          TO [DimProduct_old];
RENAME OBJECT dbo.[DimpProduct_upsert]  TO [DimProduct];

```

## <a name="ctas-recommendation-explicitly-state-data-type-and-nullability-of-output"></a>Recomendação CTAS: mencionar explicitamente o tipo de dados e a condição de nulidade de saída

Quando estiver a migrar código pode achar que se deparar com este tipo de padrão de codificação:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE result
(result DECIMAL(7,2) NOT NULL
)
WITH (DISTRIBUTION = ROUND_ROBIN)

INSERT INTO result
SELECT @d*@f
;
```

Instintivamente poderá pensar deverá migrar este código para um CTAS e seria correto. No entanto, existe um problema oculto aqui.

O código seguinte produz o mesmo resultado:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455
;

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT @d*@f as result
;
```

Repare que a coluna "resultado" executa reencaminhar os valores de condição de nulidade e tipo de dados da expressão. Isto pode levar a ténue variâncias nos valores se não tiver cuidado.

Experimente o seguinte de exemplo:

```sql
SELECT result,result*@d
from result
;

SELECT result,result*@d
from ctas_r
;
```

O valor armazenado para o resultado é diferente. Como é utilizado o valor persistente na coluna resultado em outras expressões o erro torna-se ainda mais significativo.

![][1]

Isto é particularmente importante para migrações de dados. Apesar da segunda consulta é pode mais precisa existe um problema. Os dados seriam diferentes em comparação com o sistema de origem e que oportunidades potenciais para as questões de integridade na migração. Este é um dos casos raros onde a resposta "mal" é realmente a correta!

O motivo que vemos este disparidade entre os dois resultados é para baixo até moldagem implícito tipo. No exemplo primeiro a tabela que define a definição da coluna. Quando a linha é inserida uma conversão implícita ocorre. No segundo exemplo não existe nenhuma conversão implícita tal como a expressão define o tipo de dados da coluna. Tenha também em atenção que a coluna no segundo exemplo tenha sido definida como uma coluna anulável, Considerando que no exemplo primeiro não tenha. Quando a tabela que foi criada a condição de nulidade de coluna de exemplo primeira explicitamente foi definida. No segundo exemplo-foi apenas para a esquerda para a expressão e por predefinição esta resultaria uma definição de nulo.  

Para resolver estes problemas tem explicitamente de definir a conversão e a condição de nulidade na `SELECT` parte da `CTAS` declaração. Não é possível definir estas propriedades na parte da tabela de criar.

O exemplo abaixo demonstra como corrigir o código:

```sql
DECLARE @d decimal(7,2) = 85.455
,       @f float(24)    = 85.455

CREATE TABLE ctas_r
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
SELECT ISNULL(CAST(@d*@f AS DECIMAL(7,2)),0) as result
```

Tenha em atenção o seguinte procedimento:
- CAST ou converter em poderia ter sido utilizada
- ÉNULO é utilizado para forçar a condição de nulidade não adesão
- ÉNULO é a função mais exterior
- A segunda parte da ÉNULO é uma constante, ou seja, 0

> [AZURE.NOTE] A condição de nulidade ser corretamente defini-lo é crucial para utilizar `ISNULL` e não `COALESCE`. `COALESCE`Não é uma função determinista e por isso, o resultado da expressão será sempre NULLable. `ISNULL`é diferente. É determinista. Por conseguinte, quando a segunda parte da `ISNULL` função é uma constante ou um literal, em seguida, o valor resultante será não nulo.

Esta sugestão não é apenas útil para assegurar a integridade dos seus cálculos. Também é importante para alternar entre partição de tabela. Imagine que tem esta tabela definida como a sua facto:

```sql
CREATE TABLE [dbo].[Sales]
(
    [date]      INT     NOT NULL
,   [product]   INT     NOT NULL
,   [store]     INT     NOT NULL
,   [quantity]  INT     NOT NULL
,   [price]     MONEY   NOT NULL
,   [amount]    MONEY   NOT NULL
)
WITH
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101,20020101
                    ,20030101,20040101,20050101
                    )
                )
)
;
```

No entanto, no campo de valor é uma expressão calculada não faz parte dos dados de origem.

Para criar o seu conjunto de dados com partições que poderá querer fazer isto:

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   [quantity]*[price]  AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create')
;
```

A consulta seria executado perfeitamente fina. O problema é recebida quando tenta executar o parâmetro partição. As definições de tabela não correspondem. Para tornar as definições de tabela corresponder a CTAS necessita ser alterada.

```sql
CREATE TABLE [dbo].[Sales_in]
WITH    
(   DISTRIBUTION = HASH([product])
,   PARTITION   (   [date] RANGE RIGHT FOR VALUES
                    (20000101,20010101
                    )
                )
)
AS
SELECT
    [date]    
,   [product]
,   [store]
,   [quantity]
,   [price]   
,   ISNULL(CAST([quantity]*[price] AS MONEY),0) AS [amount]
FROM [stg].[source]
OPTION (LABEL = 'CTAS : Partition IN table : Create');
```

Pode ver, por conseguinte, tipo consistência e manutenção de propriedades de condição de nulidade num CTAS é aconselhável melhor engenharia. -Ajuda para manter a integridade dos seus cálculos e também garante que é possível mudar a partições.

Consulte MSDN para obter mais informações sobre como utilizar [CTAS][]. É uma das instruções mais importantes no armazém de dados do SQL Azure. Certifique-se de que cuidadosamente compreende.

## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-ctas/ctas-results.png

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[CTAS]: https://msdn.microsoft.com/library/mt204041.aspx

<!--Other Web references-->
