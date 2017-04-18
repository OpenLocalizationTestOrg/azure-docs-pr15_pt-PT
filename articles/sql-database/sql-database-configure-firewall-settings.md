<properties
    pageTitle="Configurar uma regra da firewall ao nível do servidor de base de dados SQL | Microsoft Azure"
    description="Saiba como configurar a firewall para endereços IP aceder Azure SQL server."
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
    ms.author="rickbyh;carlrab"/>


# <a name="configure-an-azure-sql-database-server-level-firewall-rule-using-the-azure-portal"></a>Configurar uma regra de firewall de nível do servidor de base de dados do Azure SQL utilizando o Portal do Azure


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)

Azure SQL server utiliza regras de firewall para permitir ligações para os servidores e bases de dados. Pode definir as definições da firewall ao nível do servidor e ao nível da base de dados para o modelo global ou de uma base de dados de utilizador no seu Azure SQL server lógico server para seletivamente permitir o acesso à base de dados. Este tópico aborda as regras da firewall ao nível do servidor.

> [AZURE.IMPORTANT] Para permitir que as aplicações a partir do Azure ligar ao seu servidor Azure SQL, tem de estar ativadas ligações Azure. Para compreender como a firewall regras de trabalho, consulte o artigo [como configurar uma firewall de servidor do Azure SQL \- descrição geral](sql-database-firewall-configure.md). Se estiver a fazer ligações dentro dos limites do Azure na nuvem, poderá ter de abrir algumas portas TCP adicionais. Para obter mais informações, consulte o artigo o **V12 da base de dados SQL: fora vs dentro** secção de [portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados SQL](sql-database-develop-direct-route-ports-adonet-v12.md)

**Recomendação:** Utilize as regras da firewall ao nível do servidor para administradores e quando tiver muitas bases de dados que têm os mesmos requisitos de acesso e não quiser perder tempo a configurar individualmente cada base de dados. A Microsoft recomenda a utilização de regras de firewall de nível de base de dados sempre que possível, para melhorar a segurança e para tornar a sua base de dados mais portátil.

[AZURE.INCLUDE [Create SQL Database database](../../includes/sql-database-create-new-server-firewall-portal.md)]

## <a name="manage-existing-server-level-firewall-rules-through-the-azure-portal"></a>Gerir regras da firewall ao nível do servidor existentes através do portal do Azure

Repita os passos para gerir as regras da firewall ao nível do servidor.

- Para adicionar o computador atual, clique em Adicionar IP de cliente.
- Para adicionar endereços IP adicionais, escreva no nome da regra, iniciar o endereço de IP e endereço de IP de fim.
- Para modificar uma regra existente, clique em qualquer um dos campos na regra e modificar.
- Para eliminar uma regra existente, Paire o cursor sobre a regra até que seja apresentado no X no final da linha. Clique em X para remover a regra.

Clique em **Guardar** para guardar as alterações.

## <a name="next-steps"></a>Próximos passos

Para saber como um artigo sobre como utilizar Transact-SQL para criar as regras da firewall ao nível do servidor e ao nível da base de dados, consulte o artigo [regras da firewall ao nível do servidor e ao nível da base de dados de configurar a base de dados de SQL do Azure utilizar T-SQL](sql-database-configure-firewall-settings-tsql.md). 

Para como para artigos sobre como criar regras de firewall ao nível do servidor utilizar outros métodos, consulte o artigo: 

- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do PowerShell](sql-database-configure-firewall-settings-powershell.md)
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

 
