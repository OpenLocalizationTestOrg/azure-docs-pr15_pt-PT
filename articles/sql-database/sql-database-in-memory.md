<properties
    pageTitle="SQL na memória, começar | Microsoft Azure"
    description="Tecnologias de SQL na memória melhorar significativamente o desempenho da transaccionais e das cargas de trabalho de análise. Saiba como tirar partido destas tecnologias."
    services="sql-database"
    documentationCenter=""
    authors="jodebrui"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="jodebrui"/>


# <a name="get-started-with-in-memory-preview-in-sql-database"></a>Introdução na memória (pré-visualização) na base de dados SQL

Funcionalidades de na memória melhorar significativamente o desempenho da transaccionais e das cargas de trabalho de análise nas situações à direita.

Este tópico realça ao duas vivo, uma para OLTP na memória e outra para análise na memória. Cada demonstração vem completa com os passos e de que precisa para executar a demonstração. Pode:

- Utilizar o código para testar os variações para ver as diferenças nos resultados de desempenho; ou
- Leia o código para compreender o cenário e para ver como criar e utilizar os objetos de memória.

> [AZURE.VIDEO azure-sql-database-in-memory-technologies]

- [1 começar rápida: na memória OLTP tecnologias mais rápido T-SQL desempenho](http://msdn.microsoft.com/library/mt694156.aspx) -é outro artigo para o ajudar a começar a utilizar.

#### <a name="in-memory-oltp"></a>OLTP na memória

As funcionalidades na memória [OLTP](#install_oltp_manuallink) (processamento de transações online) são:

- Tabelas de memória otimizado.
- Vierem compilado procedimentos armazenados.


Uma tabela de memória otimizado tem uma representação de si próprio na memória ativa, para além da representação padrão numa unidade de disco rígida. As transações de empresas contra a tabela executadas mais rapidamente uma vez que estes interagem diretamente com apenas a representação que está na memória ativa.

Com OLTP na memória, é possível alcançar o vezes 30 obtenha nas débito da transação, consoante as especificidades da carga de trabalho.


Procedimentos armazenados vierem compilados necessitam de menos máquina instruções durante o tempo de execução de interpretada tradicional procedimentos armazenados. Vamos viu o resultado da compilação nativo durações que estão entre 1/100 da duração interpretada.


#### <a name="in-memory-analytics"></a>Análise na memória 

A funcionalidade de na memória [Analytics](#install_analytics_manuallink) é:

Índices de Columnstore melhorar o desempenho do relatório de consultas e de análise. 


#### <a name="real-time-analytics"></a>Análise em tempo real

Para [Análise em tempo real](http://msdn.microsoft.com/library/dn817827.aspx) combinar OLTP na memória e análises para obter:

- Conhecimentos aprofundados de negócio em tempo real com base em dados operacionais.


#### <a name="availability"></a>Disponibilidade


Das versões DG, disponibilidade geral:

- [Índices de Columnstore](http://msdn.microsoft.com/library/dn817827.aspx) que estão *no disco*.


Pré-visualização:

- OLTP na memória
- Análise operacional em tempo real


Considerações enquanto as funcionalidades de memória estão na pré-visualização são descritos [mais abaixo neste tópico](#preview_considerations_for_in_memory).


> [AZURE.NOTE] Estas funcionalidades de pré-visualização estão apenas disponíveis para [*Premium*](sql-database-service-tiers.md) bases de dados não flexível conjuntos de dados e não está disponível para qualquer Basic ou padrão bases de dados.  Suporte para bases de dados de Premium no agrupamentos de flexível é brevemente. 


<a id="install_oltp_manuallink" name="install_oltp_manuallink"></a>

&nbsp;

## <a name="a-install-the-in-memory-oltp-sample"></a>RESPOSTAS. Instalar o exemplo de memória OLTP

Pode criar a base de dados de exemplo AdventureWorksLT [V12] por alguns cliques no [portal do Azure](https://portal.azure.com/). Em seguida, os passos nesta secção explicam como pode enriquecer a base de dados AdventureWorksLT com:

- Tabelas na memória.
- Um procedimento armazenado vierem compilado.


#### <a name="installation-steps"></a>Passos de instalação

1. No [portal do Azure](https://portal.azure.com/), crie uma base de dados Premium num servidor V12. Defina a **origem** à base de dados de exemplo AdventureWorksLT [V12].
 - Para obter instruções detalhadas, pode ver a [sua primeira base de dados do Azure SQL de criar](sql-database-get-started.md).

2. Ligar à base de dados com o SQL Server Management Studio [(SSMS.exe)](http://msdn.microsoft.com/library/mt238290.aspx).

3. Copie o [script na memória OLTP Transact-SQL](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_oltp_sample.sql) para sua área de transferência.
 - O script de T-SQL cria os objetos necessárias na memória da base de dados de exemplo AdventureWorksLT que criou no passo 1.

4. Colar o script T-SQL na SSMS e, em seguida, execute o script.
 - É crucial a `MEMORY_OPTIMIZED = ON` declarações de criar tabela cláusula, tal como em:


```
CREATE TABLE [SalesLT].[SalesOrderHeader_inmem](
    [SalesOrderID] int IDENTITY NOT NULL PRIMARY KEY NONCLUSTERED ...,
    ...
) WITH (MEMORY_OPTIMIZED = ON);
```


#### <a name="error-40536"></a>Erro 40536


Se obtiver um erro 40536 ao executar o script T-SQL, execute o seguinte script de T-SQL para verificar se a base de dados suporta na memória:


```
SELECT DatabasePropertyEx(DB_Name(), 'IsXTPSupported');
```


Um resultado de **0** significa na memória não é suportada e 1 significa que é suportado. Para diagnosticar o problema:

- Certifique-se de que a base de dados foi criada depois das funcionalidades de memória OLTP ficaram ativas para a pré-visualização.
- Certifique-se de que a base de dados é na camada de serviço Premium.


#### <a name="about-the-created-memory-optimized-items"></a>Acerca dos produtos de memória otimizado criados

**Tabelas**: A amostra contém as tabelas de memória otimizado seguintes:

- SalesLT.Product_inmem
- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem
- Demo.DemoSalesOrderHeaderSeed
- Demo.DemoSalesOrderDetailSeed


Pode inspecionar tabelas de memória otimizado através do **Explorador de objectos** no SSMS por:

- Botão direito do rato **tabelas** > **filtro** > **As definições de filtro** > **Está optimizada memória** é igual a 1.


Ou pode consultar as vistas de catálogo tais como:


```
SELECT is_memory_optimized, name, type_desc, durability_desc
    FROM sys.tables
    WHERE is_memory_optimized = 1;
```


**Procedimento armazenado vierem compilado**: SalesLT.usp_InsertSalesOrder_inmem pode que seja inspecionado através de uma consulta de vista do catálogo:


```
SELECT uses_native_compilation, OBJECT_NAME(object_id), definition
    FROM sys.sql_modules
    WHERE uses_native_compilation = 1;
```


&nbsp;

## <a name="run-the-sample-oltp-workload"></a>Executar a carga de trabalho OLTP de exemplo

A única diferença entre as duas que se segue *procedimentos armazenados* é que o primeiro procedimento utiliza versões de memória otimizado as tabelas, enquanto a segunda procedimento utiliza as tabelas no disco normais:

- SalesLT**.** usp_InsertSalesOrder**_inmem**
- SalesLT**.** usp_InsertSalesOrder**_ondisk**


Nesta secção, consulte como utilizar o utilitário útil **ostress.exe** exe para executar os procedimentos armazenados dois níveis esforço. Pode comparar quanto tempo demora o será executado duas limite para concluir.


Quando executa ostress.exe, recomendamos que passar valores de parâmetros concebidos para ambos:

- Executar um grande número de ligações em simultâneo, utilizando - n100.
- Ter por cada loop ligação centenas de vezes, utilizando - r500.


No entanto, poderá pretender começar com muito valores inferiores como n10 - e - r50 para se certificar de tudo o que está a funcionar.


### <a name="script-for-ostressexe"></a>Script para ostress.exe


Esta secção apresenta o script T-SQL que esteja incorporado na linha de comandos nosso ostress.exe. O script utiliza itens que foram criados pelo script T-SQL instalado anteriormente.


O seguinte script insere uma ordem de vendas de exemplo com cinco itens de linha de memória otimizado seguintes *tabelas*:

- SalesLT.SalesOrderHeader_inmem
- SalesLT.SalesOrderDetail_inmem


```
DECLARE
    @i int = 0,
    @od SalesLT.SalesOrderDetailType_inmem,
    @SalesOrderID int,
    @DueDate datetime2 = sysdatetime(),
    @CustomerID int = rand() * 8000,
    @BillToAddressID int = rand() * 10000,
    @ShipToAddressID int = rand() * 10000;
    
INSERT INTO @od
    SELECT OrderQty, ProductID
    FROM Demo.DemoSalesOrderDetailSeed
    WHERE OrderID= cast((rand()*60) as int);
    
WHILE (@i < 20)
begin;
    EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT,
        @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od;
    SET @i = @i + 1;
end
```


Para tornar a versão de _ondisk da T-SQL anterior para ostress.exe, teria de substituir simplesmente ambas as ocorrências da subcadeia *_inmem* com *_ondisk*. Estes substitui afeta os nomes das tabelas e procedimentos armazenados.


### <a name="install-rml-utilities-and-ostress"></a>Instalar utilitários RML e ostress


Idealmente seria planeia executar ostress.exe numa VM Azure. Pretender criar uma [máquina de Virtual Azure](https://azure.microsoft.com/documentation/services/virtual-machines/) na mesma região geográfica Azure onde reside a sua base de dados AdventureWorksLT. Mas pode executar ostress.exe no seu portátil em vez disso.


Na VM ou em qualquer que seja alojar escolher, instale os utilitários de reprodução Markup Language (RML), que incluem ostress.exe.

- Consulte o artigo o debate ostress.exe na [Base de dados de exemplo para OLTP na memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou, consulte o artigo [Da base de dados de exemplo para OLTP na memória](http://msdn.microsoft.com/library/mt465764.aspx).
 - Ou consulte o [blogue para instalar o ostress.exe](http://blogs.msdn.com/b/psssql/archive/2013/10/29/cumulative-update-2-to-the-rml-utilities-for-microsoft-sql-server-released.aspx)



<!--
dn511655.aspx is for SQL 2014,
[Extensions to AdventureWorks to Demonstrate In-Memory OLTP]
(http://msdn.microsoft.com/library/dn511655&#x28;v=sql.120&#x29;.aspx)

whereas for SQL 2016+
[Sample Database for In-Memory OLTP]
(http://msdn.microsoft.com/library/mt465764.aspx)
-->



### <a name="run-the-inmem-stress-workload-first"></a>Executar a carga de trabalho do limite de _inmem pela primeira vez


Pode utilizar uma janela de *Linha de comandos do RML Cmd* para executar o nossa linha de comandos ostress.exe. Os parâmetros de linha de comandos direcionar ostress para:

- Executar 100 ligações em simultâneo (-n100).
- Tem de cada ligação de executar o script T-SQL 50 vezes (-r50).


```
ostress.exe -n100 -r50 -S<servername>.database.windows.net -U<login> -P<password> -d<database> -q -Q"DECLARE @i int = 0, @od SalesLT.SalesOrderDetailType_inmem, @SalesOrderID int, @DueDate datetime2 = sysdatetime(), @CustomerID int = rand() * 8000, @BillToAddressID int = rand() * 10000, @ShipToAddressID int = rand()* 10000; INSERT INTO @od SELECT OrderQty, ProductID FROM Demo.DemoSalesOrderDetailSeed WHERE OrderID= cast((rand()*60) as int); WHILE (@i < 20) begin; EXECUTE SalesLT.usp_InsertSalesOrder_inmem @SalesOrderID OUTPUT, @DueDate, @CustomerID, @BillToAddressID, @ShipToAddressID, @od; set @i += 1; end"
```


Para executar a linha de comandos ostress.exe anterior:


1. Repor o conteúdo de dados da base de dados executando o seguinte comando na SSMS, para eliminar todos os dados que foram inseridos por qualquer será executado anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Copie o texto da linha de comandos ostress.exe anterior para sua área de transferência.

3. Substituir o `<placeholders>` para os parâmetros -S - U -P -d com os valores reais corretos.

4. Execute a linha de comandos editada numa janela de RML Cmd.


#### <a name="result-is-a-duration"></a>Resultado é uma duração


Quando concluir ostress.exe, escreve a duração de execução como a sua linha final de saída na janela do RML Cmd. Por exemplo, um endereço mais curto executar de teste duração cerca de 1,5 minutos:

`11/12/15 00:35:00.873 [0x000030A8] OSTRESS exiting normally, elapsed time: 00:01:31.867`


#### <a name="reset-edit-for-ondisk-then-rerun"></a>Repor, editar para _ondisk, em seguida, volte a executar


Depois de ter o resultado da execução do _inmem, execute os seguintes passos para _ondisk executar:


1. Repor a base de dados executando o seguinte comando na SSMS, para eliminar todos os dados que tenha sido inseridos pela execução do anterior:
```
EXECUTE Demo.usp_DemoReset;
```

2. Edite a linha de comandos ostress.exe para substituir todas as *_inmem* *_ondisk*.

3. Volte a executar ostress.exe pela segunda vez e capturar o resultado de duração.

4. Repor novamente a base de dados, responsável eliminação de que podem ser uma grande quantidade de dados de teste.


#### <a name="expected-comparison-results"></a>Resultados da comparação esperado

Os nossos testes na memória tem apresentada uma melhoria de desempenho de **9 vezes** para este simplistic carga de trabalho, com ostress em execução numa VM Azure na mesma região Azure como a base de dados.



<a id="install_analytics_manuallink" name="install_analytics_manuallink"></a>

&nbsp;


## <a name="b-install-the-in-memory-analytics-sample"></a>B. Instalar o exemplo de análise na memória


Nesta secção, comparar os resultados IO e estatísticas ao utilizar um índice columnstore versus um índice de tradicional de árvore de b.


Para análise em tempo real numa carga de trabalho OLTP, é geralmente melhor utilizar um índice columnstore agrupado. Para obter detalhes, consulte [Columnstore índices descrito](http://msdn.microsoft.com/library/gg492088.aspx).



### <a name="prepare-the-columnstore-analytics-test"></a>Preparar o teste de análise columnstore


1. Utilize o portal do Azure para criar uma base de dados AdventureWorksLT fresca a partir da amostra.
 - Utilize esse nome exata.
 - Selecione qualquer camada de serviços Premium.

2. Copie o [sql_in memory_analytics_sample](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/sql_in-memory_analytics_sample.sql) para sua área de transferência.
 - O script de T-SQL cria os objetos necessárias na memória da base de dados de exemplo AdventureWorksLT que criou no passo 1.
 - O script cria a tabela de dimensão e duas tabelas de factos. As tabelas de factos são preenchidas com milhões de 3.5 linhas.
 - O script poderá demorar 15 minutos a concluir.

3. Colar o script T-SQL na SSMS e, em seguida, execute o script.
 - Crucial é a palavra-chave **COLUMNSTORE** numa instrução **CREATE INDEX** , como em:<br/>`CREATE NONCLUSTERED COLUMNSTORE INDEX ...;`

4. Definir AdventureWorksLT ao nível de compatibilidade 130:<br/>`ALTER DATABASE AdventureworksLT SET compatibility_level = 130;`
 - Nível de 130 não está diretamente relacionado com funcionalidades na memória. Mas nível 130 fornece geralmente consulta um desempenho mais rápido que é que 120.


#### <a name="crucial-tables-and-columnstore-indexes"></a>Tabelas cruciais e columnstore índices


- dbo. FactResellerSalesXL_CCI é uma tabela que tem um índice de agrupadas **columnstore** , com avançados compressão ao nível de *dados* .

- dbo. FactResellerSalesXL_PageCompressed é uma tabela que tem um equivalente normal agrupado índice remissivo, que se encontra comprimido apenas ao nível da *página* .


#### <a name="crucial-queries-to-compare-the-columnstore-index"></a>Consultas cruciais para comparar o índice columnstore


[Aqui](https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/features/in-memory/t-sql-scripts/clustered_columnstore_sample_queries.sql) estão os vários tipos de consulta T SQL pode executar para ver as melhorias de desempenho. A partir do passo 2 no script T-SQL, existe um par de consultas que sejam de interesse direta. As duas consultas ser diferentes apenas numa única linha:


- `FROM FactResellerSalesXL_PageCompressed a`
- `FROM FactResellerSalesXL_CCI a`


É um índice columnstore agrupadas na tabela**_CCI** FactResellerSalesXL.

O seguinte excerto de script de T-SQL imprime estatísticas para IO e hora para a consulta de cada tabela.


```
/*********************************************************************
Step 2 -- Overview
-- Page Compressed BTree table v/s Columnstore table performance differences
-- Enable actual Query Plan in order to see Plan differences when Executing
*/
-- Ensure Database is in 130 compatibility mode
ALTER DATABASE AdventureworksLT SET compatibility_level = 130
GO

-- Execute a typical query that joins the Fact Table with dimension tables
-- Note this query will run on the Page Compressed table, Note down the time
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO

SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_PageCompressed a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO
SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO


-- This is the same Prior query on a table with a Clustered Columnstore index CCI 
-- The comparison numbers are even more dramatic the larger the table is, this is a 11 million row table only.
SET STATISTICS IO ON
SET STATISTICS TIME ON
GO
SELECT c.Year
    ,e.ProductCategoryKey
    ,FirstName + ' ' + LastName AS FullName
    ,count(SalesOrderNumber) AS NumSales
    ,sum(SalesAmount) AS TotalSalesAmt
    ,Avg(SalesAmount) AS AvgSalesAmt
    ,count(DISTINCT SalesOrderNumber) AS NumOrders
    ,count(DISTINCT a.CustomerKey) AS CountCustomers
FROM FactResellerSalesXL_CCI a
INNER JOIN DimProduct b ON b.ProductKey = a.ProductKey
INNER JOIN DimCustomer d ON d.CustomerKey = a.CustomerKey
Inner JOIN DimProductSubCategory e on e.ProductSubcategoryKey = b.ProductSubcategoryKey
INNER JOIN DimDate c ON c.DateKey = a.OrderDateKey
WHERE e.ProductCategoryKey =2
    AND c.FullDateAlternateKey BETWEEN '1/1/2014' AND '1/1/2015'
GROUP BY e.ProductCategoryKey,c.Year,d.CustomerKey,d.FirstName,d.LastName
GO

SET STATISTICS IO OFF
SET STATISTICS TIME OFF
GO
```



<a id="preview_considerations_for_in_memory" name="preview_considerations_for_in_memory"></a>


## <a name="preview-considerations-for-in-memory-oltp"></a>Considerações sobre a pré-visualização OLTP na memória


As funcionalidades de memória OLTP base de dados do SQL Azure ficaram [ativa para a pré-visualização no 28 de Outubro de 2015](https://azure.microsoft.com/updates/public-preview-in-memory-oltp-and-real-time-operational-analytics-for-azure-sql-database/).


Na pré-visualização atual, na memória OLTP só é suportada para:

- Bases de dados que estão a uma camada de serviço *Premium* .

- Bases de dados que foram criadas após as funcionalidades de memória OLTP ficaram ativos.
 - Uma nova base de dados não consegue suportar de memória OLTP se este é restaurado a partir de uma base de dados que foi criado antes das funcionalidades de memória OLTP ficaram ativas.


Se estiver na dúvida, pode executar sempre T-SQL seguintes SELECIONE para verificar se a sua base de dados suporta OLTP na memória. Um resultado de **1** significa que a base de dados suporta OLTP na memória:

```
SELECT DatabasePropertyEx(DB_NAME(), 'IsXTPSupported');
```


Se a consulta devolve **1**, na memória OLTP é suportada nesta base de dados e qualquer cópia da base de dados e restauro de base de dados criado com base nesta base de dados.


#### <a name="objects-allowed-only-at-premium"></a>Objectos permitido apenas na Premium


Se uma base de dados contiver qualquer um dos seguintes tipos de objetos na memória OLTP ou tipos de, não é suportada mudar a camada de serviço da base de dados a partir do Premium básicos ou padrão. Para alterar a base de dados, largue pela primeira vez estes objetos:

- Tabelas de memória otimizado
- Tipos de memória otimizado tabela
- Módulos vierem compilados


#### <a name="other-relationships"></a>Outras relações


- Funcionalidades de utilizar OLTP na memória com bases de dados no flexível conjuntos de dados não é suportada durante a pré-visualização.
 - Para mover uma base de dados que tenha ou tenha tido objetos na memória OLTP a um agrupamento de flexível, siga estes passos:
  - 1. Largar qualquer tabelas de memória otimizado, tipos de tabela e vierem compilados módulos de T-SQL na base de dados
  - 2. Alterar a camada de serviço da base de dados para ecrã padrão
  - 3. Mover a base de dados para o conjunto de flexível

- Utilizar OLTP na memória com o SQL Data Warehouse não é suportada.
 - A funcionalidade de índice remissivo columnstore de análise na memória é suportada no armazém de dados SQL.

- Arquivo de consulta não capturar consultas interior vierem compilado módulos.

- Algumas funcionalidades Transact-SQL não são suportadas com OLTP na memória. Aplica-se para o Microsoft SQL Server e base de dados do SQL Azure. Para obter mais detalhes, consulte:
 - [Suporte de Transact-SQL para OLTP na memória](http://msdn.microsoft.com/library/dn133180.aspx)
 - [Construções Transact-SQL não suportadas pelo OLTP na memória](http://msdn.microsoft.com/library/dn246937.aspx)


## <a name="next-steps"></a>Próximos passos


- Experimente [utilização na memória OLTP numa aplicação do SQL Azure existente.](sql-database-in-memory-oltp-migration.md)


## <a name="additional-resources"></a>Recursos adicionais

#### <a name="deeper-information"></a>Informações mais detalhadas

- [Saiba mais sobre OLTP na memória, que se aplica ao Microsoft SQL Server e base de dados do SQL Azure](http://msdn.microsoft.com/library/dn133186.aspx)

- [Saiba mais sobre a análise operacional em tempo real no MSDN](http://msdn.microsoft.com/library/dn817827.aspx)

- Técnico no [padrões de carga de trabalho comuns e considerações de migração](http://msdn.microsoft.com/library/dn673538.aspx), que descreve os padrões de carga de trabalho onde frequentemente na memória OLTP fornece significativos ganhos de desempenho.

#### <a name="application-design"></a>Estrutura de aplicação

- [Na memória OLTP (na memória otimização)](http://msdn.microsoft.com/library/dn133186.aspx)

- [Utilização de memória OLTP numa aplicação do SQL Azure existente.](sql-database-in-memory-oltp-migration.md)

#### <a name="tools"></a>Ferramentas

- [SQL Server dados Tools pré-visualização (SSDT)](http://msdn.microsoft.com/library/mt204009.aspx), para a versão mais recente mensal.

- [Descrição dos utilitários reprodução Markup Language (RML) para o SQL Server](http://support.microsoft.com/en-us/kb/944837)

- [Armazenamento incorporado na memória monitor](sql-database-in-memory-oltp-monitoring.md) para OLTP na memória.

