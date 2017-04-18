<properties
    pageTitle="Regras de firewall de ao nível do servidor de base de dados SQL Azure utilizar a API REST | Microsoft Azure"
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


#  <a name="configure-azure-sql-database-server-level-firewall-rules-using-the-rest-api"></a>Configurar regras de firewall de nível do servidor de base de dados do Azure SQL utilizar a API REST


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de dados do Microsoft Azure SQL utiliza regras de firewall para permitir ligações para os servidores e bases de dados. Pode definir as definições da firewall ao nível do servidor e ao nível da base de dados para o modelo global ou de uma base de dados de utilizador no seu servidor de base de dados do Azure SQL para seletivamente permitir o acesso à base de dados.

> [AZURE.IMPORTANT] Para permitir que as aplicações a partir do Azure ligar ao seu servidor de base de dados, tem de estar ativadas ligações Azure. Para mais informações sobre regras de firewall e permitir ligações a partir do Azure, consulte o artigo [Firewall de base de dados do SQL Azure](sql-database-firewall-configure.md). Se estiver a fazer ligações dentro dos limites do Azure na nuvem, poderá ter de abrir algumas portas TCP adicionais. Para obter mais informações, consulte o artigo o **V12 da base de dados SQL: fora vs dentro** secção de [portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="manage-server-level-firewall-rules-through-rest-api"></a>Gerir regras da firewall ao nível do servidor através de REST API
1. Gerir regras de firewall através de REST API têm de ser autenticada. Para obter informações, consulte o artigo [Guia do programador autorização com a API do Gestor de recursos do Azure](../resource-manager-api-authentication.md).
2. Regras de nível do servidor podem ser criadas, actualizado ou eliminadas de utilizar a REST API

    Para criar ou atualizar uma regra de firewall ao nível do servidor, execute o método de colocar utilizando o seguinte:
 
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}
    
    Corpo do pedido

        {
         "properties": { 
            "startIpAddress": "{start-ip-address}", 
            "endIpAddress": "{end-ip-address}
            }
        } 
 

    Para remover uma regra da firewall ao nível do servidor existente, execute o método DELETE utilizando o seguinte:
     
        https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Sql/servers/{server-name}/firewallRules/{rule-name}?api-version={api-version}


## <a name="manage-firewall-rules-using-the-rest-api"></a>Gerir regras de firewall utilizar a API REST

* [Criar ou actualizar regra de Firewall](https://msdn.microsoft.com/library/azure/mt445501.aspx)
* [Eliminar a regra de Firewall](https://msdn.microsoft.com/library/azure/mt445502.aspx)
* [Obter a regra de Firewall](https://msdn.microsoft.com/library/azure/mt445503.aspx)
* [Lista todas as regras de Firewall](https://msdn.microsoft.com/library/azure/mt604478.aspx)
 
## <a name="next-steps"></a>Próximos passos

Para saber como um artigo sobre como utilizar Transact-SQL para criar as regras da firewall ao nível do servidor e ao nível da base de dados, consulte o artigo [regras da firewall ao nível do servidor e ao nível da base de dados de configurar a base de dados de SQL do Azure utilizar T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Para como para artigos sobre como criar regras de firewall ao nível do servidor utilizar outros métodos, consulte o artigo: 

- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do portal Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do PowerShell](sql-database-configure-firewall-settings-powershell.md)

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

 
