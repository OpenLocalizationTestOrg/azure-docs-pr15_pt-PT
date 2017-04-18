<properties
    pageTitle="Criar um novo conjunto de dados da base de dados flexível com PowerShell | Microsoft Azure"
    description="Saiba como utilizar o PowerShell para fora de escala de base de dados do SQL Azure recursos através da criação de um conjunto de base de dados flexível dimensionáveis para gerir a várias bases de dados."
    services="sql-database"
    documentationCenter=""
    authors="srinia"
    manager="jhubbard"
    editor=""/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="05/27/2016"
    ms.author="srinia"/>

# <a name="create-a-new-elastic-database-pool-with-powershell"></a>Criar um novo conjunto de base de dados flexível com PowerShell

> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)


Saiba como criar um [conjunto de base de dados flexível](sql-database-elastic-pool.md) utilizando os cmdlets do PowerShell. 

Para códigos de erro comuns, consulte o artigo [códigos de erro SQL para aplicações de cliente da base de dados SQL: erro de ligação e outros problemas de base de dados](sql-database-develop-error-messages.md).

> [AZURE.NOTE] Conjuntos de dados flexível são das versões ficará disponível (DG) em todas as regiões Azure exceto América do Norte Central-nos e Índia Ocidental onde está atualmente na pré-visualização.  Serão fornecida das versões DG de conjuntos de dados flexível nas seguintes regiões mais cedo possível. Além disso, conjuntos de dados flexível não suportam atualmente bases de dados utilizando [na memória OLTP ou análise na memória](sql-database-in-memory.md).


Tem de estar em execução Azure PowerShell 1.0 ou superior. Para obter informações detalhadas, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

## <a name="create-a-new-pool"></a>Criar um novo conjunto de dados

O cmdlet [Novo AzureRmSqlElasticPool](https://msdn.microsoft.com/library/azure/mt619378.aspx) cria um novo conjunto de dados. Os valores para eDTU por conjunto, mínimo e máximo Dtus são limitados pelo valor camada do serviço (básica, padrão ou premium). Consulte o artigo [limites de armazenamento e eDTU para flexível agrupamentos e flexível bases de dados](sql-database-elastic-pool.md#eDTU-and-storage-limits-for-elastic-pools-and-elastic-databases).

    New-AzureRmSqlElasticPool -ResourceGroupName "resourcegroup1" -ServerName "server1" -ElasticPoolName "elasticpool1" -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100


## <a name="create-a-new-elastic-database-in-a-pool"></a>Criar uma nova base de dados flexível num conjunto de dados

Utilize o cmdlet [Novo AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619339.aspx) e defina o parâmetro **ElasticPoolName** ao agrupamento de destino. Para mover uma base de dados existente num conjunto de dados, consulte o artigo [Mover uma base de dados para um conjunto de dados flexível](sql-database-elastic-pool-manage-powershell.md#Move-a-database-into-an-elastic-pool).

    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroup1" -ServerName "server1" -DatabaseName "database1" -ElasticPoolName "elasticpool1"

## <a name="create-a-pool-and-populate-it-with-multiple-new-databases"></a>Criar um conjunto de dados e preencha-a com várias novas bases de dados 

Criação de um grande número de bases de dados de um conjunto de dados pode demorar tempo quando terminar, utilizando o portal ou os cmdlets do PowerShell que criar apenas um único base de dados de cada vez. Para automatizar a criação para um novo conjunto de dados, consulte o artigo [CreateOrUpdateElasticPoolAndPopulate ](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).   

## <a name="example-create-a-pool-using-powershell"></a>Exemplo: criar um conjunto de dados através do PowerShell 

Este script cria um novo grupo de recursos Azure e um novo servidor. Quando lhe for pedido, fornece um nome de utilizador do administrador e a palavra-passe para o novo servidor (não as credenciais Azure).

    $subscriptionId = '<your Azure subscription id>'
    $resourceGroupName = '<resource group name>'
    $location = '<datacenter location>'
    $serverName = '<server name>'
    $poolName = '<pool name>'
    $databaseName = '<database name>'

    Login-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $subscriptionId

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $location -ServerVersion "12.0"
    New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName "rule1" -StartIpAddress "192.168.0.198" -EndIpAddress "192.168.0.199"

    New-AzureRmSqlElasticPool -ResourceGroupName $resourceGroupName -ServerName $serverName -ElasticPoolName $poolName -Edition "Standard" -Dtu 400 -DatabaseDtuMin 10 -DatabaseDtuMax 100

    New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -ElasticPoolName $poolName -MaxSizeBytes 10GB



## <a name="next-steps"></a>Próximos passos

- [Gerir o seu conjunto](sql-database-elastic-pool-manage-powershell.md)
- [Criar tarefas de flexível](sql-database-elastic-jobs-overview.md) Tarefas de flexível permitem-lhe executar scripts de T-SQL contra qualquer número de bases de dados no conjunto.
- [Escala de saída com a base de dados do SQL Azure](sql-database-elastic-scale-introduction.md): utilizar ferramentas de base de dados flexível para fora de escala.

