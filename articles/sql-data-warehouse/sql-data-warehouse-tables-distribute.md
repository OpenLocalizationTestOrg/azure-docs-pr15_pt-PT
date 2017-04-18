<properties
   pageTitle="Distribuir a tabelas no armazém de dados SQL | Microsoft Azure"
   description="Introdução ao distribuir a tabelas no armazém de dados do SQL Azure."
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
   ms.date="08/30/2016"
   ms.author="jrj;barbkess;sonyama"/>

# <a name="distributing-tables-in-sql-data-warehouse"></a>Distribuir a tabelas no armazém de dados SQL

> [AZURE.SELECTOR]
- [Descrição geral][]
- [Tipos de dados][]
- [Distribuir][]
- [Índice remissivo][]
- [Partição][]
- [Estatísticas][]
- [Temporária][]

Armazém de dados do SQL é que uma transformação previstos excederem consideravelmente paralela (MPP) distribuído sistema de base de dados.  Dividindo dados e a capacidade de processamento através de vários nós, armazém de dados SQL pode oferecer escalabilidade enorme - extremidade para além de qualquer sistema único.  Decidir como distribuir os seus dados no seu armazém de dados SQL é um dos fatores mais importantes para alcançar um desempenho ideal.   A tecla para um desempenho ideal é minimizar movimento de dados e sucessivamente a tecla para minimizar movimento de dados é selecionar a estratégia de distribuição à direita.

## <a name="understanding-data-movement"></a>Noções sobre movimento de dados

Num sistema MPP, os dados de cada tabela são divididos em várias bases de dados subjacentes.  As consultas mais otimizadas num sistema MPP simplesmente podem ser passadas através de executar nas bases de dados distribuídos individuais com sem interação entre outras bases de dados.  Por exemplo, digamos que tiver uma base de dados com dados de vendas que contém duas tabelas, vendas e clientes.  Se tiver uma consulta que necessita para aderir a sua tabela vendas à sua tabela de cliente e dividir das vendas e de tabelas de cliente o pelo número de cliente, colocar em cada cliente numa base de dados em separado, quaisquer consultas que participar vendas e suporte ao cliente podem ser resolvidas dentro de cada base de dados com sem dados de conhecimento das outras bases de dados.  Em contrapartida, se os dados de vendas dividido pelo número de encomenda e os dados de cliente por número de cliente, em seguida, quaisquer bases de dados determinado não terão os dados correspondentes de cada cliente e, consequentemente, se pretendia participar os dados de vendas aos dados do cliente, que precisa para obter os dados para cada cliente de outras bases de dados.  Neste exemplo segunda, seria necessário movimento de dados e apresentá-la para mover os dados do cliente para os dados das vendas, para que possam ser associadas duas tabelas.  

Movimentação de dados nem sempre é uma coisa incorretas, por vezes, é necessário resolver uma consulta.  Mas quando este passo adicional pode ser evitado, naturalmente a consulta é executada mais rápido.  Movimentação de dados com mais frequência surge quando são associadas tabelas ou agregações são executadas.  Muitas vezes que tem de fazer ambas, para que, enquanto poderá conseguir otimizar para um cenário, como uma associação, ainda precisa de movimento de dados para o ajudar a resolver para o cenário, como uma agregação.  O truque é a perceber quais são menos trabalho.  Na maioria dos casos, distribuição de tabelas de factos grandes numa coluna normalmente associadas é o método mais eficaz para reduzir a maioria dos circulação de dados.  Distribuir dados nas colunas de associação é um método muito mais comuns para reduzir o movimento de dados que distribuir dados nas colunas envolvidos numa agregação.

## <a name="select-distribution-method"></a>Selecione o método de distribuição

Nos bastidores, armazém de dados SQL divide os seus dados em 60 bases de dados.  Cada base de dados individual é referido como uma **distribuição**.  Quando são carregados dados para cada tabela, armazém de dados SQL tem de saber como dividir os seus dados através destes 60 distribuições.  

O método de distribuição é definido ao nível da tabela e atualmente existem duas opções:

1. **Arredondar robin** que distribuir dados uniformemente mas aleatoriamente.
2. **Hash distribuído** que distribui dados com base no hashing valores a partir de uma única coluna

