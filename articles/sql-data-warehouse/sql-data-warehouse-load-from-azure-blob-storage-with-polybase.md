<properties
   pageTitle="Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase) | Microsoft Azure"
   description="Saiba como utilizar PolyBase para carregar os dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL. Carrega algumas tabelas a partir de dados públicos num esquema de armazém de dados de venda a retalho Contoso."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="ckarst"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="cakarst;barbkess;sonyama"/>


# <a name="load-data-from-azure-blob-storage-into-sql-data-warehouse-polybase"></a>Carregar dados a partir do armazenamento de Blobs do Azure para armazém de dados SQL (PolyBase)

> [AZURE.SELECTOR]
- [Fábrica de dados](sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md)
- [PolyBase](sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md)

Utilize comandos PolyBase e T-SQL para carregar os dados a partir do armazenamento de Blobs do Azure para armazém de dados do SQL Azure. 

Para manter simples, este tutorial carrega duas tabelas a partir de um armazenamento Blob público do Azure para o esquema do armazém de dados de venda a retalho Contoso. Para carregar o conjunto completo de dados, execute o exemplo [carregar o armazenamento de dados de venda a retalho Contoso completo][] a partir do repositório de amostras do Microsoft SQL Server.

Neste tutorial vai:

1. Configurar PolyBase para carregar a partir do armazenamento de Blobs do Azure
2. Carregar dados públicos na sua base de dados
3. Execute otimizações depois do carregamento está concluído.


## <a name="before-you-begin"></a>Antes de começar
Para executar este tutorial, é necessária uma conta Azure que já tem uma base de dados do SQL armazém de dados. Se ainda não tiver isto, consulte o artigo [criar um armazém de dados SQL][].

## <a name="1-configure-the-data-source"></a>1. a configurar a origem de dados

PolyBase utiliza objectos externos T-SQL para definir a localização e atributos de dados externos. As definições de objecto externo estão armazenadas no armazém de dados do SQL. Os dados propriamente ditos são armazenados externamente.

### <a name="11-create-a-credential"></a>1.1. Criar uma credencial

**Ignorar este passo** se estiver a carregar os dados de públicos da Contoso. Não precisa de acesso seguro para os dados públicos dado que já está acessível a qualquer pessoa.

**Não ignorar este passo** se estiver a utilizar este tutorial como um modelo para carregar os seus próprios dados. Para aceder aos dados através de credenciais, utilize o seguinte script para criar uma credencial confinados de base de dados e, em seguida, utilizá-lo quando definir a localização da origem de dados.


```sql
-- A: Create a master key.
-- Only necessary if one does not already exist.
-- Required to encrypt the credential secret in the next step.

CREATE MASTER KEY;


-- B: Create a database scoped credential
-- IDENTITY: Provide any string, it is not used for authentication to Azure storage.
-- SECRET: Provide your Azure storage account key.


CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential
WITH
    IDENTITY = 'user',
    SECRET = '<azure_storage_account_key>'
;


-- C: Create an external data source
-- TYPE: HADOOP - PolyBase uses Hadoop APIs to access data in Azure blob storage.
-- LOCATION: Provide Azure storage account name and blob container name.
-- CREDENTIAL: Provide the credential created in the previous step.

CREATE EXTERNAL DATA SOURCE AzureStorage
WITH (
    TYPE = HADOOP,
    LOCATION = 'wasbs://<blob_container_name>@<azure_storage_account_name>.blob.core.windows.net',
    CREDENTIAL = AzureStorageCredential
);
```

Avance para o passo 2.

### <a name="12-create-the-external-data-source"></a>1.2. Criar a origem de dados externos

Utilize este comando [Criar origem de dados externa][] para armazenar a localização dos dados e o tipo de dados. 

```sql
CREATE EXTERNAL DATA SOURCE AzureStorage_west_public
WITH 
(  
    TYPE = Hadoop 
,   LOCATION = 'wasbs://contosoretaildw-tables@contosoretaildw.blob.core.windows.net/'
); 
```

> [AZURE.IMPORTANT] Se optar por torne o seu BLOBs do azure contentores de armazenamento público, lembre-se de que como o proprietário de dados será cobrado para dados taxas de saída quando dados deixa o Centro de dados. 

