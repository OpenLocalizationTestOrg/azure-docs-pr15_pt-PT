<properties
    pageTitle="Começar a trabalhar com consultas de base de dados em várias (partições vertical) | Microsoft Azure"   
    description="como utilizar a consulta de base de dados flexível com bases de dados na vertical com a partições"
    services="sql-database"
    documentationCenter=""  
    manager="jhubbard"
    authors="torsteng"/>

<tags
    ms.service="sql-database"
    ms.workload="sql-database"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="05/23/2016"
    ms.author="torsteng" />

# <a name="get-started-with-cross-database-queries-vertical-partitioning-preview"></a>Começar a trabalhar com consultas de base de dados em várias (partições vertical) (pré-visualização)

Consulta de base de dados flexível (pré-visualização) para a base de dados do Azure SQL permite-lhe executar consultas de T SQL que aparecem em várias bases de dados utilizando um ponto de ligação único. Este tópico aplica-se a [partições verticalmente bases de dados](sql-database-elastic-query-vertical-partitioning.md).  

Quando concluído, irá: Saiba como configurar e utilizar uma base de dados do SQL Azure para realizar consultas que aparecem em várias bases de dados relacionados. 

Para mais informações sobre a funcionalidade de consulta de base de dados flexível, consulte o artigo [Descrição geral das consultas de base de dados flexível de base de dados do SQL Azure](sql-database-elastic-query-overview.md). 

## <a name="create-the-sample-databases"></a>Criar as bases de dados de exemplo

Para começar, precisamos de criar duas bases de dados, **clientes** e **Encomendas**, quer seja nos servidores de lógicos mesmo ou diferentes.   

Execute consultas que se seguem da base de dados de **Encomendas** para criar a tabela **OrderInformation** e os dados de exemplo de entrada. 

    CREATE TABLE [dbo].[OrderInformation]( 
        [OrderID] [int] NOT NULL, 
        [CustomerID] [int] NOT NULL 
        ) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (123, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (149, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (857, 2) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (321, 1) 
    INSERT INTO [dbo].[OrderInformation] ([OrderID], [CustomerID]) VALUES (564, 8) 

Agora, execute após a consulta da base de dados de **clientes** para criar a tabela **CustomerInformation** e os dados de exemplo de entrada. 

    CREATE TABLE [dbo].[CustomerInformation]( 
        [CustomerID] [int] NOT NULL, 
        [CustomerName] [varchar](50) NULL, 
        [Company] [varchar](50) NULL 
        CONSTRAINT [CustID] PRIMARY KEY CLUSTERED ([CustomerID] ASC) 
    ) 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (1, 'Jack', 'ABC') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (2, 'Steve', 'XYZ') 
    INSERT INTO [dbo].[CustomerInformation] ([CustomerID], [CustomerName], [Company]) VALUES (3, 'Lylla', 'MNO') 

## <a name="create-database-objects"></a>Criar objectos de base de dados
### <a name="database-scoped-master-key-and-credentials"></a>Âmbito chave principal e as credenciais de base de dados

1. Abra o SQL Server Management Studio ou ferramentas de dados do SQL Server no Visual Studio.
2. Ligar à base de dados de encomendas e execute os seguintes comandos T-SQL:

        CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<password>'; 
        CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred 
        WITH IDENTITY = '<username>', 
        SECRET = '<password>';  

    O "nome de utilizador" e "palavra-passe" devem ser o nome de utilizador e palavra-passe utilizada para iniciar sessão para a base de dados de clientes.
    Autenticação utilizando o Azure Active Directory com consultas flexível atualmente não é suportada.

### <a name="external-data-sources"></a>Origens de dados externas
Para criar uma origem de dados externos, execute o seguinte comando da base de dados de encomendas: 

    CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH 
        (TYPE = RDBMS, 
        LOCATION = '<server_name>.database.windows.net', 
        DATABASE_NAME = 'Customers', 
        CREDENTIAL = ElasticDBQueryCred, 
    ) ;

### <a name="external-tables"></a>Tabelas externas
Crie uma tabela externa da base de dados de encomendas, que corresponde à definição da tabela CustomerInformation:

    CREATE EXTERNAL TABLE [dbo].[CustomerInformation] 
    ( [CustomerID] [int] NOT NULL, 
      [CustomerName] [varchar](50) NOT NULL, 
      [Company] [varchar](50) NOT NULL) 
    WITH 
    ( DATA_SOURCE = MyElasticDBQueryDataSrc) 

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Executar uma consulta de T-SQL de flexível da base de dados de exemplo

Assim que tiver definido a sua origem de dados externos e as suas tabelas externas agora pode utilizar T-SQL para consultar as suas tabelas externas. Execute esta consulta da base de dados de encomendas: 

    SELECT OrderInformation.CustomerID, OrderInformation.OrderId, CustomerInformation.CustomerName, CustomerInformation.Company 
    FROM OrderInformation 
    INNER JOIN CustomerInformation 
    ON CustomerInformation.CustomerID = OrderInformation.CustomerID 

## <a name="cost"></a>Custo

Atualmente, a funcionalidade de consulta de base de dados flexível é incluída para o custo da base de dados SQL Azure.  

Para informações sobre os preços consulte [Preços de base de dados SQL](/pricing/details/sql-database). 


[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->

<!--anchors-->
