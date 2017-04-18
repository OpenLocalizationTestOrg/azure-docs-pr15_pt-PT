<properties
   pageTitle="Criar um armazém de dados do SQL no portal do Azure | Microsoft Azure"
   description="Saiba como criar um armazém de dados do SQL Azure no portal do Azure"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="barbkess;lodipalm;sonyama"/>

# <a name="create-an-azure-sql-data-warehouse"></a>Criar um armazém de dados do Azure SQL

> [AZURE.SELECTOR]
- [Portal do Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Neste tutorial utiliza o Azure portal para criar um armazém de dados SQL que contenha uma base de dados de exemplo AdventureWorksDW.


## <a name="prerequisites"></a>Pré-requisitos

Para começar, é necessário:

- **Conta Azure**: visita [Azure versão de avaliação gratuita][] ou [MSDN Azure créditos][] para criar uma conta.
- **Azure SQL server**: consulte o artigo [criar um servidor de lógico de base de dados do SQL Azure com o portal do Azure][] para obter mais detalhes.

> [AZURE.NOTE] Criar um armazém de dados SQL poderá resultar num novo serviço cobrar.  Para obter mais detalhes, consulte a [preços do armazém de dados SQL][] .

## <a name="create-a-sql-data-warehouse"></a>Criar um armazém de dados SQL

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Clique em **+ Novo** > **dados + armazenamento** > **Armazém de dados SQL**.

    ![Criar](./media/sql-data-warehouse-get-started-provision/create-sample.gif)

3. No pá **Armazém de dados SQL** , preencha as informações necessárias, em seguida, prima Criar para criar.

    ![Criar a base de dados](./media/sql-data-warehouse-get-started-provision/create-database.png)

    - **Servidor**: Recomendamos que selecione o seu servidor pela primeira vez.  

    - **Nome da base de dados**: O nome que é utilizado para referenciar o armazenamento de dados SQL.  Tem de ser exclusivo no servidor.
    
    - **Desempenho**: Recomendamos começando 400 [DWUs][DWU]. Pode mover o controlo de deslize para a esquerda ou direita para ajustar o desempenho do seu armazém de dados ou dimensionar para cima ou para baixo após a criação.  Para saber mais sobre DWUs, consulte a nossa documentação no [dimensionamento](./sql-data-warehouse-manage-compute-overview.md) ou nossa [página de preços][Armazém de dados SQL preços]. 

    - **Subscrição**: selecione a [subscrição] que este armazém de dados SQL irá cobrar para.

    - **Grupo de recursos**: [grupos de recursos] [ Resource group] são contentores concebidas para ajudar a gerir a coleção de recursos Azure. Saiba mais sobre [grupos de recursos](../azure-resource-manager/resource-group-overview.md).

    - **Selecione origem**: clique em **Selecionar origem** > **amostra**. Azure preenche automaticamente a opção **Selecionar exemplo** com AdventureWorksDW.

> [AZURE.NOTE] O agrupamento predefinido para um armazém de dados SQL é SQL_Latin1_General_CP1_CI_AS. Se for necessário um agrupamento diferente, [T-SQL][] podem ser utilizados para criar a base de dados com um agrupamento diferente.

4. Clique em **Criar** para criar o seu armazém de dados SQL.

5. Aguarde alguns minutos. Quando o seu armazém de dados estiver pronta, devem ser devolvidos ao [Azure portal](https://portal.azure.com). Pode encontrar o seu armazém de dados do SQL no seu dashboard, listado em bases de dados do SQL ou no grupo de recursos que utilizou para criá-lo. 

    ![Vista de portal](./media/sql-data-warehouse-get-started-provision/database-portal-view.png)

[AZURE.INCLUDE [SQL Database create server](../../includes/sql-database-create-new-server-firewall-portal.md)] 

## <a name="next-steps"></a>Próximos passos

Agora que criou um armazém de dados SQL, está pronto para [Ligar](./sql-data-warehouse-connect-overview.md) e começar a consultar.

Para carregar os dados para armazém de dados SQL, consulte o artigo [Descrição geral de carregamento](./sql-data-warehouse-overview-load.md).

Se estiver a tentar migrar uma base de dados existente para o armazém de dados SQL, consulte o artigo [Descrição geral da migração](./sql-data-warehouse-overview-migrate.md) ou utilize [Utilitário de migração](./sql-data-warehouse-migrate-migration-utility.md).

Regras de firewall também podem ser configuradas utilizando Transact-SQL. Para mais informações, consulte o artigo [sp_set_firewall_rule][] e [sp_set_database_firewall_rule][].

Também é uma excelente ideia para ver as [melhores práticas][].

<!--Article references-->
[Criar um servidor de lógico de base de dados do Azure SQL com o portal do Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Create an Azure SQL Database logical server with PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[resource groups]: ../resource-group-template-deploy-portal.md
[Melhores práticas]: sql-data-warehouse-best-practices.md
[DWU]: sql-data-warehouse-overview-what-is.md#data-warehouse-units
[subscrição]: ../azure-glossary-cloud-terminology.md#subscription
[resource group]: ../azure-glossary-cloud-terminology.md#resource-group
[T-SQL]: ./sql-data-warehouse-get-started-create-database-tsql.md
 
<!--MSDN references-->
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx

<!--Other Web references-->
[Preços armazém de dados SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Versão de avaliação gratuita Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[MSDN créditos Azure]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