## <a name="2-configure-data-format"></a>2. configurar o formato de dados

Os dados são armazenados em ficheiros de texto no armazenamento de Blobs do Azure e cada campo está separado com delimitador. Execute este comando [Criar EXTERNOS formato de ficheiro][] para especificar o formato dos dados em ficheiros de texto. Os dados da Contoso são não comprimidos e pipe delimitado por.

```sql
CREATE EXTERNAL FILE FORMAT TextFileFormat 
WITH 
(   FORMAT_TYPE = DELIMITEDTEXT
,   FORMAT_OPTIONS  (   FIELD_TERMINATOR = '|'
                    ,   STRING_DELIMITER = ''
                    ,   DATE_FORMAT      = 'yyyy-MM-dd HH:mm:ss.fff'
                    ,   USE_TYPE_DEFAULT = FALSE 
                    )
);
``` 

## <a name="3-create-the-external-tables"></a>3. criar as tabelas externas

Agora que especificou o formato de origem e ficheiro de dados, está pronto para criar as tabelas externas. 

### <a name="31-create-a-schema-for-the-data"></a>3.1. Crie um esquema para os dados. 

Para criar um local para armazenar os dados da Contoso na base de dados, crie um esquema.

```sql
CREATE SCHEMA [asb]
GO
```

### <a name="32-create-the-external-tables"></a>3,2. Crie as tabelas externas. 

Execute este script para criar as tabelas externas DimProduct e FactOnlineSales. Todos estamos a fazer aqui é definir nomes de colunas e tipos de dados e enlace-las para a localização e o formato dos ficheiros de armazenamento de Blobs do Azure. A definição é armazenada no armazém de dados do SQL e os dados ainda se encontra no Blob de armazenamento do Azure.

O parâmetro de **localização** é a pasta na pasta de raiz no Blob de armazenamento do Azure. Cada tabela é numa pasta diferente.


```sql

--DimProduct
CREATE EXTERNAL TABLE [asb].DimProduct (
    [ProductKey] [int] NOT NULL,
    [ProductLabel] [nvarchar](255) NULL,
    [ProductName] [nvarchar](500) NULL,
    [ProductDescription] [nvarchar](400) NULL,
    [ProductSubcategoryKey] [int] NULL,
    [Manufacturer] [nvarchar](50) NULL,
    [BrandName] [nvarchar](50) NULL,
    [ClassID] [nvarchar](10) NULL,
    [ClassName] [nvarchar](20) NULL,
    [StyleID] [nvarchar](10) NULL,
    [StyleName] [nvarchar](20) NULL,
    [ColorID] [nvarchar](10) NULL,
    [ColorName] [nvarchar](20) NOT NULL,
    [Size] [nvarchar](50) NULL,
    [SizeRange] [nvarchar](50) NULL,
    [SizeUnitMeasureID] [nvarchar](20) NULL,
    [Weight] [float] NULL,
    [WeightUnitMeasureID] [nvarchar](20) NULL,
    [UnitOfMeasureID] [nvarchar](10) NULL,
    [UnitOfMeasureName] [nvarchar](40) NULL,
    [StockTypeID] [nvarchar](10) NULL,
    [StockTypeName] [nvarchar](40) NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [AvailableForSaleDate] [datetime] NULL,
    [StopSaleDate] [datetime] NULL,
    [Status] [nvarchar](7) NULL,
    [ImageURL] [nvarchar](150) NULL,
    [ProductURL] [nvarchar](150) NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/DimProduct/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
 
--FactOnlineSales
CREATE EXTERNAL TABLE [asb].FactOnlineSales 
(
    [OnlineSalesKey] [int]  NOT NULL,
    [DateKey] [datetime] NOT NULL,
    [StoreKey] [int] NOT NULL,
    [ProductKey] [int] NOT NULL,
    [PromotionKey] [int] NOT NULL,
    [CurrencyKey] [int] NOT NULL,
    [CustomerKey] [int] NOT NULL,
    [SalesOrderNumber] [nvarchar](20) NOT NULL,
    [SalesOrderLineNumber] [int] NULL,
    [SalesQuantity] [int] NOT NULL,
    [SalesAmount] [money] NOT NULL,
    [ReturnQuantity] [int] NOT NULL,
    [ReturnAmount] [money] NULL,
    [DiscountQuantity] [int] NULL,
    [DiscountAmount] [money] NULL,
    [TotalCost] [money] NOT NULL,
    [UnitCost] [money] NULL,
    [UnitPrice] [money] NULL,
    [ETLLoadID] [int] NULL,
    [LoadDate] [datetime] NULL,
    [UpdateDate] [datetime] NULL
)
WITH
(
    LOCATION='/FactOnlineSales/' 
,   DATA_SOURCE = AzureStorage_west_public
,   FILE_FORMAT = TextFileFormat
,   REJECT_TYPE = VALUE
,   REJECT_VALUE = 0
)
;
```