Por predefinição, quando não define um método de distribuição de dados, tabela será distribuída utilizando o método de distribuição **round robin** .  No entanto, tal como torna-se mais sofisticadas na sua implementação, que irá pretende considere utilizar tabelas **hash distribuído** para minimizar movimento de dados que por sua vez será otimizar o desempenho da consulta.

### <a name="round-robin-tables"></a>Tabelas de Round Robin

Utilizar o método de Round Robin da distribuição de dados é muito como parece.  À medida que os seus dados for carregados, cada linha simplesmente é enviada para a distribuição seguinte.  Este método de distribuir os dados serão sempre aleatoriamente distribuir os dados muito uniformemente ao longo de todas as distribuições.  Isto é, não existe nenhuma ordenação feito durante o processo de round robin que coloca os dados.  Uma distribuição round robin é, por este motivo, por vezes denominada um hash aleatório.  Com uma tabela distribuída round robin não é necessário para compreender os dados.  Por este motivo, tabelas de Round Robin certifique frequentemente carregamento boa destinos.

Por predefinição, não se for escolhido nenhum método de distribuição, o método de distribuição round robin será utilizado.  No entanto, enquanto round robin tabelas são fáceis de utilizar, uma vez que os dados são distribuídos aleatoriamente através do sistema significa que o sistema não garante que distribuição cada linha é no.  Como resultado, o sistema, por vezes, necessita de invocar uma operação de movimento de dados para organizar melhor os seus dados antes de pode resolver uma consulta.  Este passo adicional pode atrasar as suas consultas.

Considere a utilização de distribuição Round Robin para a sua tabela nos cenários seguintes:

- Quando começar a trabalhar como ponto de partida simple
- Se não existe chave unir óbvio
- Se não existir coluna bom candidato para hash distribuir a tabela
- Se a tabela não partilha uma chave de associação comum com outras tabelas
- Se a associação é menos importante que outras associações na consulta
- Quando a tabela é uma tabela de transição temporária

Ambos estes exemplos irão criar uma tabela de Round Robin:

```SQL
-- Round Robin created by default
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
,   [OrderDateKey]          int          NOT NULL
,   [CustomerKey]           int          NOT NULL
,   [PromotionKey]          int          NOT NULL
,   [SalesOrderNumber]      nvarchar(20) NOT NULL
,   [OrderQuantity]         smallint     NOT NULL
,   [UnitPrice]             money        NOT NULL
,   [SalesAmount]           money        NOT NULL
)
;

-- Explicitly Created Round Robin Table
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,   DISTRIBUTION = ROUND_ROBIN
)
;
```

> [AZURE.NOTE] Enquanto o round robin é o tipo de tabela predefinido a ser explícitas no seu DDL considera-se prática recomendada para que as intenções seu ou esquema da tabela são limpar a outras pessoas.

### <a name="hash-distributed-tables"></a>Hash tabelas distribuídas

Utilizando um algoritmo **Hash distribuído** a distribuição das suas tabelas pode melhorar o desempenho para muitos cenários, reduzindo movimento de dados no momento da consulta.  Tabelas hash distribuído são tabelas que estão divididas entre as bases de dados distribuídos utilizando um algoritmo hashing numa única coluna que selecionar.  A coluna de distribuição é o que determina a forma como os dados são divididos entre as bases de dados distribuídos.  A função hash utiliza a coluna de distribuição para atribuir linhas para as distribuições.  O algoritmo hashing e distribuição resultante é determinista.  Que é o mesmo valor com o mesmo tipo de dados serão tem sempre para a distribuição mesmo.    

Este exemplo irá criar uma tabela distribuída no id:

```SQL
CREATE TABLE [dbo].[FactInternetSales]
(   [ProductKey]            int          NOT NULL
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
,  DISTRIBUTION = HASH([ProductKey])
)
;
```

## <a name="select-distribution-column"></a>Selecione a coluna de distribuição

Quando escolher **hash distribuir** uma tabela, terá de selecionar uma coluna única distribuição.  Quando seleciona uma coluna de distribuição, existem três factores principais a ter em conta.  

Selecione uma única coluna que será:

1. Não ser atualizados
2. Distribuir dados uniformemente, evitando dados distorção
3. Minimizar movimento de dados

