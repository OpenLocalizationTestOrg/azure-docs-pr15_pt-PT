<properties
    pageTitle="Configuração da nova base de dados SQL com o PowerShell | Microsoft Azure"
    description="Saiba como criar uma base de dados do SQL com PowerShell. Tarefas comuns de configuração da base de dados podem ser geridas durante os cmdlets do PowerShell."
    keywords="Criar nova base de dados sql, o programa de configuração da base de dados"
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="sql-database"
    ms.devlang="NA"
    ms.topic="hero-article"
    ms.tgt_pltfrm="powershell"
    ms.workload="data-management"
    ms.date="08/19/2016"
    ms.author="sstein"/>

# <a name="create-a-sql-database-and-perform-common-database-setup-tasks-with-powershell-cmdlets"></a>Criar uma base de dados do SQL e executar tarefas comuns de configuração de base de dados com os cmdlets do PowerShell


> [AZURE.SELECTOR]
- [Portal do Azure](sql-database-get-started.md)
- [PowerShell](sql-database-get-started-powershell.md)
- [C#](sql-database-get-started-csharp.md)



Saiba como criar uma base de dados do SQL utilizando os cmdlets do PowerShell. (Para a criação de flexível bases de dados, consulte o artigo [criar um novo conjunto de base de dados flexível com PowerShell](sql-database-elastic-pool-create-powershell.md).)


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="database-setup-create-a-resource-group-server-and-firewall-rule"></a>Configuração da base de dados: criar um grupo de recursos, servidor e regras de firewall

Assim que tiver acesso ao executar os cmdlets do contra a sua subscrição do Azure selecionada, o próximo passo consiste em estabelecer o grupo de recursos que contém o servidor onde a base de dados será criado. Pode editar o comando seguinte para utilizar qualquer localização válida que escolher. Executar **(Get-AzureRmLocation | Objeto de onde {$_. Fornecedores - eq "Microsoft.Sql"}). Localização** para obter uma lista de localizações válidas.

Execute o seguinte comando para criar um grupo de recursos:

    New-AzureRmResourceGroup -Name "resourcegroupsqlgsps" -Location "westus"


### <a name="create-a-server"></a>Criar um servidor

Bases de dados SQL são criados no interior de servidores de base de dados do Azure SQL. Executar a [novo-AzureRmSqlServer] (https://msdn.microsoft.com/library/azure/mt603715(v=azure.300\).aspx) para criar um servidor. O nome do seu servidor tem de ser exclusivo para todos os servidores de base de dados do Azure SQL. Se o nome do servidor já é redirecionado, obterá um erro. Também salientar é que este comando poderá demorar vários minutos a concluir. Pode editar o comando para utilizar qualquer localização válida que escolher, mas deve utilizar a mesma localização que utilizou para o grupo de recursos que criou no passo anterior.

    New-AzureRmSqlServer -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -Location "westus" -ServerVersion "12.0"

Quando executa este comando, lhe for pedido para o seu nome de utilizador e palavra-passe. Não introduza as suas credenciais Azure. Em vez disso, introduza o nome de utilizador e palavra-passe para criar como o administrador do servidor. O script na parte inferior deste artigo mostra como definir as credenciais de servidor no código.

São apresentados os detalhes de servidor depois do servidor é criado com êxito.

### <a name="configure-a-server-firewall-rule-to-allow-access-to-the-server"></a>Configurar uma regra de firewall do servidor para permitir o acesso ao servidor

Para aceder ao servidor, é necessário estabelecer uma regra de firewall. Executar a [novo-AzureRmSqlServerFirewallRule] (comando https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx), substituir os endereços IP de início e de fim com os valores válidos para o seu computador.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -FirewallRuleName "rule1" -StartIpAddress "192.168.0.0" -EndIpAddress "192.168.0.0"

Os detalhes de regra de firewall aparecem depois da regra é criada com êxito.

Para permitir que outros serviços do Azure aceder ao servidor, adicione uma regra de firewall e defina StartIpAddress tanto EndIpAddress para 0.0.0.0. Esta regra permite o tráfego Azure a partir de qualquer subscrição Azure para aceder ao servidor.

Para mais informações, consulte o artigo [Firewall de base de dados do SQL Azure](sql-database-firewall-configure.md).


## <a name="create-a-sql-database"></a>Criar uma base de dados SQL

Agora tem um grupo de recursos, um servidor e uma regra de firewall configurado de forma a pode aceder ao servidor.

A seguinte [AzureRmSqlDatabase novo] (comando https://msdn.microsoft.com/library/azure/mt619339(v=azure.300\).aspx) cria uma base de dados do SQL (em branco) na camada serviço padrão, com um nível de desempenho S1:


    New-AzureRmSqlDatabase -ResourceGroupName "resourcegroupsqlgsps" -ServerName "server1" -DatabaseName "database1" -Edition "Standard" -RequestedServiceObjectiveName "S1"


Os detalhes de base de dados são apresentados depois da base de dados for criado com êxito.

## <a name="create-a-sql-database-powershell-script"></a>Criar uma base de dados do SQL PowerShell script

O seguinte script do PowerShell cria uma base de dados do SQL e todos os recursos dependentes. Substituir tudo `{variables}` com valores específicos para a sua subscrição e dos recursos (remover quando definir os valores a **{}** ).

    # Sign in to Azure and set the subscription to work with
    $SubscriptionId = "{subscription-id}"

    Add-AzureRmAccount
    Set-AzureRmContext -SubscriptionId $SubscriptionId

    # CREATE A RESOURCE GROUP
    $resourceGroupName = "{group-name}"
    $rglocation = "{Azure-region}"
    
    New-AzureRmResourceGroup -Name $resourceGroupName -Location $rglocation
    
    # CREATE A SERVER
    $serverName = "{server-name}"
    $serverVersion = "12.0"
    $serverLocation = "{Azure-region}"
    
    $serverAdmin = "{server-admin}"
    $serverPassword = "{server-password}" 
    $securePassword = ConvertTo-SecureString –String $serverPassword –AsPlainText -Force
    $serverCreds = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $serverAdmin, $securePassword
    
    $sqlDbServer = New-AzureRmSqlServer -ResourceGroupName $resourceGroupName -ServerName $serverName -Location $serverLocation -ServerVersion $serverVersion -SqlAdministratorCredentials $serverCreds
    
    # CREATE A SERVER FIREWALL RULE
    $ip = (Test-Connection -ComputerName $env:COMPUTERNAME -Count 1 -Verbose).IPV4Address.IPAddressToString
    $firewallRuleName = '{rule-name}'
    $firewallStartIp = $ip
    $firewallEndIp = $ip
    
    $fireWallRule = New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourceGroupName -ServerName $serverName -FirewallRuleName $firewallRuleName -StartIpAddress $firewallStartIp -EndIpAddress $firewallEndIp
    
    
    # CREATE A SQL DATABASE
    $databaseName = "{database-name}"
    $databaseEdition = "{Standard}"
    $databaseSlo = "{S0}"
    
    $sqlDatabase = New-AzureRmSqlDatabase -ResourceGroupName $resourceGroupName -ServerName $serverName -DatabaseName $databaseName -Edition $databaseEdition -RequestedServiceObjectiveName $databaseSlo
    
   
    # REMOVE ALL RESOURCES THE SCRIPT JUST CREATED
    #Remove-AzureRmResourceGroup -Name $resourceGroupName






## <a name="next-steps"></a>Próximos passos
Depois de criar uma base de dados do SQL e efetuar tarefas de configuração da base de dados básicas, está pronto para o seguinte:

- [Gerir a base de dados SQL com PowerShell](sql-database-manage-powershell.md)
- [Ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL de exemplo](sql-database-connect-query-ssms.md)


## <a name="additional-resources"></a>Recursos adicionais

- [Cmdlets da base de dados azure SQL] (https://msdn.microsoft.com/library/azure/mt574084(v=azure.300\).aspx)
- [Base de dados Azure SQL](https://azure.microsoft.com/documentation/services/sql-database/)