## <a name="4-load-the-data"></a>4. carregar os dados
Há várias formas de aceder a dados externos.  Pode consultar dados diretamente a partir da tabela externa, carregar os dados em tabelas de base de dados nova ou adicionar dados externos a tabelas de base de dados existentes.  


### <a name="41-create-a-new-schema"></a>4.1. Criar um novo esquema

CTAS cria uma nova tabela que contém dados.  Primeiro, crie um esquema para os dados da contoso.

```sql
CREATE SCHEMA [cso]
GO
```

### <a name="42-load-the-data-into-new-tables"></a>4.2. Carregar os dados para novas tabelas

Para carregar os dados a partir do armazenamento de Blobs do Azure e guardá-lo numa tabela dentro da sua base de dados, utilize a criar tabela como (Transact-SQL) instrução [SELECT][] . Carregar com CTAS tira partido de tabelas externas escritas vivamente que acabou de criar. Para carregar os dados para novas tabelas, utilize uma instrução [CTAS][] por tabela. 

CTAS cria uma nova tabela e preenche-la com os resultados de uma instrução select. CTAS define a nova tabela para os ter as mesmas colunas e tipos de dados como os resultados da instrução select. Se selecionar todas as colunas a partir de uma tabela externa, a nova tabela será uma réplica das colunas e tipos de dados na tabela externa.

Neste exemplo, vamos criar a dimensão e na tabela de factos hash distribuídas tabelas. 


```sql
SELECT GETDATE();
GO

CREATE TABLE [cso].[DimProduct]            WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[DimProduct]             OPTION (LABEL = 'CTAS : Load [cso].[DimProduct]             ');
CREATE TABLE [cso].[FactOnlineSales]       WITH (DISTRIBUTION = HASH([ProductKey]  ) ) AS SELECT * FROM [asb].[FactOnlineSales]        OPTION (LABEL = 'CTAS : Load [cso].[FactOnlineSales]        ');
```

### <a name="43-track-the-load-progress"></a>4.3 controlar o progresso de carregamento

Pode controlar o progresso da sua carga utilizar vistas de gestão de dinâmica (DMVs). 

```sql
-- To see all requests
SELECT * FROM sys.dm_pdw_exec_requests;

-- To see a particular request identified by its label
SELECT * FROM sys.dm_pdw_exec_requests as r;
WHERE r.[label] = 'CTAS : Load [cso].[DimProduct]             '
      OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
;

-- To track bytes and files
SELECT
    r.command,
    s.request_id,
    r.status,
    count(distinct input_name) as nbr_files, 
    sum(s.bytes_processed)/1024/1024 as gb_processed
FROM
    sys.dm_pdw_exec_requests r
    inner join sys.dm_pdw_dms_external_work s
        on r.request_id = s.request_id
WHERE 
    r.[label] = 'CTAS : Load [cso].[DimProduct]             '
    OR r.[label] = 'CTAS : Load [cso].[FactOnlineSales]        '
GROUP BY
    r.command,
    s.request_id,
    r.status
ORDER BY
    nbr_files desc,
    gb_processed desc;
```

## <a name="5-optimize-columnstore-compression"></a>5. optimizar a compressão de columnstore

