<properties
   pageTitle="Agrupar por opções no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para implementar o grupo pelas opções no armazém de dados do SQL Azure para desenvolver soluções."
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

# <a name="group-by-options-in-sql-data-warehouse"></a>Agrupar por opções no armazém de dados SQL

Cláusula [GROUP BY][] é utilizada para agregar dados a um conjunto de linhas de resumo. Também não tem algumas opções expandem da-funcionalidade que precisam de ser trabalhou à volta à medida que não são suportadas diretamente ao armazém de dados do SQL Azure.

Estas opções estiverem
- Agrupar por com ROLLUP
- CONJUNTOS DE AGRUPAMENTO
- Agrupar por com cubo

## <a name="rollup-and-grouping-sets-options"></a>Opções de conjuntos de rollup e agrupamento
Aqui a opção mais simples é utilizar `UNION ALL` em vez disso, para executar o rollup em vez de depender de sintaxe explícito. O resultado é exatamente o mesmo

Abaixo está um exemplo de um grupo declaração utilizando o `ROLLUP` opção:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Ao utilizar ROLLUP podemos já pediram as agregações seguintes:
- País e região
- País/região
- Total geral

Para substituir isto é necessário utilizar `UNION ALL`; especificar as agregações obrigatório explicitamente para devolver os mesmos resultados:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Para conjuntos de agrupamento todos os precisamos de fazer é adotar o capital mesmo, mas apenas criar secções UNION ALL para os níveis de agregação que pretendemos ver

## <a name="cube-options"></a>Opções do cubo
É possível criar um grupo com cubo utilizando a abordagem UNION ALL. O problema é que o código pode rapidamente transformar-se difícil e complicado. Para mitigar Isto pode utilizar esta mais avançada abordagem.

Vamos utilizar o exemplo acima.

O primeiro passo é definir 'cubo' que define a todos os níveis de agregação queremos para criar. É importante para tome nota da associação cruzada das duas tabelas derivadas. Isto gera todos os níveis para-nos. O resto do código existe realmente para a formatação.

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

Os resultados do CTAS podem ser vistos abaixo:

![][1]

É o segundo passo especificar uma tabela de destino para armazenar provisórios resultados:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

O terceiro passo é Repetir continuamente ao longo do nosso cubo de colunas efetuar a agregação. A consulta vai executar uma vez por cada linha na tabela temporária #Cube e armazenar os resultados na tabela temp #Results

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Por fim podemos devolvem os resultados ao ler o artigo simplesmente a partir da tabela temporária #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Por força o código para cima em secções e uma construção loop a gerar o código torna-se mais fácil e fáceis de manter.


## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->
[1]: media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[AGRUPAR POR]: https://msdn.microsoft.com/library/ms177673.aspx


<!--Other Web references-->