### <a name="select-distribution-column-which-will-not-be-updated"></a>Selecione a coluna de distribuição que não será atualizada

Distribuição colunas não são atualizável, por conseguinte, selecione uma coluna com valores estáticos.  Se uma coluna terá de ser atualizados, geralmente não é um candidato a distribuição boa.  Se existir um caso onde tem atualizar uma coluna de distribuição, pode fazê-lo ao primeiro eliminar a linha e, em seguida, inserir uma nova linha.

### <a name="select-distribution-column-which-will-distribute-data-evenly"></a>Selecione a coluna de distribuição que será a distribuir uniformemente dados

Uma vez que um sistema distribuído efetua apenas tão rapidamente como se sua distribuição mais lenta, é importante dividir o trabalho uniformemente entre as distribuições para alcançar execução desequilibrada através do sistema.  A forma como o trabalho é dividido num sistema distribuído é baseada onde residem os dados para cada distribuição.  Isto torna muito importante para selecionar a coluna à direita da distribuição de forma a distribuir os dados para que cada distribuição tem trabalho igual e irá demorar mesmo tempo para concluir a sua parte do trabalho.  Quando trabalho também é dividido através do sistema, os dados são distribuídos pelas distribuições.  Quando os dados não são distribuídos uniformemente, chamamos este **prejudicar de dados**.  

Para dividir uniformemente os dados e evitar distorção de dados, considere o seguinte quando seleciona a sua coluna de distribuição:

1. Selecione uma coluna que contém um número de valores distintos significativo.
2. Evite a distribuição dados nas colunas com alguns valores distintos. 
3. Evite a distribuição dados nas colunas com uma frequência de alto de valores nulos.
4. Evite a distribuição dados nas colunas de data.

Uma vez que cada valor é atribuído a 1 de 60 distribuições, para alcançar distribuição par pretenderá selecionar uma coluna que é altamente exclusivo e contiver mais de 60 valores exclusivos.  Para ilustrar, considere a um caso onde uma coluna tem apenas valores exclusivos 40.  Se esta coluna foi seleccionada como a chave de distribuição, os dados dessa tabela seriam terra em 40 distribuições no máximo, deixando 20 distribuições com sem dados e não transformação fazer.  Por outro lado, as outras 40 distribuições teria mais trabalho para o fazer se os dados uniformemente foram distribuídos mais de 60 distribuições.  Este cenário é um exemplo de dados distorção.

Em sistema MPP, cada passo da consulta aguarda todas as distribuições concluir a sua partilha do trabalho.  Se uma distribuição está a fazer mais trabalho do que as outras pessoas, em seguida, o recurso dos outros distribuições são essencialmente desperdiçado apenas à espera da distribuição ocupada.  Quando o trabalho não é distribuir uniformemente por todas as distribuições, chamamos **distorção de processamento**.  Distorção de processamento causará consultas executar mais lenta do que se a carga de trabalho pode ser distribuir uniformemente pelas distribuições.  Distorção de dados, serão direcionado para a distorção de processamento.

Evite a distribuição coluna altamente anuláveis como os valores nulos serão direcionado sobre a mesma distribuição. Distribuir a uma coluna de data também pode causar distorção de processamento de uma vez que todos os dados para uma determinada data serão direcionado sobre a mesma distribuição. Se vários utilizadores estão a executar consultas de todas as filtragens de na mesma data, em seguida, vai ser efetuar apenas 1 das 60 distribuições todo o trabalho desde a uma determinada data apenas estará numa distribuição. Neste cenário, as consultas provável que serão executada 60 vezes mais lentos do que se os dados foram quatros escalonados todas as distribuições. 

Quando existem sem colunas bom candidato, em seguida, considere utilizar round robin como o método de distribuição.

### <a name="select-distribution-column-which-will-minimize-data-movement"></a>Selecione a coluna de distribuição que irá minimizar movimento de dados

Minimizar movimento de dados ao selecionar a coluna à direita da distribuição é um dos estratégias de mais importantes para otimizar o desempenho do seu armazém de dados SQL.  Movimentação de dados com mais frequência surge quando são associadas tabelas ou agregações são executadas.  Colunas utilizadas nos `JOIN`, `GROUP BY`, `DISTINCT`, `OVER` e `HAVING` cláusulas todos efetuar para **boa** hash candidatos de distribuição. 

