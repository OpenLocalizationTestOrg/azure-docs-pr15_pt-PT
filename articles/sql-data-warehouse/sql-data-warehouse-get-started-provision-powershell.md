<properties
   pageTitle="Criar armazém de dados SQL utilizando o PowerShell | Microsoft Azure"
   description="Criar armazém de dados SQL utilizando o PowerShell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# <a name="create-sql-data-warehouse-using-powershell"></a>Criar armazém de dados SQL através do PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Este artigo mostra-lhe como criar um armazém de dados SQL através do PowerShell.

## <a name="prerequisites"></a>Pré-requisitos

Para começar, é necessário:

- **Conta Azure**: visita [Azure versão de avaliação gratuita][] ou [MSDN Azure créditos][] para criar uma conta.
- **Azure SQL server**: consulte o artigo [criar um servidor de lógico de base de dados do SQL Azure com o Portal do Azure][] ou [criar um servidor de lógico de base de dados do SQL Azure com o PowerShell][] para obter mais detalhes.
- **Grupo de recursos**: Utilize o mesmo grupo de recursos que o servidor Azure SQL ou veja [como criar um grupo de recursos][].
- **PowerShell versão 1.0.3 ou maior**: pode verificar a sua versão executando **módulo Get - ListAvailable-nome Azure**.  Pode ser instalada a versão mais recente do [Microsoft Web plataforma Installer][].  Para mais informações sobre como instalar a versão mais recente, consulte o artigo [como instalar e configurar o Azure PowerShell][].

> [AZURE.NOTE] Criar um armazém de dados SQL pode resultar num novo serviço cobrar.  Consulte o artigo [preços armazém de dados SQL][] para obter mais detalhes sobre preços.

## <a name="create-a-sql-data-warehouse"></a>Criar um armazém de dados SQL

1. Abra o Windows PowerShell.
2. Execute este cmdlet para o início de sessão para o Gestor de recursos do Azure.

    ```Powershell
    Login-AzureRmAccount
    ```
    
3. Selecione a subscrição que pretende utilizar para a sessão actual.

    ```Powershell
    Get-AzureRmSubscription -SubscriptionName "MySubscription" | Select-AzureRmSubscription
    ```

4.  Crie a base de dados. Este exemplo cria uma base de dados com o nome "mynewsqldw", com o serviço objetivo nível de "DW400", o servidor denominado "sqldwserver1", que se encontra no grupo de recursos com o nome "mywesteuroperesgp1".

    ```Powershell
    New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
    ```

Parâmetros necessários são:

- **RequestedServiceObjectiveName**: A quantidade de [DWU][] que está a pedir.  Valores suportados são: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 e DW6000.
- **DatabaseName**: O nome do armazém de dados SQL que está a criar.
- **Nomedoservidor**: O nome do servidor que está a utilizar para a criação de (tem de ser V12).
- **ResourceGroupName**: grupo de recursos que está a utilizar.  Para encontrar recursos disponíveis grupos na sua subscrição utilizam Get-AzureResource.
- **Edition**: tem de ser "DataWarehouse" para criar um armazém de dados SQL.

Parâmetros opcionais são:

- **CollationName**: O agrupamento predefinido, se não for especificado é SQL_Latin1_General_CP1_CI_AS.  Não pode ser alterado agrupamento numa base de dados.
- **MaxSizeBytes**: O tamanho máximo predefinido de uma base de dados é 10 GB.


Para obter mais detalhes sobre as opções de parâmetro, consulte o artigo [Novo AzureRmSqlDatabase][] e [Criar base de dados (armazém de dados do SQL Azure)][].

## <a name="next-steps"></a>Próximos passos

Depois do seu armazém de dados SQL já terminou aprovisionamento poderá querer experimentar o [carregamento de dados de exemplo][] ou consulte o artigo como [desenvolver][], [carregar][]ou [Migrar][].

Se está interessado em obter mais informações sobre como gerir armazém de dados SQL através de programação, consulte o artigo nosso artigo sobre como utilizar [os cmdlets do PowerShell e os REST APIs][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desenvolver]: ./sql-data-warehouse-overview-develop.md
[carregar]: ./sql-data-warehouse-load-with-bcp.md
[carregar os dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[Os cmdlets do PowerShell e os REST APIs]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Como instalar e configurar PowerShell do Azure]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Criar um servidor de lógico de base de dados do Azure SQL com o Portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Criar um servidor de lógico de base de dados do Azure SQL com PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[como criar um grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[Novo AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Criar a base de dados (armazém de dados do Azure SQL)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Microsoft Web plataforma Installer]: https://aka.ms/webpi-azps
[Preços armazém de dados SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de avaliação gratuita Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F