Por predefinição, o armazém de dados SQL armazena a tabela como um índice columnstore agrupadas. Quando um carregamento for concluída, algumas das linhas de dados não podem ser comprimidas para o columnstore.  Existe uma variedade de razões porque é que isto pode ocorrer. Para saber mais, consulte o artigo [Gerir columnstore índices][].

Para otimizar o desempenho da consulta e a compressão de columnstore após uma carga, reconstrua a tabela para forçar o índice columnstore para comprimir todas as linhas. 

```sql
SELECT GETDATE();
GO

ALTER INDEX ALL ON [cso].[DimProduct]               REBUILD;
ALTER INDEX ALL ON [cso].[FactOnlineSales]          REBUILD;
```

Para mais informações sobre manutenção columnstore índices, consulte o artigo [Gerir columnstore índices][] .

## <a name="6-optimize-statistics"></a>6. otimizar estatísticas

É melhor criar uma coluna estatísticas imediatamente após um carregamento. Existem algumas opções de estatísticas. Por exemplo, se criar uma coluna estatísticas cada coluna, poderá demorar muito tempo a recompilar todas as estatísticas. Se souber que certas colunas não estão a passar a estar predicados de consulta, pode ignorar as estatísticas de criação das colunas.

Se decidir criar uma coluna estatísticas cada coluna de cada tabela, pode utilizar o código de exemplo do procedimento armazenado `prc_sqldw_create_stats` no artigo [Estatísticas][] .

O exemplo seguinte é um bom ponto de partida para criar estatísticas. Cria uma coluna estatísticas em cada coluna na tabela de dimensão e em cada coluna unir nas tabelas de factos. Pode sempre adicionar estatísticas simples ou várias colunas para outras colunas da tabela de factos mais tarde.


```sql
CREATE STATISTICS [stat_cso_DimProduct_AvailableForSaleDate] ON [cso].[DimProduct]([AvailableForSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_BrandName] ON [cso].[DimProduct]([BrandName]);
CREATE STATISTICS [stat_cso_DimProduct_ClassID] ON [cso].[DimProduct]([ClassID]);
CREATE STATISTICS [stat_cso_DimProduct_ClassName] ON [cso].[DimProduct]([ClassName]);
CREATE STATISTICS [stat_cso_DimProduct_ColorID] ON [cso].[DimProduct]([ColorID]);
CREATE STATISTICS [stat_cso_DimProduct_ColorName] ON [cso].[DimProduct]([ColorName]);
CREATE STATISTICS [stat_cso_DimProduct_ETLLoadID] ON [cso].[DimProduct]([ETLLoadID]);
CREATE STATISTICS [stat_cso_DimProduct_ImageURL] ON [cso].[DimProduct]([ImageURL]);
CREATE STATISTICS [stat_cso_DimProduct_LoadDate] ON [cso].[DimProduct]([LoadDate]);
CREATE STATISTICS [stat_cso_DimProduct_Manufacturer] ON [cso].[DimProduct]([Manufacturer]);
CREATE STATISTICS [stat_cso_DimProduct_ProductDescription] ON [cso].[DimProduct]([ProductDescription]);
CREATE STATISTICS [stat_cso_DimProduct_ProductKey] ON [cso].[DimProduct]([ProductKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductLabel] ON [cso].[DimProduct]([ProductLabel]);
CREATE STATISTICS [stat_cso_DimProduct_ProductName] ON [cso].[DimProduct]([ProductName]);
CREATE STATISTICS [stat_cso_DimProduct_ProductSubcategoryKey] ON [cso].[DimProduct]([ProductSubcategoryKey]);
CREATE STATISTICS [stat_cso_DimProduct_ProductURL] ON [cso].[DimProduct]([ProductURL]);
CREATE STATISTICS [stat_cso_DimProduct_Size] ON [cso].[DimProduct]([Size]);
CREATE STATISTICS [stat_cso_DimProduct_SizeRange] ON [cso].[DimProduct]([SizeRange]);
CREATE STATISTICS [stat_cso_DimProduct_SizeUnitMeasureID] ON [cso].[DimProduct]([SizeUnitMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_Status] ON [cso].[DimProduct]([Status]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeID] ON [cso].[DimProduct]([StockTypeID]);
CREATE STATISTICS [stat_cso_DimProduct_StockTypeName] ON [cso].[DimProduct]([StockTypeName]);
CREATE STATISTICS [stat_cso_DimProduct_StopSaleDate] ON [cso].[DimProduct]([StopSaleDate]);
CREATE STATISTICS [stat_cso_DimProduct_StyleID] ON [cso].[DimProduct]([StyleID]);
CREATE STATISTICS [stat_cso_DimProduct_StyleName] ON [cso].[DimProduct]([StyleName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitCost] ON [cso].[DimProduct]([UnitCost]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureID] ON [cso].[DimProduct]([UnitOfMeasureID]);
CREATE STATISTICS [stat_cso_DimProduct_UnitOfMeasureName] ON [cso].[DimProduct]([UnitOfMeasureName]);
CREATE STATISTICS [stat_cso_DimProduct_UnitPrice] ON [cso].[DimProduct]([UnitPrice]);
CREATE STATISTICS [stat_cso_DimProduct_UpdateDate] ON [cso].[DimProduct]([UpdateDate]);
CREATE STATISTICS [stat_cso_DimProduct_Weight] ON [cso].[DimProduct]([Weight]);
CREATE STATISTICS [stat_cso_DimProduct_WeightUnitMeasureID] ON [cso].[DimProduct]([WeightUnitMeasureID]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CurrencyKey] ON [cso].[FactOnlineSales]([CurrencyKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_CustomerKey] ON [cso].[FactOnlineSales]([CustomerKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_DateKey] ON [cso].[FactOnlineSales]([DateKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_OnlineSalesKey] ON [cso].[FactOnlineSales]([OnlineSalesKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_ProductKey] ON [cso].[FactOnlineSales]([ProductKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_PromotionKey] ON [cso].[FactOnlineSales]([PromotionKey]);
CREATE STATISTICS [stat_cso_FactOnlineSales_StoreKey] ON [cso].[FactOnlineSales]([StoreKey]);
```