Por outro lado, colunas a `WHERE` cláusula fazer **não** efetuar para os candidatos de coluna hash boa porque estes limitarem as distribuições participam na consulta, a causar o processamento prejudicar.  Um bom exemplo de uma coluna que pode ser tentador para distribuir no, mas muitas vezes, pode causar este processamento distorção é uma coluna de data.

Regra geral, se tiver duas tabelas de factos grandes frequentemente envolvidas numa associação, irá obter o desempenho a maior parte dos através da distribuição de ambas as tabelas das colunas de associação.  Se tiver uma tabela que nunca está associada a outra tabela de factos grandes, em seguida, procure a colunas que sejam encontra nas frequentemente a `GROUP BY` cláusula.

Existem alguns critérios chaves que devem ser reunidos para evitar movimento de dados durante uma associação:

1. As tabelas envolvidas na associação tem de ser hash distribuído por **uma** das colunas a participar na associação.
2. Os tipos de dados das colunas associação tem de corresponder entre ambas as tabelas.
3. As colunas devem estar associadas com um operador é igual a.
4. O tipo de associação pode não ser uma `CROSS JOIN`.


## <a name="troubleshooting-data-skew"></a>Resolução de problemas de dados distorção

Quando os dados da tabela são distribuídos utilizando o método de distribuição hash existe a oportunidade que vai ser distorcidas algumas distribuições ter dados desproporcionalmente mais do que as outras. Distorção de excesso de dados pode afetar o desempenho da consulta porque o resultado final de uma consulta distribuído tem de aguardar a distribuição maior em execução concluir. Consoante o grau da distorção de dados poderá ter de endereço-lo.

### <a name="identifying-skew"></a>Identificar distorção

Uma forma simples para identificar uma tabela, tal como distorcida é utilizar `DBCC PDW_SHOWSPACEUSED`.  Este é uma forma muito rápida e simple de ver o número de linhas de tabela que estão armazenados em cada uma das 60 distribuições da base de dados.  Lembre-se de que o desempenho mais desequilibradas as linhas na tabela distribuída devem sejam distribuídas uniformemente ao longo de todas as distribuições.

```sql
-- Find data skew for a distributed table
DBCC PDW_SHOWSPACEUSED('dbo.FactInternetSales');
```

No entanto, se consultar as vistas de gestão de dinâmica armazém de dados do SQL Azure (das DMV) pode executar uma análise mais detalhada.  Para começar, crie a vista de [dbo.vTableSizes][] vista utilizando o SQL a partir do artigo[Descrição geral] de [Descrição geral de tabela].  Assim que a vista é criada, executar esta consulta para identificar as tabelas tem mais de 10% dados distorção.

```sql
select *
from dbo.vTableSizes 
where two_part_name in 
    (
    select two_part_name
    from dbo.vTableSizes
    where row_count > 0
    group by two_part_name
    having min(row_count * 1.000)/max(row_count * 1.000) > .10
    )
order by two_part_name, row_count
;
```

### <a name="resolving-data-skew"></a>Resolver dados distorção

Não todas as distorção é suficiente para justificar um fix.  Em alguns casos, o desempenho de uma tabela em algumas consultas pode compensar danos de dados distorção.  Para decidir se deve resolver dados distorção numa tabela, deve compreender quanto possível sobre os volumes de dados e consultas na sua carga de trabalho.   Uma forma de ver o impacto das distorção é utilizar os passos no artigo [Monitorização de consulta][] para monitorizar o impacto das distorção no desempenho da consulta e especificamente o impacto às consultas quanto tempo demorar a ser concluída nas distribuições individuais.

Distribuição de dados é uma questão de localizar o equilíbrio direita entre minimizar a distorção de dados e minimizar movimento de dados. Estes podem ser opostas objetivos e, por vezes, irá querer manter os dados distorção para reduzir movimento de dados. Por exemplo, quando a coluna de distribuição é frequentemente a coluna partilhada no associações e agregações, será ser minimizar movimento de dados. A vantagem de ter o movimento de dados mínimas poderá compensar o impacto de ter dados prejudicar. 

