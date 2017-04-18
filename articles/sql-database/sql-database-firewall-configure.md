<properties
   pageTitle="Configurar uma descrição geral do SQL server firewall | Microsoft Azure"
   description="Saiba como configurar uma firewall de base de dados SQL com as regras da firewall ao nível do servidor e ao nível da base de dados para gerir o acesso."
   keywords="firewall de base de dados"
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor="cgronlun"
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="09/14/2016"
   ms.author="rickbyh"/>

# <a name="configure-azure-sql-database-firewall-rules---overview"></a>Configurar regras de firewall de base de dados do Azure SQL \- descrição geral


> [AZURE.SELECTOR]
- [Descrição geral](sql-database-firewall-configure.md)
- [Portal do Azure](sql-database-configure-firewall-settings.md)
- [TSQL](sql-database-configure-firewall-settings-tsql.md)
- [PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [REST API](sql-database-configure-firewall-settings-rest.md)


Base de dados do Microsoft Azure SQL fornece um serviço de base de dados relacional para Azure e outras aplicações baseados na Internet. Para ajudar a proteger os seus dados, firewalls impedir que todo o acesso ao seu servidor de base de dados até que especificar quais os computadores que tem a permissão. A firewall concede acesso a bases de dados com base no endereço IP de origem de cada pedido.

Para configurar a sua firewall, pode criar regras de firewall que especificam intervalos de endereços IP aceitáveis. Pode criar regras de firewall níveis servidor e base de dados.

- **As regras da firewall ao nível do servidor:** Estas regras permitem que os clientes aceder ao seu servidor Azure SQL inteiro, ou seja, todas as bases de dados no mesmo servidor lógico. Estas regras são armazenadas na base de dados **principal** . As regras da firewall ao nível do servidor podem ser configuradas utilizando o portal ou ao utilizar instruções Transact-SQL.
- **Regras de firewall do nível de base de dados:** Estas regras permitem que os clientes aceder a bases de dados individuais dentro do seu servidor de base de dados do Azure SQL. Pode criar estas regras para cada base de dados e são armazenados nas bases de dados individuais. (Pode criar regras de firewall de nível de base de dados para a base de dados **principal** .) Estas regras podem ser útil para restringir o acesso a determinadas bases de dados (seguros) no mesmo servidor lógico. Só podem ser configuradas as regras da firewall do nível de base de dados utilizando instruções Transact-SQL.

**Recomendação:** A Microsoft recomenda a utilização de regras de firewall de nível de base de dados sempre que possível para melhorar a segurança e para tornar a sua base de dados mais portátil. Utilize as regras da firewall ao nível do servidor para administradores e quando tiver muitas bases de dados que têm os mesmos requisitos de acesso e não quiser perder tempo a configurar individualmente cada base de dados.


## <a name="firewall-overview"></a>Descrição geral do firewall

Inicialmente, todo o acesso Transact-SQL Azure SQL server está bloqueado pela firewall. Para começar a utilizar o seu servidor Azure SQL, tem de aceda ao portal do Azure e especificar um ou mais regras de firewall de nível do servidor que permitem aceder ao seu servidor Azure SQL. Utilize as regras de firewall para especificar quais intervalos de endereços IP da Internet são permitidos e, se as aplicações do Azure podem tentar ligar ao seu servidor Azure SQL.

Para seletivamente conceder acesso a apenas uma das bases de dados no seu Azure SQL server, tem de criar uma regra de nível de base de dados para a base de dados necessário. Especifique um intervalo de endereços IP para a regra de firewall de base de dados está fora do intervalo de endereço IP especificado na regra ao nível do servidor firewall e certifique-se de que o endereço IP do cliente de estação de se situar no intervalo especificado na regra de nível de base de dados.

Tentativas de ligação a partir da Internet e Azure pela primeira vez decorrer através da firewall antes que podem contactar o servidor de Azure SQL ou uma base de dados SQL, conforme mostrado no seguinte diagrama.

   ![Diagrama que descreve configuração da firewall.][1]

## <a name="connecting-from-the-internet"></a>Ligar a partir da Internet

Quando tenta um computador ligar ao seu servidor de base de dados da Internet, a firewall verifica primeiro o endereço IP de origem do pedido de acordo com o conjunto completo de regras de firewall:

- Se for o endereço IP do pedido de dentro de um dos intervalos especificados nas regras de firewall ao nível do servidor, é concedida a ligação ao seu servidor de base de dados do Azure SQL.

- Se o endereço IP do pedido de não estiver dentro de um dos intervalos especificados na regra ao nível do servidor firewall, as regras de firewall de nível de base de dados são verificadas. Se for o endereço IP do pedido de dentro de um dos intervalos especificados nas regras de firewall de nível de base de dados, a ligação é concedida apenas a base de dados que tem uma regra de nível de base de dados correspondente.

- Se o endereço IP do pedido de não estiver dentro dos intervalos especificados em qualquer uma das regras de firewall ao nível do servidor ou o nível de base de dados, o falha de pedido de ligação.

> [AZURE.NOTE] Para aceder a base de dados do SQL Azure a partir do seu computador local, certifique-se de que a firewall do seu computador local e de rede permite o envio comunicação na porta TCP 1433.


## <a name="connecting-from-azure"></a>Ligar a partir do Azure

Quando uma aplicação a partir do Azure tenta ligar ao seu servidor de base de dados, a firewall verifica que são permitidas ligações Azure. Uma firewall definição com inicial e final endereço igual a 0.0.0.0 indica que estas ligações são permitidas. Se a tentativa de ligação não é permitida, o pedido não contactar o servidor de base de dados do Azure SQL.

> [AZURE.IMPORTANT] Esta opção configura o firewall para permitir todas as ligações a partir do Azure ligações incluindo de subscrições de outros clientes. Quando selecionar esta opção, certifique-se o início de sessão e as permissões de utilizador limitar o acesso a apenas os utilizadores autorizados.

Pode ativar as ligações a partir do Azure de duas maneiras:

- No [portal do Microsoft Azure](https://portal.azure.com/), selecione a caixa de verificação **Permitir que os serviços azure ao servidor de acesso** ao criar um novo servidor.

- No [portal de clássico](http://go.microsoft.com/fwlink/p/?LinkID=161793), a partir do separador de **Configurar** um servidor, na secção **Serviços de permitidos** , clique em **Sim** para o **Microsoft Azure Services**.

## <a name="creating-the-first-server-level-firewall-rule"></a>Criar a primeira regra da firewall ao nível do servidor

A definição da firewall ao nível do servidor primeira pode ser criada utilizando o [Azure portal](https://portal.azure.com/) ou através de programação a utilizar a REST API ou Azure PowerShell. As regras da firewall do subsequentes ao nível do servidor podem ser criadas e geridas através destes métodos como através de Transact-SQL. Para melhorar o desempenho, as regras da firewall ao nível do servidor estão temporariamente em cache ao nível da base de dados. Para atualizar a cache, consulte o artigo [DBCC FLUSHAUTHCACHE](https://msdn.microsoft.com/library/mt627793.aspx). Para mais informações sobre as regras da firewall ao nível do servidor, consulte o artigo [como: configurar uma firewall de servidor do Azure SQL através do portal Azure](sql-database-configure-firewall-settings.md).

## <a name="creating-database-level-firewall-rules"></a>Criar regras de firewall de nível de base de dados

Depois de ter configurado a primeira firewall de nível do servidor, poderá querer restringir o acesso a determinadas bases de dados. Se especificar um intervalo de endereços IP a regra de firewall de nível de base de dados que se encontra fora do intervalo especificado na regra ao nível do servidor firewall, apenas os clientes que têm endereços IP do intervalo de nível de base de dados podem aceder a base de dados. Pode ter um máximo de regras de 128 firewall de nível de base de dados para uma base de dados. As regras da firewall do nível de base de dados para bases de dados de modelo global e de utilizador podem ser criadas e geridas durante Transact-SQL. Para mais informações sobre como configurar regras de firewall de nível de base de dados, consulte o artigo [sp_set_database_firewall_rule (bases de dados do SQL Azure)](https://msdn.microsoft.com/library/dn270010.aspx).

## <a name="programmatically-managing-firewall-rules"></a>Através de programação gerir regras de firewall

Para além do portal do Azure, as regras de firewall podem ser geridas através de programação utilizando Transact-SQL, REST API e Azure PowerShell. As tabelas seguintes descrevem o conjunto de comandos disponíveis para cada método.


### <a name="transact-sql"></a>O Transact-SQL

| Vista de catálogo ou procedimento armazenado                                                           | Nível     | Descrição                                          |
|--------------------------------------------------------------------------------------------|-----------|------------------------------------------------------|
| [sys.firewall_rules](https://msdn.microsoft.com/library/dn269980.aspx)                   | Servidor    | Apresenta as regras da firewall ao nível do servidor atual     |
| [sp_set_firewall_rule](https://msdn.microsoft.com/library/dn270017.aspx)             | Servidor    | Cria ou actualiza as regras da firewall ao nível do servidor       |
| [sp_delete_firewall_rule](https://msdn.microsoft.com/library/dn270024.aspx)          | Servidor    | Remove as regras da firewall ao nível do servidor                  |
| [sys.database_firewall_rules](https://msdn.microsoft.com/library/dn269982.aspx)        | Base de dados  | Apresenta as regras de firewall de nível de base de dados atual   |
| [sp_set_database_firewall_rule](https://msdn.microsoft.com/library/dn270010.aspx)    | Base de dados  | Cria ou actualiza as regras de firewall de nível de base de dados |
| [sp_delete_database_firewall_rule](https://msdn.microsoft.com/library/dn270030.aspx) | Bases de dados | Remove base de dados do nível de regras de firewall                |

### <a name="rest-api"></a>REST API

| API                  | Nível  | Descrição                                                      |
|----------------------|--------|------------------------------------------------------------------|
| [Lista regras de Firewall](https://msdn.microsoft.com/library/azure/dn505715.aspx)  | Servidor | Apresenta as regras da firewall ao nível do servidor atual                 |
| [Criar regra de Firewall](https://msdn.microsoft.com/library/azure/dn505712.aspx) | Servidor | Cria ou actualiza as regras da firewall ao nível do servidor                   |
| [Configurar a regra de Firewall](https://msdn.microsoft.com/library/azure/dn505707.aspx)    | Servidor | Atualiza as propriedades de uma regra da firewall ao nível do servidor existente |
| [Eliminar a regra de Firewall](https://msdn.microsoft.com/library/azure/dn505706.aspx) | Servidor | Remove as regras da firewall ao nível do servidor                              |


### <a name="azure-powershell"></a>Azure PowerShell

| Cmdlet                                                                                              | Nível  | Descrição                                                      |
|-----------------------------------------------------------------------------------------------------|--------|------------------------------------------------------------------|
| [Get-AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546731.aspx)    | Servidor | Devolve as regras da firewall ao nível do servidor atual                  |
| [Novo AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546724.aspx)    | Servidor | Cria uma nova regra da firewall ao nível do servidor                         |
| [Definir AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546739.aspx)    | Servidor | Atualiza as propriedades de uma regra da firewall ao nível do servidor existente |
| [Remover AzureSqlDatabaseServerFirewallRule](https://msdn.microsoft.com/library/azure/dn546727.aspx) | Servidor | Remove as regras da firewall ao nível do servidor                              |

> [AZURE.NOTE] Podem existir para cima, tanto quanto um minuto cinco atrasar para alterações as definições da firewall entre em vigor.

## <a name="troubleshooting-the-database-firewall"></a>Resolução de problemas da firewall de base de dados

Quando o acesso ao serviço de base de dados do Microsoft Azure SQL não comportam-se de como esperado, tenha em consideração os seguintes pontos:

- **Configuração da local firewall:** Antes do seu computador pode aceder a base de dados do SQL Azure, poderá ter de criar uma exceção de firewall no seu computador para porta TCP 1433. Se estiver a fazer ligações dentro dos limites do Azure na nuvem, poderá ter de abrir portas adicionais. Para obter mais informações, consulte o artigo o **V12 da base de dados SQL: fora vs dentro** secção de [portas para além das 1433 para ADO.NET 4,5 e V12 de base de dados do SQL](sql-database-develop-direct-route-ports-adonet-v12.md).

- **Tradução de endereço (NAT) de rede:** Devido ao NAT, o endereço IP utilizado pelo seu computador para ligar à base de dados do SQL Azure pode ser diferente que o endereço IP apresentado nas definições de configuração de IP do computador. Para ver o endereço IP que o computador está a utilizar para ligar ao Azure, inicie a sessão portal e navegue para o separador **Configurar** no servidor que aloja a sua base de dados. Na secção **Endereços IP permitidos** , o **Endereço IP do cliente atual** é apresentado. Clique em **Adicionar** para os **Endereços IP permitidos** para permitir que este computador para aceder ao servidor.

- **Alterações à lista Permitir não foram aplicadas ainda:** Poderão existir, tanto quanto atrasar de cinco minutos para que as alterações a configuração da base de dados do Azure SQL firewall entre em vigor.

- **o início de sessão não está autorizado ou uma palavra-passe incorretas foi utilizada:** Se um início de sessão não possui permissões no servidor de base de dados do Azure SQL ou a palavra-passe estiver incorreta, é negada a ligação ao servidor de base de dados do Azure SQL. Criar uma definição de firewall fornece apenas os clientes com uma oportunidade para tentar ligar-se ao seu servidor; cada cliente tem de fornecer as credenciais de segurança necessárias. Para mais informações sobre como preparar os inícios de sessão, consulte o artigo gerir bases de dados, os inícios de sessão e os utilizadores na base de dados do SQL Azure.

- **Endereço IP dinâmico:** Se tiver uma ligação à Internet com endereçamento de IP dinâmico e são está com dificuldades em através da firewall, poderia experimente um dos seguintes soluções:

 - Pergunte ao seu fornecedor de serviços Internet (ISP) para o intervalo de endereços IP atribuído a sua computadores de cliente acedem ao servidor de base de dados do Azure SQL e, em seguida, adicione o intervalo de endereços IP como uma regra de firewall.

 - Obter endereçamento IP estático em vez disso, para os computadores cliente e, em seguida, adicione os endereços IP como as regras de firewall.

## <a name="next-steps"></a>Próximos passos

Para saber como para artigos sobre como criar regras de firewall ao nível do servidor e ao nível da base de dados, consulte o artigo:

- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do portal Azure](sql-database-configure-firewall-settings.md)
- [Configurar regras de firewall ao nível do servidor e ao nível da base de dados de base de dados do Azure SQL utilizando o T-SQL](sql-database-configure-firewall-settings-tsql.md)
- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL através do PowerShell](sql-database-configure-firewall-settings-powershell.md)
- [Configurar regras de firewall de nível do servidor de base de dados do Azure SQL utilizar a API REST](sql-database-configure-firewall-settings-rest.md)

Para obter um tutorial sobre como criar uma base de dados, consulte o artigo [criar uma base de dados do SQL em minutos através do portal Azure](sql-database-get-started.md).
Para obter ajuda sobre como ligar a uma base de dados do Azure SQL a partir abrir origem ou aplicações de terceiros, consulte o artigo [Guia de cliente amostras de código para a base de dados SQL](https://msdn.microsoft.com/library/azure/ee336282.aspx).
Para compreender como navegar para bases de dados, consulte o artigo [Gerir a segurança da base de dados do access e inicie sessão](https://msdn.microsoft.com/library/azure/ee336235.aspx).



## <a name="additional-resources"></a>Recursos adicionais

- [Proteger a sua base de dados](sql-database-security.md)
- [Centro de segurança do motor de base de dados do SQL Server e base de dados Azure SQL](https://msdn.microsoft.com/library/bb510589)

<!--Image references-->
[1]: ./media/sql-database-firewall-configure/sqldb-firewall-1.png