## <a name="achievement-unlocked"></a>Feitos desbloqueados!

Com êxito carregou dados públicos no armazém de dados do SQL Azure. Parabéns!

Agora pode começar a consultar as tabelas a utilização de consultas como o seguinte:

```sql
SELECT  SUM(f.[SalesAmount]) AS [sales_by_brand_amount]
,       p.[BrandName]
FROM    [cso].[FactOnlineSales] AS f
JOIN    [cso].[DimProduct]      AS p ON f.[ProductKey] = p.[ProductKey]
GROUP BY p.[BrandName]
```

## <a name="next-steps"></a>Próximos passos
Para carregar os dados de armazém de dados de venda a retalho Contoso completos, utilize o script para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento armazém de dados SQL][].

<!--Image references-->

<!--Article references-->
[Criar um armazém de dados SQL]: sql-data-warehouse-get-started-provision.md
[Load data into SQL Data Warehouse]: sql-data-warehouse-overview-load.md
[Descrição geral do desenvolvimento armazém de dados SQL]: sql-data-warehouse-overview-develop.md
[Gerir columnstore índices]: sql-data-warehouse-tables-index.md
[Estatísticas]: sql-data-warehouse-tables-statistics.md
[CTAS]: sql-data-warehouse-develop-ctas.md
[label]: sql-data-warehouse-develop-label.md

<!--MSDN references-->
[CRIAR A ORIGEM DE DADOS EXTERNA]: https://msdn.microsoft.com/en-us/library/dn935022.aspx
[CRIAR O FORMATO DE FICHEIRO EXTERNO]: https://msdn.microsoft.com/en-us/library/dn935026.aspx
[Criar tabela como SELECIONAR (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[sys.dm_pdw_exec_requests]: https://msdn.microsoft.com/library/mt203887.aspx
[REBUILD]: https://msdn.microsoft.com/library/ms188388.aspx

<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433
[Carregar o armazenamento de dados de venda a retalho Contoso completo]: https://github.com/Microsoft/sql-server-samples/tree/master/samples/databases/contoso-data-warehouse/readme.md
