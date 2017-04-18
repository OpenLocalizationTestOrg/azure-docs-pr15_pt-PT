<properties
    pageTitle="Configurar as regras da firewall ao nível do servidor de base de dados do Azure SQL utilizando o PowerShell | Microsoft Azure"
    description="Saiba como configurar a firewall para endereços IP que acedem a bases de dados Azure SQL."
    services="sql-database"
    documentationCenter=""
    authors="stevestein"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/09/2016"
    ms.author="sstein"/>


# <a name="configure-azure-sql-database-server-level-firewall-rules-by-using-powershell"></a>Configurar as regras da firewall ao nível do servidor de base de dados do Azure SQL utilizando o PowerShell


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de dados SQL Azure utiliza regras de firewall para permitir ligações para os servidores e bases de dados. Pode definir as definições da firewall ao nível do servidor e ao nível da base de dados para a base de dados principal ou uma base de dados de utilizador no seu servidor de base de dados SQL para seletivamente permitir o acesso à base de dados.

> [AZURE.IMPORTANT] Para permitir que as aplicações a partir do Azure ligar ao seu servidor de base de dados, tem de estar ativadas ligações Azure. Para mais informações sobre regras de firewall e permitir ligações a partir do Azure, consulte o artigo [Firewall de base de dados do SQL Azure](sql-database-firewall-configure.md). Se estiver a fazer ligações dentro dos limites do Azure na nuvem, poderá ter de abrir algumas portas TCP adicionais. Para obter mais informações, consulte o artigo o "V12 da base de dados SQL: fora vs dentro" secção de [portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).


[AZURE.INCLUDE [Start your PowerShell session](../../includes/sql-database-powershell.md)]

## <a name="create-server-firewall-rules"></a>Criar regras de firewall de servidor

Firewall ao nível do servidor podem ser criadas regras, atualizados e eliminadas utilizando o Azure PowerShell.

Para criar uma nova regra da firewall ao nível do servidor, execute o [novo-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx) cmdlet. O exemplo seguinte permite um intervalo de endereços IP do servidor Contoso.

    New-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' -ServerName 'Contoso' -FirewallRuleName "ContosoFirewallRule" -StartIpAddress '192.168.1.1' -EndIpAddress '192.168.1.10'       

Para modificar uma regra de nível do servidor firewall existente, execute o [conjunto-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx) cmdlet. O exemplo seguinte altera o intervalo de endereços IP aceitáveis para a regra denominada ContosoFirewallRule.

    Set-AzureRmSqlServerFirewallRule -ResourceGroupName 'resourcegroup1' –StartIPAddress 192.168.1.4 –EndIPAddress 192.168.1.10 –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'

Para eliminar uma regra da firewall ao nível do servidor existente, execute o [remover-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx) cmdlet. O exemplo seguinte elimina a regra denominada ContosoFirewallRule.

    Remove-AzureRmSqlServerFirewallRule –RuleName 'ContosoFirewallRule' –ServerName 'Contoso'


## <a name="manage-firewall-rules-by-using-powershell"></a>Gerir regras da firewall ao utilizar o PowerShell

Também pode utilizar o PowerShell para gerir regras de firewall. Para obter mais informações, consulte os seguintes tópicos:

* [Novo AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603860(v=azure.300\).aspx)
* [Remover AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603588(v=azure.300\).aspx)
* [Conjunto AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603789(v=azure.300\).aspx)
* [Get-AzureRmSqlServerFirewallRule] (https://msdn.microsoft.com/library/azure/mt603586(v=azure.300\).aspx)


## <a name="next-steps"></a>Próximos passos

Para obter informações sobre como utilizar Transact-SQL para criar regras de firewall ao nível do servidor e ao nível da base de dados, consulte o artigo [regras da firewall ao nível do servidor e ao nível da base de dados de configurar a base de dados de SQL do Azure utilizando o T-SQL](sql-database-configure-firewall-settings-tsql.md).

Para obter informações sobre como criar regras de firewall de nível do servidor que utilizem outros métodos, consulte o artigo:

- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do portal Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL utilizar a API REST](sql-database-configure-firewall-settings-rest.md)

Para obter um tutorial sobre como criar uma base de dados, consulte o artigo [criar uma base de dados do SQL em minutos através do portal Azure](sql-database-get-started.md).
Para obter ajuda sobre como ligar a uma base de dados do Azure SQL a partir abrir origem ou aplicações de terceiros, consulte o artigo [Guia de cliente amostras de código para a base de dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para bases de dados, consulte o artigo [Gerir a segurança da base de dados do access e inicie sessão](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionais

- [Proteger a sua base de dados](sql-database-security.md)
- [Centro de segurança do motor de base de dados do SQL Server e base de dados Azure SQL](https://msdn.microsoft.com/library/bb510589)


<!--Image references-->
[1]: ./media/sql-database-configure-firewall-settings/AzurePortalBrowseForFirewall.png
[2]: ./media/sql-database-configure-firewall-settings/AzurePortalFirewallSettings.png
<!--anchors-->
