<properties
   pageTitle="Criar um armazém de dados SQL com TSQL | Microsoft Azure"
   description="Saiba como criar um armazém de dados do SQL Azure com TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-a-sql-data-warehouse-database-by-using-transact-sql-tsql"></a>Criar uma base de dados do SQL Data Warehouse utilizando Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra-lhe como criar um armazém de dados SQL utilizando o T-SQL.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, é necessário: 

- **Conta Azure**: visita [Azure versão de avaliação gratuita][] ou [MSDN Azure créditos][] para criar uma conta.
- **Azure SQL server**: consulte o artigo [criar um servidor de lógico de base de dados do SQL Azure com o Portal do Azure][] ou [criar um servidor de lógico de base de dados do SQL Azure com o PowerShell][] para obter mais detalhes.
- **Grupo de recursos**: Utilize o mesmo grupo de recursos que o servidor Azure SQL ou veja [como criar um grupo de recursos][].
- **Ambiente executar T-SQL**: pode utilizar o [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][]ou [SSMS][] executar T-SQL.

> [AZURE.NOTE] Criar um armazém de dados SQL pode resultar num novo serviço cobrar.  Consulte o artigo [preços armazém de dados SQL][] para obter mais detalhes sobre preços.

## <a name="create-a-database-with-visual-studio"></a>Criar uma base de dados com o Visual Studio

Se estiver familiarizado com o Visual Studio, consulte o artigo [Consulta (Visual Studio) de armazenamento de dados SQL Azure][].  Para começar, abra o Explorador de objeto do SQL Server no Visual Studio e ligar ao servidor que aloja a base de dados do SQL armazém de dados.  Quando estiver ligado, pode criar um armazém de dados SQL, executando o seguinte comando SQL na base de dados **principal** .  Este comando cria a base de dados MySqlDwDb com um serviço de objectivo de DW400 e permitir que a base de dados aumentar a um tamanho máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## <a name="create-a-database-with-sqlcmd"></a>Criar uma base de dados com sqlcmd

Em alternativa, pode executar o mesmo comando com sqlcmd executando os seguintes numa linha de comandos.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

O agrupamento predefinido quando não especificado é SQL_Latin1_General_CP1_CI_AS agrupar.  O `MAXSIZE` pode ser entre 250 GB e 240 TB.  O `SERVICE_OBJECTIVE` pode ser entre DW100 e DW2000 [DWU][].  Para obter uma lista de todos os valores válidos, consulte a documentação do MSDN para [Criar a base de dados][].  MAXSIZE tanto SERVICE_OBJECTIVE podem ser alteradas com um comando de T SQL [ALTER DATABASE][] .  O agrupamento de uma base de dados não pode ser alterado após a criação.   Deve ser utilizado cuidado quando alterar SERVICE_OBJECTIVE como alterar DWU faz com que um reinício dos serviços, que cancela todas as consultas em voo.  Alterar MAXSIZE não for reiniciado serviços tal como está apenas uma operação de metadados simples.

## <a name="next-steps"></a>Próximos passos

Depois do seu armazém de dados SQL já terminou aprovisionamento pode [carregar os dados de exemplo][] ou consulte o artigo como [desenvolver][], [carregar][]ou [Migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Consulta Azure SQL dados armazém (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desenvolver]: sql-data-warehouse-overview-develop.md
[carregar]: sql-data-warehouse-overview-load.md
[carregar os dados de exemplo]: sql-data-warehouse-load-sample-databases.md
[Criar um servidor de lógico de base de dados do Azure SQL com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor de lógico de base de dados do Azure SQL com PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[SQLCMD]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CRIAR A BASE DE DADOS]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTERAR A BASE DE DADOS]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Preços armazém de dados SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de avaliação gratuita Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
