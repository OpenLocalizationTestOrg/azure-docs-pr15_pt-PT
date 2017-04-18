<properties
    pageTitle="Azure base de dados SQL server e nível de base de dados as regras de firewall utilizando o T-SQL | Microsoft Azure"
    description="Saiba como configurar a firewall para endereços IP que acedem a bases de dados Azure SQL."
    services="sql-database"
    documentationCenter=""
    authors="BYHAM"
    manager="jhubbard"
    editor=""/>


<tags
    ms.service="sql-database"
    ms.workload="data-management"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article" 
    ms.date="08/30/2016"
    ms.author="rickbyh"/>


# <a name="configure-azure-sql-database-server-level-and-database-level-firewall-rules-using-t-sql"></a>Configurar regras de firewall ao nível do servidor e ao nível da base de dados de base de dados do Azure SQL utilizando o T-SQL


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de dados do Microsoft Azure SQL utiliza regras de firewall para permitir ligações para os servidores e bases de dados. Pode definir as definições da firewall ao nível do servidor e ao nível da base de dados para o modelo global ou de uma base de dados de utilizador no seu servidor de base de dados do Azure SQL para seletivamente permitir o acesso à base de dados.

> [AZURE.IMPORTANT] Para permitir que as aplicações a partir do Azure ligar ao seu servidor de base de dados, tem de estar ativadas ligações Azure. Para mais informações sobre regras de firewall e permitir ligações a partir do Azure, consulte o artigo [Firewall de base de dados do SQL Azure](sql-database-firewall-configure.md). Se estiver a fazer ligações dentro dos limites do Azure na nuvem, poderá ter de abrir algumas portas TCP adicionais. Para obter mais informações, consulte o artigo o **V12 da base de dados SQL: fora vs dentro** secção de [portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)


## <a name="server-level-firewall-rules"></a>Regras da firewall ao nível do servidor

Apenas o principal ao nível do servidor de início de sessão ou o administrador do Azure Active Directory pode criar uma regra de nível do servidor firewall utilizando Transact-SQL.

1. Inicie uma janela de consulta e se ligar à base de dados principal virtual utilizando o SQL Server Management Studio.
2. As regras da firewall ao nível do servidor podem ser selecionadas, criadas, actualizadas ou eliminadas da dentro da janela de consulta.
3. Para criar ou atualizar as regras da firewall ao nível do servidor, execute o `sp_set_firewall_rule` procedimento armazenado. O exemplo seguinte permite um intervalo de endereços IP do servidor Contoso.<br/>Comece por ver quais as regras já existem.

        SELECT * FROM sys.firewall_rules ORDER BY name;

    Em seguida, adicione uma regra de firewall.

        EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
            @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'

    Para eliminar uma regra de firewall ao nível do servidor, execute o procedimento sp_delete_firewall_rule armazenados. O exemplo seguinte elimina a regra denominada ContosoFirewallRule.
 
        EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
 
 Para mais informações sobre estes procedimentos armazenados, consulte o artigo [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx) e [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx).

## <a name="database-level-firewall-rules"></a>Regras da firewall do nível de base de dados

Apenas um utilizador de base de dados com a permissão de **controlo** da base de dados (por exemplo, o proprietário da base de dados) pode criar uma regra de firewall de nível de base de dados.

1. Depois de criar uma firewall de nível do servidor para o seu endereço IP, inicie uma janela de consulta através do portal do Classic ou através de SQL Server Management Studio.
2. Ligar à base de dados para o qual pretende criar uma regra de firewall de nível de base de dados.

    Para criar um novo ou atualizar uma regra de firewall de nível de base de dados existente, execute o `sp_set_database_firewall_rule` procedimento armazenado. O exemplo seguinte cria uma nova regra de firewall denominada ContosoFirewallRule.
 
        EXEC sp_set_database_firewall_rule @name = N'ContosoFirewallRule', 
            @start_ip_address = '192.168.1.11', @end_ip_address = '192.168.1.11'
 
    Para eliminar uma regra de firewall de nível de base de dados existente, execute o `sp_delete_database_firewall_rule` procedimento armazenado. O exemplo seguinte elimina a regra denominada ContosoFirewallRule.
`
   EXECUÇÃO sp_delete_database_firewall_rule @name = N'ContosoFirewallRule'

Para mais informações sobre estes procedimentos armazenados, consulte o artigo [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx) e [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx).

## <a name="next-steps"></a>Próximos passos

Para como para artigos sobre como criar regras de firewall ao nível do servidor utilizar outros métodos, consulte o artigo: 

- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL utilizando o Portal do Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL utilizar a API REST](sql-database-configure-firewall-settings-rest.md)

Para obter um tutorial sobre como criar uma base de dados, consulte o artigo [criar uma base de dados do SQL em minutos através do portal Azure](sql-database-get-started.md).
Para obter ajuda sobre como ligar a uma base de dados do Azure SQL a partir abrir origem ou aplicações de terceiros, consulte o artigo [Guia de cliente amostras de código para a base de dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para bases de dados, consulte o artigo [Gerir a segurança da base de dados do access e inicie sessão](https://msdn.microsoft.com/library/azure/ee336235.aspx).


## <a name="additional-resources"></a>Recursos adicionais

- [Proteger a sua base de dados](sql-database-security.md)
- [Centro de segurança do motor de base de dados do SQL Server e base de dados Azure SQL](https://msdn.microsoft.com/library/bb510589)
