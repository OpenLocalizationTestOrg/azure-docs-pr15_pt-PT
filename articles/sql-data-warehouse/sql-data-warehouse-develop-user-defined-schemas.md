<properties
   pageTitle="Esquemas definidos pelo utilizador no armazém de dados do SQL | Microsoft Azure"
   description="Sugestões para utilizar esquemas de Transact-SQL no armazém de dados do SQL Azure para desenvolver soluções."
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

# <a name="user-defined-schemas-in-sql-data-warehouse"></a>Esquemas definidos pelo utilizador no armazém de dados SQL

Armazém de dados tradicional utilize frequentemente bases de dados separadas para criar os limites de aplicações com base em carga de trabalho, domínio ou segurança. Por exemplo, um armazém de dados do SQL Server tradicional pode incluir uma base de dados de transição, uma base de dados do armazém de dados e algumas bases de dados de ser de dados. Nesta topologia cada base de dados funciona como uma carga de trabalho e o limite de segurança na arquitectura.

Por outro lado, armazém de dados SQL executa a carga de trabalho de armazém de dados completa dentro de uma base de dados. Cruzada da base de dados não são permitidas associações. Por conseguinte, armazém de dados SQL espera todas as tabelas utilizadas pelo armazém a ser armazenado dentro de uma base de dados.

> [AZURE.NOTE] SQL Data Warehouse não suporta consultas de base de dados cruzada qualquer tipo de. Por conseguinte, implementações do armazém de dados que tirar partido este padrão terão de ser revisto.

## <a name="recommendations"></a>Recomendações

Estas são as recomendações para consolidar das cargas de trabalho, segurança, domínio e limites do funcionais utilizando esquemas definidas pelo utilizador

1. Utilizar uma base de dados do armazém de dados SQL para executar a sua carga de trabalho do armazém de dados completa
2. Consolidar o seu ambiente de armazém de dados existente para utilizar uma base de dados do armazém de dados SQL
3. Tirar partido **esquemas definidos pelo utilizador** para fornecer o limite da implementado anteriormente utilizar bases de dados.

Se não tem sido utilizados esquemas definidos pelo utilizador anteriormente, em seguida, terá uma folha em branco. Simplesmente utilize o nome da base de dados antigo como base para sua esquemas definidos pelo utilizador na base de dados SQL armazém de dados.

Se já tiverem sido utilizados esquemas, em seguida, tem algumas opções:

1. Remover os nomes de legado esquema e começar a trabalhar
2. Manter os nomes de esquema legado por previamente pendente o nome do esquema legado ao nome da tabela
3. Manter os nomes de esquema legado através da implementação de vistas sobre a tabela num esquema extra para criar a estrutura do esquema antigo.

> [AZURE.NOTE] No primeiro controlo, opção 3 parecer como a opção mais apelativa. No entanto, o diabo é o mais detalhadamente. Vistas são só de leitura na armazém de dados SQL. Quaisquer modificações nos dados ou tabela, seriam necessário a ser executado em relação à tabela base. Opção 3 também inclui uma camada de vistas no seu sistema. Poderá pretender atribuir a esta algumas pensamento adicional se estiver a utilizar vistas na sua arquitetura já.


### <a name="examples"></a>Exemplos:

Implementar esquemas definidos pelo utilizador com base nos nomes de base de dados

```sql
CREATE SCHEMA [stg]; -- stg previously database name for staging database
GO
CREATE SCHEMA [edw]; -- edw previously database name for the data warehouse
GO
CREATE TABLE [stg].[customer] -- create staging tables in the stg schema
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[customer] -- create data warehouse tables in the edw schema
(       CustKey BIGINT NOT NULL
,       ...
);
```

Mantêm esquema legado nomes por previamente pendente-los para o nome da tabela. Utilize esquemas para o limite de carga de trabalho.

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- edw defines the data warehouse boundary
GO
CREATE TABLE [stg].[dim_customer] --pre-pend the old schema name to the table and create in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
);
GO
CREATE TABLE [edw].[dim_customer] --pre-pend the old schema name to the table and create in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
);
```

Manter os nomes de esquema legado utilizar vistas

```sql
CREATE SCHEMA [stg]; -- stg defines the staging boundary
GO
CREATE SCHEMA [edw]; -- stg defines the data warehouse boundary
GO
CREATE SCHEMA [dim]; -- edw defines the legacy schema name boundary
GO
CREATE TABLE [stg].[customer] -- create the base staging tables in the staging boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE TABLE [edw].[customer] -- create the base data warehouse tables in the data warehouse boundary
(       CustKey BIGINT NOT NULL
,       ...
)
GO
CREATE VIEW [dim].[customer] -- create a view in the legacy schema name boundary for presentation consistency purposes only
AS
SELECT  CustKey
,       ...
FROM    [edw].customer
;
```

> [AZURE.NOTE] Qualquer alteração na estratégia de esquema necessita de consultar o modelo de segurança da base de dados. Em muitos casos, poderá ser possível simplificar o modelo de segurança, atribuindo permissões ao nível de esquema. Se forem necessárias permissões mais granular, em seguida, pode utilizar funções de base de dados.

## <a name="next-steps"></a>Próximos passos
Para obter mais sugestões de desenvolvimento, consulte o artigo [Descrição geral do desenvolvimento][].

<!--Image references-->

<!--Article references-->
[Descrição geral do desenvolvimento]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->