A forma típica de resolver dados distorção é criar a tabela com uma coluna de distribuição diferente. Uma vez que não existe nenhuma forma para alterar a distribuição de coluna numa tabela existente, a forma de alterar a distribuição de uma tabela para o recriar com um [[CTAS]].  Aqui estão dois exemplos de como resolver dados distorção:

### <a name="example-1-re-create-the-table-with-a-new-distribution-column"></a>Exemplo 1: Criar a tabela com uma nova coluna de distribuição

Este exemplo utiliza [[CTAS]] para criar uma tabela com uma coluna de distribuição hash diferente. 

```sql
CREATE TABLE [dbo].[FactInternetSales_CustomerKey] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  HASH([CustomerKey])
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_CustomerKey')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_CustomerKey] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_CustomerKey] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_CustomerKey] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_CustomerKey] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_CustomerKey] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_CustomerKey] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_CustomerKey] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_CustomerKey] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_ProductKey];
RENAME OBJECT [dbo].[FactInternetSales_CustomerKey] TO [FactInternetSales];
```

### <a name="example-2-re-create-the-table-using-round-robin-distribution"></a>Exemplo 2: Recrie a tabela utilizando a distribuição round robin

Este exemplo utiliza [[CTAS]] para criar uma tabela com round robin em vez de uma distribuição hash. Esta alteração vai produzir mesmo distribuição dos dados pelo valor movimento de dados maior. 

```sql
CREATE TABLE [dbo].[FactInternetSales_ROUND_ROBIN] 
WITH (  CLUSTERED COLUMNSTORE INDEX
     ,  DISTRIBUTION =  ROUND_ROBIN
     ,  PARTITION       ( [OrderDateKey] RANGE RIGHT FOR VALUES (   20000101, 20010101, 20020101, 20030101
                                                                ,   20040101, 20050101, 20060101, 20070101
                                                                ,   20080101, 20090101, 20100101, 20110101
                                                                ,   20120101, 20130101, 20140101, 20150101
                                                                ,   20160101, 20170101, 20180101, 20190101
                                                                ,   20200101, 20210101, 20220101, 20230101
                                                                ,   20240101, 20250101, 20260101, 20270101
                                                                ,   20280101, 20290101
                                                                )
                        )
    )
AS
SELECT  *
FROM    [dbo].[FactInternetSales]
OPTION  (LABEL  = 'CTAS : FactInternetSales_ROUND_ROBIN')
;

--Create statistics on new table
CREATE STATISTICS [ProductKey] ON [FactInternetSales_ROUND_ROBIN] ([ProductKey]);
CREATE STATISTICS [OrderDateKey] ON [FactInternetSales_ROUND_ROBIN] ([OrderDateKey]);
CREATE STATISTICS [CustomerKey] ON [FactInternetSales_ROUND_ROBIN] ([CustomerKey]);
CREATE STATISTICS [PromotionKey] ON [FactInternetSales_ROUND_ROBIN] ([PromotionKey]);
CREATE STATISTICS [SalesOrderNumber] ON [FactInternetSales_ROUND_ROBIN] ([SalesOrderNumber]);
CREATE STATISTICS [OrderQuantity] ON [FactInternetSales_ROUND_ROBIN] ([OrderQuantity]);
CREATE STATISTICS [UnitPrice] ON [FactInternetSales_ROUND_ROBIN] ([UnitPrice]);
CREATE STATISTICS [SalesAmount] ON [FactInternetSales_ROUND_ROBIN] ([SalesAmount]);

--Rename the tables
RENAME OBJECT [dbo].[FactInternetSales] TO [FactInternetSales_HASH];
RENAME OBJECT [dbo].[FactInternetSales_ROUND_ROBIN] TO [FactInternetSales];
```

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre a estrutura da tabela, consulte o artigo [distribuir][], [índice][], [partição][], [Tipos de dados][], [Estatísticas][] e [Tabelas temporárias][temporário] artigos.

Para obter uma descrição geral das melhores práticas, consulte o artigo [Práticas recomendadas do armazém de dados SQL][].


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
[Monitorização de consulta]: ./sql-data-warehouse-manage-monitor.md
[dbo.vTableSizes]: ./sql-data-warehouse-tables-overview.md#querying-table-sizes

<!--MSDN references-->
[DBCC PDW_SHOWSPACEUSED()]: https://msdn.microsoft.com/library/mt204028.aspx

<!--Other Web references-->
