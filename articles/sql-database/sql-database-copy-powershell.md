<properties 
    pageTitle="Copiar uma base de dados do Azure SQL através do PowerShell | Microsoft Azure" 
    description="Criar cópia de uma base de dados do Azure SQL através do PowerShell" 
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.date="09/08/2016"
    ms.author="sstein"
    ms.workload="data-management"
    ms.topic="article"
    ms.tgt_pltfrm="NA"/>


# <a name="copy-an-azure-sql-database-using-powershell"></a>Copiar uma base de dados do Azure SQL através do PowerShell


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-copy.md)
- [Portal do Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

Este artigo mostra como copiar uma base de dados SQL com o PowerShell para o mesmo servidor, para um servidor diferente, ou copie uma base de dados para um [conjunto de base de dados flexível](sql-database-elastic-pool.md). A operação de cópia da base de dados utiliza o cmdlet [AzureRmSqlDatabaseCopy novo](https://msdn.microsoft.com/library/mt603644.aspx) . 


Para concluir este artigo, precisa do seguinte:

- Base de dados do Azure SQL (uma base de dados para copiar). Se não tiver uma base de dados do SQL, crie uma seguir os passos neste artigo: [criar a primeira base de dados do Azure SQL](sql-database-get-started.md).
- A versão mais recente do Azure PowerShell. Para obter informações detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).


Muitas funcionalidades novas da base de dados SQL só são suportadas quando está a utilizar o [modelo de implementação do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md), para que exemplos utilizam os [cmdlets do PowerShell de base de dados do SQL Azure](https://msdn.microsoft.com/library/azure/mt574084.aspx) para Gestor de recursos. O modelo de implementação clássica existente [cmdlets (clássicos) da base de dados do SQL Azure](https://msdn.microsoft.com/library/azure/dn546723.aspx) são suportadas para retrocompatibilidade, mas recomendamos que utilize os cmdlets do Gestor de recursos.


>[AZURE.NOTE] Dependendo do tamanho da base de dados, a operação de cópia poderá demorar algum tempo para concluir.


## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar uma base de dados do SQL no mesmo servidor

Para criar a cópia no mesmo servidor, omita a `-CopyServerName` parâmetro (ou defini-lo no mesmo servidor).

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyDatabaseName "database1_copy"

## <a name="copy-a-sql-database-to-a-different-server"></a>Copiar uma base de dados do SQL para um servidor diferente

Para criar a cópia num servidor diferente, inclua o `-CopyServerName` parâmetro e defini-lo para um servidor diferente. O servidor de *cópia* já tem de existir. Se é num grupo de recursos diferentes, em seguida, também tem de incluir o `-CopyResourceGroupName` parâmetro.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -CopyServerName "server2" -CopyDatabaseName "database1_copy"


## <a name="copy-a-sql-database-into-an-elastic-database-pool"></a>Copie uma base de dados do SQL para um agrupamento de base de dados flexível

Para criar uma cópia de uma base de dados do SQL num conjunto de dados, defina o `-ElasticPoolName` parâmetro para um conjunto existente.

    New-AzureRmSqlDatabaseCopy -ResourceGroupName "resourcegoup1" -ServerName "server1" -DatabaseName "database1" -CopyResourceGroupName "poolResourceGroup" -CopyServerName "poolServer1" -CopyDatabaseName "database1_copy" -ElasticPoolName "poolName"


## <a name="resolve-logins"></a>Resolver os inícios de sessão

Para resolver os inícios de sessão depois de concluída a operação de cópia, consulte o artigo [resolver os inícios de sessão](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes)


## <a name="example-powershell-script"></a>Exemplo de script PowerShell

O seguinte script assume todos os grupos de recursos, servidores, e o agrupamento de já existentes (substitua os valores de variáveis com os recursos existentes). Tudo o que tem de existir, exceto a cópia da base de dados.

    # Sign in to Azure and set the subscription to work with
    # ------------------------------------------------------
    $SubscriptionId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId
    
    
    # SQL database source (the existing database to copy)
    # ---------------------------------------------------
    $sourceDbName = "db1"
    $sourceDbServerName = "server1"
    $sourceDbResourceGroupName = "rg1"
    
    # SQL database copy (the new db to be created)
    # --------------------------------------------
    $copyDbName = "db1_copy"
    $copyDbServerName = "server2"
    $copyDbResourceGroupName = "rg2"
    
    # Copy a database to the same server
    # ----------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyDatabaseName $copyDbName
    
    # Copy a database to a different server
    # -------------------------------------
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -CopyDatabaseName $copyDbName
    
    # Copy a database into an elastic database pool
    # ---------------------------------------------
    $poolName = "pool1"
    
    New-AzureRmSqlDatabaseCopy -ResourceGroupName $sourceDbResourceGroupName -ServerName $sourceDbServerName -DatabaseName $sourceDbName -CopyResourceGroupName $copyDbResourceGroupName -CopyServerName $copyDbServerName -ElasticPoolName $poolName -CopyDatabaseName $copyDbName



    

## <a name="next-steps"></a>Próximos passos

- Consulte o artigo [copiar uma base de dados do Azure SQL](sql-database-copy.md) para uma descrição geral de copiar uma base de dados do SQL Azure.
- Consulte o artigo [copiar uma base de dados do Azure SQL através do portal Azure](sql-database-copy-portal.md) para copiar uma base de dados através do portal Azure.
- Consulte o artigo [copiar uma base de dados do Azure SQL utilizando o T-SQL](sql-database-copy-transact-sql.md) para copiar uma base de dados utilizando o Transact-SQL.
- Veja [como gerir a segurança da base de dados do Azure SQL após a recuperação de falhas](sql-database-geo-replication-security-config.md) para saber mais sobre a gestão de utilizadores e inícios de sessão ao copiar uma base de dados para um servidor lógico diferente.


## <a name="additional-resources"></a>Recursos adicionais

- [Novo AzureRmSqlDatabase](https://msdn.microsoft.com/library/mt603644.aspx)
- [Get-AzureRmSqlDatabaseActivity](https://msdn.microsoft.com/library/mt603687.aspx)
- [Gerir os inícios de sessão](sql-database-manage-logins.md)
- [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md)
- [Exportar a base de dados para um BACPAC](sql-database-export.md)
- [Descrição geral de continuidade do negócio](sql-database-business-continuity.md)
- [Documentação de base de dados SQL](https://azure.microsoft.com/documentation/services/sql-database/)
- [Referência do Cmdlet de PowerShell do Azure SQL da base de dados](https://msdn.microsoft.com/library/mt574084.aspx)
