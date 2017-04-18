<properties
    pageTitle="Gerir a base de dados do Azure SQL com PowerShell | Microsoft Azure"
    description="Gestão de base de dados SQL Azure com PowerShell."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="monicar"/>

<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="sstein"/>

# <a name="manage-azure-sql-database-with-powershell"></a>Gerir a base de dados do Azure SQL com PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-manage-portal.md)
- [O Transact-SQL (SSMS)](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-manage-powershell.md)

Este tópico mostra os cmdlets do PowerShell, que são utilizadas para executarem várias tarefas de base de dados do Azure SQL. Para uma lista completa, consulte [Cmdlets de base de dados do SQL Azure](https://msdn.microsoft.com/library/mt574084.aspx).


## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos para os nossos base de dados SQL e recursos Azure relacionados com o cmdlet [AzureRmResourceGroup novo](https://msdn.microsoft.com/library/azure/mt759837.aspx) .

```
$resourceGroupName = "resourcegroup1"
$resourceGroupLocation = "northcentralus"
New-AzureRmResourceGroup -Name $resourceGroupName -Location $resourceGroupLocation
```

Para mais informações, consulte o artigo [Utilizar o PowerShell Azure com o Gestor de recursos do Azure](../powershell-azure-resource-manager.md).
Para obter um script de exemplo, consulte o artigo [criar uma base de dados do SQL PowerShell script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).

## <a name="create-a-sql-database-server"></a>Criar um servidor de base de dados SQL

Crie um servidor de base de dados SQL com o cmdlet [AzureRmSqlServer novo](https://msdn.microsoft.com/library/azure/mt603715.aspx) . Substitua o nome do seu servidor *servidor1* . Nomes de servidor tem de ser exclusivos por todos os servidores de base de dados do Azure SQL. Se o nome do servidor já é redirecionado, obterá um erro. Este comando poderá demorar vários minutos a concluir. O grupo de recursos já tem de existir na sua subscrição.

```
$resourceGroupName = "resourcegroup1"

$sqlServerName = "server1"
$sqlServerVersion = "12.0"
$sqlServerLocation = "northcentralus"
$serverAdmin = "loginname"
$serverPassword = "password" 
$securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
$creds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    

$sqlServer = New-AzureRmSqlServer -ServerName $sqlServerName `
 -SqlAdministratorCredentials $creds -Location $sqlServerLocation `
 -ResourceGroupName $resourceGroupName -ServerVersion $sqlServerVersion
```

Para mais informações, consulte o artigo [o que é a base de dados SQL](sql-database-technical-overview.md). Para obter um script de exemplo, consulte o artigo [criar uma base de dados do SQL PowerShell script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-server-firewall-rule"></a>Criar uma regra de firewall do servidor de base de dados SQL

Crie uma regra de firewall para aceder ao servidor com o cmdlet [AzureRmSqlServerFirewallRule novo](https://msdn.microsoft.com/library/azure/mt603860.aspx) . Execute o seguinte comando, substituir os endereços IP de início e de fim com os valores válidos para o seu cliente. O grupo de recursos e o servidor tem de existir na sua subscrição.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$firewallRuleName = "firewallrule1"
$firewallStartIp = "0.0.0.0"
$firewallEndIp = "255.255.255.255"

New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -FirewallRuleName $firewallRuleName `
 -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
```

Para permitir o acesso outros serviços Azure ao seu servidor, crie uma regra de firewall e defina ambos os `-StartIpAddress` e `-EndIpAddress` para **0.0.0.0**. Esta regra de firewall especial que permite que todo o tráfego Azure aceder ao servidor.

Para mais informações, consulte o artigo [Firewall de base de dados do SQL Azure](https://msdn.microsoft.com/library/azure/ee621782.aspx). Para obter um script de exemplo, consulte o artigo [criar uma base de dados do SQL PowerShell script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="create-a-sql-database-blank"></a>Criar uma base de dados do SQL (em branco)

Crie uma base de dados com o cmdlet [AzureRmSqlDatabase novo](https://msdn.microsoft.com/library/azure/mt619339.aspx) . O grupo de recursos e o servidor tem de existir na sua subscrição. 

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Standard"
$databaseServiceLevel = "S0"

$currentDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para mais informações, consulte o artigo [o que é a base de dados SQL](sql-database-technical-overview.md). Para obter um script de exemplo, consulte o artigo [criar uma base de dados do SQL PowerShell script](sql-database-get-started-powershell.md#create-a-sql-database-powershell-script).


## <a name="change-the-performance-level-of-a-sql-database"></a>Alterar o nível de desempenho de uma base de dados SQL

Dimensione a base de dados para cima ou para baixo com o cmdlet [Set-AzureRmSqlDatabase](https://msdn.microsoft.com/library/azure/mt619433.aspx) . O grupo de recursos, servidor e base de dados tem de existir na sua subscrição. Definir o `-RequestedServiceObjectiveName` para um único espaço (como o seguinte fragmento) para camada básica. Defini-lo *S0*, *S1*, *P1*, *P6*, etc., como no exemplo anterior para outras camadas.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

$databaseName = "database1"
$databaseEdition = "Basic"
$databaseServiceLevel = " "

Set-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName `
 -ServerName $sqlServerName -DatabaseName $databaseName `
 -Edition $databaseEdition -RequestedServiceObjectiveName $databaseServiceLevel
```

Para obter mais informações, consulte o artigo [Opções de base de dados SQL e o desempenho: compreender o que está disponível em cada camada de serviço](sql-database-service-tiers.md). Para obter um script de exemplo, consulte o artigo [script do PowerShell de exemplo para alterar o nível de camadas e o desempenho do serviço da base de dados SQL](sql-database-scale-up-powershell.md#sample-powershell-script-to-change-the-service-tier-and-performance-level-of-your-sql-database).

## <a name="copy-a-sql-database-to-the-same-server"></a>Copiar uma base de dados do SQL no mesmo servidor

Copie uma base de dados do SQL no mesmo servidor com o cmdlet [AzureRmSqlDatabaseCopy novo](https://msdn.microsoft.com/library/azure/mt603644.aspx) . Definir o `-CopyServerName` e `-CopyResourceGroupName` para os mesmos valores como o seu grupo de servidor e recurso da base de dados de origem.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

$copyDatabaseName = "database1_copy"
$copyServerName = $sqlServerName
$copyResourceGroupName = $resourceGroupName

New-AzureRmSqlDatabaseCopy -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName `
 -CopyDatabaseName $copyDatabaseName -CopyServerName $sqlServerName `
 -CopyResourceGroupName $copyResourceGroupName
```

Para mais informações, consulte o artigo [copiar uma base de dados do SQL Azure](sql-database-copy.md). Para obter um script de exemplo, consulte o artigo [copiar uma base de dados do SQL PowerShell script](sql-database-copy-powershell.md#example-powershell-script).


## <a name="delete-a-sql-database"></a>Eliminar uma base de dados SQL

Elimine uma base de dados SQL com o cmdlet [AzureRmSqlDatabase remover](https://msdn.microsoft.com/library/azure/mt619368.aspx) . O grupo de recursos, servidor e base de dados tem de existir na sua subscrição.

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"
$databaseName = "database1"

Remove-AzureRmSqlDatabase -DatabaseName $databaseName `
 -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="delete-a-sql-database-server"></a>Eliminar um servidor de base de dados SQL

Elimine um servidor com o cmdlet [AzureRmSqlServer remover](https://msdn.microsoft.com/library/azure/mt603488.aspx) .

```
$resourceGroupName = "resourcegroup1"
$sqlServerName = "server1"

Remove-AzureRmSqlServer -ServerName $sqlServerName -ResourceGroupName $resourceGroupName
```

## <a name="create-and-manage-elastic-database-pools-using-powershell"></a>Criar e gerir conjuntos de dados da base de dados flexível através do PowerShell

Para obter detalhes sobre como criar conjuntos de base de dados flexível através do PowerShell, consulte o artigo [criar um novo conjunto de base de dados flexível com PowerShell](sql-database-elastic-pool-create-powershell.md).

Para obter detalhes sobre a gestão de agrupamentos de base de dados flexível através do PowerShell, consulte o artigo [Monitor e gerir um conjunto de base de dados flexível com PowerShell](sql-database-elastic-pool-manage-powershell.md).



## <a name="related-information"></a>Informações relacionadas

- [Cmdlets de base de dados do Azure SQL](https://msdn.microsoft.com/library/azure/mt574084.aspx)
- [Referência do Cmdlet Azure](https://msdn.microsoft.com/library/azure/dn708514.aspx)
